# Introduction to Linux

Linux is an alternative operating system for various different types of computers.

There are many different "flavours" of linux, which is to say that there are many different types of linux distributions that have different philosophies to the others. There is a lot of choice when it comes to this platform. Some distributions favour speed over stability and vice versa. Some offer the latest packages/software with minimal testing done before hand while others test packages/software for months before releasing updates. 

An example of one linux distribution (or flavour) is Ubuntu, one you may have heard of.

<figure>
<img src="./resources/img/ubuntu_desktop.jpg">
<figcaption style="font-style: italic; display: flex; flex-direction: column; align-items: center">Desktop interface for Ubuntu 19.04</figcaption>
</figure>

One big difference between Windows and linux operating systems is that most of the software that you use on linux can and is installed via a "package manager". This is a piece of software that manages installs of software on your behalf and can fetch software from a central, trusted source. It can also be configured to fetch software from other sources.

Another big difference is that, while it is not strictly necessary, it's often the case that you will end up using the terminal a lot more in linux than in Windows.

## The linux terminal

The terminal is a window into the inner workings of a linux distribution. The graphical environment you're used to seeing interacts with the operating system the same way that the terminal does, it's just presented to you graphically. 

For example, when you open the file explorer to see your files, the graphical environment (desktop) will actually call a command the same way you would in the terminal to show you those files.

Here is an example of a linux terminal:
![Gnome terminal](resources/img/linux_terminal.png)

Here you see `[edebeste@ArchSANBI ~]$`. The first part, `edebeste`, represents the user account that you are logged into. My user account on my computer is called `edebeste`. The `@ArchSANBI` part represents the computer that you are using. My laptop is named `ArchSANBI`, so `edebeste@ArchSANBI` is saying that the user `edebeste` is logged into the machine (my laptop) `ArchSANBI`. The `~` represents that I am in my user accounts home folder. The `$` shows that it is ready for your input to the terminal. From here on, you type a command and press enter to execute it.