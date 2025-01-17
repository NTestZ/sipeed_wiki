# 上手使用

## 测试屏幕

硬件正常的话，开机屏幕会自动输出内核信息，也可以使用`echo "hello sipeed">/dev/tty0`重定向信息到屏幕上。

<img src="https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/image-20210805150728052.png" alt="image-20210805150728052" style="zoom:50%;" />

## 测试摄像头

armbian内置了ffmpeg，可以快速捕捉sensor数据并输出到屏幕上

使用如下命令快速捕捉sensor并输出到屏幕上

    ffmpeg -i /dev/video0 -vframes 1  -s 240*240 -pix_fmt rgb565le  -vf transpose=2,transpose=2 -f fbdev /dev/fb0


![image-20210805165654537](https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/image-20210805165654537.png)

## 连接网络

使用 nmtui 指令可以进入可视化的配网界面，选择 Activate a connection ， 选择对应的SSID，输入连接密码，确认即可。

![202108051626](https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/202108051626.gif)

设置完毕后使用ifconfig查看本机操作，然后就可以使用ssh等操作了。

![image-20210805162936212](https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/image-20210805162936212.png)

也可以使用apt下载各种应用

    sudo apt install neofetch

![image-20210805165620823](https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/image-20210805165620823.png)

如果遇到终端显示错位，还需要`sudo apt-get install xterm`安装下xterm，然后`resize`即可。

![202108061015](https://raw.githubusercontent.com/USTHzhanglu/picture/main/img/202108061015.gif)
