# PICO-8 用户手册

> [!IMPORTANT]
>
> 该版本的手册属于非官方翻译，由 fadedflower 个人翻译并自用，出现翻译错误属于正常现象，请务必指出

PICO-8 v0.2.6  
https://www.pico-8.com  
(c) Copyright 2014-2024 Lexaloffle Games LLP  
作者：Joseph White // hey@lexaloffle.com 

PICO-8 的构建用到下面这些库： 

SDL2 http://www.libsdl.org  
Lua 5.2 http://www.lua.org  // 见 license.txt  
ws281x by jgarff       // 见 license.txt  
GIFLIB http://giflib.sourceforge.net/  
WiringPi http://wiringpi.com/  
libb64 by Chris Venter  
miniz by Rich Geldreich  
z8lua by Sam Hocevar https://github.com/samhocevar/z8lua

本手册的最新版本（格式含 html，txt）以及其他资源：

https://www.lexaloffle.com/pico-8.php?page=resources

## 欢迎来到 PICO-8

PICO-8 是一款虚构游戏机，能够制作、分享和游玩小游戏以及其他计算机程序。启动时，机器首先会给你提供一个 Shell，用来输入 Lua 程序。它还提供了简单的内置工具，用来创建精灵图像、地图和声音。

PICO-8 的严苛限制是经过精挑细选的，这样使用起来会更有乐趣，鼓励小而精的设计，希望能给予 PICO-8 卡带独特的外表和风格。

## 技术规范

> 显示屏：   128x128，固定 16 色色板  
> 输入：       6 按钮控制器  
> 卡带：       32k 数据，以 png 文件格式存储  
> 声音：       4 通道，64 清晰度芯片  
> 代码：       P8 Lua（代码最高 8192 个 Token）  
> CPU：       每秒 4M 虚拟机指令  
> 精灵图像：单块 128 个 8x8 精灵图像（附加 128 个图像，位于共享内存）  
> 地图：       128x32 瓦片地图（附加 128x32 地图，位于共享内存）

# 1 起步

## 1.1 键位

> ALT+ENTER：切换全屏  
> ALT+F4：       快速退出（Windows）  
> CTRL-Q：      快速退出（Mac，Linux）  
> CTRL-R：      重载 / 运行 / 重启卡带  
> CTRL-S：      快速保存当前卡带  
> CTRL-M：     静音 / 取消静音  
> ENTER / P：  暂停菜单（卡带运行期间）

> 玩家 1 默认键位：鼠标指针 + ZX / NM / CV  
> 玩家 2 默认键位：SDFE + tab，Q / shift A

要改变默认键位，在 PICO-8 内部使用 KEYCONFIG 实用工具：

```
> KEYCONFIG
```

## 1.2 Hello World

PICO-8 启动后，尝试输入下面这些命令并用 enter 键结束：

``````lua
> PRINT("HELLO WORLD")
> RECTFILL(80,80,120,100,12)
> CIRCFILL(70,90,20,14)
> FOR I=1,4 DO PRINT(I) END
``````

> [!NOTE]
>
> PICO-8 只显示大写字符 -- 所以只需要正常输入，不需要大写锁定！

在代码编辑模式中，使用像这样的命令以及两个特殊的回调函数 `_UPDATE` 和 `_DRAW` ，你可以构建一个交互式程序。例如，接下来这个程序允许你使用方向键四处移动一个圆。按 Esc 键切换到代码编辑器，输入或者复制粘贴下列代码：

``````lua
X = 64  Y = 64
FUNCTION _UPDATE()
  IF (BTN(0)) THEN X=X-1 END
  IF (BTN(1)) THEN X=X+1 END
  IF (BTN(2)) THEN Y=Y-1 END
  IF (BTN(3)) THEN Y=Y+1 END
END
 
FUNCTION _DRAW()
  CLS(5)
  CIRCFILL(X,Y,7,14)
END
``````

现在按下 Esc 键回到终端，输入 `RUN` （或者按下 CTRL-R）查看运行结果。需要更复杂的程序，请参考 Demo 卡带（输入 `INSTALL_DEMOS`）。

如果你想要保存你的程序，日后再用，使用 `SAVE` 命令：

``````
> SAVE PINKCIRC
``````

要再次加载这个程序：

``````
> LOAD PINKCIRC
``````

