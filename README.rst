What happens when...
====================

This repository is an attempt to answer the age-old interview question "What happens when you type google.com into your browser's address box and press enter?"

Except instead of the usual story, we're going to try to answer this question in as much detail as possible. No skipping out on anything.

This is a collaborative process, so dig in and try to help out! There are tons of details missing, just waiting for you to add them! So send us a pull request, please!

This is all licensed under the terms of the `Creative Commons Zero`_ license.

Read this in `简体中文`_ (simplified Chinese), `日本語`_ (Japanese), `한국어`_
(Korean) and `Spanish`_. NOTE: these have not been reviewed by the alex/what-happens-when
maintainers.

Table of Contents
====================

.. contents::
   :backlinks: none
   :local:

The "g" key is pressed
----------------------
The following sections explain the physical keyboard actions and the OS interrupts.
When you press the key "g" the browser receives the event and the auto-complete functions kick in.
Depending on your browser's algorithm and if you are in private/incognito mode or not various suggestions will be presented to you in the dropdown below the URL bar.
Most of these algorithms sort and prioritize results based on search history, bookmarks, cookies, and popular searches from the internet as a whole.
As you are typing "google.com" many blocks of code run and the suggestions will be refined with each keypress.
It may even suggest "google.com" before you finish typing it.

下面的章节解释了物理键盘的动作和操作系统的中断。

当你按下 "g "键时，浏览器会接收到该事件，自动完成功能就会启动。

根据你的浏览器的算法，以及是否处于私人/隐身模式，各种建议将在URL栏下面的下拉菜单中呈现给你。

这些算法中的大多数都是根据搜索历史、书签、cookies和整个互联网的热门搜索来对结果进行分类和优先排序。

当你输入 "google.com "时，许多代码块都在运行，建议将随着每次按键而完善。

它甚至可能在你完成输入之前，就建议你输入 "google.com"。

The "enter" key bottoms out
---------------------------

To pick a zero point, let's choose the Enter key on the keyboard hitting the bottom of its range.
At this point, an electrical circuit specific to the enter key is closed (either directly or capacitively).
This allows a small amount of current to flow into the logic circuitry of the keyboard, which scans the state of each key switch, debounces the electrical noise of the rapid intermittent closure of the switch, and converts it to a keycode integer, in this case 13.
The keyboard controller then encodes the keycode for transport to the computer.
This is now almost universally over a Universal Serial Bus (USB) or Bluetooth connection, but historically has been over PS/2 or ADB connections.

为了选择一个零点，让我们选择键盘上的 Enter 键打到其范围的底部。

在这一点上，一个专门针对 Enter 键的电路被关闭（直接或电容式）。

这允许少量的电流流入键盘的逻辑电路，扫描每个按键开关的状态，去掉开关快速间歇性关闭的电噪声，并将其转换为按键代码的整数，在这种情况下为13。

然后，键盘控制器对键码进行编码，以便传送到计算机上。

现在几乎普遍是通过通用串行总线（USB）或蓝牙连接，但在历史上是通过PS/2或ADB连接。

*In the case of the USB keyboard:*

1. The USB circuitry of the keyboard is powered by the 5V supply provided over pin 1 from the computer's USB host controller.

2. The keycode generated is stored by internal keyboard circuitry memory in a register called "endpoint".

3. The host USB controller polls that "endpoint" every ~10ms (minimum value declared by the keyboard), so it gets the keycode value stored on it.

4. This value goes to the USB SIE (Serial Interface Engine) to be converted in one or more USB packets that follow the low-level USB protocol.

5. Those packets are sent by a differential electrical signal over D+ and D- pins (the middle 2) at a maximum speed of 1.5 Mb/s, as an HID (Human Interface Device) device is always declared to be a "low-speed device" (USB 2.0 compliance).

6. This serial signal is then decoded at the computer's host USB controller, and interpreted by the computer's Human Interface Device (HID) universal keyboard device driver.  The value of the key is then passed into the operating system's hardware abstraction layer.

1. 键盘的USB电路由计算机的USB主机控制器的第1脚提供的5V电源供电。

2. 产生的键码由内部键盘电路存储器存储在一个称为 "端点 "的寄存器中。

3. USB主机控制器每隔约10ms就会对 "端点 "进行轮询（最小值键盘声明的最小值），所以它获得存储在上面的键码值。

4. 这个值被送到USB SIE（串行接口引擎），在一个或多个遵循低级USB协议的USB数据包中转换。

5. 这些数据包通过D+和D-引脚（中间的2个）的差分电信号发送，最高速度为1.5 Mb/s，因为HID（人机接口设备）设备总是被声明为 "低速设备"（符合USB 2.0）。

6. 然后这个串行信号在计算机的主机USB控制器处被解码，并由计算机的人机接口设备（HID）通用键盘设备驱动程序进行解释。 然后，按键的值被传入操作系统的硬件抽象层。

*In the case of Virtual Keyboard (as in touch screen devices):*

- When the user puts their finger on a modern capacitive touch screen, a tiny amount of current gets transferred to the finger. This completes the circuit through the electrostatic field of the conductive layer and creates a voltage drop at that point on the screen. The ``screen controller`` then raises an interrupt reporting the coordinate of the keypress.

- Then the mobile OS notifies the currently focused application of a press event in one of its GUI elements (which now is the virtual keyboard application buttons).

- The virtual keyboard can now raise a software interrupt for sending a 'key pressed' message back to the OS.

- This interrupt notifies the currently focused application of a 'key pressed' event.

1. 当用户将手指放在现代电容式触摸屏上时，少量的电流会被转移到手指上。这通过导电层的静电场完成了电路，并在屏幕上的那个点产生了电压降。然后，"屏幕控制器 "提出一个中断，报告按键的坐标。

2. 然后，移动操作系统通知当前关注的应用程序在其GUI元素之一（现在是虚拟键盘应用程序的按钮）中发生了按压事件。

3. 虚拟键盘现在可以提出一个软件中断，以发送一个 'key pressed' 的消息返回到操作系统。

4. 这个中断通知当前 focused 的应用程序有一个 'key pressed' 事件。


Interrupt fires [NOT for USB keyboards]
---------------------------------------

The keyboard sends signals on its interrupt request line (IRQ), which is mapped to an ``interrupt vector`` (integer) by the interrupt controller.
The CPU uses the ``Interrupt Descriptor Table`` (IDT) to map the interrupt vectors to functions (``interrupt handlers``) which are supplied by the kernel.
When an interrupt arrives, the CPU indexes the IDT with the interrupt vector and runs the appropriate handler.
Thus, the kernel is entered.

