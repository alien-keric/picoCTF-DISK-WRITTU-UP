
```
Author: syreal

#### Description

All we know is the file with the flag is named `down-at-the-bottom.txt`... Disk image: [dds2-alpine.flag.img.gz](https://mercury.picoctf.net/static/6cd5ca45d75250451931cea538fb38c0/dds2-alpine.flag.img.gz)

```

# STEP 1
```
using 'mmls' to determine the disk partition for for this image 

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ mmls dds2-alpine.flag.img 
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000262143   0000260096   Linux (0x83)

```

# STEP 2
```
we determinng the file system used using fsstat command.

┌──(snow㉿alienx)-[/tmp/DISK]                                                                                                 
└─$ fsstat -o 2048  dds2-alpine.flag.img | more                                                                               
FILE SYSTEM INFORMATION                                                                                                       
--------------------------------------------
File System Type: Ext3
Volume Name: 
Volume ID: dc53a3bb0ae739a5164c89db56bbb12f

Last Written at: 2021-02-16 21:21:20 (EAT)
Last Checked at: 2021-02-16 21:21:19 (EAT)

Last Mounted at: 2021-02-16 21:21:19 (EAT)
Unmounted properly
Last mounted on: /os/mnt

Source OS: Linux
Dynamic Structure
Compat Features: Journal, Ext Attributes, Resize Inode, Dir Index
InCompat Features: Filetype, 
Read Only Compat Features: Sparse Super, Large File, 
----- <SNIP>-----------------------------

NB:file system used for this image (Ext3)
```


# STEP 3
```
we need to display  the contents of the files  and see if we can get the flag here
┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -o 2048 dds2-alpine.flag.img                    
d/d 26417:      home
d/d 11: lost+found
r/r 12: .dockerenv
d/d 20321:      bin
d/d 4065:       boot
d/d 6097:       dev
d/d 2033:       etc
d/d 8129:       lib
d/d 14225:      media
d/d 16257:      mnt
d/d 18289:      opt
d/d 16258:      proc
d/d 18290:      root
d/d 16259:      run
d/d 18292:      sbin
d/d 12222:      srv
d/d 16260:      sys
d/d 18369:      tmp
d/d 12223:      usr
d/d 14229:      var
V/V 32513:      $OrphanFiles



we can try to check the flag from one directory to another but lets start with the root directory trying to find the flag(offset=18290)
```

# STEP 4
```
now lets see what is in the root directory 

command: 

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -o 2048 dds2-alpine.flag.img 18290
r/r 18291:      down-at-the-bottom.txt


now we need to go for the flag since because we already know its offset(18291) and the command here is 'icat'



┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -o 2048 dds2-alpine.flag.img 18290
r/r 18291:      down-at-the-bottom.txt

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ icat -o 2048 dds2-alpine.flag.img 18290
rG
  .
   ..sGdown-at-the-bottom.txt                                                                                                                              
┌──(snow㉿alienx)-[/tmp/DISK]
└─$ icat -o 2048 dds2-alpine.flag.img 18291
   _     _     _     _     _     _     _     _     _     _     _     _     _  
  / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \ 
 ( p ) ( i ) ( c ) ( o ) ( C ) ( T ) ( F ) ( { ) ( f ) ( 0 ) ( r ) ( 3 ) ( n )
  \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/ 
   _     _     _     _     _     _     _     _     _     _     _     _     _  
  / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \ 
 ( s ) ( 1 ) ( c ) ( 4 ) ( t ) ( 0 ) ( r ) ( _ ) ( n ) ( 0 ) ( v ) ( 1 ) ( c )
  \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/ 
   _     _     _     _     _     _     _     _     _     _     _  
  / \   / \   / \   / \   / \   / \   / \   / \   / \   / \   / \ 
 ( 3 ) ( _ ) ( f ) ( f ) ( 2 ) ( 7 ) ( f ) ( 1 ) ( 3 ) ( 9 ) ( } )
  \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/   \_/ 

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ picoCTF{f0r3ns1c4t0r_n0v1c3_ff27f139} 

and there we got the flag 

another way to do this you can use the commmand below:

icat -i raw -f ext3 -o 2048 -r dds2-alpine.flag.img 18291
```