# Garnet2.0, A Short tutorial
### Introduction
GEM5 simulator dedicated for  computer architecture research that encompass system and process level microarchitecture. It is a merge of the best aspects of  M5 and GEM5. The simulator is highly confiugrable and includes multiple CPU and ISA models. The project is a result of joined efforts of many companies and schools (AMD, ARM, HP, MIPS, Princeton, MIT, uTexas, wisc, and umich.) Moreover, it is an open source and mainly written in C++ and python.
### Focus and assumption
The main focus of this tutorial is how to run and modify Garnet as a stand alone in GEM5. Garnet models the interconnection network in GEM5. It is cyclic accurate, implements the micro-architecture of on-chip network router, and uses GEM5 ruby memory system for topology and routing. I assume you have seccussfully installed GEM5. If not, please visit http://learning.gem5.org/book/index.html. Let us cut the long story short and jump into Garnet
### Compile and first run 
To run Garnet as a stand alone, you first need to compile it with the following command 
```
scons build/NULL/gem5.debug PROTOCOL=Garnet_standalone    
```
GEM5 includes a template configruation file to run Garnet as a stand alone. This file is located in
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
All files related to Garnet2.0 are located in 
```
src/mem/ruby/network/garnet2.0/
```
The following are the main files:
* Router.cc, implements router microarchitecture. 
* NetworkLink.cc, implement network links to transpose flits.
* CreditLink.cc, implement credit links to transpose credits. 
* NetworkInterface.cc, implements communication links between a router and, either, a CPU or Directory. 
* GarnetNetwork.cc, build and connect all NoC components. 
* Sconscript, includes files need to by compiled and used by GEM5.  

The configuration file is located in 
```
configs/example/garnet_synth_traffic.py
```




