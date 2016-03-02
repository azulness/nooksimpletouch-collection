Nook2折腾笔记，折腾之后，是完美



恢复出厂状态（unroot）： 让一切从新开始，需要分别reset系统分区（所谓的8次打断法）和数据分区（所谓三键恢复）。 1、reset系统分区 如果你没有root，或者无法用adb连接到设备，使用8次打断法，即在完全关机的状态下，按电源键3秒后松手，在闪屏后按电源键15秒松手，然后按电源键3秒后松手……如此8次后，再次按3秒开机后，设备会进入到restore factory的界面，等待她自动恢复重启即可。 如果你已经root了，并且可以连上abd，那么敲入以下命令即可进入恢复界面： adb shell echo -n -e "\x08\x00\x00\x00" > /rom/devconf/BootCnt? reboot 复制代码 2、reset数据分区 依次点击“Settings”－“Device Settings”－“Device Info”－“Erase & Deregister Device”－“Reset Nook" button”即可。 若提示失败，关机，开机，在闪屏后同时按住下面的两个翻页键15秒左右会有提示是否reset的画面，点击n键（两次）即可。



备份和还原： 使用DiskImage?（www.roadkil.net/program.php?ProgramID=12）来操作。备份系统可以确保你的设备不会因为软件原因而变砖。 1、备份。用DiskImage?将noogie.img写到TF卡（一定要通过读卡器）。将设备完全关闭，插卡，开机。在屏幕上出现了“root forever”之后插上数据线。然后用DiskImage?将设备的整个磁盘写成一个img文件，小心保存这个img文件吧。 2、恢复。同样的，用DiskImage?将noogie.img写到TF卡（参见下面的root过程）（一定要通过读卡器）。将设备完全关闭，插卡，开机。在屏幕上出现了“root forever”之后插上数据线。用DiskGenius?等磁盘分区管理软件读取NOOK2的分区信息，删除所有分区后，用DiskImage?将你之前保存的img文件（或者http://forum.xda-developers.com/showthread.php?t=1289233获取）写回到设备。



root root本身是一个非常简单的过程，只是替换了NOOK2上boot分区的ramdisk文件而已。 1、准备好以个读卡器，一张128mb以上容量的TF卡，以个NOOK2，一台电脑。 2、下载noogie.img.gz（http://www.multiupload.com/OFI609MP8V） 和uRamdisk\_rooted（http://www.multiupload.com/WKLQHCT334）。 3、下载win32imagewriter（https://launchpad.net/win32-image-writer/），将noogie.img通过读卡器写���到TF卡。 4、将noogie卡插入到完全关机的NOOK2，再开机。会进入到显示“root forever”的界面。 5、将NOOK2通过数据线连接电脑，电脑上会看到一个名称为boot的分区，将uRamdisk\_rooted重命名为uRamdisk，替换该分区的同名文件。弹出设备，弹出TF卡。按电源键15秒关机。按电源键开机。 6、电脑上安装adb（提取Android SDK中的几个文件即可，请自行搜索方法），打开adb，敲入以下命令： adb connect <NOOK2的ip地址> 复制代码 若提示成功连接，则说明root成功。