键盘在其中断请求线（IRQ）上发送信号，中断控制器将其映射到一个 "中断向量"``interrupt vector``（整数）。
CPU使用 "中断描述符表" ``Interrupt Descriptor Table`` （IDT）将中断向量映射到由内核提供的函数（"中断处理程序"``interrupt handlers``）。
当一个中断到来时，CPU用中断向量索引IDT并运行适当的处理程序。
这样，就进入了内核。

(On Windows) A ``WM_KEYDOWN`` message is sent to the app
--------------------------------------------------------

The HID transport passes the key down event to the ``KBDHID.sys`` driver which converts the HID usage into a scancode. In this case, the scan code is ``VK_RETURN`` (``0x0D``).

The ``KBDHID.sys`` driver interfaces with the ``KBDCLASS.sys`` (keyboard class driver). This driver is responsible for handling all keyboard and keypad input in a secure manner.

It then calls into ``Win32K.sys`` (after potentially passing the message through 3rd party keyboard filters that are installed). This all happens in kernel mode.

``Win32K.sys`` figures out what window is the active window through the ``GetForegroundWindow()`` API.

This API provides the window handle of the browser's address box.

The main Windows "message pump" then calls ``SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)``. ``lParam`` is a bitmask that indicates further information about the keypress: repeat count (0 in this case), the actual scan code (can be OEM dependent, but generally wouldn't be for ``VK_RETURN``), whether extended keys (e.g. alt, shift, ctrl) were also pressed (they weren't), and some other state.

The Windows ``SendMessage`` API is a straightforward function that adds the message to a queue for the particular window handle (``hWnd``).

Later, the main message processing function (called a ``WindowProc``) assigned to the ``hWnd`` is called in order to process each message in the queue.

The window (``hWnd``) that is active is actually an edit control and the ``WindowProc`` in this case has a message handler for ``WM_KEYDOWN`` messages.

This code looks within the 3rd parameter that was passed to ``SendMessage`` (``wParam``) and, because it is ``VK_RETURN`` knows the user has hit the ENTER key.

1. HID传输系统将按键下降事件传递给``KBDHID.sys``驱动程序，该驱动程序将HID使用情况转换为扫描码。在这种情况下，扫描代码是`VK_RETURN`（`0x0D`）。

2. ``KBDHID.sys``驱动程序与``KBDCLASS.sys``（键盘类驱动程序）接口。该驱动负责以安全的方式处理所有的键盘和小键盘输入。

3. 然后它调用 ``Win32K.sys``（在可能通过安装的第三方键盘过滤器后）。这一切都发生在内核模式下。

4. ``Win32K.sys``通过``GetForegroundWindow()``的API找出哪个窗口是活动窗口。

5. 这个API提供了浏览器地址框的窗口句柄。

6. 然后主Windows "消息泵 message pump" 调用 ``SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)``。 ``lParam``是一个比特掩码，表示有关按键的进一步信息：重复次数（本例中为0），实际的扫描代码（可能与OEM有关，但对于 ``VK_RETURN`` 一般不会），是否也按了扩展键（例如alt, shift, ctrl）（它们没有），以及其他一些状态。

7. Windows的 ``SendMessage`` API是一个直接的函数，将消息添加到特定窗口句柄（ ``hWnd`` ）的队列中。

8. 之后，分配给 ``hWnd`` 的主要消息处理函数（称为 ``WindowProc`` ）被调用，以处理队列中的每条消息。

9. 活动的窗口（ ``hWnd``）实际上是一个编辑控件，在这种情况下， ``WindowProc``有一个处理 ``WM_KEYDOWN`` 消息的函数。

10. 这段代码在传递给 ``SendMessage``(``wParam``)的第三个参数中寻找，因为它是 ``VK_RETURN``，所以知道用户已经按了ENTER键。

(On OS X) A ``KeyDown`` NSEvent is sent to the app
--------------------------------------------------

The interrupt signal triggers an interrupt event in the I/O Kit kext keyboard driver.

The driver translates the signal into a key code which is passed to the OS X ``WindowServer`` process.

Resultantly, the ``WindowServer`` dispatches an event to any appropriate (e.g. active or listening) applications through their Mach port where it is placed into an event queue.

Events can then be read from this queue by threads with sufficient privileges calling the ``mach_ipc_dispatch`` function.

This most commonly occurs through, and is handled by, an ``NSApplication`` main event loop, via an ``NSEvent`` of ``NSEventType`` ``KeyDown``.

中断信号在I/O Kit kext键盘驱动程序中触发一个中断事件。

驱动程序将信号转化为按键代码，并传递给OS X的 ``WindowServer`` 进程。

因此， ``WindowServer`` 通过其Mach端口向任何适当的（如活动或监听）应用程序发送事件，并将其放入一个事件队列。

然后，有足够权限的线程可以从这个队列中读取事件，调用 ``mach_ipc_dispatch`` 函数。这通常是通过 ``NSApplication`` 主事件循环，通过 ``NSEvent`` 的 ``NSEventType`` ``KeyDown`` 来处理的。

(On GNU/Linux) the Xorg server listens for keycodes
---------------------------------------------------

When a graphical ``X server`` is used, ``X`` will use the generic event
driver ``evdev`` to acquire the keypress.

A re-mapping of keycodes to scancodes is made with ``X server`` specific keymaps and rules.

When the scancode mapping of the key pressed is complete, the ``X server``
sends the character to the ``window manager`` (DWM, metacity, i3, etc), so the ``window manager`` in turn sends the character to the focused window.

The graphical API of the window  that receives the character prints the
appropriate font symbol in the appropriate focused field.

当使用图形化的 ``X server`` 时， ``X`` 将使用通用的事件驱动 ``evdev`` 来获取按键。

使用 ``X server`` 特定的键位图和规则，将键位码 keycodes 重新映射到扫描码 scancodes。

当按下的键的 scancode 映射完成后， ``X server`` 将字符发送到 ``window manager`` 窗口管理器（DWM、metacity、i3 等），因此， ``window manager`` 窗口管理器反过来将字符 character 发送到焦点窗口。

收到该字符的窗口的图形API, 会在相应的焦点区域打印出, 适当的字体符号。

Parse URL
---------

* The browser now has the following information contained in the URL (Uniform Resource Locator):

    - ``Protocol``  "http" Use 'Hyper Text Transfer Protocol'

    - ``Resource``  "/" Retrieve main (index) page

* 浏览器现在的URL（统一资源定位器）中包含以下信息。

    - ``Protocal`` "http" 使用 '超文本传输协议'。

    - ``Resource`` "/" 检索主（index）页


Is it a URL or a search term?
-----------------------------

