---
title: 文件IO方式操作
---

## GPIO编号及复用功能


在Linux中，GPIO 使用0～MAX_INT之间的整数标识。

对于32位CPU，每组GPIO 32个，引脚号就是按顺序排列。

LicheePi Zero的所有IO的复用功能及GPIO标识号为：

\>\> 总共52个IO，所有IO上电默认状态为高阻态, 驱动电流强度20mA \>\> PB和PG具有中断功能

|Pin Name|Pin Number|  Func2  |    Func3    |  Func4  |Func5| Func6  |
| ------ | -------- | ------- | ---------- | ------- | ----- | ---- |
|PB0     |        32|UART2_TX |\-           |\-       |\-   |PB_EINT0|
|PB1     |        33|UART2_RX |\-           |\-       |\-   |PB_EINT1|
|PB2     |        34|UART2_RTS|\-           |\-       |\-   |PB_EINT2|
|PB3     |        35|UART2_CTS|\-           |\-       |\-   |PB_EINT3|
|PB4     |        36|PWM0     |\-           |\-       |\-   |PB_EINT4|
|PB5     |        37|PWM1     |\-           |\-       |\-   |PB_EINT5|
|PB6     |        38|TWI0_SCK |\-           |\-       |\-   |PB_EINT6|
|PB7     |        39|TWI0_SDA |\-           |\-       |\-   |PB_EINT7|
|PB8     |        40|TWI1_SCK |UART0_TX     |\-       |\-   |PB_EINT8|
|PB9     |        41|TWI1_SDA |UART0_RX     |\-       |\-   |PB_EINT9|
|PC0     |        64|SDC2_CLK |SPI0_MISO    |\-       |\-   |\-      |
|PC1     |        65|SDC2_CMD |SPI0_CLK     |\-       |\-   |\-      |
|PC2     |        66|SDC2_RST |SPI0_CS      |\-       |\-   |\-      |
|PC3     |        67|SDC2_D0  |SPI0_MOSI    |\-       |     |        |
|PE0     |       128|CSI_PCLK |LCD_CLK      |\-       |\-   |\-      |
|PE1     |       129|CSI_MCLK |LCD_DE       |\-       |\-   |\-      |
|PE2     |       130|CSI_HSYNC|LCD_HSYNC    |\-       |\-   |\-      |
|PE3     |       131|CSI_VSYNC|LCD_VSYNC    |\-       |\-   |\-      |
|PE4     |       132|CSI_D0   |LCD_D2       |\-       |\-   |\-      |
|PE5     |       133|CSI_D1   |LCD_D3       |\-       |\-   |\-      |
|PE6     |       134|CSI_D2   |LCD_D4       |\-       |\-   |\-      |
|PE7     |       135|CSI_D3   |LCD_D5       |\-       |\-   |\-      |
|PE8     |       136|CSI_D4   |LCD_D6       |\-       |\-   |\-      |
|PE9     |       137|CSI_D5   |LCD_D7       |\-       |\-   |\-      |
|PE10    |       138|CSI_D6   |LCD_D10      |\-       |\-   |\-      |
|PE11    |       139|CSI_D7   |LCD_D11      |\-       |\-   |\-      |
|PE12    |       140|CSI_D8   |LCD_D12      |\-       |\-   |\-      |
|PE13    |       141|CSI_D9   |LCD_D13      |\-       |\-   |\-      |
|PE14    |       142|CSI_D10  |LCD_D14      |\-       |\-   |\-      |
|PE15    |       143|CSI_D11  |LCD_D15      |\-       |\-   |\-      |
|PE16    |       144|CSI_D12  |LCD_D18      |\-       |\-   |\-      |
|PE17    |       145|CSI_D13  |LCD_D19      |\-       |\-   |\-      |
|PE18    |       146|CSI_D14  |LCD_D20      |\-       |\-   |\-      |
|PE19    |       147|CSI_D15  |LCD_D21      |\-       |\-   |\-      |
|PE20    |       148|CSI_FIELD|CSI_MIPI_MCLK|\-       |\-   |\-      |
|PE21    |       149|CSI_SCK  |TWI1_SCK     |UART1_TX |\-   |\-      |
|PE22    |       150|CSI_SDA  |TWI1_SDA     |UART1_RX |\-   |\-      |
|PE23    |       151|\-       |LCD_D22      |UART1_RTS|\-   |\-      |
|PE24    |       152|\-       |LCD_D23      |UART1_CTS|\-   |\-      |
|PF0     |       160|SDC0_D1  |JTAG_MS      |\-       |\-   |\-      |
|PF1     |       161|SDC0_D0  |JTAG_DI      |\-       |\-   |\-      |
|PF2     |       162|SDC0_CLK |UART0_TX     |\-       |\-   |\-      |
|PF3     |       163|SDC0_CMD |JTAG_DO      |\-       |\-   |\-      |
|PF4     |       164|SDC0_D3  |UART0_RX     |\-       |\-   |\-      |
|PF5     |       165|SDC0_D2  |JTAG_CK      |\-       |\-   |\-      |
|PF6     |       166|\-       |\-           |\-       |\-   |\-      |
|PG0     |       192|SDC1_CLK |\-           |\-       |\-   |PG_EINT0|
|PG1     |       193|SDC1_CMD |\-           |\-       |\-   |PG_EINT1|
|PG2     |       194|SDC1_D0  |\-           |\-       |\-   |PG_EINT2|
|PG3     |       195|SDC1_D1  |\-           |\-       |\-   |PG_EINT3|
|PG4     |       196|SDC1_D2  |\-           |\-       |\-   |PG_EINT4|
|PG5     |       197|SDC1_D3  |\-           |\-       |\-   |PG_EINT5|


