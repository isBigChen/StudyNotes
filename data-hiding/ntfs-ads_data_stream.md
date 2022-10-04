参考链接`https://blog.csdn.net/amryu/article/details/122770431`

# NTFS和ADS

> NTFS（New Technology File System）是Windows NT内核默认使用的一种文件系统。

> ADS（alternate data streams）是NTFS交换数据流。在NTFS文件系统下，每个文件除了自身主数据流外，还可以存在许多非主文件流，比如ADS可以是一个文件后的追加的数据流。ADS也叫备用数据流。

# ADS数据流

ADS数据流可以被单独创建，也可以被指定到宿主文件后面。ADS数据流的创建和删除借助windows自带的命令即可。

## 创建单独ADS流

要创建文本格式的ADS流的实验，打开一个空目录，输入`echo "hello world" > :ads.txt`即可。此时就创建了这个数据流，用微软的streams工具命令`streams -s`可以看到这个数据流，但是对于单独的ADS流是看不到内容的，如果想要看到内容需要自己下载NtfsStreamsEditor软件查看。

> streams工具链接`https://docs.microsoft.com/en-us/sysinternals/downloads/streams`

## 删除单独ADS流

由于未指定宿主文件，一般的方法无法删除。可以采取删除其上一级目录的方法来删除这个单独的ADS数据流文件；也可以用NtfsStreamsEditor删除。

## 创建指定宿主文件的ADS流

> 宿主文件是指在Windows中**可以正常显示**、运行或编辑的**任何类型文件**。

比如有文件`host.txt`，要在其后面创建ADS流，`echo "hello world" > host.txt:ads.txt`

对于创建在指定宿主文件的ADS流，可以通过ADS流的后缀类型对应的命令打开查看，比如这里是txt格式的，可以用notepad打开，`notepad host.txt:ads.txt`

## 删除指定宿主文件的ADS流

可以直接删除宿主文件，也可以用NtfsStreamsEditor删除。

## 任意类型的ADS流

> 除文本文件以为，包括可执行文件在内的任何常规文件都可以被设置成ADS数据流文件。

使用`type`命令将非文本格式的文件以ADS流的形式附加的宿主文件上。比如`type calc.exe > host.txt:ads.exe`原理就是将`calc.exe`复制一下放到`hosts.txt`后面的`ads.exe`。这样即便删除掉原目录下的`calc.exe`，也不会对ADS流的`ads.exe`有影响。

> 这里要提一个细节，就是如果ads.exe这个文件自身作为宿主文件有其ADS数据流，那么这时候我们将ads.exe作为ADS数据流与host.txt（作为宿主文件）相关联，ads.exe其自身的ADS数据流是不会被一起关联的。也就是复制ads.exe的内容作为ADS数据流的时候，**仅仅复制了ads.exe的内容，并没有将ads.exe自身的关联的ADS数据流复制上。**

> 如果ADS流是图片的话，可以用图片查看器查看ADS流图片`mspaint hosts.txt:ads.jpg`。

实际上，ADS数据流文件的通用查看方法是：`ADS文件对应的打开/编辑程序 宿主文件名：数据流文件名`。

> 另外一个需要注意的点是，只用winrar压缩时才能保存ads流数据。

# ADS数据流文件的执行

当ADS数据流是一个可执行文件时，可以用`start`命令来执行它，`start host.txt:a.exe`，但是这里执行不成功，报错没关联文件。