When no protocol or valid domain name is given the browser proceeds to feed the text given in the address box to the browser's default web search engine.

In many cases the URL has a special piece of text appended to it to tell the search engine that it came from a particular browser's URL bar.

当没有给出协议或有效域名时，浏览器将地址框中的文本, 输入到浏览器的, 默认网络搜索引擎。

在许多情况下，URL有一段特殊的文字附加在上面，以告诉搜索引擎它来自一个特定浏览器的URL栏。

Convert non-ASCII Unicode characters in the hostname
------------------------------------------------

* The browser checks the hostname for characters that are not in ``a-z``,
  ``A-Z``, ``0-9``, ``-``, or ``.``.

* Since the hostname is ``google.com`` there won't be any, but if there were the browser would apply `Punycode`_ encoding to the hostname portion of the URL.

* 浏览器检查主机名中是否有不属于 ``a-z`` 的字符 ``A-Z``, ``0-9``, ``-``, 或 ``.``中的字符。

* 由于主机名是 ``google.com``，所以不会有任何字符，但如果有的话，浏览器会对URL的主机名部分应用 `Punycode`_ 编码。

Check HSTS list
---------------
* The browser checks its "preloaded HSTS (HTTP Strict Transport Security)" list. This is a list of websites that have requested to be contacted via HTTPS only.

* If the website is in the list, the browser sends its request via HTTPS instead of HTTP. Otherwise, the initial request is sent via HTTP.  (Note that a website can still use the HSTS policy *without* being in the HSTS list.  The first HTTP request to the website by a user will receive a response requesting that the user only send HTTPS requests.  However, this single HTTP request could potentially leave the user vulnerable to a `downgrade attack`_, which is why the HSTS list is included in modern web browsers.)

* 浏览器检查其 "预装 HSTS（HTTP严格传输安全 HTTP Strict Transport Security）"列表。这是一个要求只通过HTTPS联系的网站的列表。

* 如果该网站在列表中，浏览器将通过HTTPS而不是HTTP发送请求。否则，初始请求将通过HTTP发送。 (请注意，一个网站仍然可以使用HSTS策略， *不* 在HSTS列表中。 用户向网站发出的第一个HTTP请求将收到一个响应，要求用户只发送HTTPS请求。 然而，这个单一的HTTP请求有可能使用户受到`降级攻击`_，这就是为什么HSTS列表包含在现代网络浏览器中的原因)。

DNS lookup
----------

* Browser checks if the domain is in its cache. (to see the DNS Cache in Chrome, go to `chrome://net-internals/#dns <chrome://net-internals/#dns>`_).

* If not found, the browser calls ``gethostbyname`` library function (varies by OS) to do the lookup.

* ``gethostbyname`` checks if the hostname can be resolved by reference in the local ``hosts`` file (whose location `varies by OS`_) before trying to resolve the hostname through DNS.

* If ``gethostbyname`` does not have it cached nor can find it in the ``hosts`` file then it makes a request to the DNS server configured in the network stack. This is typically the local router or the ISP's caching DNS server.

* If the DNS server is on the same subnet the network library follows the ``ARP process`` below for the DNS server.

* If the DNS server is on a different subnet, the network library follows the ``ARP process`` below for the default gateway IP.

1. 浏览器检查该域名是否在其缓存中。(要查看Chrome浏览器的 DNS 缓存，请到 `chrome://net-internals/#dns <chrome://net-internals/#dns>`_)。

* 如果没有找到，浏览器会调用``gethostbyname``库函数（因操作系统而异）来进行查询。

* ``gethostbyname`` 检查主机名是否可以在本地 ``hosts`` 文件（其位置 `因操作系统而异`_） 中通过引用来解决，然后再尝试通过 DNS 解决该主机名。

* 如果 ``gethostbyname`` 没有缓存，也不能在 ``hosts`` 文件中找到它，那么它就向网络堆栈中配置的 DNS 服务器提出请求。这通常是指本地路由器或 ISP 的缓存 DNS 服务器。

* 如果 DNS 服务器在同一个子网中，网络库会按照下面的 ``ARP process`` 对 DNS 服务器进行访问。

* 如果 DNS 服务器在不同的子网，网络库按照下面的 ``ARP process`` 来处理默认网关IP。

ARP process
-----------

In order to send an ARP (Address Resolution Protocol) broadcast the network stack library needs the target IP address to lookup.

It also needs to know the MAC address of the interface it will use to send out the ARP broadcast.

The ARP cache is first checked for an ARP entry for our target IP. If it is in the cache, the library function returns the result: Target IP = MAC.

If the entry is not in the ARP cache:

* The route table is looked up, to see if the Target IP address is on any of the subnets on the local route table. If it is, the library uses the interface associated with that subnet. If it is not, the library uses the interface that has the subnet of our default gateway.

* The MAC address of the selected network interface is looked up.

* The network library sends a Layer 2 (data link layer of the `OSI model`_) ARP request:

为了发送ARP（地址解析协议）广播，网络堆栈库需要目标IP地址来查询。

它还需要知道它将用于发送 ARP 广播的接口的MAC地址。

首先检查 ARP 缓存中是否有我们目标 IP 的 ARP 条目。如果它在缓存中，库函数会返回结果。目标 IP = MAC。

如果该条目不在ARP缓存中。

1. 查阅路由表，看目标IP地址是否在本地路由表的任何子网中。如果是， library 就使用与该子网相关的接口。如果不是， library 就使用与我们默认网关的子网有关的接口。

2. 所选网络接口的 MAC 地址被查询。

3. 网络库发送一个第二层（ `OSI model`_ 的数据链路层）ARP请求。

``ARP Request``::

    Sender MAC: interface:mac:address:here
    Sender IP: interface.ip.goes.here
    Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)
    Target IP: target.ip.goes.here

Depending on what type of hardware is between the computer and the router:

取决于计算机和路由器之间是什么类型的硬件。

Directly connected:
直接连接。

* If the computer is directly connected to the router the router response with an ``ARP Reply`` (see below)

* 如果计算机直接连接到路由器，路由器会以 ``ARP Reply`` 来回应（见下文）。

Hub:

* If the computer is connected to a hub, the hub will broadcast the ARP request out of all other ports. If the router is connected on the same "wire", it will respond with an ``ARP Reply`` (see below).

* 如果计算机连接到集线器上，集线器将向所有其他端口广播 ARP 请求。如果路由器连接在同一条 "wire"上，它将以 ``ARP Reply`` 来回应（见下文）。

Switch:

* If the computer is connected to a switch, the switch will check its local CAM/MAC table to see which port has the MAC address we are looking for. If the switch has no entry for the MAC address it will rebroadcast the ARP request to all other ports.

