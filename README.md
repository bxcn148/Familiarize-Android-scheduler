Download link :https://programming.engineering/product/familiarize-android-scheduler/


# Familiarize-Android-scheduler
Familiarize Android scheduler
Objectives

Compile the Android kernel.

Familiarize Android scheduler

Implement a weighted round robin scheduler.

Get experience with software engineering techniques.

CS356 Operating System Projects

Enviroment

Implementation

AVD(Android Virtual Devices)

SDK version r24.4.1

Development

Linux （64-bits）

Ubuntu (recommended)

Debian

Fedora

VMware

What to Submit

A“tar” file of your DIRECTORY, containing:

All *.c, *.h files you have changed in Linux kernel.

Any “readme” or “.pdf” files asked for in the project

Screen captures of the scheduler test

If you cannot get your program to work, submit a run of whatever you can get to work as you can get partial credit

DO NOT SUBMIT your object or executable files, REMOVE them before you pack your directory.

How to Submit

Pack your code in a project directory

tar –cvf Prj2+StudentID.tar project1

Send your Prj2+StudentID.tar file on Canvas.

For Help?

Teaching Assistant

Menghan Guo

Email: 15667083571@163.com

Jiafeng Xu

Email: xujiafenga3@163.com

CS356 Operating System Projects

Compile the Linux Kernel

Compile the Linux Kernel (cont.)

Modify Makefile in the kernel

Change

ARCH ?= $(SUBARCH)

CROSS_COMPILE ?=

To

Compile the Linux Kernel (cont.)

Execute the following command:

Compile the Linux Kernel (cont.)

Then you can see a GUI configuration dialog:

Compile the Linux Kernel (cont.)

Open the Compile the kernel with debug info in Kernel hacking:

Compile the Linux Kernel (cont.)

Enable loadable module support with Forced module loading, Module unloading and Forced module unloading in it:

Compile the Linux Kernel (cont.)

Enable loadable module support with Forced module loading, Module unloading and Forced module unloading in it:

Compile the Linux Kernel (cont.)

Compile it

The number of -j* depends on the number of cores of your system.

CS356 Operating System Projects

Problem

Android supports three built-in scheduling policies: Normal, FIFO and RR.

In this problem, you are required to implement a new scheduling policy:

Weighted Round Robin.

The blog relevant to linux kernel scheduler could be helpful to you：

https://helix979.github.io/jkoo/post/os-scheduler/

Problem

Weighted Round Robin (WRR)

Round-robin scheduling treats all tasks equally, but there are times when it is desirable to give some tasks preference over the others.

Android Tasks can be classified into foreground groups and background groups.

WRR assigned more milliseconds as a time slice for foreground groups. (In our problem, 100ms for fore and 10ms for back)

Problem

Foreground and Background Groups

From user level, you can run ps -P on the device or emulator to check the assigned groups for each task.

At the kernel level, a task’s group information can be found using a task pointer. Refer to the line 96 in

kernel/sched/debug.c and use that function appropriately.

The return value will be “/” for a foreground (and system group in earlier versions) group, “/bg_non_interactive” for a background group.

Implementation Details

Files MAY NEED Modification

/arch/arm/configs/goldfish_armv7_defconfig

/include/linux/sched.h

/kernel/sched/core.c

/kernel/sched/sched.h

To implement WRR scheduler, you need to create a new class in the directory /kernel/sched/, that is

/kernel/sched/wrr.c

IMPORTANT: If you feel confused on what to do in wrr.c, read /kernel/sched/rt.c carefully. In rt.c, RR and FIFO are well implemented.

Implementation Details

/arch/arm/configs/goldfish_armv7_defconfig

In this file, you need to add a new line as follows:

Implementation Details

/include/linux/sched.h

In this file, you need to:

Define SCHED_WRR (Refer to SCHED_RR, about Line 42). The value of SCHED_WRR should be 6.

Define sched_wrr_entity (Refer to sched_rt_entity, about Line 1250)

Define time slice for foreground and background groups. (Refer to RR_TIMESLICE, about Line 1280)

Add a sched_wrr_entity varaible to task_struct (About Line 1310).

Declare a wrr_rq struct. (Refer to struct cfs_rq, about Line 150)

Maybe a little more to be revised. It depends on your implementation.

