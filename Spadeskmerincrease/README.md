 My prexisting version of SPaDES (3.12.0) is here /home/CAM/egordon/spades/SPAdes-3.12.0-Linux/bin/spades.py which is a linux version that was installed ready to go but it turns out that it can't be adjusted. We need compile the source code ourselves if we want to adjust k-mer sizes so I will make a new directory and install the most current version of SPAdes there, via downloading with **wget** and unzipping the contents with **tar**.
 
 
```
 mkdir SPADESkmer
 cd SPADESkmer
 wget http://cab.spbu.ru/files/release3.13.1/SPAdes-3.13.1.tar.gz
 tar -xzf SPAdes-3.13.1.tar.gz
 cd SPAdes-3.13.1
```
 
 Next we compile the source code with this command to allow maximum k-mer size to be adjusted up. You may need to install some various programs like gcc. 
 
```
 ./spades_compile.sh -DSPADES_MAX_K 254
 ```
 nano ~/SPADESkmer/SPAdes-3.13.1/src/spades_pipeline/options_storage.py
```
 find MAX_K on line 45 and chnage to 256
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


And that should work! 



 -DSPADES_MAX_K option and lift the checks in spades.py and around
  SPAdes using -DSPADES_MAX_K option, and then changed the 'MAX_K' value in the 'options_storage.py' file accordingly.
  MAX_K constant in spades_pipeline/options_storage.py