## sysfs操作GPIO


/sys/class/gpio目录下的三种文件：

-   export/unexport文件
-   gpioN指代具体的gpio引脚
-   gpio_chipN指代gpio控制器

**export/unexport：**

-   /sys/class/gpio/export，只写，写入GPIO编号来向内核申请GPIO控制权（前提是没有内核代码申请这个gpio端口）,
    成功后会在目录下生成gpioN目录。
-   /sys/class/gpio/unexport和导出的效果相反。

**gpioN:**

指代某个具体的gpio端口, 内有以下属性文件：

|Attribution|Read/Write|             Value             |     Function     |
| ------ | -------- | ------- | ---------- |
|direction  |RW        |in,out;low,high                |设置输入输出      |
|value      |RW        |0,非零                         |读取或者写入IO电平|
|edge       |RW        | none , rising , falling , both|配置中断触发方式  |
|active_low |RW        |0,非零                         |设置低电平有效    |

**gpiochipN**

gpiochipN表示的就是一个gpio_chip,用来管理和控制一组gpio端口的控制器，该目录下存在以下属性文件：

|Attribution|                 Function                  |
| ------ | -------- |
|base       |和N相同，表示控制器管理的最小的端口编号。  |
|lable      |诊断使用的标志，寄存器地址，1c20800.pinctrl|
|ngpio      |表示控制器管理的gpio端口数量，A~G，224     |

使用sysfs操作GPIO的例子：

    #echo 192 > /sys/class/gpio/export  #导出 PG0, GREEN
    #ls /sys/class/gpio/
    export     gpio192    gpiochip0  unexport
    #ls /sys/class/gpio/gpio192/
    active_low direction subsystem/ value device/ power/ uevent
    #echo "out" > /sys/class/gpio/gpio192/direction #设置为输出
    #echo 0 > /sys/class/gpio/gpio192/value #亮灯
    #echo 1 > /sys/class/gpio/gpio192/value #灭灯
    #echo "in" > /sys/class/gpio/gpio192/direction #设置为输入
    #cat /sys/class/gpio/gpio192/value #读取电平
    0

用户可以参考以上操作进行GPIO控制。

注意对重要引脚的导出操作可能会使系统崩溃。

LicheePi
Zero提供了简单的shell脚本进行GPIO读写(代码在https://github.com/Lichee-Pi/lichee-pi-zero/tree/master/SoftWare，下同)：

    gpio.sh init 192 out
    gpio.sh set 192 out
    gpio.sh get 192
    gpio.sh w 192 1
    gpio.sh r 192 
    gpio.sh deinit 192

## 附录（gpio.sh源码）


```
#!/bin/sh
function help()
{
echo "gpio.sh usage:"
echo "        gpio.sh init PG0 out"
echo "        gpio.sh set PG0 out"
echo "        gpio.sh get PG0"
echo "        gpio.sh w PG0 1"
echo "        gpio.sh r PG0"
echo "        gpio.sh deinit PG0"
}

if [ $# -lt 2 ]; then
        help;
        exit;
fi

portpin=`echo $2 | tr 'a-z' 'A-Z'`;
port=${portpin:1:1};
pin=${portpin:2:1};
#echo $port
#echo $pin
num=`printf "%d" "'$port"`;
num=`expr \( $num - 65 \) \* 32 + $pin`;
if [ $? -ne 0 ]; then
        help;
        exit                         
fi                                   
#echo $num                                

case $1 in                                            
        init)                                         
        echo $num > /sys/class/gpio/export            
        echo $3 > /sys/class/gpio/gpio${num}/direction
        ;;                                            
        set)                                           
        echo $3 > /sys/class/gpio/gpio${num}/direction 
        ;;                                             
        get)                                           
        echo `cat /sys/class/gpio/gpio${num}/direction`
        ;;                                             
        w)                                             
        echo $3 > /sys/class/gpio/gpio${num}/value
        ;;                                        
        r)                                        
        echo `cat /sys/class/gpio/gpio{num}/value`    
        ;;                                            
        deinit)                                       
        echo $num > /sys/class/gpio/unexport          
        ;;      
        *)                                            
        help                                          
        ;;                                            
esac  
```
