# WRITE-UPS

## 1. ctrl+vmem  

1. Узнаем с какой системы сняли дамп.
```sh
pig@door:~$ volatility -f Vasya.vmem imageinfo

Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/pig/Vasya.vmem)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80002e4b0a0L
          Number of Processors : 4
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002e4cd00L
                KPCR for CPU 1 : 0xfffff880009ef000L
                KPCR for CPU 2 : 0xfffff88002f69000L
                KPCR for CPU 3 : 0xfffff88002fdf000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2023-06-14 17:09:24 UTC+0000
     Image local date and time : 2023-06-14 23:09:24 +0600
```
2. Теперь смотрим буфер обмена. Получаем флаг.
```sh
pig@door:~$ volatility -f Vasya.vmem --profile=Win7SP1x64 clipboard

Session    WindowStation Format                         Handle Object             Data
---------- ------------- ------------------ ------------------ ------------------ -----------------------------------
         1 WinSta0       CF_UNICODETEXT                0xf01a1 0xfffff900c1a6e360 surctf_some_password_on_clipboard
         1 WinSta0       CF_TEXT                          0x10 ------------------
         1 WinSta0       0xc0179L                          0x0 ------------------
         1 WinSta0       CF_TEXT                           0x1 ------------------
         1 ------------- ------------------            0xc0179 0xfffff900c1aca5a0
```

`flag: surctf_some_password_on_clipboard`

## 2. vmem_imager  

1. Узнаем с какой системы сняли дамп.
```sh
pig@door:~$ volatility -f Vasya.vmem imageinfo

Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/pig/Vasya.vmem)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80002e4b0a0L
          Number of Processors : 4
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002e4cd00L
                KPCR for CPU 1 : 0xfffff880009ef000L
                KPCR for CPU 2 : 0xfffff88002f69000L
                KPCR for CPU 3 : 0xfffff88002fdf000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2023-06-14 17:09:24 UTC+0000
     Image local date and time : 2023-06-14 23:09:24 +0600
```

2. Смотрим дерево процессов, находим процессы приложений, например: `explorer.exe`, `mspaint.exe`, `notepad.exe`. Выбираем один из них и запоминаем Pid.
```sh
pig@door:~$ volatility -f Vasya.vmem --profile=Win7SP1x64 pstree

Volatility Foundation Volatility Framework 2.6
Name                                                    Pid   PPid   Thds   Hnds Time
---------------------------------------------------- ------ ------ ------ ------ ----
 0xfffffa80024af1b0:csrss.exe                           324    308      9    316 2023-06-14 17:06:21 UTC+0000
 0xfffffa80024eb730:wininit.exe                         352    308      3     79 2023-06-14 17:06:22 UTC+0000
. 0xfffffa80025309e0:services.exe                       456    352      9    186 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa80027f87c0:svchost.exe                      1152    456     19    332 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa80025ac320:svchost.exe                       652    456      6    248 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa80025e0b30:svchost.exe                       784    456     14    304 2023-06-14 17:06:22 UTC+0000
... 0xfffffa800292c6d0:dwm.exe                         1564    784      4     76 2023-06-14 17:06:23 UTC+0000
.. 0xfffffa80028f7b30:taskhost.exe                     1476    456     10    199 2023-06-14 17:06:23 UTC+0000
.. 0xfffffa80018b57e0:sppsvc.exe                        312    456      6    157 2023-06-14 17:08:23 UTC+0000
.. 0xfffffa800261a630:svchost.exe                       816    456     35    835 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa8002bfa060:svchost.exe                      1472    456      9    114 2023-06-14 17:08:01 UTC+0000
.. 0xfffffa800267e740:svchost.exe                       972    456     13    248 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa800253fb30:svchost.exe                       568    456     12    360 2023-06-14 17:06:22 UTC+0000
... 0xfffffa8002c06b30:WmiPrvSE.exe                    1952    568      8    131 2023-06-14 17:07:23 UTC+0000
.. 0xfffffa80027065b0:svchost.exe                       468    456     19    331 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa80029feb30:svchost.exe                      1908    456      9     98 2023-06-14 17:06:23 UTC+0000
.. 0xfffffa80025de060:svchost.exe                       732    456     22    451 2023-06-14 17:06:22 UTC+0000
... 0xfffffa800263cb30:audiodg.exe                      896    732      7    132 2023-06-14 17:06:22 UTC+0000
.. 0xfffffa80026892e0:svchost.exe                      1004    456     23    420 2023-06-14 17:06:22 UTC+0000
. 0xfffffa800190fb30:lsass.exe                          464    352      7    446 2023-06-14 17:06:22 UTC+0000
. 0xfffffa800253b490:lsm.exe                            472    352     10    142 2023-06-14 17:06:22 UTC+0000
 0xfffffa80006af040:System                                4      0     84    486 2023-06-14 17:06:21 UTC+0000
. 0xfffffa800193c870:smss.exe                           244      4      2     32 2023-06-14 17:06:21 UTC+0000
 0xfffffa8002504b30:winlogon.exe                        408    360      4    112 2023-06-14 17:06:22 UTC+0000
 0xfffffa80024ec060:csrss.exe                           372    360      8    210 2023-06-14 17:06:22 UTC+0000
 0xfffffa8002937b30:*explorer.exe*                     1592   1552     23    703 2023-06-14 17:06:23 UTC+0000
. 0xfffffa8002a3fb30:regsvr32.exe                      1052   1592      0 ------ 2023-06-14 17:06:24 UTC+0000
. 0xfffffa80018d07f0:wmplayer.exe                      1184   1592     18    555 2023-06-14 17:08:16 UTC+0000
. 0xfffffa8002580b30:*mspaint.exe*                     1676   1592      7    126 2023-06-14 17:08:01 UTC+0000
. 0xfffffa8002bee5e0:*notepad.exe*                     2044   1592      2     58 2023-06-14 17:07:41 UTC+0000
```

3. Выгружаем дамп процесса.
```sh
pig@door:~$ volatility -f Vasya.vmem --profile=Win7SP1x64 memdump -p 1592 --dump-dir .

Volatility Foundation Volatility Framework 2.6
************************************************************************
Writing explorer.exe [  1592] to 1592.dmp
```

4. Переименовываем в `.data`.
```sh
pig@door:~$ mv 1592.dmp 1592.data
```

5. Закидываем файл в `GIMP` и настраиваем смещение и ширину. Получаем флаг.
![](forensic/forensic.png)

`flag: surctf_its_window_buffer`
