## ECE 565 Programming Assignment 1 Fall 2020
### Professor Tim Rogers

1. Introduction

 This assignment will serve as an introduction to the gem5 simulator. It will walk you through the necessary steps to set up the simulator on your account, and provide you with some introductory tasks that will help you familiarize yourself with the gem5 structure and source tree. While the tasks aren’t difficult in and of themselves, do not underestimate the time it will take to get familiar with the gem5 source tree before you’ll be able to complete the tasks.
 The gem5 simulator is written primarily in C++. However, configurations are done in Python, so you’ll need to be familiar with Python as well. It’s easy to read, but be aware that spacing in Python matters if you find yourself editing a Python file for the first time – it’s worth finding a quick tutorial on Python to learn the basics. A utility called SWIG is used to combine the configurations in Python and the actual simulator written in C++.


2. Setup

  The assignment has been made available through github classroom. To sign up - please link your github account with your Purdue email by following the directions here:

  https://classroom.github.com/a/PrGvMvVm

  The programming assignment will be completed on a cluster of "qstruct" servers. There are 19 of these machines. You can access them by ssh’ing into qstruct.ecn.purdue.edu from any terminal. You will use your Purdue Career Account username and password. Below is the sample command for logging in:

  ```console
  ssh <career-user-id>@qstruct.ecn.purdue.edu
  ```

  Once logged into qstruct, you will need to clone your version of the assignment from github classroom.
  To make a copy of your code locally use:

  ```console
  git clone https://github.com/purdue-ece565-2020/assignment-1-v2-<your-github-user-name>.git
  ```

  Git is the world's most popular revision control system, so if you are not familiar with it, now is a great time :).
  Given it's popularity, it is very well documented simply googling will give you a number of high-quality tutorials to get you familiar with it. This assignemnet doc will provide you with the basic commands.

  You now have your own fresh copy of gem5! Going into your gem5 directory, you’ll see a variety of folders, including the src directory, where most of your changes will be made. You may find yourself working in the configs directory from time to time as well. It is worth spending some time exploring these directories to get a feel for where different things are.

  TODO - include image

3. Building gem5

  gem5 is a highly configurable architectural simulator that supports a number of ISAs (x86, ARM, MIPS, SPARC, POWER, RISCV), CPU Models (InOrder, O3, AtomicSimple, TimingSimple), and two Memory Models (Classic, Ruby). To understand how to build gem5, you must understand what you are building first. Example gem5 build files are located in gem5/build_opts. For example, the ’X86’ file is as follows:

  TARGET_ISA = ’x86’
  CPU_MODELS = ’AtomicSimpleCPU,TimingSimpleCPU,O3CPU’
  PROTOCOL = ’MI_example’


  TODO - include image
