使用 ZeroBrane Studio 和 Eclipse LDT 断点调试 quick-cocos2d-x

**2013-12-13 更新：** 加入quick官网提供的调试方式链接
<hr>

[quick-cocos2d-x][quick] 是个基于 cocos2d-x 的 Lua Binding 加强版。本文介绍在quick-cocos2d-x中进行断点调试的方法。

为了便于阅读和减少废话，本文有如下假设：

1. 读者阅读过 [quick-x-player 使用说明][quickplayer] 和 [初窥 Quick-cocos2d-x][quickfirst] ;
2. 读者了解 quick-cocos2d-x 项目的文件夹结构；
3. 读者安装了 ZeroBrane Studio 0.39 或/和 Eclipse LDT 1.0；
3. 本文基于 quick-cocos2d-x 提供的 coinflip sample 进行调试。

提纲如下：

1. [Quick官方提供的调试方式](#quick)
1. [在 ZeroBrane Studio 中进行断点调试](#zbs)
2. [在 Eclipse LDT 中进行断点调试](#ldt)
<!--more-->

<a name="quick"></a>
## 一、 Quick官方提供的调试方式

廖大已经在 Quick X Player 中加入了LDT调试支持，也就是说，LDT的 debugger.lua 模块已经被廖大加入到了 Quick X Player 中，不需要像本文下面写的那样 `require("debugger")` 了。

取而代之，是需要打开调试的项目后，选择菜单 Player -> Auto Connect Debugger。

详情请参阅quick官网： [用 Eclipse LDT 调试 quick-cocos2d-x 游戏][official]

<a name="zbs"></a>
## 二、 在ZeroBrane Studio中进行断点调试

2013-12-13 更新：由于quick作者修改了调试方式，可能此处介绍的方法不再有效，请参阅 [一、 Quick官方提供的调试方式](#quick)

ZeroBrane Studio是一个用Lua写成的跨平台Lua IDE。界面使用 [wxLua][wxlua] 实现。

### 1. 调试模块

ZeroBrane Studio 使用 modbdebug 模块（位于 [ZeroBrane]/lualibs/mobdebug/mobdebug.lua） 实现调试支持。为了让项目找到这个模块，我采用最简单的方法，将该模块复制进入 coinflip 的 scripts 文件夹。

若不希望这样粗暴，可采用另外两种方法，参考： [Remote debugging][zbdebugging]

### 2. require mobdebug

在 coinflip/scripts/main.lua 的第一行加入下面的代码，让项目启动调试支持。

<pre lang="lua">
require("mobdebug").start()
</pre>

### 3. 启动调试服务器

在 ZeroBrane Studio 中选择 `Project->Start Debugger Server` 命令。如果该命令是灰色的，说明调试服务器已经启动了。

### 4. 加断点

编辑 game.lua 文件，在32行 `game.enterChooseLevelScene()` 处选择 `Project -> Toggle BreakPoint` 加入断点。

### 5. 启动 quick-player

在 quick-player 中启动 coinflip 项目，ZeroBrane Studio 会自动停在 main.lua 中。按 `Project -> Continue` 继续运行，游戏界面出现。

单击游戏中的 "Start" 按钮，调试停止在 game.lua 中的断点处。如下图所示：

![断点调试][zbdebug1]  
[查看大图][zbdebug1]

### 6. 进入源码调试

若要进入框架内部调试，可以取消 main.lua 中的 `CCLuaLoadChunksFromZip("res/framework_precompiled.zip")` 调用，然后将 `[quick-cocos2d-x]/framework` 复制的 `coinflip/scripts/` 文件夹，这样在调试的时候，就可以进入框架内部了。如下图所示：

![框架内部][zbdebug2]  
[查看大图][zbdebug2]

<a name="ldt"></a>
## 三、 在Eclipse LDT 中进行断点调试

2013-12-13 更新：由于quick作者修改了调试方式，可能此处介绍的方法不再有效，请参阅 [一、 Quick官方提供的调试方式](#quick)

LDT(Lua Development Tools)是一个 Eclipse 插件，支持Lua语言的编写和调试。

### 1. 调试模块

LDT 使用 另一个调试模块来实现调试支持。LDT可以自动生成这个模块。

单击 `Run -> Debug Configurations` 菜单，新建一个 `Lua Attach to Application` 配置，点击其中的 `Lua Debugger Client` 将模块输出到 coinclip 的 scripts 文件夹，默认文件名为 debugger.lua 。

![输出Debugger模块][ldtdebug1]
[查看大图][ldtdebug1]

### 2. require debugger

在 coinflip/scripts/main.lua 的第一行加入下面的代码，让项目启动调试支持。

<pre lang="lua">
require("debugger")("127.0.0.1", 10000, "luaidekey")
</pre>

这里的 luaidekey 是调试过程中IDE用来保持会话的键名，与上面 Debug Configurations 配置界面中的 IDE Key 相同。

### 3. 启动调试服务器

单击 `Run -> Debug Configurations` 菜单，选择刚才新建的配置，单击 Debug 按钮。

### 4. 加断点

编辑 game.lua 文件，在32行 `game.enterChooseLevelScene()` 处加入断点。

### 5. 启动 quick-player

在 quick-player 中启动 coinflip 项目，单击游戏中的 "Start" 按钮，调试停止在 game.lua 中的断点处。如下图所示：

![调试信息][ldtdebug2]
[查看大图][ldtdebug2]

若要进入 framework 源码内部调试，见上方的 6. 进入源码内部调试 。

关于LDT更详细的调试信息，可以阅读 [Debugging a Lua program][ldtdebug] 。

[quick]: http://quick-x.com/
[quickplayer]: http://cn.quick-x.com/?p=39
[quickfirst]: http://dualface.github.io/blog/2013/07/31/quick-first-time/
[wxlua]: http://wxlua.sourceforge.net/
[zbdebugging]: http://studio.zerobrane.com/doc-remote-debugging.html#setup_environment_for_debugging
[ldt]: http://www.eclipse.org/koneki/ldt/
[ldtdebug]: http://wiki.eclipse.org/Koneki/LDT/Developer_Area/User_Guides/User_Guide_1.0#Debugging_a_Lua_program
[official]: http://cn.quick-x.com/?p=1527

[zbdebug1]: /wp-content/uploads/2013/10/zbdebug1.png
[zbdebug2]: /wp-content/uploads/2013/10/zbdebug2.png
[ldtdebug1]: /wp-content/uploads/2013/10/ldtdebug1.png
[ldtdebug2]: /wp-content/uploads/2013/10/ldtdebug2.png
