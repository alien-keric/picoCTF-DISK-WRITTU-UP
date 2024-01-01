```
This challenge was all about disk images(linux based), which gonna introduce some few commands that can be used to check the size of partitions and so on
```

# STEP 1
```
Its is good to extract the disk image in the /tmp directory

after that we need to  determine the layout of the partition table with `mmls` command

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ mmls disk.flag.img
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
003:  000:001   0000206848   0000360447   0000153600   Linux Swap / Solaris x86 (0x82)
004:  000:002   0000360448   0000614399   0000253952   Linux (0x83)

NB: main concetrate with the Start offset size i.e
0000002048
0000206848
0000360448
```


# STEP 2
```
After we have got the partition information now we need to determine what kind of file system the image is using with a command 'fsstat'

command: `fsstat -o 2048 disk.flag.img`

┌──(snow㉿alienx)-[/tmp/DISK]                                                                                                 
└─$ fsstat -o 2048 disk.flag.img                                                                                              
FILE SYSTEM INFORMATION                                                                                                       
--------------------------------------------                                                                                  
File System Type: Ext4                                                                                                        
Volume Name:                                                                                                                  
Volume ID: 8e023955b4e7dab7e04b7643076ccf0f                                                                                   

Last Written at: 2021-09-29 21:10:02 (EAT)                                                                                    
Last Checked at: 2021-09-29 18:57:16 (EAT)                                                                                    

Last Mounted at: 2021-09-29 21:06:00 (EAT)                                                                                    
Unmounted properly                                                                                                            
Last mounted on: /mnt/boot                                                                                                    

Source OS: Linux                                                                                                              
Dynamic Structure                                                                                                             
Compat Features: Journal, Ext Attributes, Resize Inode, Dir Index                                                             
InCompat Features: Filetype, Extents, Flexible Block Groups,                                                                  
Read Only Compat Features: Sparse Super, Large File, Huge File, Extra Inode Size

NB: we can see that this linux image is using ext4 file system
```

`
# STEP 3
```
After we have know what file system the image was using we can use 'fsl' command to list the files and directories in each partition, the command will be followed by some arguments and options such as `-i` is the image type, `-f` is the file system type, and `-r` is to recursively display directories.

lets start with first partition(0000002048 or 2048)

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -i raw -f ext4 -o 2048 -r disk.flag.img                
d/d 11: lost+found
r/r 12: ldlinux.sys
r/r 13: ldlinux.c32
r/r 15: config-virt
r/r 16: vmlinuz-virt
r/r 17: initramfs-virt
l/l 18: boot
r/r 20: libutil.c32
r/r 19: extlinux.conf
r/r 21: libcom32.c32
r/r 22: mboot.c32
r/r 23: menu.c32
r/r 14: System.map-virt
r/r 24: vesamenu.c32
V/V 25585:      $OrphanFiles

And we get nothing of interest time to move on the other partition (the third partition is of much of interest here)


┌──(snow㉿alienx)-[/tmp/DISK]                                                                                                 
└─$ fls -i raw -f ext4 -o 0000360448 -r disk.flag.img  | more                                                                 
d/d 451:        home                                                                                                          
d/d 11: lost+found
d/d 12: boot
d/d 1985:       etc
+ r/r 23:       group
+ r/r 24:       group-
+ r/r 25:       shadow
+ r/r 26:       shadow-
+ r/r 27:       passwd
+ r/r 28:       passwd-
+ r/r 29:       hosts
+ d/d 30:       zoneinfo
++ r/r 31:      UTC
+ r/r * 32(realloc):    resolv.conf
+ d/d 33:       keymap
++ r/r 34:      us.bmap.gz
+ r/r 35:       inittab
+ d/d 36:       conf.d
++ r/r * 493(realloc):  .apk.e0f21bd25c6c70139df2cde7bf1a36d489c455ae1ded76d7
++ r/r * 494(realloc):  .apk.0864ab1550d7450acef161ac8801ce25e921c2a9d91a6862
++ r/r * 495(realloc):  .apk.4a7da402791126ef582f6f6615a3ab47cac81b903da9fafc
++ r/r * 496(realloc):  .apk.c9958cbaf43ea23cd909f245cdcbdbcf30ee548c1700f691
++ r/r * 497(realloc):  .apk.30eead8aa0227aae5fb84d0facfa2631616ea5aa2533f106
++ r/r * 498(realloc):  .apk.d6591ddc80d9477092aefa23a5203e231dd24cfd514397f6
++ r/r * 499(realloc):  .apk.a63c0b8e2d2265b663b88b349840e7a0cc957f053332f6ea
++ r/r * 500(realloc):  .apk.921bdd6fd04913e9b5ea4fc784a3b29dcd3b4c9dc7368ecd
++ r/r * 501(realloc):  .apk.baeca32de18ed66be8ab7d9debb632351182b260206c29f6
++ r/r * 502(realloc):  .apk.57b5ae549369eeeeada859ae959a80bba32f7f1c3b50a3ce
++ r/r * 503(realloc):  .apk.d1e36cff4304bf69c0323b664a1a886920a08c637e262e89
++ r/r * 504(realloc):  .apk.8808ffa01785d84aa9b99bfab19fcb9a196d979625db3780
++ r/r * 505(realloc):  .apk.7a252540ebef811a6a4b3bb66912fd6d42f150a44acd7d13
++ r/r * 506(realloc):  .apk.923c6ff52cf0985688a735c527739a0380c979eb728c66dd
++ r/r 37:      loadkmap

NB: since it contains more information we can attach the command with grep and try to grep a flag here 

i.e ┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -i raw -f ext4 -o 0000360448 -r disk.flag.img  | grep flag
++ r/r * 2082(realloc): flag.txt
++ r/r 2371:    flag.uni.txt

```

# STEP 4
```
After we have know a location were we can get our flags we can now try to cat the content with 'icat' command
i.e

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ fls -i raw -f ext4 -o 0000360448 -r disk.flag.img  | grep flag
++ r/r * 2082(realloc): flag.txt
++ r/r 2371:    flag.uni.txt

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ icat -i raw -f ext4 -o 360448 -r disk.flag.img 2082 
3.449677            13.056403

┌──(snow㉿alienx)-[/tmp/DISK]
└─$ icat -i raw -f ext4 -o 360448 -r disk.flag.img 2371 
picoCTF{by73_5urf3r_adac6cb4}


NB: the realloc(flag.txt) contans nothing 
```