* If the switch has an entry in the MAC/CAM table it will send the ARP request to the port that has the MAC address we are looking for.

* If the router is on the same "wire", it will respond with an ``ARP Reply`` (see below)

1. 如果计算机连接到交换机，交换机将检查其本地的 CAM/MAC 表，看看哪个端口有我们要找的MAC地址。如果交换机没有该 MAC 地址的条目，它将向所有其他端口重新广播 ARP 请求。

2. 如果交换机在 MAC/CAM 表中有一个条目，它将把 ARP 请求发送到有我们要找的 MAC 地址的端口。

3. 如果路由器在同一 "wire"上，它将以 ``ARP Reply`` 来回应（见下文）。

``ARP Reply``::

    Sender MAC: target:mac:address:here
    Sender IP: target.ip.goes.here
    Target MAC: interface:mac:address:here
    Target IP: interface.ip.goes.here

Now that the network library has the IP address of either our DNS server or the default gateway it can resume its DNS process:

* The DNS client establishes a socket to UDP port 53 on the DNS server,
  using a source port above 1023.
* If the response size is too large, TCP will be used instead.
* If the local/ISP DNS server does not have it, then a recursive search is requested and that flows up the list of DNS servers until the SOA is reached, and if found an answer is returned.

现在，网络库已经有了我们的 DNS 服务器或默认网关的IP地址，它可以恢复其 DNS 进程。

* DNS 客户端在 DNS 服务器上建立了一个 UDP 端口53的套接字。 使用一个高于 1023 的源端口。
* 如果响应大小太大，将使用 TCP 代替。
* 如果 local/ISP 的 DNS 服务器没有，则要求进行递归搜索，并在 DNS 服务器列表中流动，直到到达 SOA，如果找到答案则返回。

Opening of a socket
-------------------
Once the browser receives the IP address of the destination server, it takes that and the given port number from the URL (the HTTP protocol defaults to port 80, and HTTPS to port 443), and makes a call to the system library function named ``socket`` and requests a TCP socket stream - ``AF_INET/AF_INET6`` and ``SOCK_STREAM``.

* This request is first passed to the Transport Layer where a TCP segment is crafted. The destination port is added to the header, and a source port is chosen from within the kernel's dynamic port range (ip_local_port_range in Linux).

* This segment is sent to the Network Layer, which wraps an additional IP header. The IP address of the destination server as well as that of the current machine is inserted to form a packet.

* The packet next arrives at the Link Layer. A frame header is added that includes the MAC address of the machine's NIC as well as the MAC address of the gateway (local router). As before, if the kernel does not know the MAC address of the gateway, it must broadcast an ARP query to find it.

一旦浏览器收到目标服务器的IP地址，就会从URL中获取该地址和给定的端口号（HTTP协议默认为80端口，HTTPS为443端口），并调用系统库函数`socket`，请求一个TCP套接字流 - ``AF_INET/AF_INET6`` 和 ``SOCK_STREAM``。

* 这个请求首先被传递到传输层，在那里制作了一个TCP段。目标端口被添加到标题中，并从内核的动态端口范围（Linux的ip_local_port_range）中选择一个源端口。

* 该段被发送到网络层，该层包裹了一个额外的IP头。目的地服务器的IP地址以及当前机器的IP地址被插入，形成一个数据包。

* 该数据包接下来到达链路层。加入一个帧头，包括机器网卡的MAC地址以及网关（本地路由器）的MAC地址。和以前一样，如果内核不知道网关的MAC地址，它必须广播ARP查询以找到它。

At this point the packet is ready to be transmitted through either:

在这一点上，数据包已经准备好通过任何一种方式进行传输。

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

For most home or small business Internet connections the packet will pass from your computer, possibly through a local network, and then through a modem (MOdulator/DEModulator) which converts digital 1's and 0's into an analog signal suitable for transmission over telephone, cable, or wireless telephony connections. On the other end of the connection is another modem which converts the analog signal back into digital data to be processed by the next `network node`_ where the from and to addresses would be analyzed further.

Most larger businesses and some newer residential connections will have fiber or direct Ethernet connections in which case the data remains digital and is passed directly to the next `network node`_ for processing.

Eventually, the packet will reach the router managing the local subnet. From there, it will continue to travel to the autonomous system's (AS) border routers, other ASes, and finally to the destination server. Each router along the way extracts the destination address from the IP header and routes it to the appropriate next hop. The time to live (TTL) field in the IP header is decremented by one for each router that passes. The packet will be dropped if the TTL field reaches zero or if the current router has no space in its queue (perhaps due to network congestion).

对于大多数家庭或小型企业的互联网连接来说，数据包将从你的计算机通过，可能通过一个本地网络，然后通过一个调制解调器（MOdulator/DEModulator），将数字1和0转换为模拟信号，适合通过电话、电缆或无线电话连接传输。连接的另一端是另一个调制解调器，它将模拟信号转换为数字数据，由下一个 `network node`_ 处理，在那里将进一步分析来自和来自的地址。

大多数大型企业和一些较新的住宅连接将有光纤或直接以太网连接，在这种情况下，数据仍然是数字的，并直接传递到下一个 `network node`_ 进行处理。

最终，数据包将到达管理本地子网的路由器。从那里，它将继续旅行到自治系统（AS）的边界路由器，其他AS，最后到目的地服务器。沿途的每个路由器从 IP 头中提取目标地址，并将其路由到适当的下一跳。每经过一个路由器，IP 头中的生存时间（TTL）字段就会减一。如果 TTL 字段达到零或当前路由器的队列中没有空间（可能是由于网络拥堵），数据包将被丢弃。

This send and receive happens multiple times following the TCP connection flow:

* Client chooses an initial sequence number (ISN) and sends the packet to the server with the SYN bit set to indicate it is setting the ISN

* Server receives SYN and if it's in an agreeable mood:
   * Server chooses its own initial sequence number
   * Server sets SYN to indicate it is choosing its ISN
   * Server copies the (client ISN +1) to its ACK field and adds the ACK flag to indicate it is acknowledging receipt of the first packet

* Client acknowledges the connection by sending a packet:
   * Increases its own sequence number
   * Increases the receiver acknowledgment number
   * Sets ACK field

* Data is transferred as follows:
   * As one side sends N data bytes, it increases its SEQ by that number
   * When the other side acknowledges receipt of that packet (or a string of packets), it sends an ACK packet with the ACK value equal to the last received sequence from the other

* To close the connection:
   * The closer sends a FIN packet
   * The other sides ACKs the FIN packet and sends its own FIN
   * The closer acknowledges the other side's FIN with an ACK

