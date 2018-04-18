# Garnet2.0, A Short tutorial
### Introduction
GEM5 simulator dedicated to computer architecture research that encompasses system and process level microarchitecture. It is a merge of the best aspects of  M5 and GEM5. The simulator is highly configurable and includes multiple CPU and ISA models. The project is a result of joined efforts of many companies and schools (AMD, ARM, HP, MIPS, Princeton, MIT, uTexas, wisc, and umich.) Moreover, it is an open source and mainly written in C++ and python.
### Focus and assumption
The main focus of this tutorial is how to run and modify Garnet as a stand-alone in GEM5. Garnet models the interconnection network in GEM5. It is cyclic accurate, implements the micro-architecture of on-chip  router, and uses GEM5 ruby memory system for topology and routing. I assume you have successfully installed GEM5. If not, please visit http://learning.gem5.org/book/index.html. Let us cut the long story short and jump into Garnet
### Compile and first run 
To run Garnet as a stand-alone, you first need to compile it with the following command 
```
scons build/NULL/gem5.debug PROTOCOL=Garnet_standalone    
```
GEM5 includes a template configuration file to run Garnet as a stand-alone. This file is located in
```
configs/example/garnet_synth_traffic.py
```
Use the below command to run GEM5 using the garnet_synth_traffic.py configuration file with default configuration parameters. 
```
./build/NULL/gem5.debug configs/example/garnet_synth_traffic.py 
```

### Configuration parameters
In general, all the configurations can be found in config/ folder. For our purpose, most of the configuration parameters related to Garnet can be found in the following files and folders 
```
configs/common/Options.py    --- General configration parameters (i.e. number CPUs, directories, memory size, ... etc.)  
configs/network/Network.py   --- Network configuration parameters (i.e. router & link latency, routing algorithm, topology... etc.) 
configs/topologies/          --- topologies are defined here. 
configs/example/garnet_synth_traffic.py  --- Our template file, include configuration parameters related to a single run (i.e. traffic pattern type, injection rate, number of simulation cycles, ... etc.)
```
You can change any default value of any configuration parameter directly in the related configuration file or you can change it from command line as follows
```
./build/NULL/gem5.debug configs/example/garnet_synth_traffic.py [--configuration_name=value]
```
Remember to not add any space between configuration name, '=', and value. Let us take an example. 
```
./build/NULL/gem5.debug configs/example/garnet_synth_traffic.py --num-cpus=4 --num-dirs=4 --network=garnet2.0 --topology=Mesh_XY --mesh-rows=2 --sim-cycles=10000 --synthetic=uniform_random --injectionrate=0.05 --vcs-per-vnet=4
```
The above command will run Garnet as a stand alone with the following configurations. 
* [--num-cpus=4] number of CPU = 4 
* [--num-dirs=4] number of cache directories = 4
* [--network=garnet2.0] configure the network as garnet network
* [--topology=Mesh_XY] use Mesh_XY.py topology in configs/topologies/ 
* [--mesh-rows=2] number of rows in the network layout
* [--sim-cycles=10000] run simulation for 10000 cycles
* [--synthetic=uniform_random] traffic pattern 
* [--injectionrate=0.05] injection rate
* [--vcs-per-vnet=4] number of VCs per vitrual network

### Garnet Source Files
Garnet2.0 is written in `C++` and uses python to pass the configuration parameters to the `C++` objects. All the files are available in  
```
src/mem/ruby/network/garnet2.0/
```
In this folder, the NoC and the router micro-architecture is implemented. 

### Scons script
New Source files will be compiled into GEM5 only if they are added to the [Scons](http://www.scons.org) script in the folder where the source file resides. [Scons](http://www.scons.org) is a modern software construct tool (similar to [Make](https://en.wikipedia.org/wiki/Make_(software))); it's scripts are written in python. In GEM5, any folder that includes a [Scons](http://www.scons.org) script file will be compiled into GEM5 according to the scripts content. Let us take the `Scons` script in  Garnet folder as an example. This script is located in
```
src/mem/ruby/network/garnet2.0/Sconscript
```
It includes the following content
```
Import('*')

if env['PROTOCOL'] == 'None':
    Return()

SimObject('GarnetLink.py')
SimObject('GarnetNetwork.py')

Source('GarnetLink.cc')
Source('GarnetNetwork.cc')
Source('InputUnit.cc')
Source('NetworkInterface.cc')
Source('NetworkLink.cc')
Source('OutVcState.cc')
Source('OutputUnit.cc')
Source('Router.cc')
Source('RoutingUnit.cc')
Source('SwitchAllocator.cc')
Source('CrossbarSwitch.cc')
Source('VirtualChannel.cc')
Source('flitBuffer.cc')
Source('flit.cc')
Source('Credit.cc')
```
The script is strightforward. To add source file, say `x.cc`, simple add `Source('x.cpp')` in the Scons script. 

### Debugging 
As you already know from low-level programming classes, debugging is not a simple task. For large applications, like GEM5, a bug may occur anywhere in any file. As a result, it is very tedius and, sometimes, very time-consuming. Personally, I mainly use `printf`, `assert`, and, [gdb](https://www.gnu.org/software/gdb/) (GNU Project Debugger) to debug a C++ application. Also, following code conventions and style of an application can occasionally help especially if you ask others to look into your code for help or recommendation. With all that, I still, from time to time, get tease by silly bugs. For now, let me give you quick hints on how I use  `printf`, `assert`, and `gdb`.
#### `printf`
`printf` can help you to print the content of a variable. From there, you may know if your problem is related to a wrong initialization or out of bound value. As a basic example, 
```
bool process_is_free[100]; 
...
int p = get_next_process_id(); 
printf("Process ID %d\n", p); 
process_is_free[p] = false; 
...
```
In the above code, if we get segmentation fault here. Then, probably, the value of `p` is wrong. To check the value we simply print its value and check its output. If `p` was not in the proper range, then we get a hint that the bug might be in the function `get_next_process_id()`. However, If you have lots of content from the program output, I would be hard to follow your debugging outputs. So the best, in my opinion, is to stop the program at a suspicious location using `assert` statement. 
#### `assert`
Assert function takes an expression.
```
void assert( int expression ); 
```
If the expression is false, assert will send to standard error the input expression, the file name, and line number of that assert; and then call abort function. I use it to validate variable contents. If case something is wrong, I would know directly that I have a problem either with my assumption or the variable content at that specific assert statement. For example, 
```
bool process_is_free[100]; 
...
int p = get_next_process_id(); 
assert(p >= 0 && p < 100); 
process_is_free[p] = false; 
...
```
#### `gdb`
The GNU Project Debugger is extremely helpful for adding breakpoints, stepping, and inspect crashes. Although I'm not an expert in `gnu`, I use it to inspect crashes (i.e. sigmentation fault). It can help you to know exactly which statement caused a segmentation fault. You can debug a program as follows
```
bash# gdb program.cc      
(gdb) run [program.cc parameters] 
```
For more details, please refer to this [tutorial](https://www.tutorialspoint.com/gnu_debugger/index.htm).

#### Further resources
* http://tusharkrishna.ece.gatech.edu/teaching/garnet_gt/
* http://learning.gem5.org/book/index.html
* http://learning.gem5.org/index.html
* http://www.gem5.org/Garnet2.0
* http://www.gem5.org/Interconnection_Network
* http://www.gem5.org/Garnet_Synthetic_Traffic
* http://synergy.ece.gatech.edu/wp-content/uploads/sites/332/2017/09/Garnet2.0-Tutorial_gem5-workshop_ARM-Rsh-Summit2017.pdf










