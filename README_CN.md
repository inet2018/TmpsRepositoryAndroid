
# TPMS SDK使用说明 - Android

[aar包下载地址](https://github.com/inet2018/TmpsRepositoryAndroid/releases)

[English documentation](README.md)

该文档为指导Android开发人员在Android 4.4及以上系统中集成TPMS-SDK-Android，主要为一些关键的使用示例
## 一、使用条件：
1.Android SDK最低版本android4.4（API 19）。
2.设备所使用蓝牙版本需要4.0及以上。
3.配置java1.8
4.项目依赖androidx库

## 二、导入SDK


```
repositories {
    flatDir {
        dirs 'libs'
    }
}


1.将JitPack存储库添加到您的构建文件中
将其添加到存储库末尾的root build.gradle中：
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}

2.添加依赖项
	dependencies {
	        implementation 'com.github.inet2018:TmpsRepositoryAndroid:1.2.0'//蓝牙库

	}

3.在gradle中配置java1.8
    android {
        ...
        compileOptions {
            sourceCompatibility 1.8
            targetCompatibility 1.8
        }
    }

也可以使用aar包依赖,请自行下载放到项目的libs中,下载地址在文档顶部



```

## 二、权限设置

```
<!--In most cases, you need to ensure that the device supports BLE.-->
<uses-feature
    android:name="android.hardware.bluetooth_le"
    android:required="true"/>

<uses-permission android:name="android.permission.BLUETOOTH"/>
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>

<!--Android 6.0 and above. Bluetooth scanning requires one of the following two permissions. You need to apply at run time.-->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

<!--Optional. If your app need dfu function.-->
<uses-permission android:name="android.permission.INTERNET"/>
```

>  6.0及以上系统必须要定位权限，且需要手动获取权限

## 三、开始集成



### 1,初始化,[key注册地址](http://sdk.aicare.net.cn/register)
```
TpmsSDK.init(this,key,secret);
```

### 2,实现TpmsScan.TpmsScanListener方法
```
TpmsScan mTpmsScan = new TpmsScan(new TpmsScan.TpmsScanListener() {
            @Override
            public void onGetData(String mac, String deviceId, int rssi, float pressure, float battery,int temp, int status, float mcuVersion, int year, int month, int day,float bleVersion){

            }
        });
```
### 3,搜索 TpmsScan.startScan(String[] deviceIdS);
```
    /**
     * 搜索设备,默认搜索所有的设备并通过TpmsScanListener接口返回
     */
     TpmsScan.startScan()

   /**
     * 搜索设备,搜索传入的设备id数组的设备并通过TpmsScanListener接口返回
     *
     */
     TpmsScan.startScan(String[] deviceIdS)

搜索到的设备会在TpmsScan.TpmsScanListener接口中的onGetData(...)返回
```
### 4,TpmsScan.TpmsScanListener接口返回的数据解析
```
public void onGetData(String mac, String deviceId, int rssi, float pressure, float battery,int temp, int status, float mcuVersion, int year, int month, int day,float bleVersion);

mac:设备mac地址
deviceId:设备id
rssi:信号值
pressure:气压
battery:电压
temp:温度
status:状态
    /**
     * 设备状态
     */
    public enum DeviceState {
        /**
         * 正常
         */
        NORMAL,
        /**
         * 漏气
         */
        LEAK,
        /**
         * 充气
         */
        INFLATE,
        /**
         * 启动
         */
        START,
        /**
         * 上电
         */
        POWER_ON,
        /**
         * 唤醒
         */
        WEAK_UP,
        /**
         * 温度异常
         */
        TEMP_ERROR,
        /**
         * 电量异常
         */
        BATTERY_ERROR,
        /**
         * 未知
         */
        UNKNOWN
    }

mcuVersion:mcu版本号
year,month,day :日期
bleVersion:蓝牙版本号


    /**
     * 设备安装位置
     */
    public enum DevicePosition {
        /**
         * 左前
         */
        LEFT_FRONT,
        /**
         * 左后
         */
        LEFT_REAR,
        /**
         * 右前
         */
        RIGHT_FRONT,
        /**
         * 右后
         */
        RIGHT_REAR
    }

```
### 5,停止搜索TpmsScan.stopScan();