Implementation Details

/kernel/sched/sched.h （path is different from last page）

In this file, you need to:

Declare a wrr_rq struct. (Refer to struct rt_rq, about Line 90)

Define a new struct wrr_rq (Refer to rt_rq, about Line 300).

Add a wrr_rq variable to struct rq (About line 400) and similarly add a list_head variable as the figure shows.

Declare some extern variables and functions（*）. (You can refer to extern var/func of rt in the same file, About Line 190-210, Line 880, Line 900, Line 1170-1180). E.g.

Maybe a little more to be revised. It depends on your implementation.

Implementation Details

/kernel/sched/core.c

In this file, you need to:

Revise function: static void __sched_fork(struct task_struct *p)

Revise function: static void __setscheduler(struct rq *rq, struct task_struct *p, int policy, int prio)

Revise function: static void __sched_setscheduler(struct task_struct *p, int policy, const struct sched_param *param, bool user)

Add init_wrr_rq(&rq->wrr). (Refer to init_rt_rq(), about Line 7230)

Revise function: static void free_sched_group(struct task_group *tg) and struct task__group *sched_create_group(struct task_group *parent) (about Line 7500-7600)

Maybe a little more to be revised. It depends on your implementation.

Implementation Details

/kernel/sched/core.c

To know what to revise, you have to read the code carefully and know what are they implemented for.

For example, when revising __sched_setscheduler, we meet the following code segment:

Since we have one more policy WRR now, we should change it to:

Implementation Details

/kernel/sched/wrr.c

This is the major file in which you write codes. You can refer to rt.c in the same directory to learn how to write wrr.c

Here, we give a framework of wrr_sched_class

Implementation Details



/kernel/sched/wrr.c

For funtions labeled “Required”, you need to implement it in wrr.c

For functions labeled “Never need impl”, you can just put them dummy

Implementation Details

/kernel/sched/wrr.c

Remember that in wrr.c, when you want to allocate time slice to a task with WRR as its policy, you should judge whether it is a foreground task or background task, and allocate corresponding time slice to it.

The blog relevant to linux kernel scheduler could be helpful to you：

https://helix979.github.io/jkoo/post/os-scheduler/

Implementation Details

To put your /kernel/sched/wrr.c into effect:

• You need to revise the Makefile in /kernel/sched like this:

CS356 Operating System Projects

What to show

Basic

We will provide an apk processtest.apk.

Add some printk(“”) in wrr.c or some other places which proves there is a task using WRR as a policy.

Write a test file, which can change the scheduler in user space.

Change the apk’s scheduler to WRR when the apk is in foreground groups, and give out some information (pid, name, timeslice, and some others you like).

Change the apk’s scheduler to WRR when the apk is in background groups, and give out some information (pid, name, timeslice, and some others you like).

To check the pid of processtest.apk in Android shell, you may use:

What to show

Basic

Print information when app is in foreground:

Print information when app is in background:

What to show

Bonus (10 points in Final Score):

Any extended ideas can be considered into the bonus!

Here are some of the ideas we provide, I hope you won’t be limited to these:

Can you come up with a method to compare the performance of RR, FIFIO, NORMAL and WRR?

Can you build WRR in a multi-cpu architecture and implement load balance?

Hints

Hints

To install the processtest.apk to your AVD:

Initiate your Android Virtual Device

Type the command in Ubuntu Shell:

adb install [the path of apk in Ubuntu (not in Android)]

Launch your apk by clicking the icon in Android AppList

Report

Explain how your wrr.c work.

Explain what have you done in all the other files except wrr.c.

Any further analysis is welcome.

Something to Specify

To give you an overview of this project:

You need to write 500 lines (more or less) of codes in:

/kernel/sched/wrr.c

You need to revise the following files to put wrr.c into effect:

/arch/arm/configs/goldfish_armv7_defconfig

/include/linux/sched.h

/kernel/sched/sched.h

/kernel/sched/core.c

/kernel/sched/Makefile

You need to write a test script to print the Scheduling Information of processtest.apk in foreground and background.

Deadline

Demo & Presentation

Demo:

Demo slots will be posted in the WeChat group. Please sign your name in one of the available slots.

Presentation:

You are encouraged to present your design of the project optionally. The presentation will be in the afternoon of June 6, .
