 My prexisting version of SPaDES (3.12.0) is here /home/CAM/egordon/spades/SPAdes-3.12.0-Linux/bin/spades.py which is a linux version that was installed ready to go but it turns out that it can't be adjusted. We need compile the source code ourselves if we want to adjust k-mer sizes so I will make a new directory and install the most current version of SPAdes there, via downloading with **wget** and unzipping the contents with **tar**.
 
 
```
 mkdir SPADESkmer
 cd SPADESkmer
 wget http://cab.spbu.ru/files/release3.13.1/SPAdes-3.13.1.tar.gz
 tar -xzf SPAdes-3.13.1.tar.gz
 cd SPAdes-3.13.1
```
 
 Next we compile the source code with this command to allow maximum k-mer size to be adjusted up. 
 
```
 ./spades_compile.sh -DSPADES_MAX_K 254
 ```
 nano ~/SPADESkmer/SPAdes-3.13.1/src/spades_pipeline/options_storage.py
```
 find MAX_K on line 45 and chnage to 256
 ```
 
 get an error when running  ./spades_compile.sh
 
 so unload gcc and load gcc more modern
 
 
 
 -DSPADES_MAX_K option and lift the checks in spades.py and around
  SPAdes using -DSPADES_MAX_K option, and then changed the 'MAX_K' value in the 'options_storage.py' file accordingly.
  MAX_K constant in spades_pipeline/options_storage.py


whereis gcc
Default is 
/lib/gcc/


/bin/gcc
/bin/g++