## 1.3 示例卡带

这些卡带包含在 PICO-8 里，若要安装，输入下面的命令：

``````
> INSTALL_DEMOS
> CD DEMOS
> LS
 
HELLO      来自 PICO-8 的问候
API        演示大部分 PICO-8 的功能
JELPI      平台游戏 Demo 以及 2p 支持
CAST       2.5D 光线投射 Demo
DRIPPY     绘制往下流淌的涂鸦
WANDER     简单的步行模拟器
COLLIDE    墙和物体碰撞的示例
``````

要运行一个卡带，打开 PICO-8 并输入：

``````
> LOAD JELPI
> RUN
``````

按 Esc 键停止程序运行，再按一次进入编辑模式。

一小部分 BBS 卡带可以用下面的命令安装到 `/GAMES` ：

``````
> INSTALL_GAMES
``````

## 1.4 文件系统

以下这些命令用来管理文件和目录（文件夹）：

``````
LS          列出当前目录
CD BLAH     切换目录
CD ..       切换上一级目录
CD /        切换回根目录（在 PICO-8 的虚拟磁盘）
MKDIR BLAH  创建一个目录
FOLDER      在宿主操作系统的文件浏览器中打开当前目录
``````

`LOAD BLAH` 从当前目录中加载一个卡带，`SAVE BLAH` 将卡带保存到当前目录。

如果你想要把文件移到别处，复制一份或者删除他们，使用 `FOLDER` 命令并在宿主操作系统中完成这些操作。

PICO-8 的磁盘的默认位置是：

Windows：C:/Users/Yourname/AppData/Roaming/pico-8/carts  
OSX：/Users/Yourname/Library/Application Support/pico-8/carts  
Linux：~/.lexaloffle/pico-8/carts

你可以在 pico-8/config.txt 中修改这个位置以及其他设置。

> [!TIP]
>
> 磁盘目录可以映射到云盘（Dropbox，Google Drive 或者类似的平台），这能够在不同宿主机上的 PICO-8 机器中共享单一磁盘。

## 1.5 加载和保存

使用 `LOAD` 和 `SAVE` 的时候，`.P8` 扩展名可以省略，它会自动添加。

``````
> SAVE FOO
SAVED FOO.P8
``````

卡带文件也可以拖放到 PICO-8 窗口以加载。

使用 `.p8.png` 文件扩展名可以以一种特殊的图像格式保存卡带，看上去像一个卡带。使用 `.p8.rom` 会保存为原始的 32k 二进制格式。

使用 `@clip` 文件名以加载或保存到剪贴板。

如果卡带能在 2040 个字符内进行编码（只含代码和 gfx），使用 `@url` 文件名以生成 pico-8-edu.com 链接并保存到剪贴板。

一旦加载或保存了卡带，你还能用 CTRL-S 快速保存卡带。

### 保存为 .p8.png 卡带，带有文本标签和预览图像

要生成一个附在卡带上的标签图像，首先运行程序并按下 CTRL-7 捕捉屏幕当前的图像。以“--”开头的前两行程序文本也会被绘制到卡带的标签上。

``````
-- OCEAN DIVER LEGENDS
-- BY LOOPY
``````

### .p8.png / .p8.rom 格式的代码大小限制

保存为 `.png` 或 `.rom` 格式时，代码的压缩大小必须少于 15360 字节，这样总大小就会小于等于 ß32k。

要了解代码当前的大小，使用 `INFO` 命令。保存为 `.p8` 格式则没有这种压缩大小的限制。

## 1.6 使用外部文本编辑器

用单独的文本编辑器直接编辑 `.p8` 文件是可行的。满足下列条件时，使用 CTRL-R 运行卡带会自动重载文件：

1. PICO-8 编辑器中没有未保存的改动
2. 文件内容与上一次加载的版本相比有改动

如果磁盘上的卡带和编辑器内都有所改动，屏幕会显示一条通知：

``````
DIDN'T RELOAD; UNSAVED CHANGES
``````

还有另一种方法，`.lua` 文本文件可以在单独的文本编辑器中修改，之后在卡带中使用 `#INCLUDE` （在合适的代码位置），这样每次运行时，这些代码都会包含到卡带代码中：

``````lua
#INCLUDE YOURFILE.LUA
``````

## 1.7 备份