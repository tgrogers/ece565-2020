## ECE 565 Programming Assignment 1 Fall 2020
### Professor Tim Rogers

1. **Introduction**
    
    This assignment will serve as an introduction to the gem5 simulator. It will walk you through the necessary steps to set up the simulator on your account, and provide you with some introductory tasks that will help you familiarize yourself with the gem5 structure and source tree. While the tasks aren’t difficult in and of themselves, do not underestimate the time it will take to get familiar with the gem5 source tree before you’ll be able to complete the tasks.
 The gem5 simulator is written primarily in C++. However, configurations are done in Python, so you’ll need to be familiar with Python as well. It’s easy to read, but be aware that spacing in Python matters if you find yourself editing a Python file for the first time – it’s worth finding a quick tutorial on Python to learn the basics. A utility called SWIG is used to combine the configurations in Python and the actual simulator written in C++.
    
1. **Setup**
    
    The assignment has been made available through github classroom. To sign up - please link your github account with your Purdue email by following the directions here:
    
    https://classroom.github.com/a/PrGvMvVm
    
    The programming assignment will be completed on a cluster of "qstruct" servers. There are 19 of these machines. You can access them by ssh’ing into qstruct.ecn.purdue.edu from any terminal. You will use your Purdue Career Account username and password. Below is the sample command for logging in:
    ```console
    ssh <your-career-user-id>@qstruct.ecn.purdue.edu
    ```
    
    Once logged into qstruct, you will need to install a missing python package and clone your version of the assignment from github classroom. To make a copy of your code locally use:
    
    ```console
    pip3 install --user six
    git clone https://github.com/purdue-ece565-2020/assignment-1-v2-<your-github-user-name>.git
    ```
    
    Git is the world's most popular revision control system, so if you are not familiar with it, now is a great time :).
    Given it's popularity, it is very well documented simply googling will give you a number of high-quality tutorials to get you familiar with it. This assignemnet doc will provide you with the basic commands.
    You now have your own fresh copy of gem5! Going into your gem5 directory, you’ll see a variety of folders, including the src directory, where most of your changes will be made. You may find yourself working in the configs directory from time to time as well. It is worth spending some time exploring these directories to get a feel for where different things are.
    
    <img src="screen1.png" width="50%" />
    
1. **Building gem5**
    
    gem5 is a highly configurable architectural simulator that supports a number of ISAs (x86, ARM, MIPS, SPARC, POWER, RISCV), CPU Models (InOrder, O3, AtomicSimple, TimingSimple), and two Memory Models (Classic, Ruby). To understand how to build gem5, you must understand what you are building first. Example gem5 build files are located in gem5/build_opts. For example, the ’X86’ file is as follows:
    
    ```console
    TARGET_ISA = ’x86’
    CPU_MODELS = ’AtomicSimpleCPU,TimingSimpleCPU,O3CPU’
    PROTOCOL = ’MI_example’
    ```
    
    <img src="screen2.png" width="50%" />
    
    We will be using the latest version of gem5, which has fairly up-to-date documentation outside of this assignment.
    For additional pointers on gem5, please see the book on learning gem5:
    
    https://www.gem5.org/documentation/learning_gem5/introduction/
    
    This file indicates that the target ISA is X86, and all of the CPU Models should be compiled in. The last line PROTOCOL is a specific type of coherence protocol for the Ruby Memory Model, which we will ignore for now.
For this assignment, we will use the x86 build configuration. Now, the command to build this configuration is:
    
    ```console
    scons-3 -j 4 ./build/X86/gem5.opt
    ```
    
    You may be missing the gem5 style hook. If so, just hit enter and let the script install the style hook for you. This build will take quite a while the first time around, so using multiple processes is valuable. Even with 4 processes, it can take over 10 minutes to build. Figure 5 shows the completion of a successful build. When you build the simulator with scons, all of your source code is copied into the gem5/build directory for that particular build. This means two things. First, you can always simply remove the build directory (or a specific build’s directory inside gem5/build) to start from scratch (think of it like a "make clean"). Second, never make any manual changes within the build directory. You should make your changes elsewhere (i.e. gem5/src), and re-build the simulator. Re-building gem5 after the first build typically only takes a minute or two, depending on your changes.