安装Superuser.apk、su和busybox（必须） 光root一点意义都没有，此步骤是之后折腾的基础。假设你的adb放在“C:/android-sdk-windows/platform-tools”。 1、下载Superuser.apk（http://nookdevs.com/images/a/a9/Superuser.zip）、su（http://nookdevs.com/images/e/e0/Su.zip）、busybox（http://www.megaupload.com/?d=VFEI8UIF）。都解压放在adb目录。 2、执行以下命令以安装Superuser.apk和su： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb install Superuser.apk adb push su /data/local/ adb shell cd /system/bin mount -o remount,rw /dev/block/mmcblk0p5 /system cat /data/local/su > su chmod 6755 su reboot 复制代码 等待重启完毕 3、执行以下命令安装busybox： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell su mount -o remount,rw /dev/block/mmcblk0p5 /system exit exit adb push busybox /data/local adb shell su cd /system mkdir /system/xbin cd /data/local chmod 755 busybox /data/local/busybox cp /data/local/busybox /system/xbin/busybox cd /system/xbin chmod 755 busybox ./busybox --install -s /system/xbin rm /data/local/busybox reboot 复制代码 开启设备本地安装apk的功能（必须） 我并不准备安装gapps，我只须要安装极少量的软件，但这一步也是必须的。 1、下载sqlite3（http://www.sqlite.org/sqlite-shell-win32-x86-3070900.zip），解压放到adb目录。 2、执行以下命令即可： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell mount -o rw,remount -t ext2 /dev/block/mmcblk0p5 /system exit adb pull /data/data/com.android.providers.settings/databases/settings.db settings.db sqlite3 settings.db "update secure set value=1 where name='install\_non\_market\_apps';" adb push settings.db /data/data/com.android.providers.settings/databases/settings.db 复制代码 修改NOOK2的按键映射，在标题栏增加返回键和菜单键（必须） 这个工具有两个jar文件和一个apk文件组成，必须安装，非常方便，也是后面折腾的基础。 1、下载两个jar和nooktouchtools（http://forum.xda-developers.com/showthread.php?t=1289894）。将得到的android.policy.jar、services.jar和nooktouchtools.apk都放���adb目录。 2、敲入以下命令以替换jar： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell mount -o remount,rw /dev/block/mmcblk0p5 /system exit adb push android.policy.jar /system/framework/ adb push services.jar /system/framework/ adb reboot 复制代码 等待重启 3、安装nooktouchtools： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb install NookTouchTools?-1.0b2.apk 复制代码 关闭自动升级（可选） 如果你不想让NOOK2自动升级的话…… cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb pull /data/data/com.bn.devicemanager/databases/devicemanager.db devicemanager.db sqlite3 devicemanager.db sqlite> update registry set value='manual' where name='com.bn.device.fota.mode'; sqlite> .q adb push devicemanager.db /data/data/com.bn.devicemanager/databases/devicemanager.db adb reboot 复制代码 删除自带的两本书Quick\_Start?和User\_Guide?（可选） 这两本书真心不想让它出现在书架上。 cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell mount -o rw,remount -t ext2 /dev/block/mmcblk0p5 /system rm /system/media/books/Quick\_Start?.epub rm /system/media/books/User\_Guide?.epub 复制代码 删除电话程序（可选，其实并不能减少耗电） 有人说root之后耗电增加，查看系统耗电发现cell standby和phone idle占用了大多数电量。其实XDA告诉我们，这两个项目尽管在耗电统计里占用多数，但对系统真是的耗电是没有任何影响的，cell standby的话，没有基带，基本不会有耗电；而phone idle其实就是system idle，是系统空闲而已。但是，如果你想删除以求心理安慰的话，尽管去做吧，对系统不会有任何负作用的： cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell mount -o remount,rw /dev/block/mmcblk0p5 /system mv /system/app/Phone.apk /system/app/Phone.OLD mv /system/app/TelephonyProvider?.apk /system/app/TelephonyProvider?.OLD reboot 复制代码 至此，折腾的差不多了。下面是安装软件和修改NOOK2界面的时间了。



XDA的几位高手分别都发布了一键root的img或是脚本。里面基本上都会带有新的launcher和google应用套件。可是，我问我自己，我真的需要吗？答案是否定的，这也是我如前文一步一步折腾的原因，我只需要极少量的修改来满足最基本的需求，看扫描PDF和无线传书。如果你也跟我一样，只当NOOK2是个读书的伴侣的话，或许你会同意我下面的做法。我不需要新的launcher，n键呼出的快捷菜单上有五个按键，依次是home、lib、shop、search、setting，其中home、shop和search是完全可以舍弃的。而通过nooktouchtools我们可以做到将这三个按键替换成我们自定义的程序。这样就能在不按照launcher的情况下打开应用程序了。



现在来谈谈我选择的apk（应用）吧。前文提过了我用来看扫描PDF的Perfect Viewer。无线传书呢？比较了几款软件后，我选择了SwiFTP这款不到100k的小软件，通过将NOOK2变成ftp服务器的方式进行无线文件的传输。然后由于系统管理和进程管理的需求，我好需要一款任务管理器和文件管理器。我选择的是我在Android习惯使用的高级任务管理器和Root Explorer。另外，美观起见，我替换了上述几款应该用的图标，修改（缩短）了名称。



这里有一个小插曲，在之安装我这几款应用的情况下，我如何运行nooktouchtools（用来修改按键映射）呢？这还得靠adb。输入 cd C:/android-sdk-windows/platform-tools adb connect yourNSTip adb shell am start -n io.xor.nooktouchtools/io.xor.nooktouchtools.MainActivity? 复制代码 即可打开nooktouchtools，打开之后就可以修改相应的按键设置了。我的设置是home－Perfect Viewer，Lib不变，shop－Root Explorer，search－SwiFTP，Setting不变，左上翻页－电源按键（因为带了套子，按后面的电源键不方便），左下翻页－menu，右上翻页不变，右下翻页不变，n键不变。



至此，我的NOOK2成了一台可以供我方便的、唯美的阅读伴侣了。