+++
title = "Xkeysnail 安利"
categories = ["Linux"]
draft = false
+++

吃我一记安利系列...

前几日新换了一个键盘, GH60 那种的, 不过是一个 64 键的...扒拉扒拉, 虽然以后可能会再发一下这个键盘, 但是这里就先不说了.

不过换了键盘之后, 有一个现实的问题就这样摆在了我的面前, 即如何输入各路功能键呢?

中间的波折过程比如 xmodmap 或者是 xkb 的那一套东西都略去不表, 只说最终的方案:
xkeysnail!


## 安装和设置说明 {#安装和设置说明}

xkeysnail 是 mooz 开发的一款基于 python 的快捷键软件, 相关历史背景也不说了...在他的代码中提供了类似于 xcape 的使类似于 shift, ctrl 之类的修饰键的单击行为变成一个特定的可以自己设定的按键. 而这个功能在 [Lenbok/xkeysnail](https://github.com/Lenbok/xkeysnail/tree/feat-conditional-multipurpose-modmap) 的分支下实现了按软件定义修饰键单击行为的功能, 然后我在使用过程中修复了一点处理逻辑上的问题, 并添加修饰键按下超时后取消的功能, 即 [nlfiasel/xkeysnail](https://github.com/nlfiasel/xkeysnail) 这个分支.

在用过一段时间之后, 还没有发现什么新的问题...暂时来讲应该是没有添加新的 bug
吧...(逃

对于这个软件可以直接参照原作者提供的文档, 同时我也添上自己目前的配置文件作为参考(见本小节最后):

在这份配置文件中, 我将

-   左 shift 的单次点击映射为 F13, 长按依旧是作为 shift,
    (至于为啥定义为 F13? 以后再说...)
-   capslock 先映射为左 ctrl
    (说起来当初使用这个软件主要是看上了它支持左右修饰键的区分 2333),
-   左 ctrl 的单击行为定义为 esc, 长按行为依旧是 ctrl,
-   tab 键的单击行为映射为 tab, 长按作为右 ctrl,
    用来配合触发基于右 ctrl 定义的按键层.

至于将分号和回车的长按定义为右 shift 的这种设定...就不是很推荐了, 因为会降低一些按键的容错, 但是大写比如说 S 确实也是方便了不少的, 以上.

```python
# -*- coding: utf-8 -*-
import re
from xkeysnail.transform import *

# 定义按下多功能修饰键后1s 放弃输出单个按键
define_timeout(1)

# 定义全局键位映射
define_modmap({
    Key.ESC: Key.GRAVE,
    Key.CAPSLOCK: Key.LEFT_CTRL,
})

# 定义多功能修饰键的按键映射
define_multipurpose_modmap({
    Key.TAB: [Key.TAB, Key.RIGHT_CTRL],
    Key.LEFT_CTRL: [Key.ESC, Key.LEFT_CTRL],
    Key.ENTER: [Key.ENTER, Key.RIGHT_SHIFT],
    Key.SEMICOLON: [Key.SEMICOLON, Key.RIGHT_SHIFT],
})

# 定义特定软件的多功能修饰键的按键映射
define_conditional_multipurpose_modmap(re.compile(r'Emacs'), {
    Key.TAB: [Key.TAB, Key.RIGHT_CTRL],
    Key.LEFT_CTRL: [Key.ESC, Key.LEFT_CTRL],
    Key.ENTER: [Key.ENTER, Key.RIGHT_SHIFT],
    Key.LEFT_SHIFT: [Key.F13, Key.LEFT_SHIFT],
    Key.SEMICOLON: [Key.SEMICOLON, Key.RIGHT_SHIFT],
})

# 定义全局的快捷键映射
define_keymap(None, {
    K("RC-KEY_4"): K("F4"),
    K("RC-KEY_5"): K("F5"),
    K("RC-KEY_6"): K("F6"),
    K("RC-KEY_7"): K("F7"),
    K("RC-KEY_8"): K("F8"),
    K("RC-KEY_9"): K("F9"),
    K("RC-P"): K("MUTE"),
    K("RC-LEFT_BRACE"): K("VOLUMEDOWN"),
    K("RC-RIGHT_BRACE"): K("VOLUMEUP"),
    K("RC-BACKSLASH"): K("PRINT"),
    K("RC-APOSTROPHE"): K("CAPSLOCK"),

    K("RC-H"): K("LEFT"),
    K("RC-J"): K("DOWN"),
    K("RC-K"): K("UP"),
    K("RC-L"): K("RIGHT"),
    K("LC-RC-H"): K("LC-LEFT"),
    K("LC-RC-J"): K("LC-DOWN"),
    K("LC-RC-K"): K("LC-UP"),
    K("LC-RC-L"): K("LC-RIGHT"),

    K("LC-RC-LM-KEY_1"): K("LC-LM-F1"),
    K("LC-RC-LM-KEY_2"): K("LC-LM-F2"),
    K("LC-RC-LM-KEY_3"): K("LC-LM-F3"),
})
```


## 自动启动说明 {#自动启动说明}

参照内容:

-   [xkeysnail 使用指南](https://www.aloxaf.com/2019/11/xkeysnail%5Ftutorial/)
-   [xkeysnailをsystemdでデーモン化](https://qiita.com/samurai20000@github/items/2e1d779e806a7e8543d6)

针对于内容简单的做一下搬运和总结吧, 其实使用指南那一位已经写得很好了...

```sh
sudo groupadd uinput
sudo usermod -a -G input,uinput $USER
```

然后, 编写两个 udev 规则并保存, 此处还是建议参照使用指南的那位的教程进行手动修改,
确认一下文件的修改不会对当前的系统造成不可逆的影响.

```sh
echo "KERNEL==\"event*\", NAME=\"input/%k\", MODE=\"660\", GROUP=\"input\"" | sudo tee /etc/udev/rules.d/input.rules
echo "KERNEL==\"uinput\", GROUP=\"uinput\"" | sudo tee /etc/udev/rules.d/uinput.rules
```

在 udev 规则写入完成后输入下面的命令应用规则

```sh
sudo udevadm control --reload-rules && udevadm trigger
```

之后就是写 systemd 的配置文件(这里就直接照抄原作者啦, 记得将原作者的 `ExecStart`
后面的配置文件位置的参数修改成自己的):

```conf
# https://qiita.com/samurai20000@github/items/2e1d779e806a7e8543d6

[Unit]
Description=xkeysnail

[Service]
# DISPLAY 的值应该和 `echo $DISPLAY` 的值相同
Environment=DISPLAY=:0
ExecStartPre=-xhost +SI:localuser:root
# 此处禁用了按键状态的输出, 因为我不想要一大堆毫无卵用的日志...
ExecStart=xkeysnail -q /home/aloxaf/.config/xkeysnail/config.py
Type=simple
Restart=always
RestartSec=2

[Install]
WantedBy=default.target
```

将上面这些内容保存到 `~/.config/systemd/user/xkeysnail.service`

之后执行 `systemctl --user --now enable xkeysnail.service` , 即可设置并启动
xkeysnail 了

在对配置文件进行修改后, 可以直接使用 `systemctl --user restart xkeysnail` , 重启服务, 稍等一会之后就会应用新的设置了.
