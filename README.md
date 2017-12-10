# nexus 5 内核打印open日志最简单方法！

## nexus 5 4.4.4-r1 原版rom，kernel替换为以下版本。

git版本

```
https://android.googlesource.com/kernel/msm.git
commit d59db4e8622b347e047d61567766fb76cbde392b
Author: Shuzhen Wang <shuzhenw@codeaurora.org>
Date:   Sun Mar 16 23:02:53 2014 -0700
```

文件修改

```
/*
./fs/open.c
大约第1000行左右 long do_sys_open(int dfd, const char __user *filename, int flags, umode_t mode)
插入以下代码：
*/
	struct timex txc;
	struct rtc_time tm;
	do_gettimeofday(&(txc.time));
	rtc_time_to_tm(txc.time.tv_sec, &tm);
	//如果想要时间，就带上上面4行
		if(/*过滤条件*/(filename)){
			printk(KERN_WARNING "[%02d:%02d:%02d.%03ld %u:%u]open(%s,%d,%d)\n",
				tm.tm_hour, tm.tm_min, tm.tm_sec, txc.time.tv_usec / 1000,
        (task_tgid_vnr(current)), (task_pid_vnr(current)),
				filename, flags, mode
			);
		}
```
## 然后编译

生成boot.img麻烦，直接用原版，然后用bootimg-cofface.py只替换zImage
shell例子：
```
bootimg-cofface.py --unpack-bootimg
cp 内核代码目录/arch/arm/boot/zImage-dtb kernel
bootimg-cofface.py --repack-bootimg
```
