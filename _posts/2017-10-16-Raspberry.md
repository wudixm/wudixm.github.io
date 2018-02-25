### 安装python 的蓝牙模块pybluez

要安装pybluez 需要先安装python-dev(不安装提示缺少Python.h 文件)，

```shell
sudo apt-get install python-dev
```

再安装libbluetooth-dev(不安装提示bluetooth/bluetooth.h: No such file or directory)，

```shell
sudo apt-get install libbluetooth-dev
```

再安装pybluez

```shell
sudo pip install pybluez
```

### 控制输出电平高低

- T型扩展板接入面包板之后面包板上的各个口与实际Pi上的顺序不一致，如右侧从上往下数第七个口是gpio.setmode(BOARD)的22口，如下面的程序中的pin 值为22。但是如果将pin值改为24，则输出的端口在左侧的从上往下第2个口(记不太清，大概这个位置)

```python
import RPi.GPIO as gpio
import time
class test:
        pin = 22 # 这个是面包板上右侧从上往下数第7个口
        @staticmethod
        def a():

                gpio.setwarnings(False)
                gpio.setmode(gpio.BOARD)
                time.sleep(1)
                print "begin to while"
                count = 1
                while(count < 3):
                        gpio.setup(test.pin, gpio.OUT)
                        gpio.output(test.pin, gpio.LOW)
                        time.sleep(10)
                        #gpio.output(test.pin, gpio.HIGH)
                        #count = count + 1 这个注释掉了，这样可以让程序一直运行
                        #time.sleep(10)
                print "100 count finishedd" # 这些打印是在测试的时候方便看的，数值不是准确值

                gpio.cleanup() # 如果不清理端口状态，端口的电平会一直是上一次改变后的值

if __name__ == '__main__':
        test.a()
```

### 继电器

VCC接树莓派5V针脚（红色）
GND 接树莓派GND针脚（棕色）
IN接树莓派GPIO25，或者其他的GPIO也可以，只要与程序控制的pin脚是一致的（橙色）
红色、棕色、橙色的杜邦线接法与下图“面包板接线图”是一致的

常开，正常状态为开路（开路即断开的），通电后关闭（连通）
公共端，顾名思义
常闭，正常状态为闭合（连通状态），通电后断开（开路）
因此，继电器是没有正负极之分，正负极可以任意接在常开或常闭，是否有电流通过取决于当前低电端给予的电平情况。
PS：接入家用电之后，强电端与继电器扩展版底部都是有电的，需谨慎操作，不幸的我就被电过一次，所以折腾的时候请小心。

#### 原理

磁继电器一般由铁芯、线圈、衔铁、触点簧片等组成的只要在线圈两端加上一定的电压，线圈中就会流过一定的电流，从而产生电磁效应，衔铁就会在电磁力吸引的作用下克服返回弹簧的拉力吸向铁芯，从而带动衔铁的动触点与静触点（常开触点）吸合。当线圈断电后，电磁的吸力也随之消失，衔铁就会在弹簧的反作用力返回原来的位置，使动触点与原来的静触点（常闭触点）释放。这样吸合、释放，从而达到了在电路中的导通、切断的目的。对于继电器的“常开、常闭”触点，可以这样来区分：继电器线圈未通电时处于断开状态的静触点，称为“常开触点”；处于接通状态的静触点称为“常闭触点”。继电器一般有两股电路，为低压控制电路和高压工作电路。