1. **Running gem5: Hello World**
    
    The gem5.opt file you built with scons is your binary – this is what you will use to run the simulator. It takes in options and a simulation script (this is where Python comes in). To get started, let’s run a Hello World program on the simulator. Looking in your gem5/configs directory, you’ll see directories including example and spec2k6. You’ll be using spec2k6 configurations to run benchmarks later, but for now take a look in the example directory. The se.py script will be what we need to run Hello World. Take a look at it. You won’t understand all of it just now, but you’ll see how it’s setting up different gem5 configurations.
    
    Now that we have a script to use with gem5, we need an actual Hello World binary to run on the simulator. gem5 comes with Hello World binaries already compiled for each ISA it supports. You can find them in gem5/tests/test-progs/hello/bin. Since we’ve built an x86 model, we’ll want the gem5/tests/test-progs/hello/bin/x86/linux/hello binary. 
    
    To actually run Hello World, go back to your gem5/ directory. The gem5 binary, the configuration script, and the hello binary are all used with the following command:
    
    ```console
    ./build/X86/gem5.opt configs/example/se.py -c tests/test-progs/hello/bin/x86/linux/hello
    ```
   
    Notice that now there is a gem5/m5out directory. Inside, you’ll see a couple of configuration and stats files – they’re specific to your Hello World run. These will be very useful for tracking data in the future.
    
1. **Running gem5: Benchmarks**
    
    For the programming assignment, you’ll need to run benchmarks from the SPEC2006 benchmark suite. These are available at /home/min/a/ece565/benchspec/, and a Python configuration script to run the benchmarks is in your copy of gem5.
The benchmarks you’ll be running in this assignment are sjeng, libquantum, and bzip2. Using the gem5/configs/spec2k6/run.py script, you can run the following command:

    ```console
    ./build/X86/gem5.opt -d my_outputs configs/spec2k6/run.py -b sjeng --maxinsts=250000
    ```
    
    As before with Hello World, we simply use the gem5.opt binary we built to run the simulator. However, here we added a -d flag to the gem5.opt run. This allows us to specify an output directory, rather than use the default m5out directory we saw during our Hello World run. You can see the new "my_outputs" directory created for the output of this run. Note that the -d flag came before the Python script file. This is because the -d flag is an option for the gem5.opt binary, not an option for the script.
    
    Now looking at the script used to run the SPEC2006 benchmarks, we see it’s located in the configs/spec2k6 directory we saw earlier. Here, we use the -b flag to specify which benchmark we want to run – in this case, sjeng. The --maxinsts=X flag is used to specify how many instructions to run the benchmarks for. These benchmarks are massive and take hours upon hours to complete in full, so it will often be useful to run them only for a specific number of instructions. Since these options all apply to the script, they come after the script in the command, not before it.
    
    Try running the other benchmarks, bzip2 and libquantum, and have them all output to separate directories. More options for the gem5/configs/spec2k6/run.py script can be seen by using the -h flag, or by going into the file and looking around.

