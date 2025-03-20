Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   

|
|
==
[AreFileApisANSI](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-arefileapisansi) 确定文件 I/O 函数是使用 ANSI 还是 OEM 字符集代码页。
==
==
[AreShortNamesEnabled](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-areshortnamesenabled) AreShortNamesEnabled 函数确定是否为指定的卷启用短名称。
==
==
[CompareFileTime](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-comparefiletime) 比较两个文件时间。
==
==
[CreateDirectoryA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createdirectorya) 新建目录。 (CreateDirectoryA)
==
==
[CreateDirectoryW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createdirectoryw) 新建目录。 (CreateDirectoryW)
==
==
[CreateFile2](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createfile2) 创建或者打开文件或 I/O 设备。
==
==
[CreateFileA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createfilea) 创建或者打开文件或 I/O 设备。 最常用的 I/O 设备如下：_file、文件流、目录、物理磁盘、卷、控制台缓冲区、磁带驱动器、通信资源、mailslot 和管道。 (ANSI)
==
==
[CreateFileW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createfilew) 创建或者打开文件或 I/O 设备。 最常用的 I/O 设备如下：_file、文件流、目录、物理磁盘、卷、控制台缓冲区、磁带驱动器、通信资源、mailslot 和管道。 (Unicode)
==
==
[DefineDosDeviceW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-definedosdevicew) 定义、重新定义或删除 MS-DOS 设备名称。 (DefineDosDeviceW)
==
==
[DeleteFileA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-deletefilea) 删除现有的文件。 (DeleteFileA)
==
==
[DeleteFileW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-deletefilew) 删除现有的文件。 (DeleteFileW)
==
==
[DeleteVolumeMountPointW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-deletevolumemountpointw) 删除驱动器号或装载的文件夹。 (DeleteVolumeMountPointW)
==
==
[FileTimeToLocalFileTime](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-filetimetolocalfiletime) 将文件时间转换为本地文件时间。
==
==
[FindClose](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findclose) 关闭 FindFirstFile、FindFirstFileEx、FindFirstFileNameW、FindFirstFileNameTransactedW、FindFirstFileTransacted、FindFirstStreamTransactedW 或 FindFirstStreamW 函数打开的文件搜索句柄。
==
==
[FindCloseChangeNotification](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findclosechangenotification) 停止更改通知句柄监视。
==
==
[FindFirstChangeNotificationA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstchangenotificationa) 创建更改通知句柄并设置初始更改通知筛选条件。 (ANSI)
==
==
[FindFirstChangeNotificationW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstchangenotificationw) 创建更改通知句柄并设置初始更改通知筛选条件。 (Unicode)
==
==
[FindFirstFileA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstfilea) 在目录中搜索名称与特定名称（如果使用通配符，则为部分名称）匹配的文件或子目录。 (ANSI)
==
==
[FindFirstFileExA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstfileexa) 在目录中搜索名称和属性与指定的名称和属性匹配的文件或子目录。 (FindFirstFileExA)
==
==
[FindFirstFileExW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstfileexw) 在目录中搜索名称和属性与指定的名称和属性匹配的文件或子目录。 (FindFirstFileExW)
==
==
[FindFirstFileNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstfilenamew) 创建指向指定文件的所有硬链接的枚举。 FindFirstFileNameW 函数返回枚举的句柄，该句柄可用于 FindNextFileNameW 函数的后续调用。
==
==
[FindFirstFileW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstfilew) 在目录中搜索名称与特定名称（如果使用通配符，则为部分名称）匹配的文件或子目录。 (Unicode)
==
==
[FindFirstStreamW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirststreamw) 枚举指定文件或目录中具有 ::$DATA 流类型的第一个流。
==
==
[FindFirstVolumeW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findfirstvolumew) 检索计算机上的卷的名称。 (FindFirstVolumeW)
==
==
[FindNextChangeNotification](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextchangenotification) 请求操作系统在下次检测到相应更改时，发出更改通知句柄信号。
==
==
[FindNextFileA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextfilea) 从先前的 FindFirstFile、FindFirstFileEx 或 FindFirstFileTransacted 函数调用继续搜索文件。 (ANSI)
==
==
[FindNextFileNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextfilenamew) 使用成功的 FindFirstFileNameW 函数调用返回的句柄继续枚举指向文件的硬链接。
==
==
[FindNextFileW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextfilew) 从先前的 FindFirstFile、FindFirstFileEx 或 FindFirstFileTransacted 函数调用继续搜索文件。 (Unicode)
==
==
[FindNextStreamW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextstreamw) 继续执行先前的 FindFirstStreamW 函数调用启动的流搜索。
==
==
[FindNextVolumeW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findnextvolumew) 继续通过调用 FindFirstVolume 函数启动的卷搜索。 (FindNextVolumeW)
==
==
[FindVolumeClose](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-findvolumeclose) 关闭指定的卷搜索句柄。
==
==
[FlushFileBuffers](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-flushfilebuffers) 刷新指定文件的缓冲区并将所有缓冲数据写入文件。
==
==
[GetCompressedFileSizeA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getcompressedfilesizea) 检索用于存储指定文件的磁盘存储的实际字节数。 (ANSI)
==
==
[GetCompressedFileSizeW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getcompressedfilesizew) 检索用于存储指定文件的磁盘存储的实际字节数。 (Unicode)
==
==
[GetDiskFreeSpaceA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskfreespacea) 检索有关指定磁盘的信息，包括磁盘上的可用空间量。 (ANSI)
==
==
[GetDiskFreeSpaceExA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskfreespaceexa) 检索有关磁盘卷上可用空间量的信息，即空间总量、可用空间总量以及与调用线程关联的用户可用空间总量。 (ANSI)
==
==
[GetDiskFreeSpaceExW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskfreespaceexw) 检索有关磁盘卷上可用空间量的信息，即空间总量、可用空间总量以及与调用线程关联的用户可用空间总量。 (Unicode)
==
==
[GetDiskFreeSpaceW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskfreespacew) 检索有关指定磁盘的信息，包括磁盘上的可用空间量。 (Unicode)
==
==
[GetDiskSpaceInformationA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskspaceinformationa) 获取位于给定根路径的卷的磁盘空间信息。
==
==
[GetDiskSpaceInformationW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdiskspaceinformationw) 获取位于给定根路径的卷的磁盘空间信息。
==
==
[GetDriveTypeA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdrivetypea) 确定磁盘驱动器是可移动的、固定的、CD-ROM、RAM 磁盘，还是网络驱动器。 (ANSI)
==
==
[GetDriveTypeW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getdrivetypew) 确定磁盘驱动器是可移动的、固定的、CD-ROM、RAM 磁盘，还是网络驱动器。 (Unicode)
==
==
[GetFileAttributesA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfileattributesa) 检索指定文件或目录的文件系统属性。 (ANSI)
==
==
[GetFileAttributesExA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfileattributesexa) 检索指定文件或目录的属性。 (ANSI)
==
==
[GetFileAttributesExW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfileattributesexw) 检索指定文件或目录的属性。 (Unicode)
==
==
[GetFileAttributesW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfileattributesw) 检索指定文件或目录的文件系统属性。 (Unicode)
==
==
[GetFileInformationByHandle](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfileinformationbyhandle) 检索指定文件的文件信息。 (GetFileInformationByHandle)
==
==
[GetFileSize](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfilesize) 检索指定文件的大小（以字节为单位）。
==
==
[GetFileSizeEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfilesizeex) 检索指定文件的大小。
==
==
[GetFileTime](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfiletime) 检索文件或目录的创建、上次访问和上次修改日期和时间。
==
==
[GetFileType](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfiletype) 检索指定文件的文件类型。
==
==
[GetFinalPathNameByHandleA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfinalpathnamebyhandlea) 检索指定文件的最终路径。 (ANSI)
==
==
[GetFinalPathNameByHandleW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfinalpathnamebyhandlew) 检索指定文件的最终路径。 (Unicode)
==
==
[GetFullPathNameA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfullpathnamea) 检索指定文件的完整路径和文件名。 (ANSI)
==
==
[GetFullPathNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getfullpathnamew) 检索指定文件的完整路径和文件名。 (Unicode)
==
==
[GetLogicalDrives](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getlogicaldrives) 检索一个表示当前可用磁盘驱动器的位掩码。
==
==
[GetLogicalDriveStringsW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getlogicaldrivestringsw) 使用在系统中指定有效驱动器的字符串填充缓冲区。 (GetLogicalDriveStringsW)
==
==
[GetLongPathNameA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getlongpathnamea) 将指定路径转换为其长格式。 (ANSI)
==
==
[GetLongPathNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getlongpathnamew) 将指定路径转换为其长格式。 (Unicode)
==
==
[GetShortPathNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getshortpathnamew) 检索指定路径的短路径格式。 (GetShortPathNameW)
==
==
[GetTempFileNameA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettempfilenamea) 创建临时文件的名称。 如果生成唯一的文件名，则会创建一个空文件并释放其句柄；否则仅生成文件名。 (GetTempFileNameA)
==
==
[GetTempFileNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettempfilenamew) 创建临时文件的名称。 如果生成唯一的文件名，则会创建一个空文件并释放其句柄；否则仅生成文件名。 (GetTempFileNameW)
==
==
[GetTempPath2A](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettemppath2a) 根据调用进程的特权检索为临时文件指定的目录的路径。 (ANSI)
==
==
[GetTempPath2W](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettemppath2w) 根据调用进程的特权检索为临时文件指定的目录的路径。 (Unicode)
==
==
[GetTempPathA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettemppatha) 检索为临时文件指定的目录的路径。 (ANSI)
==
==
[GetTempPathW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-gettemppathw) 检索为临时文件指定的目录的路径。 (Unicode)
==
==
[GetVolumeInformationA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumeinformationa) 检索与指定根目录关联的文件系统和卷的相关信息。 (ANSI)
==
==
[GetVolumeInformationByHandleW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumeinformationbyhandlew) 检索与指定文件关联的文件系统和卷的相关信息。
==
==
[GetVolumeInformationW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumeinformationw) 检索与指定根目录关联的文件系统和卷的相关信息。 (Unicode)
==
==
[GetVolumeNameForVolumeMountPointW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumenameforvolumemountpointw) 检索与指定卷装入点（驱动器号、卷 GUID 路径或装载的文件夹）关联的卷的卷 GUID 路径。 (GetVolumeNameForVolumeMountPointW)
==
==
[GetVolumePathNamesForVolumeNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumepathnamesforvolumenamew) 检索指定卷的驱动器号和装载的文件夹路径的列表。 (GetVolumePathNamesForVolumeNameW)
==
==
[GetVolumePathNameW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-getvolumepathnamew) 检索装载指定路径的卷装入点。 (GetVolumePathNameW)
==
==
[LocalFileTimeToFileTime](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-localfiletimetofiletime) 根据协调世界时 (UTC) 将本地文件时间转换为文件时间。
==
==
[LockFile](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-lockfile) 通过调用进程锁定指定的文件以供独占访问。
==
==
[LockFileEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-lockfileex) 通过调用进程锁定指定的文件以供独占访问。 此函数可以同步或异步运行，并可以请求独占锁或共享锁。
==
==
[QueryDosDeviceW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-querydosdevicew) 检索有关 MS-DOS 设备名称的信息。 (QueryDosDeviceW)
==
==
[ReadFile](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-readfile) 从指定的文件或输入/输出 (I/O) 设备读取数据。 读取发生在文件指针指定的位置（如果设备支持）。
==
==
[ReadFileEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-readfileex) 从指定的文件或输入/输出 (I/O) 设备读取数据。 它以异步方式报告其完成状态，并在读取已完成或取消并且调用线程处于可警报等待状态时调用指定的完成例程。
==
==
[ReadFileScatter](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-readfilescatter) 从文件读取数据并将其存储在缓冲区数组中。
==
==
[RemoveDirectoryA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-removedirectorya) 删除现有空目录。 (ANSI)
==
==
[RemoveDirectoryW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-removedirectoryw) 删除现有空目录。 (Unicode)
==
==
[SetEndOfFile](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setendoffile) 将指定文件的物理文件大小设置为文件指针的当前位置。
==
==
[SetFileApisToANSI](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileapistoansi) 使文件 I/O 函数对当前进程使用 ANSI 字符集代码页。
==
==
[SetFileApisToOEM](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileapistooem) 使进程的文件 I/O 函数使用 OEM 字符集代码页。
==
==
[SetFileAttributesA](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileattributesa) 设置文件或目录的属性。 (ANSI)
==
==
[SetFileAttributesW](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileattributesw) 设置文件或目录的属性。 (Unicode)
==
==
[SetFileInformationByHandle](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileinformationbyhandle) 设置指定文件的文件信息。
==
==
[SetFileIoOverlappedRange](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfileiooverlappedrange) 将虚拟地址范围与指定的文件句柄相关联。
==
==
[SetFilePointer](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfilepointer) 移动指定文件的文件指针。 (SetFilePointer)
==
==
[SetFilePointerEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfilepointerex) 移动指定文件的文件指针。 (SetFilePointerEx)
==
==
[SetFileTime](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfiletime) 设置文件或目录的创建、上次访问和上次修改日期和时间。
==
==
[SetFileValidData](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-setfilevaliddata) 设置指定文件的有效数据长度。 此函数在非常有限的方案中有用。 有关详细信息，请参见“备注”部分。
==
==
[UnlockFile](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-unlockfile) 解锁打开的文件中的区域。
==
==
[UnlockFileEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-unlockfileex) 解锁指定文件中的区域。 此函数可以同步或异步运行。
==
==
[WriteFile](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-writefile) 将数据写入指定的文件或输入/输出 (I/O) 设备。
==
==
[WriteFileEx](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-writefileex) 将数据写入指定的文件或输入/输出 (I/O) 设备。 它以异步方式报告其完成状态，并在写入已完成或取消并且调用线程处于可警报等待状态时调用指定的完成例程。
==
==
[WriteFileGather](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-writefilegather) 从缓冲区数组检索数据并将数据写入文件。
==
 > 来自 <[https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/#functions](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/#functions)>