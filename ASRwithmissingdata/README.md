Documenting my efforts to export ASR with missing data from mesquite into R for modern plotting. Mesquite will only export discrete characters unfortunately. 

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
plotSimmap(lsimmap, colors=cols, fsize = 0.37, ftype = "bi", lwd =1.75, direction ="upwards", offset=0.5, mar=c(0.0,0.0,0.0,0.0))
```
Then add a legend
```
add.simmap.legend(leg=c("unknown","absent","present"), colors=cols, shape="circle", x=60, y=-6.5*par()$usr[3], prompt=F, size=1, align.tip.label=TRUE)
```
!Screenshot(https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacterx.png?raw=true)

Looks ok...lets see you wanted to do a complicated cophylogeny thing to plot two characters at once. 

Import this data and ladderize again.
```
simmap9 <- read.simmap("char9simmapmod.txt",format="nexus", version=1.5)
lsimmap9 <- ladderize.simmap(simmap9, right=T)
```
This data has more character states and it is useful to make it colorblind friendly so one option is to use the package [incmisc](https://www.r-bloggers.com/tol-color-schemes/)

```
install.packages("inlmisc")
library(inlmisc)
```

It has 7 states plus missing data so you can see some colorblind friendly values with this function...and many others.  
```
plot(inlmisc::GetColors(7, scheme = "bright"))
```
I used these colors to define the states fo rthe next plot. 

```
cols9 <- c("9" = "#BBBBBB", "0" = "black", "1" = "#EE6677", "2" = "#CCBB44", "3" = "#AA3377", "4" = "#228833", "5" = "#66CCEE","6" = "#4477AA")
```

Now finally we can plot it .

!Screenshot(https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacter9.png?raw=true)


Ok now to plot both trees. Split up the plotting area into two columns
```
par(mfrow=c(1,2), mai=c(0.0,0.0,0.0,0.0))
```

Plot the first tree rightwards with a legend ...adjusted using the plot parameters

```
plotSimmap(lsimmap, colors=cols, fsize = 0.37, ftype = "bi", lwd =1.75, offset=5.5, mar=c(0.0,0.0,0.0,0.0))
add.simmap.legend(leg=c("unknown","absent","present"), colors=cols, shape="circle", x=par()$usr[1], y=-5*par()$usr[3], prompt=F, size=1)

```

!Screenshot(https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacter9next.png?raw=true)





```

```

