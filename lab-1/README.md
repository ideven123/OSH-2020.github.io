# Lab 1

## 开始之前的准备

- [Linux 的安装与使用](linux/)
- [学习使用 Git](git/)
- [学习 GitHub 的简单使用，搭建实验私有仓库](github/)

然后你需要安装 `qemu`，以及其他一些开发需要的组件。以 Ubuntu 18.04 为例：

```shell
sudo apt install git \
  qemu-system-x86 build-essential flex bison bc \
  ncurses-dev libssl-dev libelf-dev \
  nasm
```

注意：
- 关于使用的工具与系统的使用只需要比较基础的用法即可。
- Linux 的使用不是完全强制的，但是对于其他环境出现的问题，我们不会进行详细的指导。

## 实验内容

你需要阅读以下内容，并且按照「实验要求」部分来完成。

- [编译 Linux 内核并在 QEMU 中测试](kernel/)
- [构建初始内存盘 (initrd, init ram disk)](initrd/)
- [使用汇编语言，编写 x86 裸金属 (bare-metal) 程序](mbr/)

助教提供的三个测试程序：[binaries.zip](binaries.zip)

## 实验要求

请按照以下目录结构组织你的 GitHub 仓库：

```
.                         // Git 根目录
├── lab1                  // 实验一根目录
│   ├── docs              // 实验一实验报告根目录
│   │   └── *.md          // 文件名自拟，可以写在一个或多个 Markdown 文件中
│   ├── linux
│   │   ├── bzImage
│   │   ├── initrd.cpio.gz
|   │   └── .config       //（可选）
│   └── mbr
│       └── mbr.asm
├── .gitignore            // 使用此文件忽略你不想提交的文件   
└── README.md             // 整个仓库的 README 文件，可以将姓名学号写在其中
```

本次实验满分 10 分。你需要完成：

- 关于 Git 与 GitHub 仓库：
  - 正确创建了仓库，助教可访问，且**仓库权限为私有 (private)**。（**必须完成**）
  - Git 记录能够清晰显示你的实验进度，没有出现以下的情况。（1 分）
    - 很大一部分的 commit 由 GitHub 网页版生成，即通过网页版文件上传的方式提交实验的文件。
    - 只有一个，或两三个 commit。
    - 上传了大量与实验要求无关的文件，没有设置 `.gitignore`。
- 关于 Linux 内核：
  - 正确编译了内核，并将编译出的 `arch/x86/boot/bzImage` 放在你的仓库中的 `lab1/linux/` 下面。（1 分）
  - 你裁剪了内核，裁剪得到的内核大小不超过 8 MiB (= 8388608 bytes)，并且它可以完成以下「关于 `initrd` 与 `init` 程序」中的全部任务。（1 分）
    - 如果你没有完成「关于 `initrd` 与 `init` 程序」中的全部任务，助教会使用自己的 `initrd` 进行测试。
  - 在上一条的基础上，裁剪得到的内核大小不超过 4 MiB (= 4194304 bytes)。（0.5 分）
- 关于 `initrd` 与 `init` 程序：
  - 构造了格式正确的 `initrd.cpio.gz` 文件，并将对应文件放在你的仓库中的 `lab1/linux/initrd.cpio.gz`。（0.5 分）
  - 使用你编译的内核和 `initrd.cpio.gz`，其中的 `/init` 能够成功执行助教提供的程序 1。（0.5 分）
  - (\*) 使用你编译的内核和 `initrd.cpio.gz`，其中的 `/init` 能够成功依次执行助教提供的程序 1、2、3，并且程序 3 执行完成后不出现内核恐慌 (Kernel Panic)。（1.5 分）
- 关于 x86 裸金属 MBR 程序：
  - 汇编源文件在仓库中的 `lab1/mbr/mbr.asm` 下，源文件汇编得到的二进制文件可以在 QEMU 环境下向屏幕输出文字。（1 分）
  - (\*) 汇编程序能够首先清空屏幕，之后每隔大约 1 秒（不需要非常精确），向屏幕上输出一行文字。（1.5 分）
- 思考题：
  - 在下面给出的思考题中任选 4 道并完成它们。（1.5 分）

对于含 (*) 程序的运行参考效果，助教录制了视频，放在了 B 站（由于众所周知的原因，还要等待一段时间）和 [YouTube](https://www.youtube.com/watch?v=F_Qlz-9I3A8) 上。

**注意：你需要写实验报告，并且报告需要要点齐全。在要点齐全的情况下可以精简，可以描述你在完成实验时遇到的问题与解决方法，也可以提供建议等。如果对以上每个给分大项（除 Git 相关内容外）没有对应的实验报告，你可能不会得到这一部分的分数。**

### 思考题

1. 在使用 `make menuconfig` 调整 Linux 编译选项的时候，你会看到有些选项是使用 `[M]` 标记的。它们是什么意思？在你的 `init` 启动之前的整个流程中它们会被加载吗？如果不，在正常的 Linux 系统中，它们是怎么被加载的？
2. 在「构建 `initrd`」的教程中我们创建了一个示例的 init 程序。为什么屏幕上输出 "Hello, Linux!" 之后，Linux 内核就立刻 kernel panic 了？
3. 为什么我们编写 C 语言版本的 `init` 程序在编译时需要静态链接？我们能够在这里使用动态链接吗？
4. 在 Vlab 平台上，即使是真正的 root 也无法使用 `mknod` 等命令，查找资料，尝试简要说明为什么 fakeroot 环境却能够正常使用这些命令。
5. 在介绍 BusyBox 的一节，我们发现 `init` 程序可以是一段第一行是 `#!/bin/sh` 的 shell 脚本。尝试解释为什么它可以作为系统第一个启动的程序，并且说明这样做需要什么条件。
6. 我们的 MBR 样例程序使用了 BIOS 调用 (`int 0x10`) 以显示文本。Linux 的 `init` 程序也可以用这种办法输出文本吗？为什么？
7. MBR 能够用于编程的部分只有 510 字节，而目前的系统引导器（如 GRUB2）可以实现很多复杂的功能：如从不同的文件系统中读取文件、引导不同的系统启动、提供美观的引导选择界面等，用 510 字节显然是无法做到这些功能的。它们是怎么做到的？
8. 目前，越来越多的 PC 使用 UEFI 启动。请简述使用 UEFI 时系统启动的流程，并与传统 BIOS 的启动流程比较。