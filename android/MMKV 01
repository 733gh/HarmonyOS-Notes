# **MMKV简介**

腾讯微信团队于2018年9月底宣布开源 MMKV ，这是基于 mmap 内存映射的 key-value 组件，底层序列化/反序列化使用 protobuf 实现，主打高性能和稳定性。近期也已移植到 Android 平台，一并对外开源。

MMKV 是基于 mmap 内存映射的 key-value 组件，底层序列化/反序列化使用 protobuf 实现，性能高，稳定性强。从 2015 年中至今，在 iOS 微信上使用已有近 3 年，其性能和稳定性经过了时间的验证。近期也已移植到 Android 平台，一并开源。

MMKV最新源码托管地址：https://github.com/Tencent/MMKV

## **MMKV 源起**

在微信客户端的日常运营中，时不时就会爆发特殊文字引起系统的 crash ，文章里面设计的技术方案是在关键代码前后进行计数器的加减，通过检查计数器的异常，来发现引起闪退的异常文字。在会话列表、会话界面等有大量 cell 的地方，希望新加的计时器不会影响滑动性能；另外这些计数器还要永久存储下来——因为闪退随时可能发生。

这就需要一个性能非常高的通用 key-value 存储组件，我们考察了 SharedPreferences、NSUserDefaults、SQLite 等常见组件，发现都没能满足如此苛刻的性能要求。考虑到这个防 crash 方案最主要的诉求还是实时写入，而 mmap 内存映射文件刚好满足这种需求，我们尝试通过它来实现一套 key-value 组件。

## *MMKV 原理*

**内存准备**

通过 mmap 内存映射文件，提供一段可供随时写入的内存块，App 只管往里面写数据，由操作系统负责将内存回写到文件，不必担心 crash 导致数据丢失。

**数据组织**

数据序列化方面我们选用 protobuf 协议，pb 在性能和空间占用上都有不错的表现。考虑到我们要提供的是通用 kv 组件，key 可以限定是 string 字符串类型，value 则多种多样（int/bool/double 等）。要做到通用的话，考虑将 value 通过 protobuf 协议序列化成统一的内存块（buffer），然后就可以将这些 KV 对象序列化到内存中。

**写入优化**

标准 protobuf 不提供增量更新的能力，每次写入都必须全量写入。考虑到主要使用场景是频繁地进行写入更新，我们需要有增量更新的能力：将增量 kv 对象序列化后，直接 append 到内存末尾；这样同一个 key 会有新旧若干份数据，最新的数据在最后；那么只需在程序启动第一次打开 mmkv 时，不断用后读入的 value 替换之前的值，就可以保证数据是最新有效的。

**空间增长**

使用 append 实现增量更新带来了一个新的问题，就是不断 append 的话，文件大小会增长得不可控。例如同一个 key 不断更新的话，是可能耗尽几百 M 甚至上 G 空间，而事实上整个 kv 文件就这一个 key，不到 1k 空间就存得下。这明显是不可取的。我们需要在性能和空间上做个折中：以内存 pagesize 为单位申请空间，在空间用尽之前都是 append 模式；当 append 到文件末尾时，进行文件重整、key 排重，尝试序列化保存排重结果；排重后空间还是不够用的话，将文件扩大一倍，直到空间足够。

**数据有效性**

考虑到文件系统、操作系统都有一定的不稳定性，我们另外增加了 crc 校验，对无效数据进行甄别。

CRC即循环冗余校验码（Cyclic Redundancy Check [1] ）：是数据通信领域中最常用的一种查错校验码，其特征是信息字段和校验字段的长度可以任意选定。循环冗余检查（CRC）是一种数据传输检错功能，对数据进行多项式计算，并将得到的结果附在帧的后面，接收设备也执行类似的算法，以保证数据传输的正确性和完整性

## **MMKV for Android 特有功能**

我们不是简简单单地照搬 iOS 的实现，在迁移到 Android 的过程中，深入分析了 Android 平台现有 kv 组件的痛点，在原有功能基础上，开发了 Android 特有的功能。

