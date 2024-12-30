# Hyper-V-GPU-Partition-Guide

This is a quick guide to making a Hyper-V VM for gaming that uses GPU Paravirtualization to "Partition" a GPU.
This is designed for host systems that have an Nvidia GPU.
It is possible to do this with AMD GPUs but the driver files listed on steps 32-33 will be diffrent and I do not know what they would be.
This guide is intended to allow the VM 50% of the GPU performance, you can alter this percentage and even have more than one VM running assuming you have sufficient resources.

Prerequisites;
	Windows 10+ Pro edition
	Minimum of 16GB RAM, Recommended 32+GB
	Minimum 6 Core CPU, 8+ Core CPU recommended.
	A GPU with enough power to play the desired game below 50% GPU load.
	Enough Disk space to house the VM and Game. Recommended ~256GB for larger games like Vermintide 2.
	Windows ISO of the same version as host (no Rufus W11 MS user bypass)
