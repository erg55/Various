My prexisting version of SPaDES (3.12.0) is a linux version that was easy to install but it turns out that it can't be adjusted. We need to compile the source code ourselves if we want to adjust k-mer sizes so I will make a new directory and install the most current version of SPAdes (3.13.1) there, via downloading with **wget** and unzipping the contents with **tar**.
 
 
```
 mkdir SPADESkmer
 cd SPADESkmer
 wget http://cab.spbu.ru/files/release3.13.1/SPAdes-3.13.1.tar.gz
 tar -xzf SPAdes-3.13.1.tar.gz
 cd SPAdes-3.13.1
```

Edit the cmake line of spades_compile file to look like this to allow maximum k-mer size to be adjusted up. If doing this on a cluster you may need to add another line here. 
```
cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="$PREFIX" -DSPADES_MAX_K=251 $* "$BASEDIR/src"
 ```
Next we need to actually compile the source code. You may need to install some programs like gcc. 
 
```
 ./spades_compile.sh
 ```
  You have to edit a line in options_storage.py after compiling.
  Find MAX_K parameter on line 45 and change the value to 256 
```
nano ~/SPADESkmer/SPAdes-3.13.1/src/spades_pipeline/options_storage.py
```
 
 
 ### CLUSTER complications
 You may get an error regarding gcc when running  ./spades_compile.sh
 
Module unloading gcc and loading more current gcc doesnt work because it is using the default location in the /usr/bin folder. So let's find where the active module is using **whereiss**

 ```
whereis gcc
 ```

It looks like the current version is in isg/shared/apps/. 
So add this one line in compiler_spades.py before the cmake command
 ```
export CC=/isg/shared/apps/gcc/6.4.0/bin/gcc
 ```


And that should work! Though make sure and module load the up to date version of gcc before running SPAdes otherwise it will fail. 




NOTE: You must also feed merged reads as single reads (e.g., -s as one combined files) and not provide single reads separately or SPAdes will tell you that the max read length is too short for assemblies over 150 bases. 




