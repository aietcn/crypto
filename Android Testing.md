# Android Testing

## Information Gathering

### System Information

### Package/Application

- About Package:

            adb shell dumpsys package <pkg_name>

- Package Log:

            adb logcat | grep `adb shell ps | grep com.example.package | cut -c10-15`


## Screen Recording

set file name, resolution, bit rate

            adb shell screenrecord --size 840x480 --bit-rate 2000000 /sdcard/test.mp4

get the file 

            adb pull /sdcard/test.mp4
