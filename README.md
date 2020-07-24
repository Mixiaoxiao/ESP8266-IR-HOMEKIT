# ESP8266-IR-HOMEKIT

## 原生HomeKit红外空调遥控

![HomeKit](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/home_app_pages.png) 

主芯片为ESP8266，原生HomeKit红外空调遥控，支持数十种空调遥控协议。

现阶段为体验版固件，在`firmware`文件夹中提供固件下载。

目前仍在开发中，部分功能以后可能会有改动。

注：空调，Air Conditioner，简写为AC

## 主要功能

- 原生HomeKit协议，无需服务器桥接，基于我的[Arduino-HomeKit-ESP8266](https://github.com/Mixiaoxiao/Arduino-HomeKit-ESP8266)
- 支持控制空调的开关、模式、温度、风速、扫风、灯光
- 支持数十种空调遥控协议，红外控制基于[IRremoteESP8266](https://github.com/crankyoldgit/IRremoteESP8266)


## WiFi配网

AP模式配网，自敲代码，效果图见下一小节图示的的倒数第二项
- ESP8266在未联网/断网时会生成`ESP_CONFIG_XXXXXX`的热点
- 手机连接该热点会自动弹出配网页面，如果未自动弹出可手动访问192.168.4.1
- 扫描WiFi，填写WiFi名称和密码点击发送配置，连接成功后会自动退出配网模式并关闭AP热点
- WiFi状态指示灯IO2(D2)：即芯片上靠近天线处的蓝色LED，未联网为闪烁，已联网为熄灭

## Web功能

空调红外(IR)设置与控制，访问`http://<esp_ip>`，Web APP型页面，效果见下图

注：`<esp_ip>`为你的ESP8266联网后的IP地址，下同


![ir_homekit_web_pages.png](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/ir_homekit_web_pages.png) 

- 设置空调协议Protocol、子型号Model，**需设置与实体遥控器匹配的空调协议和子型号**
- 控制空调的开关Power、模式Mode、温度Temperature、风速Fan Speed、扫风Swing（垂直V/水平H）、灯光Light

## Web扩展功能

- WiFi Config，自敲代码的配网功能，访问`http://<esp_ip>/wificonfig`，用于配网或重新设置网络，见上一小节图示的倒数第二项
- File Manager，自敲代码的Web文件管理功能，访问`http://<esp_ip>/file`，用于管理SPIFFS文件系统，支持上传/查看/删除文件，见上一小节图示的最后一项
- Web Update，访问`http://<esp_ip>/update`，用于固件更新


## HomeKit家庭添加配件

- 配对设置代码：`111-11-111`

![add_accessory.png](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/add_accessory.png) 


## 硬件连接

引脚定义
- IR发射：IO14(D5)，必需
- IR接收：IO12(D6)，非必需
- WiFi指示灯：IO2(D2)，即ESP8266芯片上靠近天线处的蓝色LED
- 红外指示灯：IO13(D7)，非必需
- 按键：IO0(D3)，即NodeMCU开发板上的Flash按键

![hardware_wire.png](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/hardware_wire.png) 

- 红外发射管：图中白色两脚LED所示，即普通的940nm红外发射管，直径3mm或5mm均可。一般长脚为正极，灯罩圆形有缺口侧为负极
- NPN型三极管(非必需)：图中的带“N”标志的为NPN型三极管，用于放大电流
- 红外接收管(非必需)：图中黑色三脚LED所示，VS1828、HX1838、PC638通用型红外接收头，引脚从左到右为DATA(OUT)、GND、VCC

**最简电路：仅使用红外发射管，正极脚接NodeMCU的IO14(D5)，负极脚接NodeMCU的GND。**

ESP8266的IO驱动电流约为10mA，可使得红外发射管正常工作，但遥控距离较近，一般仅有1米左右。建议使用三极管或MOS管驱动，实现更高电流更远遥控。
实测自制的PCB，使用6枚全向100mA电流的5MM发射管可实现全屋无死角遥控，可参考网上的米家/天猫精灵万能红外遥控的拆机图。

按键功能(以后可能会有改动)，即NodeMCU的Flash按键：

- 单击：进入红外接收识别状态，用空调原配遥控器对准红外接收管按下任意按键，将尝试识别该空调协议，在Web页面的`Last IR Receive`一栏处会显示最后一次识别到的空调协议（需刷新页面）。识别到有效协议后会自动退出识别状态。超时1分钟未识别到有效协议也会退出识别状态
- 双击：发射一次红外信号（使用当前的空调设置）
- 长按：重置HomeKit（移除配对信息）并重启


## 附：将网页添加到桌面

iOS上可将网页放到桌面，当作Web APP来用

![add_to_home_screen.png](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/add_to_home_screen.png) 

## 附：固件烧录

![esp_flash_example.png](https://raw.github.com/Mixiaoxiao/ESP8266-IR-HOMEKIT/master/image/esp_flash_example.png)

- 可使用乐鑫官方提供的烧录工具，[官网下载地址](https://www.espressif.com/zh-hans/support/download/other-tools)
- 下载`Flash 下载工具（ESP8266 & ESP32 & ESP32-S2）`，运行时选择`Developer Mode`
- 提供的固件bin为完整固件，刷写至`0`地址，如图靠上面一栏的@后面填0
- 右下角COM口选择你的NodeMCU开发板接入电脑的COM口，如果不确定是哪个可以都试试
- BAUD即波特率，数值越高烧写速度越快，一般可选921600
- 其他选项可按图选择，图中配置仅针对NodeMCU
- 都选好后点击左下角的`START`即可下载，921600波特率下一般不到10秒

## 注意

- 必须正确选择空调的协议Protocol、子型号Model 
- 因存在代工等原因，协议和空调品牌不一定完全对应，如格力(Gree)空调可能为`18. KELVINATOR`协议
- 子型号Model(1~6)仅对部分协议有效，`(5)Fujitsu/Panasonic`意为当协议选为Fujitsu或Panasonic时选用其下属的第(5)个子型号，其他协议不具有第(5)个子型号

## QQ群：686409089

更多对于ESP8266/ESP32开发以及HomeKit库的使用和交流，欢迎大家加群讨论：

![QQGroup](https://raw.github.com/Mixiaoxiao/Arduino-HomeKit-ESP8266/master/extras/qq_group_qrcode.png) 
