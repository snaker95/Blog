# Input/Output Error : Bad Blocks - How to Restart Linux

[![input output errors](https://linoxide.com/wp-content/uploads/2012/10/bad_disk.jpg)](https://www.linoxide.com/wp-content/uploads/2012/10/bad_disk.jpg)

This article I am trying to explain how to deal with "Input/output error " when you initiate any commands in Linux. I have pointed out some examples that report a similar error.

> \# du 
>
> -bash: /usr/bin/du: Input/output error

> \# mkdir sampledir
> mkdir: cannot create directory `sampledir': Input/output error

Input/output error while running the command mostly due to two reason. Either it could be bad blocks on the disk or someone hacked your machine. In this situation first suggestion would be to check /var/log/messages for any disk related alerts (might see some sense key alerts).

> tail -n 100 /var/log/messages
> tail -f /var/log/messages

If you notice any disk issues, try any disk utility to confirm this. One option would be to use smarttools. You can check smartool option to [fix bad blocks](https://www.linoxide.com/general/how-to-fix-repair-bad-blocks-in-linux/). If you already installed it you can take advantage of that.

Next immediate action would take a backup of your system. Good if you already have it :-). If you have good backup its safe to reboot. If your system was hacked there are chances they might mess up your files and system would not work after reboot.

You can also try fsck but it really dont fix most time if it bad block issue. Fsck can fix only if it related to any filesystem related issues.

Usually, fsck will fail in between as when it try to read the file at bad block.

Now if try to reboot, it can also give the same output. You can try init 6.

> \# reboot
> bash: /sbin/reboot: Input/output error

> \# shutdown -r now
> bash: /sbin/shutdown: Input/output error

If the above reboot commands doesn't work try either forced reboot or shutdown

**Forced Reboot**

> echo 1 > /proc/sys/kernel/sysrq
> echo b > /proc/sysrq-trigger

#### Forced Shutdown

> echo 1 > /proc/sys/kernel/sysrq
> echo o > /proc/sysrq-trigger

Thanks for reading the article and let me know your comments.



[参考](https://linoxide.com/how-tos/inputoutput-error-bad-blocks-how-to-restart-linux/)

