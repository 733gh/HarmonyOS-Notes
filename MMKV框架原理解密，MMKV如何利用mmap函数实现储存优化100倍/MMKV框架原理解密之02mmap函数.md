  除了标准的文件 IO，例如 open, read, write，内核还提供接口允许应用将文件 map 到内存。使得内存中的一个字节与文件中的一个字节一一对应。

- 优势
  - 读写文件避免了 `read()` 和 `write()` 系统调用，也避免了数据的拷贝。
  - 多个进程 map 同一个对象，可以共享数据。
  - 可以直接使用指针来跳转到文件某个位置，不必使用 `lseek()` 系统调用。
- 劣势
  - 内存浪费。由于必须要使用整数页的内存。



![img](https://upload-images.jianshu.io/upload_images/4482847-a04d010b9c8e2391.png?imageMogr2/auto-orient/strip|imageView2/2/w/1000/format/webp)

mmap 原理

## 使用方法

函数原型为：



```cpp
#include <sys/mman.h>

void * mmap (void *addr,
             size_t len,
             int prot,
             int flags,
             int fd,
             off_t offset);
```

- **addr**
   这个参数是建议地址（hint），没有特别需求一般设为0。这个函数会返回一个实际 map 的地址。
- **len**
   文件长度。
- **prot**
   表明对这块内存的保护方式，不可与文件访问方式冲突。
   `PROT_NONE`
   无权限，基本没有用
   `PROT_READ`
   读权限
   `PROT_WRITE`
   写权限
   `PROT_EXEC`
   执行权限
- **flags**
   描述了映射的类型。
   `MAP_FIXED`
   开启这个选项，则 addr 参数指定的地址是作为必须而不是建议。如果由于空间不足等问题无法映射则调用失败。不建议使用。
   `MAP_PRIVATE`
   表明这个映射不是共享的。文件使用 copy on write 机制映射，任何内存中的改动并不反映到文件之中。也不反映到其他映射了这个文件的进程之中。如果只需要读取某个文件而不改变文件内容，可以使用这种模式。
   `MAP_SHARED`
   和其他进程共享这个文件。往内存中写入相当于往文件中写入。会影响映射了这个文件的其他进程。与 `MAP_PRIVATE`冲突。
- **fd**
   文件描述符。进行 map 之后，文件的引用计数会增加。因此，我们可以在 map 结束后关闭 fd，进程仍然可以访问它。当我们 unmap 或者结束进程，引用计数会减少。
- **offset**
   文件偏移，从文件起始算起。

如果失败，mmap 函数将返回 `MAP_FAILED`。
