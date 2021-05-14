# Cmpe-283-Assignment4
CMPE 283: Cloud Virtualization

## Prompt
This assignment builds upon assignment 3. The purpose of this assignment is to illustrate the difference in performance when using nested paging versus shadow paging, and to illustrate the different exit frequencies and types. 

## Nested Paging
1. Run your assignment 3 code and boot a test VM using that code. 
2.Once the VM has booted, record total exit count information (total count for each type of exit handled by KVM). You should do this via a sequence of queries of CPUID leaf function 0x4FFFFFFE.

From the inner VM, run the following (EPT)

```
cpuid -l 0x4FFFFFFF

```
<br/>
![nested paging](./nested_paging_output.jpeg?raw=true)

## Shadow Paging
1. Shutdown your test (inner) VM.
2. Remove the ‘kvm-intel’ module from your running kernel: 
```
rmmod kvm-intel.ko
```

3. Reload the kvm-intel module with the parameter ept=0 (this will disable nested paging and force KVM to use shadow paging instead). The module you want is usually found in /lib/modules/XXX/kernel/arch/x86/kvm, where XXX is the version of the kernel you build for assignment 3 – don’t make a mistake and use the one that came with the stock Linux installation.
```
insmod  /lib/modules/XXX/kernel/arch/x86/kvm/kvm-intel.ko ept=0
```

4. Boot the same test VM again, and capture the same output 
5. Rerun exiting and capture the output. 
```
cpuid -l 0x4FFFFFFF
```
<br/>
![shadow paging](https://github.com/lindavy/Cmpe-283-Assignment4/blob/main/shadow_paging_output.jpeg)

## Conclusion
Here are the rough number of key exits that occurred between nested and shadow paging. 

|Exit Type| Nested      |  > or < | Shadow     |
| :---:  | :---:       |    :----:   |    :---:      |
|External Interrupt (1)| 40k | > | 30k  |
| Halt (12) | 3.6k | < | 10k |
| I/O Instruction (3) | 115k | < | 130k | 
| WRMSR (32) | 36k | < | 54k |
| CPUID (10) | 19.3k | > | 18.9k |
| EPT Violation (48) | 269k | > | 259k | 

The rest of the exit types not listed share similar exit values.

## Followup Questions
1. Dhruwaksh ran assignment 3 on his VM and captured the output for both nested and shadow paging. Afterwards, Dhruwaksh and Linda both reviewed lecture 7 material and discussed the validity of the exit values. We went highlighted the exit types that had the largest differences and went back to the SDM to review what they were. 

2. View Nested and Shadow Paging sections. 

3. What did you learn from the count of exits? Was the count what you expected? If not, why not? 

Based on our output, shadow paging has more exits than nested paging for exit types 12, 3 and 32. We expected the number of exits related to memory writing/reading should increase in shadow paging since #PF exiting is enabled. For instructions that attempt to access memory, more exits will happen if the entry doesn't exist in the TLB. As shown in the table and screenshots above, shadow paging has significantly more exits for interrupt values 12, 3, 32.  

4.What changed between the two runs (ept vs no-ept)?

In shadow paging, for every virtual to physical mapping that doesn't exist in the active PT or the TLB, the guest VM will have to exit and transfer control to the VMM. (#PF is enabled) The VMM will be responsible for checking if the guest is accessing an appropriate memory address, then put it into the currently active PT. More exits will occur if no-ept is enabled. 


