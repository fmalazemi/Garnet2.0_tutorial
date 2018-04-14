# Garnet2.0, A Short tutorial
### Introduction
GEM5 simulator dedicated for  computer architecture research that encompass system and process level microarchitecture. It is a merge of the best aspects of  M5 and GEM5. The simulator is highly confiugrable and includes multiple CPU and ISA (ARM, MIPS, x86, ... etc) models. The project is a result of joined efforts from AMD, ARM, HP, MIPS, Princeton, MIT,and the universities of Texas, Wisconsin, and Michigan  an open source and mainly written in C++ and python. More detials please visit www.Gem5.org. 
### Focus and assumption
The main focus of this tutorial is how to run and modify Garnet2.0 as a stand alone in GEM5. Garnet2.0 models the interconnection network in GEM5; it is cyclic accurate, implements the micro-architectural of on-chip network router, and uses GEM5 ruby memory system for topology and routing. I assume you have seccussfully installed GEM5. If not, please visit http://learning.gem5.org/book/index.html. Let us cut the long story short and jump into Garnet2.0 
### Important files
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

So, if you have a 


