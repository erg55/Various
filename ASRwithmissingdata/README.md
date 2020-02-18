Documenting my efforts to export ASR with missing data from mesquite into R for modern plotting. 

With a tree with a traced character open, select trace and "Export Ancestral States Trace" > SIMMAP 1.5 Format


![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/Screen%20Shot%202020-02-18%20at%2011.53.36%20AM.png?raw=true)


Now, if you have any missing data unfortunately Mesquite does not assign any character state for those tips nor any node containing only missing data. We can fix this problem by searching for any ":" in the Nexus tree file not followed by a [&map={#}]. I used TextWrangler to assign a new character state (2 since this was a binary character) to missing data nodes. 

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/Screen%20Shot%202020-02-18%20at%2012.11.18%20PM.png?raw=true)

Lastly, I removed the stem node state as advised by Liam Revell [here](https://grokbase.com/t/r/r-sig-phylo/154a8besdj/failure-to-read-simmap-1-5-formatted-tree-into-r) 


![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/removestem.png?raw=true)


I was finally able to import the parsimony-mapped adulterated simmap file into R using 
```
simmap <- read.simmap("char5simmapmod.txt",format="nexus", version=1.5)
```

I made the new character state I assigned to the missing data nodes (2 in this case) gray. 
```
cols <- c("2" = "gray", "0" = "black", "1" = "red")
```
and plotted the ladderized tree

```
lsimmap <- ladderize.simmap(simmap, right=F)
plotSimmap(lsimmap, colors=cols, fsize = 0.37, ftype = "bi", lwd =1.75, direction ="upwards", offset=0.5, asp=150, mar=c(0.0,0.0,0.0,0.0))
```




