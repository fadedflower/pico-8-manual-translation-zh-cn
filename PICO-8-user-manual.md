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

当你不保存改动并退出，或者覆盖已有文件时，卡带的一份备份会保存到 {appdata}/pico-8/backup 。输入 `BACKUP` 也会把当前卡带的副本保存到相同的文件夹。

要在宿主操作系统的文件浏览器中打开备份文件夹，使用：

``````
> FOLDER BACKUPS
``````

把这些文件拖放到 PICO-8 窗口以进行加载是可行的。

从版本 0.2.4c 开始，如果编辑器不处于空闲状态，每 20 分钟会保存一份周期备份，这也就意味着备份文件夹的大小每 5 小时会增长大概 1MB。这项设置可以在 config.txt 中禁用或调整

## 1.8 配置

每次会话的开始，PICO-8 会从 config.txt 中读取配置信息，退出时也会保存配置（所以你应当在 PICO-8 未运行的时候编辑 config.txt 文件）

config.txt 文件的位置取决于宿主操作系统：

Windows：C:/Users/Yourname/AppData/Roaming/pico-8/config.txt  
OSX：/Users/Yourname/Library/Application Support/pico-8/config.txt  
Linux：~/.lexaloffle/pico-8/config.txt

使用 `-home` 开关（下文所述）可以用不同的路径来存放 config.txt 以及其他数据。

有些设置可以在 PICO-8 运行期间进行修改，输入 `CONFIG SETTING VALUE` 。（输入 `CONFIG` 本身来获取设置列表）

### 命令行参数

> [!NOTE]
>
> 这些开关会覆盖 config.txt 里的设置

pico8 [switches] [filename.p8]

| 开关                 | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| -width n             | 设置窗口宽度                                                 |
| -height n            | 设置窗口高度                                                 |
| -windowed n          | 设置窗口模式为关（0）或开（1）                               |
| -volume n            | 设置音频音量 0..256                                          |
| -joystick n          | 从玩家 n (0..7) 开始使用摇杆控制                             |
| -pixel_perfect n     | 1 为无过滤的整数倍屏幕缩放（默认是开）                       |
| -preblit_scale n     | 复制到屏幕前，显示缩放 n 倍（和 -pixel_perfect 0 一起时比较有用） |
| -draw_rect x,y,w,h   | 绘制 PICO-8 屏幕的绝对窗口坐标和大小                         |
| -run filename        | 加载并运行卡带                                               |
| -x filename          | 以无头模式执行 PICO-8 卡带，随后退出（实验性功能！！）       |
| -export param_str    | 以无头模式运行 EXPORT 命令，随后退出（参阅导出部分下的注解） |
| -p param_str         | 把一个参数字符串传递到指定卡带                               |
| -splore              | 以 splore 模式启动                                           |
| -home path           | 设置存放 config.txt 和其他用户数据文件的路径                 |
| -root_path path      | 设置存放卡带文件的路径                                       |
| -desktop path        | 设置保存截图和 gif 的位置                                    |
| -screenshot_scale n  | 截图的缩放。默认值：3（368x368 像素）                        |
| -gif_scale n         | gif 捕捉的缩放。默认值：2（256x256 像素）                    |
| -gif_len n           | 以秒为单位，设置 gif 的最大长度（1..120）                    |
| -gui_theme n         | 使用 1 设置高对比度编辑器配色方案                            |
| -timeout n           | 下载超时前要等待多少秒（默认值：30）                         |
| -software_blit n     | 设置使用软件 Blit 模式为关（0）或开（1）                     |
| -foreground_sleep_ms | 帧与帧之间要休眠多少毫秒                                     |
| -background_sleep_ms | 后台运行时，帧与帧之间要休眠多少毫秒                         |
| -accept_future n     | 1 为允许加载使用未来版本的 PICO-8 制作的卡带                 |
| -global_api n        | 1 为 API 函数留在全局作用域（调试时有用）                    |



### 控制器设置

PICO-8 使用 SDL2 控制器配置方案，启动时会探测常见控制器，同时也会在与 config.txt 同目录的 sdl_controllers.txt 中寻找自定义映射。  
sdl_controller.txt 每行有一条映射。

要为你的控制器生成自定义映射字符串，要么使用 SDL2 附带的 `controllermap` 程序，要么尝试 http://www.generalarcade.com/gamepadtool/

要得知 SDL2 探测得到的控制器 ID，运行 PICO-8 之后，在 log.txt 中查找 `joysticks` 或者 `Mapping` 。不同的操作系统，ID 有可能有所区别。参见：https://www.lexaloffle.com/bbs/?tid=32130

要设置哪些键盘按键触发摇杆按键，使用 `KEYCONFIG` 。

## 1.9 截图和 GIF

卡带运行的时候，使用：

> CTRL-6 保存截图到桌面  
> CTRL-7 捕捉卡带标签图像  
> CTRL-8 开始录制视频  
> CTRL-9 保存 GIF 视频到桌面（默认 8 秒）

你可以随时保存视频（视频永远都在录制）；CTRL-8 只是重置视频的起始点。录制时间要长于 8 秒，使用 `CONFIG` 命令（最大值：120）

``````
CONFIG GIF_LEN 60
``````

如果你想要录制每次都重置（以创建不重叠的序列），使用：

``````
CONFIG GIF_RESET_MODE 1
``````

gif 格式无法精确到 30fps ，所以 PICO-8 转而使用最接近的值：33.3fps。

如果你保存到桌面时出现了问题，请尝试在 config.txt 中配置别的桌面路径

## 1.10 分享卡带

有三种方式能分享用 PICO-8 制作的卡带：

1. 直接与其他 PICO-8 用户分享 `.p8` 或 `.p8.png` 文件

   输入 `FOLDER` 以在你的宿主操作系统中打开当前文件夹

2. 把卡带发送到 Lexaloffe BBS 以获得可在网页游玩的版本

   http://www.lexaloffle.com/pico-8.php?page=submit

3. 导出卡带到独立的 html/js 或者原生二进制播放器（参阅“导出工具”小节以得到更多细节）

## 1.11 SPLORE

`SPLORE` 是一个内置实用工具，用来浏览并组织本地和 bbs（在线）卡带。输入 `SPLORE` [enter] 启动，或者用`-splore` 开关启动 PICO-8 。

完全用摇杆控制 `SPLORE` 是可行的：

> LEFT 和 RIGHT 切换卡带列表  
> UP 和 DOWN 选择每个列表的项目  
> X，O 或 MENU 启动卡带

在卡带内，按 MENU 收藏一个卡带或退回 `SPLORE` 。如果你使用键盘，在卡带列表视图中选中卡带后，可以按 F 收藏卡带。

查看 BBS 卡带列表时，使用最顶部的一项重新下载卡带列表。如果你处于离线状态，显示的就是最后一次下载的列表，你依然可以游玩你已经下载的卡带。

如果你在没有互联网访问的机器上安装了 PICO-8 ，你也可以使用 `INSTALL_GAMES` 添加一小部分预装的 BBS 卡带到 /games
