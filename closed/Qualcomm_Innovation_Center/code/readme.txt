######
###### Build mlperf_app.apk with QTI backend support for testing on Android
######

git clone https://github.com/mlcommons/mobile_app_open
cd mobile_app_open
git checkout submission-v3.0
git apply 0001-QTI-submission-for-MLPerf-V3.0.patch
# copy the snpe sdk to mobile_back_qti
# make sure to copy snpe-2.07.0.4260,
make OFFICIAL_BUILD=false FLUTTER_BUILD_NUMBER=1 WITH_QTI=1 docker/flutter/android/release

# Install app to device
adb install output/android-apks/release.apk
#Open the app once on the device (to create the app's directory)

# Generate the DLCs
export SNPE_SDK=<path to unzipped snpe-2.07.0.4260>
cd mobile_back_qti/DLC/
make

# Push the models to the device
adb push output/DLC/mlperf_models /sdcard/Android/data/org.mlcommons.android/mlperfbench/files/

# Push the datatsets to the device
adb push mlperf_datasets /sdcard/mlperf_datatsets

# select submission mode and press GO


#######
####### Build commandline app with QTI backend support for testing on Windows on Arm
#######

git clone https://github.com/mlcommons/mobile_app_open
cd mobile_app_open
git checkout submission-v3.0
git apply 0001-QTI-submission-for-MLPerf-V3.0.patch
# copy the snpe sdk for windows to mobile_back_qti
# make sure to copy snpe-2.07.0.4260_windows,
# Sample path to arm64RT libs: "C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Redist\MSVC\14.29.30133\arm64\Microsoft.VC142.CRT"
make WITH_QTI=1 WITH_TFLITE=0 FLUTTER_MSVC_ARM_DLLS=<path to arm64RT libs> flutter/windows/cmdline/release

# the binary files are generated in output\flutter-windows\cmdline

# Generate the DLCs (needs to be done using Android SNPE SDK on a linux machine)
# Same DLCs as generated above for Android can also be used.
export SNPE_SDK=<path to unzipped snpe-2.07.0.4260>
cd mobile_back_qti/DLC/
make
 
#push all the command line files in to the Windows device.
#push all the datasets to the device to C:\Dropbox\lite_datasets and complete datasets to C:\Dropbox\mlperf_datasets
#push all the DLCs to the device to C:\Dropbox\mlperf_models
#open powershell on the device. CD to the cmdline folder on the device.
#Use the script .\run_performance_tests.bat to run performance tests
#Use the script .\run_accuracy_test.bat to run accuracy tests
.\run_performance_tests.bat --dataset C:\Dropbox\lite_datasets --models C:\Dropbox\mlperf_models
.\run_accuracy_tests.bat --dataset C:\Dropbox\mlperf_datasets --models C:\Dropbox\mlperf_models
#see the results in accuracy_results.txt and performance_results.txt