1. Assignment
    
    gem5 is an immensely complex piece of software with over 100k lines of code. However, it is not necessary to understand all of gem5 prior to being able to effectively use it. For this assignment you should focus on the InOrder CPU Model located in /src/cpu/inorder. The InOrder CPU currently models a 5-stage pipeline (F|D|EX|M|WB).  (Hint: Use the -h flag with your Python script to find out how to specify the CPU model you use.)
    One caveat of this CPU model is that each pipeline stage/component inherits from an abstracted resource object. As with every resource, there is the contingency of encountering a structural hazard. The occurrence of a structural hazard is dependent on the width of the corresponding stage. Thus in order to model one outstanding instruction per stage, you have to adjust the width of each stage to be 1.
    For this programming assignment, you will be required to implement the following changes in gem5:

    * Disable bypassing
    * Degrade branch prediction
    * Split the Execution stage into two separate pipeline stages
    
    Each of these are detailed further below. For each change, you will need to run the sjeng, bzip2, and libquantum benchmarks for 100 million instructions. These results will then be compared to the baseline gem5 performance for the InOrder CPU model. Make sure to take advantage of different output directories to avoid overwriting output data from different runs.
    
    1. **Disable Bypassing**
    
        The InOrder CPU already accounts for the timing model of register bypassing between relevant pipeline stages. Checking whether or not a register value can be bypassed from a later pipeline stage depends on two conditions:
    
        * Whether or not the corresponding value is valid
        * Can the value be forwarded
        
        What needs to be done for this part of the assignment is to implement an option to disable register bypassing in all cases, such that the CPU is forced to stall on every dependency.
        
     1. **Degrade Branch Prediction**
        
        The InOrder CPU already implements a few branch predictor modules, including a tournament predictor and a simpler Branch Target Buffer (BTB). The pipeline timing enables you to figure out at the EX stage whether or not the branch prediction was correct. What you need to do is implement an option that will allow you to not only enable/disable the branch predictor, but degrade its accuracy to different levels as well.
        
        Note that the pipeline already handles the squashing of instructions fetched from the wrong path. On a misprediction, the pipeline will initiate calls to update the corresponding branch predictor’s entry with the correct target address.
        
        It would be functionally correct to squash the pending instructions if the predicted target is correct, but it would not be functionally correct to consider the branch correctly predicted when the predicted target does not match the actual branch target, and still consider it to be correct. For this part of the assignment, you need to implement a feature that degrades the branch predictor’s accuracy by treating some correctly predicted branches as incorrect. You will need to generate a random number between 0 and 1, and for a specified accuracy of X%, if the generated number is greater than X%, consider it a misprediction. Note that this "accuracy" is not the branch predictor’s accuracy, but the percentage of it’s original accuracy. That is, 100% would be equal to the default branch predictor’s accuracy, 50% would be half of the original predictor’s accuracy, and 0% would be an always incorrect predictor – it always predicts the wrong thing.
        
        For this, you will need to run the benchmark simulations for those three "accuracies:"
        
        * 100%
        * 50%
        * 0%
        
    1. **Split Execution Stage**
    
        For this part, we want to be able to split up the Execution Unit into two stages. Modern pipelines employ deeper pipelines in order to increase the clock frequency. Instead of having a more complex stage that requires additional cycles, the corresponding stage is split into smaller stages, where each requires fewer cycles. However, as you know there is a trade-off for every design decision. What you need to do is split up the EX stage into EX1 + EX2 accordingly. In doing so, you need to figure out how to indicate that dependencies exist between the newly formed stages such that the pipeline accounts for stalls correctly.

1. **Submission instructions**
    
    You will submit your code through the github classroom interface. Some basic command you will need for git are:
    
    ```console
    git status # Tells you what files you have modified
    git add <file name> # Once you have modified a file, adding it will "stage" it for commiting.
    git commit # commits all the files you currently have staged
    git push # sends all the commits you have made to the remote reposity, where they are backed up and the instructor can see them.
    ```
    
    Note that gem5 has some committing requirements. You have to structure your commit message like:
    
    ```console
    git commit -m "<component-touched>: short description"
    # i.e.
    git commit -m "python: made the scripts work with python3"
    ```
    
    This restriction is mostly for development purposes, if you want to tag everything with *cpu:* or *python*, thats fine.
    
    As a general rule (for your own sake) I recommend committing often, so that you can undo things you messed up and you have a nice record for yourself of all the things you have done. When grading the assignment, I will only look at the final version of the code, regardless of the commit history.
    
    Submit a report (maximum two pages) with three graphs. Each graph should summarize the results of one of the experiments. (Suggested format: Plot bar-graphs with benchmarks on the X-axis and IPC on the Y-axis. For each benchmark, show two or more bars; one is the baseline and the rest correspond to your changes.) The report is NOT meant to be an exercise in writing. I do not expect any text beyond a 2-3 sentence summary of the key observations for each graph. However, this is a minimum and not a maximum. If you have text you want me to see (e.g., assumptions, simplifications, data-gathering difficulties etc.), feel free to write additional text in the report. Also if the branch of code you want me to look at is anything other than *master*, please indicate this in the report.
