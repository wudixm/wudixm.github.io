### python 虚拟环境

#### 安装虚拟环境

virtualenv，就是一个三方包
安装命令，pip install virtualenv蜂鸣器

#### 使用

virtualenv {dir / path}

命令virtualenv就可以创建一个独立的Python运行环境，我们还加上了参数--no-site-packages，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。

#### 激活

source bin/activate

#### 退出

deactivate

#### 内部
virtualenv是如何创建“独立”的Python运行环境的呢？原理很简单，就是把系统Python复制一份到virtualenv的环境，用命令source venv/bin/activate进入一个virtualenv环境时，virtualenv会修改相关环境变量，让命令python和pip均指向当前的virtualenv环境。

#### 删除

直接删除虚拟环境所在目录即