**多进程访问**

通过与 Android 开发同学的沟通，了解到系统自带的 SharedPreferences 对多进程的支持不好。现有基于 ContentProvider 封装的实现，虽然多进程是支持了，但是性能低下，经常导致 ANR。考虑到 mmap 共享内存本质上的多进程共享的，我们在这个基础上，深入挖掘了 Android 系统的能力，提供了可能是业界最高效的多进程数据共享组件。具体实现原理我们中秋节后分享，心急的同学可以前往 GitHub 查看源码和 wiki 文档。

**匿名内存**

在多进程共享的基础上，考虑到某些敏感数据(例如密码)需要进程间共享，但是不方便落地存储到文件上，直接用 mmap 不合适。我们了解到 Android 系统提供了 Ashmem 匿名共享内存的能力，发现它在进程退出后就会消失，不会落地到文件上，非常适合这个场景。我们很愉快地提供了 Ashmem MMKV 的功能。

**数据加密**

不像 iOS 提供了硬件层级的加密机制，在 Android 环境里，数据加密是非常必须的。MMKV 使用了 AES CFB-128 算法来加密/解密。我们选择 CFB 而不是常见的 CBC 算法，主要是因为 MMKV 使用 append-only 实现插入/更新操作，流式加密算法更加合适。事实上这个功能也回馈到了 iOS 版，所以现在两个系统的 MMKV 都有加密功能。

## Android 快速上手

MMKV 已托管到 bintray(JCenter)，可以直接使用。在 App 的 build.gradle 里加上依赖：
MMKV 的使用非常简单，所有变更立马生效，无需调用 sync、apply。 在 App 启动时初始化 MMKV，设定 MMKV 的根目录(files/mmkv/)，例如在 MainActivity 里：

MMKV 提供一个全局的实例，可以直接使用：

如果不同业务需要区别存储，也可以单独创建自己的实例：

SharedPreferences 迁移
MMKV 提供了 importFromSharedPreferences() 函数，可以比较方便地迁移数据过来。

MMKV 还额外实现了一遍 SharedPreferences、SharedPreferences.Editor 这两个 interface，在迁移的时候只需两三行代码即可，其他 CRUD 操作代码都不用改。

```
dependencies {
    implementation 'com.tencent:mmkv:1.0.10'
    // replace "1.0.10" with any available version
}
1234
  @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        String rootDir = MMKV.initialize(this);
        System.out.println("mmkv root: " + rootDir);
    }
    private void getSomeValue(){
        MMKV kv = MMKV.defaultMMKV();
        kv.encode("bool", true);
        boolean bValue = kv.decodeBool("bool");
        kv.encode("int", Integer.MIN_VALUE);
        int iValue = kv.decodeInt("int");
        kv.encode("string", "Hello from mmkv");
        String str = kv.decodeString("string");
    }
12345678910111213141516
```

补充：
mmkv使用的是用户不可见的私有存储，和sp类似，并不需要权限，但是和app绑定：
mmkv的写入逻辑是：当我们覆盖某个值的时候，它并不会立即删除前面的值，会保留，然后每个key，value有存储限制，当触发存储限制的时候，才会执行删除，这样即使我们频繁的覆盖，也不会引起太多的性能损耗

解答一些疑问：mmkv是可以自己设定不同的存储目录的
MMKV.mmkvWithID(MMKV_KEY, relativePath);

补充适用建议：如果使用请务必做code19版本的适配，这个在github官网有说明

依赖下面这个库，然后对19区分处理
implementation ‘com.getkeepsafe.relinker:relinker:1.3.1’

```
    if (android.os.Build.VERSION.SDK_INT == 19) {
        MMKV.initialize(relativePath, new MMKV.LibLoader() {
            @Override
            public void loadLibrary(String libName) {
                ReLinker.loadLibrary(context, libName);
            }
        });
    } else {
        MMKV.initialize(context);
    }
12345678910
```

在mmkv的使用过程中，我的对象存储方式是，转化成json串，通过字符串存储，使用的时候在取出来转化
