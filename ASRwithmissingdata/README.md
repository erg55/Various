Documenting my efforts to export ASR with missing data from mesquite into R for modern plotting.

***Discrete characters***

Mesquite will only export discrete characters unfortunately. 

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
![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacterx.png?raw=true)

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

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacter9.png?raw=true)


Ok now to plot both trees. Split up the plotting area into two columns
```
par(mfrow=c(1,2), mai=c(0.0,0.0,0.0,0.0))
```

Plot the first tree rightwards with a legend ...adjusted using the plot parameters to find a good place for it.....we also offset the tips to look closer to what we want in the end. 

```
plotSimmap(lsimmap, colors=cols, fsize = 0.37, ftype = "bi", lwd =1.75, offset=5.5, mar=c(0.0,0.0,0.0,0.0))
add.simmap.legend(leg=c("unknown","absent","present"), colors=cols, shape="circle", x=par()$usr[1], y=-5*par()$usr[3], prompt=F, size=1)

```

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacter9next.png?raw=true)


Ok now add the second tree with all the character states. 

```
plotSimmap(lsimmap9, colors=cols9, fsize = 0.37, ftype = "bi", lwd =1.75, direction ="leftwards", offset=3.5, mar=c(0.0,0.0,0.0,0.0))
add.simmap.legend(leg=c("unknown","uniformly dark","with large contrasting patches","mottled","uniformly pale","dark with small pale marks","suffused","white bands associated with veins on dark background"), prompt=F, x=-0.75*par()$usr[1], y=-6.5*par()$usr[3], colors=cols9, shape="circle")

```

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testtreecharacterboth.png?raw=true)

Looks ok but maybe we can get it to be nicer. For example, we can try a [fancy way of aligning tip labels](http://blog.phytools.org/2017/05/plotting-non-ultrametric-tree-with.html) for the first plot and add a title to the legend. 

```
par(mfrow=c(1,2), mai=c(0.0,0.0,0.0,0.0))
plotSimmap(lsimmap, colors=cols, fsize = 0.000001, ftype = "bi", lwd =1.75, mar=c(0.0,0.0,0.0,0.0), asp =0.0035)
add.simmap.legend(leg=c("unknown","absent","present"), title="x", colors=cols, shape="circle", x=par()$usr[1], y=-5*par()$usr[3], prompt=F, size=1)
text(x=-1*par()$usr[1],  y=-6*par()$usr[3], labels="Fossula spongiosa on fore/mid leg (male)", cex= 1)
obj<-get("last_plot.phylo",envir=.PlotPhyloEnv)
text(rep(max(obj$xx[1:Ntip(lsimmap)]),Ntip(lsimmap)),obj$yy[1:Ntip(lsimmap)],
     labels=lsimmap$tip.label,font=3,cex=0.37,pos=4)
for(i in 1:Ntip(lsimmap)) lines(c(obj$xx[i],max(obj$xx[1:Ntip(lsimmap)])),
                           rep(obj$yy[i],2),lty="dotted")

```

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testalign.png?raw=true)

Now add the second plot the same way. 
```
plotSimmap(lsimmap9, colors=cols9, fsize = 0.00001, ftype = "bi", lwd =1.75, direction ="leftwards", offset=3.5, mar=c(0.0,0.0,0.0,0.0), asp =0.0035)
obj<-get("last_plot.phylo",envir=.PlotPhyloEnv)
for(i in 1:Ntip(lsimmap9)) lines(c(obj$xx[i],min(obj$xx[1:Ntip(lsimmap9)])),
                                rep(obj$yy[i],2),lty="dotted")
add.simmap.legend(leg=c("unknown","uniformly dark","with large contrasting patches","mottled","uniformly pale","dark with small pale marks","suffused","white bands associated with veins on dark background"), prompt=F, x=-13.5*par()$usr[1], y=-6.5*par()$usr[3], colors=cols9, shape="circle")
text(x=-16.5*par()$usr[1],  y=-7.5*par()$usr[3], labels="Coloration", cex= 1)

```

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/testalign2.png?raw=true)

Ok and do some final adjustment in a graphic program. 

![Screenshot](https://github.com/erg55/Various/blob/master/ASRwithmissingdata/mirrorphylo.png?raw=true)



***Continuous characters***

Mesquite offers no way to export a contiunous character ancestral state reconstruction. Many R programs are incompatabile with missing data. The only work around I've found for visualizing these character is with using the program [phylopars](https://github.com/ericgoolsby/Rphylopars/wiki/Example-2:-Missing-Data-Imputation-and-Ancestral-State-Reconstruction). 
```

```


```

```

