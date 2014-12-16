---
layout: post
title: "Compliling Linux Kernel"
description: ""
category: LinuxKernel
tags: [Linux, Kernel]
modified: 2014-12-16

comments: true
share: true
---

<p>曾经重新编译内核是一件多么复杂的事情，很多人都会有这样的恐惧感。然而，看完本文以后，希望你会感到一身轻松，原来内核的编译可以如此简单。</p>

<p>一、升级内核的原因
升级的原因有很多，例如新的硬件驱动，去除内核 Bug ，一般而言新内核会比旧版本更快，更稳定和可靠。</p>

<p>二、下载源码并解压
FreeLAMP.com 首页右上方提供 LAMP 各版本的最新下载连接。你可以直接点击下载。
下载后，我们把文件压缩到 /usr/src：</p>

<pre><code>gzip -dc linux-2.4.19.tar.gz | tar xvvf -
mv linux-2.4.19 /usr/src
cd /usr/src
</code></pre>

<p>如果有 linux 目录存在，先删除，然后，建立 linux 软连接：</p>

<pre><code>rm -rf linux
ln -s linux-2.4.19 linux
</code></pre>

<p>对于下载补丁的看官，所作的事情比较简单：</p>

<pre><code>gzip -d path-2.4.19.gz
mv patch-2.4.19 /usr/src
</code></pre>

<p>确定硬件：
在升级内核前，你需要知道自己机器上的硬件来安装合适的模块。查看硬件的命令有：
lspci 列出所有的 pci 设备。
dmesg 也能给出很多有用的信息。</p>

<p>三、编译源码</p>

<pre><code>cd /usr/src/linux
make mrproper
</code></pre>

<p>接下来可以选择下面的一个命令来配置内核：</p>

<p>‘make config’ – 适合于慢速的远程终端连接</p>

<p>‘make menuconfig’ – 适合于高速的远程终端或者本机</p>

<p>‘make xconfig’ – 在 X11 窗口上</p>

<p>‘make oldconfig’ – 采用以前的 .config 文件 (升级时十分有用)</p>

<p>然后，根据菜单仔细选择合适的配置，删除你的系统不需要的配置，内核越小，系统运行越快。配置完毕后，存盘退出。输入：</p>

<pre><code>make dep ;
make bzImage ;
make modules
make modules_install
</code></pre>

<p>拷贝 boot 文件，</p>

<pre><code>cp /usr/src/linux/arch/i386/boot/bzImage /boot/kernel-2.4.19
</code></pre>

<p>/boot 下面的文件名可以随意取，但是 kernel-2.4.19 比较简洁明了，容易记忆。</p>

<p>四、配置 LILO</p>

<p>cd /etc
修改 lilo.conf ，文件结尾处添加：</p>

<pre><code>image=/boot/kernel-2.4.19
label=linux-2.4.19
read-only
root=/dev/hda1
</code></pre>

<p>重新运行 lilo</p>

<p>重新启动机器，如果能正常运行的话，那么恭喜你，成功了。
如果有问题的话，把 default 修改为旧的内核版本，重新运行 lilo 就可以。
然后，重新编译内核：</p>

<pre><code>cd /usr/src
make clean
</code></pre>

<p>重新配置，检查可能存在的问题。</p>

<p>安装补丁
安装补丁十分的简单，就只要一步：</p>

<pre><code>cd /usr/src

patch -p0 &lt; patch-2.4.19
</code></pre>
