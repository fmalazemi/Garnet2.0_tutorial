# GEM5, A Short tutorial
GEM5 simulator dedicated for  computer architecture research that encompass system and process level microarchitecture. It is a merge of the best aspects of  M5 and GEM5. The simulator is highly confiugrable and includes multiple CPU and ISA (ARM, MIPS, x86, ... etc) models. The project is a result of joined efforts from AMD, ARM, HP, MIPS, Princeton, MIT,and the universities of Texas, Wisconsin, and Michigan  an open source and mainly written in C++ and python. More detials please visit www.Gem5.org. 
# Focus and assumption
The main focus of this tutorial is how to run and modify Garnet2.0 under GEM5. I assume you have seccussfully installed GEM5. If not, please visit http://learning.gem5.org/book/index.html. Let us cut the long story short and jump into Garnet2.0 
# Garnet2.0
Garnet2.0 models the interconnection network in GEM5.  It is cyclic accurate, implements the micro-architectural of on-chip network router, and uses GEM5 ruby memory system for topology and routing. 
