##### Oppo手机适配

随着硬件的发展，手机的屏幕形态也呈现出多样化。OPPO即将推出一款屏幕高宽比更大、带有刘海的凹形屏幕的手机，其屏幕规格如下：

![img](https://cdofs.oppomobile.com/cdo-portal/201803/05/296d865aefaa93f0bccabacfb91c0389.png)



图左：全屏显示示意图，绿色区域为可显示区域

图右：16：9显示示意图，绿色区域为可显示区域



采用宽度为1080px,  高度为2280px的圆弧显示屏。 屏幕顶部凹形区域不能显示内容，宽度为324px,  高度为80px。



##### 2. **如何识别凹形屏**

本次凹形屏规格的机型型号：

PAAM00

PAAT00

PACM00

PACT00

CPH1831

CPH1833

上述机型的屏幕规格完全相同，不需分别做差异化处理，统一适配即可。



如何识别凹形屏：

context.getPackageManager().hasSystemFeature(“com.oppo.feature.screen.heteromorphism”)，返回 true为凹形屏 ，可识别OPPO的手机是否为凹形屏。





##### 3. **应用适配布局说明**

您在设计应用界面布局时，请确保布局填满屏幕，并且内容无论横屏或竖屏显示都不会被屏幕凹形槽遮蔽。

![img](https://cdofs.oppomobile.com/cdo-portal/201803/05/95f0b4436ce196e2184d47908dc3dccb.png)

应用横屏全屏显示



![img](https://cdofs.oppomobile.com/cdo-portal/201803/05/f00cacf87d413eee03f9c2eb790b1b6b.png)

应用竖屏全屏显示



##### 4. **应用适配内容**

应用完整的全面屏显示，将为用户带来极致的全屏效果体验，增强应用的用户粘性。



适配具体内容：

1、声明全屏显示。

2、适配沉浸式状态栏，避免状态栏部分显示应用具体内容。

3、如果应用可横排显示，避免应用两侧的重要内容被遮挡。



![img](https://cdofs.oppomobile.com/cdo-portal/201803/12/8e8fb3d93bf60576f0a70f48c9c714d0.png)

应用横屏全屏显示





![img](https://cdofs.oppomobile.com/cdo-portal/201803/12/6a274654975dc8a11d927f90fc6157dd.png)

应用竖屏全屏显示





##### 5. **常见问题**

1）PPT中介绍的凹形屏的规格是否固定的？以后其他机型是否会有变化？

公告针对上述机型手机，不针对后续机型。后续机型凹型屏的大小、尺寸、位置可能变化。



2）凹型屏是否有统一处理逻辑？

目前在设置 -- 显示 -- 应用全屏显示 -- 凹形区域显示控制，里面有关闭凹形区域开关，用户可通过这个关闭凹形区域避免遮挡（原则可参考google android P 设计说明）。



3）后续是否会兼容Android P？

Google Android P 会有标准的API获取凹形屏，后续会按照标准API提供适配方案，并兼容老方案。







##### 6. **如何适配全面屏手机**

根据谷歌兼容性（CTS）标准要求,应用必须按以下方式中的任意一种，在AndroidManifest.xml中配置方可全屏显示，否则将以非全屏显示。

方式一：配置支持最大高宽比

\* <meta-data android:name="android.max_aspect"  android:value="ratio_float" />

\* android:maxAspectRatio="ratio_float"   （API LEVEL 26）

说明：以上两种接口可以二选一，ratio_float = 屏幕高 / 屏幕宽 （如oppo新机型屏幕分辨率为2280 x 1080， ratio_float = 2280 / 1080 = 2.11，建议设置 ratio_float为2.2或者更大）



方式二：支持分屏，注意验证分屏下界面兼容性

android:resizeableActivity="true"  

建议采用方式二适配支持全面屏。

详见官方文档：https://source.android.google.cn/compatibility/cdd?hl=zh-cn
