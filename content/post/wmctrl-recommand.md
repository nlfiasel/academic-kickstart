+++
title = "wmctrl 安利"
categories = ["Linux"]
draft = false
+++

从前的从前, 我的程序切换方案是通过设置 meta+[123qweasd] 的方式在九个虚拟桌面之间来回切换, 通过固定程序与虚拟桌面的对应关系实现想要使用的程序的一键直达. 在日常的使用中其实还是有很多的不方便之处的, 比如终端这种每个桌面都可能会打开的位置分布比较随机的应用. 另外维护应用与虚拟桌面的对应关系这件事情本身也是需要做额外的努力的.

然而现在, 通过 wmctrl 以及与之配合的脚本, 可以方便的通过一个快捷键在指定的应用程序的多个实例之间循环跳跃. 十分之方便. 至于快捷键如何设置, 因为我是使用的
Xkeysnail , 所以后面我会贴上我在 Xkeysnail 中的设置.

安装嘛就是很简单的(arch):

```sh
sudo pacman -S wmctrl
```

使用 wmctrl 进行应用程序的跳转, 只需要执行, 其中 `app` 是想要启动的应用名:

```sh
wmctrl -x -a app
```

然后让我们回归正题, 随便指定一个位置并放置脚本, 比如我是放置在
`~/.doom.d/dot/wmctrl/cycle` , 脚本来源于 [这里](https://stackoverflow.com/questions/15172735/cycle-through-windows-of-the-same-application-using-wmcrtl) , 同时在第五行做了一点小修改, 以指定根据程序的 wm\_class 进行跳转:

```sh
#!/bin/bash
win_class=$1 # 'terminator' # $1

# get list of all windows matching with the class above
win_list=$(wmctrl -x -l | awk '{print $1,$3}' | grep -i $win_class | awk '{print $1}' )

# get id of the focused window
active_win_id=$(xprop -root | grep '^_NET_ACTIVE_W' | awk -F'# 0x' '{print $2}')
if [ "$active_win_id" == "0" ]; then
    active_win_id=""
fi

# get next window to focus on, removing id active
switch_to=$(echo $win_list | sed s/.*$active_win_id// | awk '{print $1}')

# if the current window is the last in the list ... take the first one
if [ "$switch_to" == '' ];then
   switch_to=$(echo $win_list | awk '{print $1}')
fi

# switch to window
wmctrl -i -a $switch_to
```

然后在 Xkeysnail 中快捷键的设置方式为, 首先定义一个函数简化启动程序的命令写法:

```python
import shlex
def cycle(app):
    _cycle = "bash /home/nlfiasel/.doom.d/dot/wmctrl/cycle "
    return shlex.split(_cycle+app)
```

然后定义 keymap:

```python
K("RC-D"): launch(cycle("dolphin")),
K("RC-E"): launch(cycle("emacs")),
K("RC-Q"): launch(cycle("mendeley")),
K("RC-R"): launch(cycle("konsole")),
K("RC-S"): launch(cycle("mpv")),
K("RC-T"): launch(cycle("telegram")),
K("RC-W"): launch(cycle("chromium")),
```

因为在我的设置中, TAB 的按下等效于 RC, 所以经过这样的设置我就可以很简单的在各个应用之间跳转, 比如在多个终端之间的跳转仅仅需要不断的按 TAB+R 就可以了, 逻辑上其实就是指定应用的 ALT+TAB .