这种发送和接收是按照 TCP 连接流程多次进行的。

* 客户端选择一个初始序列号（ISN），并将数据包发送给服务器，同时设置 SYN 位以表示它正在设置 ISN

* 服务器收到 SYN，如果它处于同意的状态。
   * 服务器选择自己的初始序列号
   * 服务器设置 SYN 以表示它正在选择它的 ISN
   * 服务器将（客户端 ISN+1）复制到它的 ACK 字段，并添加 ACK 标志，以表示它确认收到第一个数据包。

* 客户端通过发送一个数据包来确认连接。
   * 增加自己的序列号
   * 增加接收方的确认号码
   * 设置 ACK 字段

* 数据的传输方式如下。
   * 当一方发送 N 个数据字节时，它的 SEQ 增加该数字
   * 当另一方确认收到该数据包（或一串数据包）时，它将发送一个 ACK 数据包， ACK 值等于最后从另一方收到的序列。

* 要关闭连接。
   * 近端发送一个 FIN 数据包
   * 另一方对 FIN 数据包进行 ACK，并发送自己的FIN数据包。
   * 更近的一方用ACK确认另一方的 FIN。

TLS handshake
-------------
* The client computer sends a ``ClientHello`` message to the server with its Transport Layer Security (TLS) version, list of cipher algorithms and compression methods available.

* The server replies with a ``ServerHello`` message to the client with the TLS version, selected cipher, selected compression methods and the server's public certificate signed by a CA (Certificate Authority). The certificate contains a public key that will be used by the client to encrypt the rest of the handshake until a symmetric key can be agreed upon.

* The client verifies the server digital certificate against its list of trusted CAs. If trust can be established based on the CA, the client generates a string of pseudo-random bytes and encrypts this with the server's public key. These random bytes can be used to determine the symmetric key.

* The server decrypts the random bytes using its private key and uses these bytes to generate its own copy of the symmetric master key.

* The client sends a ``Finished`` message to the server, encrypting a hash of the transmission up to this point with the symmetric key.

* The server generates its own hash, and then decrypts the client-sent hash to verify that it matches. If it does, it sends its own ``Finished`` message to the client, also encrypted with the symmetric key.

* From now on the TLS session transmits the application (HTTP) data encrypted with the agreed symmetric key.

* 客户端计算机向服务器发送一个 ``ClientHello`` 消息，其中包括其传输层安全（TLS）版本、可用的密码算法和压缩方法列表。

* 服务器向客户发送 ``ServerHello`` 消息，包括 TLS 版本、选择的密码、选择的压缩方法和由 CA（证书机构）签署的服务器公共证书。该证书包含一个公钥，客户端将使用该公钥对握手的其余部分进行加密，直到可以商定一个对称的密钥。

* 客户端根据其信任的 CA 列表验证服务器的数字证书。如果可以根据 CA 建立信任，客户端会生成一串伪随机字节，并用服务器的公钥对其进行加密。这些随机字节可以用来确定对称密钥。

* 服务器使用其私钥对随机字节进行解密，并使用这些字节来生成其自己的对称主密钥副本。

* 客户端向服务器发送一个 ``Finished`` 消息，用对称密钥加密到此为止的传输的哈希值。

* 服务器生成自己的哈希值，然后解密客户端发送的哈希值以验证其是否匹配。如果匹配，它就向客户发送自己的 ``Finished`` 消息，也是用对称密钥加密的。

* 从现在开始，TLS 会话传输的应用（HTTP）数据是用约定的对称密钥加密的。

If a packet is dropped
----------------------

Sometimes, due to network congestion or flaky hardware connections, TLS packets will be dropped before they get to their final destination. The sender then has to decide how to react. The algorithm for this is called `TCP congestion control`_. This varies depending on the sender; the most common algorithms are `cubic`_ on newer operating systems and `New Reno`_ on almost all others.

* Client chooses a `congestion window`_ based on the `maximum segment size`_ (MSS) of the connection.

* For each packet acknowledged, the window doubles in size until it reaches the 'slow-start threshold'. In some implementations, this threshold is adaptive.

* After reaching the slow-start threshold, the window increases additively for each packet acknowledged. If a packet is dropped, the window reduces exponentially until another packet is acknowledged.

有时，由于网络拥堵或硬件连接不稳定，TLS 数据包在到达其最终目的地之前就会被丢弃。这时发件人必须决定如何应对。这方面的算法被称为 `TCP拥堵控制 TCP congestion control`。这取决于发件人；最常见的算法是较新的操作系统上的 `cubic`_ 和几乎所有其他系统上的 `New Reno`_。

* 客户端根据连接的 `最大网段大小 maximum segment size`_（MSS）选择一个 `拥堵窗口 congestion window`_。

* 对于每个确认的数据包，窗口的大小增加一倍，直到达到 "慢速启动阈值"。在一些实施方案中，这个阈值是自适应的。

* 在达到慢速启动阈值后，每确认一个数据包，窗口都会加法增加。如果一个数据包被丢弃，窗口会以指数形式减少，直到另一个数据包被确认。

HTTP protocol
-------------

If the web browser used was written by Google, instead of sending an HTTP request to retrieve the page, it will send a request to try and negotiate with the server an "upgrade" from HTTP to the SPDY protocol.

如果使用的网络浏览器是由谷歌编写的，那么它将发送一个请求，试图与服务器协商从HTTP "升级 "到SPDY协议，而不是发送一个HTTP请求来检索该页面。

If the client is using the HTTP protocol and does not support SPDY, it sends a request to the server of the form

如果客户端使用的是 HTTP 协议，不支持 SPDY，它就会向服务器发送一个下面形式的请求::

    GET / HTTP/1.1
    Host: google.com
    Connection: close
    [other headers]

where ``[other headers]`` refers to a series of colon-separated key-value pairs formatted as per the HTTP specification and separated by single newlines.

其中 ``[other headers]``指的是一系列用冒号分隔的 key-value 对，按照 HTTP 规范的格式，用 single newlines 分隔。

