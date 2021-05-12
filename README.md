# Cmpe-283-Assignment4
CMPE 283: Cloud Virtualization

## Prompt
This assignment builds upon assignment 3. The purpose of this assignment is to illustrate the difference in performance when using nested paging versus shadow paging, and to illustrate the different exit frequencies and types. 

## Procedure



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
1.For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented / researched. (You may skip this question if you are doing the lab by yourself). 

3.Include a sample of your print of exit count output from dmesg from “with ept” and “without ept”. 

3.What did you learn from the count of exits? Was the count what you expected? If not, why not? 
4.What changed between the two runs (ept vs no-ept)?
