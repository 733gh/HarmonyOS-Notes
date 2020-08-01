# 什么是热修复技术？



关于热修复这个名词，并不陌生。相信大家都有过更新window补丁的经历，通过补丁可以动态修复系统的漏洞，只不过这个过程对用户而言是可选及自行操作。

bug ---》 

过时

修复技术

那么关于Android平台的热修复技术，简单来说，就是通过下发补丁包，让已安装的客户端动态更新，让用户可以不用重新安装APP，就能够修复软件缺陷的一种技术。

![](https://upload-images.jianshu.io/upload_images/5125122-50c2fb502983a3bb?imageMogr2/auto-orient/strip|imageView2/2/w/685/format/webp)



随着热修复技术的发展，不仅可以修复代码，同时可以修复资源文件及SO库。

![](https://upload-images.jianshu.io/upload_images/5125122-cb67bc0991f396fb?imageMogr2/auto-orient/strip|imageView2/2/w/672/format/webp)



# 为什么要使用热修复技术？

在回答这个问题之前，我觉得应该先思考如下几个问题。

1. 开发上线的版本能保证不存在Bug么？
2. 修复后的版本能保证用户都及时更新么？
3. 如何最大化减少线上Bug对业务的影响？
4. 

从这些角度来说，相信大家应该都能有所体会，热修复技术带来的优势不言而喻。

1. 可快速修复，避免线上Bug带来的业务损失，把损失降到最低。
2. 保证客户端的更新率，无须用户进行版本升级安装
3. 良好的用户体验，无感知修复异常。节省用户下载安装成本。

# 怎么选择热修复技术方案？

## 国内主流的技术方案

1、阿里系

| 名称                                                         | 说明                                  |
| ------------------------------------------------------------ | ------------------------------------- |
| [AndFix ](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Falibaba%2FAndFix) | 开源，实时生效                        |
| [HotFix](https://links.jianshu.com/go?to=http%3A%2F%2Fbaichuan.taobao.com%2Fproduct%2Fhotfix.htm%3Fspm%3Da3c0d.7662652.1998907869.2.309abe485ZwUCh) | 阿里百川，未开源，免费、实时生效      |
| [Sophix](https://links.jianshu.com/go?to=https%3A%2F%2Fhelp.aliyun.com%2Fproduct%2F51340.html%3Fspm%3Da2c4g.11186623.6.540.61a72ef2DAZ30l) | 未开源，商业收费，实时生效/冷启动修复 |

HotFix是AndFix的优化版本，Sophix是HotFix的优化版本。目前阿里系主推是Sophix。

2、腾讯系

| 名称                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Qzone超级补丁](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI1MTA1MzM2Nw%3D%3D%26mid%3D400118620%26idx%3D1%26sn%3Db4fdd5055731290eef12ad0d17f39d4a) | QQ空间，未开源，冷启动修复                                   |
| [QFix](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Falexclin0188%2FQFixPatch) | 手Q团队，开源，冷启动修复                                    |
| [Tinker](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.tinkerpatch.com%2FDocs%2Fintro) | 微信团队，开源，冷启动修复。提供分发管理，[基础版免费](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.tinkerpatch.com%2FPrice) |

3、其他

| 名称                                                         | 说明                       |
| ------------------------------------------------------------ | -------------------------- |
| [Robust ](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FMeituan-Dianping%2FRobust) | 美团， 开源，实时修复      |
| [Nuwa](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fjasonross%2FNuwa) | 大众点评，开源，冷启动修复 |
| [Amigo](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Feleme%2FAmigo) | 饿了么，开源，冷启动修复   |

## 方案对比

| 方案对比   | Sophix               | Tinker                       | nuwa | AndFix | Robust | Amigo |
| ---------- | -------------------- | ---------------------------- | ---- | ------ | ------ | ----- |
| 类替换     | yes                  | yes                          | yes  | no     | no     | yes   |
| So替换     | yes                  | yes                          | no   | no     | no     | yes   |
| 资源替换   | yes                  | yes                          | yes  | no     | no     | yes   |
| 全平台支持 | yes                  | yes                          | yes  | no     | yes    | yes   |
| 即时生效   | 同时支持             | no                           | no   | yes    | yes    | no    |
| 性能损耗   | 较少                 | 较小                         | 较大 | 较小   | 较小   | 较小  |
| 补丁包大小 | 小                   | 较小                         | 较大 | 一般   | 一般   | 较大  |
| 开发透明   | yes                  | yes                          | yes  | no     | no     | yes   |
| 复杂度     | 傻瓜式接入           | 复杂                         | 较低 | 复杂   | 复杂   | 较低  |
| Rom体积    | 较小                 | Dalvik较大                   | 较小 | 较小   | 较小   | 大    |
| 成功率     | 高                   | 较高                         | 较高 | 一般   | 最高   | 较高  |
| 热度       | 高                   | 高                           | 低   | 低     | 高     | 低    |
| 开源       | no                   | yes                          | yes  | yes    | yes    | yes   |
| 收费       | 收费（设有免费阈值） | 收费（基础版免费，但有限制） | 免费 | 免费   | 免费   | 免费  |
| 监控       | 提供分发控制及监控   | 提供分发控制及监控           | no   | no     | no     | no    |

> 参考Tinker及Sophix官方对比



以美团的Robust为例，Robust 的原理可以简单描述为：

1、打基础包时插桩，在每个方法前插入一段类型为 ChangeQuickRedirect 静态变量的逻辑，插入过程对业务开发是完全透明

2、加载补丁时，从补丁包中读取要替换的类及具体替换的方法实现，新建ClassLoader加载补丁dex。当changeQuickRedirect不为null时，可能会执行到accessDispatch从而替换掉之前老的逻辑，达到fix的目的

![](https://upload-images.jianshu.io/upload_images/5125122-fc799bbd4a587560.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

Robust 官方介绍示例图

下面通过Robust的源码来进行分析。
 首先看一下打基础包是插入的代码逻辑，如下：



```tsx
public static ChangeQuickRedirect redirect;
protected void onCreate(Bundle bundle) {
        //为每个方法自动插入修复逻辑代码，如果ChangeQuickRedirect为空则不执行
        if (redirect != null) {
            if (PatchProxy.isSupport(new Object[]{bundle}, this, redirect, false, 78)) {
                PatchProxy.accessDispatchVoid(new Object[]{bundle}, this, redirect, false, 78);
                return;
            }
        }
        super.onCreate(bundle);
        ...
    }
```

Robust的核心修复源码如下：



```java
public class PatchExecutor extends Thread {
    @Override
    public void run() {
        ...
        applyPatchList(patches);
        ...
    }
    /**
     * 应用补丁列表
     */
    protected void applyPatchList(List<Patch> patches) {
        ...
        for (Patch p : patches) {
            ...
            currentPatchResult = patch(context, p);
            ...
            }
    }
     /**
     * 核心修复源码
     */
    protected boolean patch(Context context, Patch patch) {
        ...
        //新建ClassLoader
        DexClassLoader classLoader = new DexClassLoader(patch.getTempPath(), context.getCacheDir().getAbsolutePath(),
                null, PatchExecutor.class.getClassLoader());
        patch.delete(patch.getTempPath());
        ...
        try {
            patchsInfoClass = classLoader.loadClass(patch.getPatchesInfoImplClassFullName());
            patchesInfo = (PatchesInfo) patchsInfoClass.newInstance();
            } catch (Throwable t) {
             ...
        }
        ...
        //通过遍历其中的类信息进而反射修改其中 ChangeQuickRedirect 对象的值
        for (PatchedClassInfo patchedClassInfo : patchedClasses) {
            ...
            try {
                oldClass = classLoader.loadClass(patchedClassName.trim());
                Field[] fields = oldClass.getDeclaredFields();
                for (Field field : fields) {
                    if (TextUtils.equals(field.getType().getCanonicalName(), ChangeQuickRedirect.class.getCanonicalName()) && TextUtils.equals(field.getDeclaringClass().getCanonicalName(), oldClass.getCanonicalName())) {
                        changeQuickRedirectField = field;
                        break;
                    }
                }
                ...
                try {
                    patchClass = classLoader.loadClass(patchClassName);
                    Object patchObject = patchClass.newInstance();
                    changeQuickRedirectField.setAccessible(true);
                    changeQuickRedirectField.set(null, patchObject);
                    } catch (Throwable t) {
                    ...
                }
            } catch (Throwable t) {
                 ...
            }
        }
        return true;
    }
}
```



### 优点

- 高兼容性（Robust只是在正常的使用DexClassLoader）、高稳定性，修复成功率高达99.9%
- 补丁实时生效，不需要重新启动
- 支持方法级别的修复，包括静态方法
- 支持增加方法和类
- 支持ProGuard的混淆、内联、优化等操作

### 缺点

- 代码是侵入式的，会在原有的类中加入相关代码
- so和资源的替换暂时不支持
- 会增大apk的体积，平均一个函数会比原来增加17.47个字节，10万个函数会增加1.67M

 
