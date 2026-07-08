
# tcl-ir
####TCLwifi遥控器改造  
必要说明：  
涉及到电路改造，没有任何经验的小白不要做  

1、拆机：  
大力出奇迹，外壳应该是超声波焊接的，用椅子螺丝刀砸进去，然后慢慢就撬开了，注意备好502胶水，回头需要沾水去  

2、硬件改造：  
设备默认使用了一枚内置的红外代码芯片，由于我们要改造硬件，需要使用homeassistant的红外组件来控制，所以不需要这枚内置的红外芯片了，所有红外代码库都将使用homeassistant的smartAC红外组件    
如图所示，需要拆掉一个芯片，拆芯片的小技巧，弄一段铜线，围在2边的脚上面，然后加焊锡，当所有脚上都有焊锡的时候，镊子轻轻一拨就下来了，不会拆的去B站找相关视频，反正我觉得挺简单的  
然后就是飞线了，原来的硬件红外接受和发送在一个引脚上面，我看这个红外芯片的原理图是这么画的，自己万用表量了一下，也是这样的，但是我们使用esphome固件就得把收和发拆开，  
先要弄掉一个电阻，其实电阻让红外接收断开。然后从电阻一个焊点上飞线到esp32上面，其实这个地方也可以不飞，因为固件里面的红外接受有问题，感觉解码出来的红外代码不对  
然后就是从红外芯片的焊盘上面飞一根线到esp32芯片上面这个是红外发送

<img width="3072" height="4550" alt="hard" src="https://github.com/user-attachments/assets/5f9b8f29-15c2-429a-a0a8-2f815c6de8fc" />




3、固件编译  
修改secrets.yaml，把里面的密码参数，wifi参数修改位自己的。然后编译main.yaml这个配置文件  

4、刷机  
按照这个图接线，只需要把ttl线接到3V3,GND,ESPTX,ESPRX这4个脚，上电的时候GPIO0接地，就能进入到刷机模式，  
这个图片上的大部分gpio编号我已经量出来了  

<img width="1152" height="648" alt="tcl" src="https://github.com/user-attachments/assets/2ab41d85-42b2-4002-957a-7a403eb6e698" />


5、连接homeassistant  
网页登陆esphome之后，设置mqtt参数，重启，就能在homeassistant里面自动发现设备了  

6、添加空调设备  
将smartac.zip里面的文件夹解压到homeassistant配置目录的custom_components目录下面，大概就是如下的目录结构，复制过去之后重启hass  
custom_components---smartac----  
                           |---codes
                           |---translations
                           |---climate.py
                           |---。。。
在hass的设备里面添加smartac的设备
选择品牌之后，选择mqtt  输入框里面填写   tcl_ir-48bcb8/ir_send这里每个设备的ID{48bcb8}是不一样的，需要修改为实际id  
<img width="605" height="637" alt="hass_add_dev" src="https://github.com/user-attachments/assets/76e494f9-6202-4b7e-9e42-aa8614353793" />

然后就是测试红外代码库了，这里有个小技巧，发送红外命令的时候，用手机摄像头凑近了去看遥控器里面是否有红光，有红光说明硬件没问题，mqtt的topic配置的也没问题，  
正常添加之后就能使用这个遥控器了，但是此时的语音遥控功能还不能使用，需要添加一个自动化  

7，添加自动化，使用离线语音  
将tcl_ir_coice_ac.yaml复制到hass的配置目录的blueprints/automation/homeassistant/下面  
然后在开发者工具里面重启一下自动化  
然后进入自动化里面，新建一个自动化，选择TCL IR Remote to Midea AC Control如图所示，选择遥控器上面的一个sound的实体，再选择你刚刚创建的空调实体
<img width="1497" height="498" alt="automation" src="https://github.com/user-attachments/assets/2384b995-65de-445b-a02a-19e9909fa6a4" />

点击保存后使用语音命令就能控制空调了  
所有的语音命令参照：语音命令.txt里面



####特别说明smartac这个组件不是我原创，作者最先发布于hass论坛。大家可以去论坛上搜索smartac这个帖子，由于作者没有后续更新了，我自己修复了一下，