(This assumes the web browser being used doesn't have any bugs violating the HTTP spec. This also assumes that the web browser is using ``HTTP/1.1``, otherwise it may not include the ``Host`` header in the request and the version specified in the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

(假设使用的网络浏览器没有任何违反HTTP规范的错误。这也假设网络浏览器使用的是 ``HTTP/1.1``，否则它可能不会在请求中包括 ``Host``头，而在 ``GET`` 请求中指定的版本将是 ``HTTP/1.0`` 或 ``HTTP/0.9``)。

HTTP/1.1 defines the "close" connection option for the sender to signal that the connection will be closed after completion of the response. For example,

HTTP/1.1 为发送方定义了 "close" 连接选项，以示在完成响应后关闭连接。比如说


    Connection: close

HTTP/1.1 applications that do not support persistent connections MUST include the "close" connection option in every message.

不支持持久连接的 HTTP/1.1 应用, 必须在每个消息中包括 "close" 连接选项。

After sending the request and headers, the web browser sends a single blank newline to the server indicating that the content of the request is done.

在发送请求和标头后，网络浏览器向服务器发送 a single blank newline，表示请求的内容已经完成。

The server responds with a response code denoting the status of the request and responds with a response of the form

服务器用, 一个表示请求状态的 response code, 进行响应，并以如下形式进行回应::

    200 OK
    [response headers]

Followed by a single newline, and then sends a payload of the HTML content of ``www.google.com``. The server may then either close the connection, or if headers sent by the client requested it, keep the connection open to be reused for further requests.

后面是一个 single newline，然后发送一个 HTML 内容的有效载荷 ``www.google.com``。然后，服务器可以关闭连接，或者, 如果客户端发送的 headers 中需要这个连接，则保持连接开放，以便为以后的请求重新使用。

If the HTTP headers sent by the web browser included sufficient information for the webserver to determine if the version of the file cached by the web browser has been unmodified since the last retrieval (ie. if the web browser included an ``ETag`` header), it may instead respond with a request of the form

如果网络浏览器发送的HTTP头, 包括足够的信息，使网络服务器, 能够确定网络浏览器缓存的文件版本, 自上次检索以来是否未被修改（即如果网络浏览器包括一个 ``ETag`` 头），它可以用以下形式的 request 代替 respond::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieve the HTML from its cache.
并且，没有有效载荷，网络浏览器则从其缓存中, 检索HTML。

After parsing the HTML, the web browser (and server) repeats this process for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page, except instead of ``GET / HTTP/1.1`` the request will be ``GET /$(URL relative to www.google.com) HTTP/1.1``.

在解析了HTML之后，网络浏览器（和服务器）对 HTML 页面引用的每一个资源（图像、CSS、favicon.ico等）重复这一过程，只是请求不是 ``GET / HTTP/1.1``，而是 ``GET /$(URL relative to www.google.com) HTTP/1.1``。

If the HTML referenced a resource on a different domain than ``www.google.com``, the web browser goes back to the steps involved in resolving the other domain, and follows all steps up to this point for that domain. The ``Host`` header in the request will be set to the appropriate server name instead of ``google.com``.

如果HTML引用了与 ``www.google.com``不同的域上的资源，网络浏览器就会回到解析其他域的步骤，并遵循该域到此为止的所有步骤。请求中的 ``Host`` 头将被设置为适当的服务器名称，而不是 ``google.com``。

HTTP Server Request Handle
--------------------------
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on the server-side. The most common HTTPD servers are Apache or nginx for Linux and IIS for Windows.

* The HTTPD (HTTP Daemon) receives the request.

* The server breaks down the request to the following parameters:

   * HTTP Request Method (either ``GET``, ``HEAD``, ``POST``, ``PUT``, ``PATCH``, ``DELETE``, ``CONNECT``, ``OPTIONS``, or ``TRACE``). In the
     case of a URL entered directly into the address bar, this will be ``GET``.

   * Domain, in this case - google.com.

   * Requested path/page, in this case - / (as no specific path/page was
     requested, / is the default path).

* The server verifies that there is a Virtual Host configured on the server that corresponds with google.com.

* The server verifies that google.com can accept GET requests.

* The server verifies that the client is allowed to use this method (by IP, authentication, etc.).

* If the server has a rewrite module installed (like mod_rewrite for Apache or URL Rewrite for IIS), it tries to match the request against one of the configured rules. If a matching rule is found, the server uses that rule to rewrite the request.

* The server goes to pull the content that corresponds with the request, in our case it will fall back to the index file, as "/" is the main file (some cases can override this, but this is the most common method).

* The server parses the file according to the handler. If Google is running on PHP, the server uses PHP to interpret the index file, and streams the output to the client.

HTTPD（HTTP Daemon）服务器是, 在服务器端处理请求/响应的服务器。最常见的 HTTPD 服务器是 Linux 的 Apache 或 nginx 和 Windows 的 IIS。

1. HTTPD（HTTP Daemon）接收请求。

2. 服务器将请求分解为以下参数。

   * HTTP请求方法（ ``GET', ``HEAD``, ``POST``, ``PUT``, ``PATCH``, ``DELETE``, ``CONNECT``, ``OPTIONS``, 或 ``TRACE``）。在如果是直接在地址栏输入的URL，这将是 ``GET``。

   * 域名，在本例中是 ``google.com``。

   * 请求的 path/page，在这种情况下 - / (因为没有特定的路径/页面被请求，/是默认路径) 因为没有请求特定的 path/page，/ 是默认路径）。

* 服务器验证在服务器上有一个与 google.com 相对应的虚拟主机配置。

* 服务器验证 google.com 可以接受 GET 请求。

* 服务器验证客户是否被允许使用这种方法（通过IP、认证等）。

* 如果服务器安装了重写模块（如 Apache 的 mod_rewrite 或 IIS 的 URL Rewrite），它试图将请求与配置的规则之一进行匹配。如果找到一个匹配的规则，服务器会使用该规则重写请求。

* 服务器去提取与请求相对应的内容，在我们的例子中，它将回落到索引文件，因为 ``/`` 是 main file（有些情况下可以覆盖这一点，但这是最常见的方法）。

* 服务器根据处理程序来解析文件。如果 Google 运行在 PHP 上，服务器会使用 PHP 来解释索引文件，并将输出流向客户端。


Behind the scenes of the Browser
----------------------------------

Once the server supplies the resources (HTML, CSS, JS, images, etc.) to the browser it undergoes the below process:

* Parsing - HTML, CSS, JS
* Rendering - Construct DOM Tree → Render Tree → Layout of Render Tree → Painting the render tree

一旦服务器向浏览器提供资源（HTML、CSS、JS、图像等），就会经历以下过程。

* 解析--HTML、CSS、JS
* 渲染 - 构建DOM树 → 渲染树 → 渲染树的布局 → 绘制渲染树

Browser
-------

The browser's functionality is to present the web resource you choose, by requesting it from the server and displaying it in the browser window.

The resource is usually an HTML document, but may also be a PDF, image, or some other type of content. The location of the resource is specified by the user using a URI (Uniform Resource Identifier).

The way the browser interprets and displays HTML files is specified in the HTML and CSS specifications. These specifications are maintained by the W3C (World Wide Web Consortium) organization, which is the standards organization for the web.

Browser user interfaces have a lot in common with each other. Among the
common user interface elements are:

* An address bar for inserting a URI
* Back and forward buttons
* Bookmarking options
* Refresh and stop buttons for refreshing or stopping the loading of
  current documents
* Home button that takes you to your home page

浏览器的功能是, 通过从服务器上请求网络资源, 并将其显示在浏览器窗口中，来呈现你所选择的网络资源。

该资源通常是一个 HTML 文档，但也可能是一个 PDF、图像或其他类型的内容。资源的位置是由用户用 URI（统一资源标识符）指定的。

浏览器解释和显示 HTML 文件的方式是在 HTML 和 CSS 规范中规定的。这些规范由W3C（万维网联盟）组织维护，它是网络的标准组织。

浏览器的用户界面有很多共同点。其中的共同的用户界面元素有。

* 一个用于插入URI的地址栏
* 后退和前进按钮
* 书签选项
* 刷新和停止按钮，用于刷新或停止加载当前文件。
  当前文件的刷新和停止按钮
* 主页按钮，可以带你到你的主页

**Browser High-Level Structure**

The components of the browsers are:

* **User interface:** The user interface includes the address bar, back/forward button, bookmarking menu, etc. Every part of the browser display except the window where you see the requested page.

* **Browser engine:** The browser engine marshals actions between the UI and the rendering engine.

* **Rendering engine:** The rendering engine is responsible for displaying requested content. For example if the requested content is HTML, the rendering engine parses HTML and CSS, and displays the parsed content on the screen.

* **Networking:** The networking handles network calls such as HTTP requests, using different implementations for different platforms behind a platform-independent interface.

* **UI backend:** The UI backend is used for drawing basic widgets like combo boxes and windows. This backend exposes a generic interface that is not platform-specific. Underneath it uses operating system user interface methods.

* **JavaScript engine:** The JavaScript engine is used to parse and execute JavaScript code.

* **Data storage:** The data storage is a persistence layer. The browser may need to save all sorts of data locally, such as cookies. Browsers also support storage mechanisms such as localStorage, IndexedDB, WebSQL and FileSystem.

浏览器的组成部分是:

1. **User interface 用户接口 ：** 用户接口包括地址栏、后退/前进按钮、书签菜单等。浏览器显示的每一个部分，除了你看到请求的页面的窗口。

2. **Browser engine 浏览器引擎：** 浏览器引擎在用户界面和渲染引擎之间传递动作。

3. **Rendering engine 渲染引擎：** 渲染引擎负责显示请求的内容。例如，如果请求的内容是HTML，渲染引擎会解析HTML和CSS，并将解析后的内容显示在屏幕上。

4. **Networking 网络：** 网络处理网络调用，如HTTP请求，在一个独立于平台的接口后面为不同的平台使用不同的实现。

5. **UI backend UI后端：** UI后端用于绘制基本的小工具，如组合框和窗口。这个后端暴露了一个通用的接口，不针对特定平台。在这之下，它使用操作系统的用户界面方法。

6. **JavaScript engine：** JavaScript 引擎用于解析和执行 JavaScript 代码。

7. **Data storage 数据存储：**数据存储是一个持久层。浏览器可能需要在本地保存各种数据，如 cookie。浏览器也支持存储机制，如 localStorage、IndexedDB、WebSQL和FileSystem。

HTML parsing
------------

The rendering engine starts getting the contents of the requested document from the networking layer. This will usually be done in 8kB chunks.

The primary job of the HTML parser is to parse the HTML markup into a parse tree.

The output tree (the "parse tree") is a tree of DOM element and attribute nodes. DOM is short for Document Object Model. It is the object presentation of the HTML document and the interface of HTML elements to the outside world like JavaScript. The root of the tree is the "Document" object. Prior to any manipulation via scripting, the DOM has an almost one-to-one relation to the markup.

渲染引擎开始从网络层获取所请求的文件的内容。这通常会以 8kB 为单位进行。

HTML解析器的主要工作是将HTML标记解析为一个解析树。

输出树（"解析树 parse tree"）是一棵 DOM 元素和属性节点的树。DOM 是文档对象模型 Document Object Model 的简称。它是 HTML 文档的对象呈现，也是 HTML 元素与外界的接口，如 JavaScript。树的根是 "Document" 对象。在通过脚本进行任何操作之前，DOM 与标记 markup 有几乎一对一的关系。

**The parsing algorithm**

HTML cannot be parsed using the regular top-down or bottom-up parsers.

The reasons are:

* The forgiving nature of the language.
* The fact that browsers have traditional error tolerance to support well known cases of invalid HTML.
* The parsing process is reentrant. For other languages, the source doesn't change during parsing, but in HTML, dynamic code (such as script elements containing `document.write()` calls) can add extra tokens, so the parsing process actually modifies the input.

Unable to use the regular parsing techniques, the browser utilizes a custom parser for parsing HTML. The parsing algorithm is described in
detail by the HTML5 specification.

The algorithm consists of two stages: tokenization and tree construction.

HTML不能用, 常规的自上而下, 或自下而上的, 解析器进行解析。

其原因是:

* 该语言的宽容性。

* 浏览器具有传统的容错能力，支持众所周知的无效 HTML 的情况。

* 解析过程是可重入的。对于其他语言，解析过程中源码不会改变，但在 HTML 中，动态代码（如包含 `document.write()` 调用的脚本元素）可以添加额外的标记 tokens，所以解析过程实际上修改了输入。

由于无法使用常规的解析技术，浏览器利用一个自定义的解析器来解析 HTML。解析算法的详细描述见 详细描述了 HTML5 规范。

该算法由两个阶段组成：标记化 tokenization 和树状结构 tree construction。

**Actions when the parsing is finished**

The browser begins fetching external resources linked to the page (CSS, images, JavaScript files, etc.).

At this stage the browser marks the document as interactive and starts parsing scripts that are in "deferred" mode: those that should be executed after the document is parsed. The document state is set to "complete" and a "load" event is fired.

Note there is never an "Invalid Syntax" error on an HTML page. Browsers fix any invalid content and go on.

浏览器开始获取链接到该页面的外部资源（CSS、图像、JavaScript文件等）。

在这个阶段，浏览器将文档标记为交互式 interactive，并开始解析处于 "延迟 deferred"模式的脚本：那些应该在, 文档被解析后执行的脚本。文档的状态被设置为 "complete"，并触发 "load" 事件。

注意在一个 HTML 页面上永远不会出现 "无效语法 Invalid Syntax" 的错误。浏览器会修复任何无效的内容并继续前进。

CSS interpretation
------------------

* Parse CSS files, ``<style>`` tag contents, and ``style`` attribute values using `"CSS lexical and syntax grammar"`_

* Each CSS file is parsed into a ``StyleSheet object``, where each object contains CSS rules with selectors and objects corresponding CSS grammar.  

* A CSS parser can be top-down or bottom-up when a specific parser generator is used.

* 使用 `"CSS词法和语法 CSS lexical and syntax grammar"`_ 解析 CSS 文件、 ``<style>`` 标签内容, 和 ``style`` 属性值。

* 每个 CSS 文件都被解析成一个 ``StyleSheet object``，每个对象都包含, 有选择器的 CSS 规则, 和对象对应的 CSS 语法。

* 当使用一个特定的解析器生成器时，一个CSS解析器可以是, 自上而下, 或自下而上的。

Page Rendering
--------------

* Create a 'Frame Tree' or 'Render Tree' by traversing the DOM nodes, and calculating the CSS style values for each node.

* Calculate the preferred width of each node in the 'Frame Tree' bottom-up by summing the preferred width of the child nodes and the node's horizontal margins, borders, and padding.

* Calculate the actual width of each node top-down by allocating each node's available width to its children.

* Calculate the height of each node bottom-up by applying text wrapping and summing the child node heights and the node's margins, borders, and padding.

* Calculate the coordinates of each node using the information calculated above.

* More complicated steps are taken when elements are ``floated``, positioned ``absolutely`` or ``relatively``, or other complex features are used. See http://dev.w3.org/csswg/css2/ and http://www.w3.org/Style/CSS/current-work for more details.

* Create layers to describe which parts of the page can be animated as a group without being re-rasterized. Each frame/render object is assigned to a layer.

* Textures are allocated for each layer of the page.

* The frame/render objects for each layer are traversed and drawing commands are executed for their respective layer. This may be rasterized by the CPU or drawn on the GPU directly using D2D/SkiaGL.

* All of the above steps may reuse calculated values from the last time the webpage was rendered, so that incremental changes require less work.

* The page layers are sent to the compositing process where they are combined with layers for other visible content like the browser chrome, iframes and addon panels.

* Final layer positions are computed and the composite commands are issued via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for asynchronous rendering and the frame is sent to the window server.

#. 通过遍历 DOM 节点创建一个 "框架树 Frame Tree" 或 "渲染树 Render tree"，并计算每个节点的 CSS 样式值。

#. 自下而上地计算 "框架树 Frame Tree" 中每个节点的首选宽度，方法是将子节点的首选宽度与节点的水平边距、边框和填充相加。

#. 通过将每个节点的可用宽度分配给其子节点，自上而下地计算每个节点的实际宽度。

#. 通过应用文本包装并将子节点的高度与节点的边距、边框和填充相加，自下而上计算每个节点的高度。

#. 使用上面计算的信息计算每个节点的坐标。

#. 当元素 ``floated``、 ``absolutely`` 或 ``relatively`` 定位，或使用其他复杂的特征时，会采取更复杂的步骤。更多细节见 http://dev.w3.org/csswg/css2/ 和 http://www.w3.org/Style/CSS/current-work。

#. 创建图层来描述页面的哪些部分可以作为一组动画而不被重新光栅化。每个帧/渲染对象都被分配到一个层。

#. 为页面的每个层分配纹理。

#. 每个层的框架/渲染对象被遍历，并为其各自的层执行绘图命令。这可以由CPU进行光栅化处理，或者直接使用D2D/SkiaGL在GPU上绘制。

#. 所有上述步骤都可以重复使用上次渲染网页时的计算值，因此，增量变化需要较少的工作。

#. 页面图层被送到合成过程中，在那里它们与其他可见内容的图层相结合，如浏览器的chrome、iframes和addon面板。

#. 最终的图层位置被计算出来，合成命令通过Direct3D/OpenGL发出。GPU的命令缓冲区被刷新到GPU上进行异步渲染，帧被发送到窗口服务器上。

GPU Rendering
-------------

* During the rendering process the graphical computing layers can use general purpose ``CPU`` or the graphical processor ``GPU`` as well.

* When using ``GPU`` for graphical rendering computations the graphical software layers split the task into multiple pieces, so it can take advantage of ``GPU`` massive parallelism for float point calculations required for the rendering process.

#. 在渲染过程中，图形计算层可以使用通用的 "CPU "或图形处理器 "GPU"。

#. 当使用`GPU'进行图形渲染计算时，图形软件层将任务分成多个部分，因此它可以利用`GPU'的大规模并行性进行渲染过程中所需要的浮点计算。


Window Server
-------------

Post-rendering and user-induced execution
-----------------------------------------

After rendering has been completed, the browser executes JavaScript code as a result of some timing mechanism (such as a Google Doodle animation) or user interaction (typing a query into the search box and receiving suggestions).

Plugins such as Flash or Java may execute as well, although not at this time on the Google homepage. Scripts can cause additional network requests to be performed, as well as modify the page or its layout, causing another round of page rendering and painting.

渲染完成后，由于一些计时机制（如Google Doodle动画）或用户互动（在搜索框中输入查询并收到建议），浏览器会执行 JavaScript 代码。

诸如 Flash 或 Java 等插件也可能执行，尽管目前在谷歌主页上没有。脚本可能导致执行额外的网络请求，以及修改页面或其布局，导致另一轮的页面渲染和绘画。

.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
.. _`TCP congestion control`: https://en.wikipedia.org/wiki/TCP_congestion_control
.. _`cubic`: https://en.wikipedia.org/wiki/CUBIC_TCP
.. _`New Reno`: https://en.wikipedia.org/wiki/TCP_congestion_control#TCP_New_Reno
.. _`congestion window`: https://en.wikipedia.org/wiki/TCP_congestion_control#Congestion_window
.. _`maximum segment size`: https://en.wikipedia.org/wiki/Maximum_segment_size
.. _`varies by OS` : https://en.wikipedia.org/wiki/Hosts_%28file%29#Location_in_the_file_system
.. _`简体中文`: https://github.com/skyline75489/what-happens-when-zh_CN
.. _`한국어`: https://github.com/SantonyChoi/what-happens-when-KR
.. _`日本語`: https://github.com/tettttsuo/what-happens-when-JA
.. _`downgrade attack`: http://en.wikipedia.org/wiki/SSL_stripping
.. _`OSI Model`: https://en.wikipedia.org/wiki/OSI_model
.. _`Spanish`: https://github.com/gonzaleztroyano/what-happens-when-ES
