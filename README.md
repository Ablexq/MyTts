
# 科大讯飞语音合成

SDK在集成开发测试阶段服务量限制为最高500次/每日，完成提额申请或商务合作后可以解除。

[语音合成](https://doc.xfyun.cn/msc_android/%E8%AF%AD%E9%9F%B3%E5%90%88%E6%88%90.html)

[Android_SDK相关问题和答案汇总](http://bbs.xfyun.cn/forum.php?mod=viewthread&tid=24442&extra=page%3D1)

![](imgs/在线合成语音asset.png)

![](imgs/在线合成语音lib.png)

![](imgs/离线合成语音asset.png)

![](imgs/离线合成语音lib.png)


# 模块化多application：

参考：

[rongkun/ComponentJava](https://github.com/rongkun/ComponentJava)

[ DRPrincess/DRComponentDemo](https://github.com/DRPrincess/DRComponentDemo)


每个【应用模块】下的application的name都设置为BaseApplication：
``` 
<application
    android:name="com.example.base.BaseApplication"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme"
    tools:ignore="AllowBackup,GoogleAppIndexingWarning">
</application>
```
BaseApplication中使用反射通过接口动态初始化各个模块的application：
``` 
private void modulesApplicationInit(){
    for (String moduleImpl : ModuleConfig.MODULESLIST){
        try {
            Class<?> clazz = Class.forName(moduleImpl);
            Object obj = clazz.newInstance();
            if (obj instanceof ApplicationImpl){
                ((ApplicationImpl) obj).onCreate(this);
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
    }
}
```

# 权限问题

``` 
java.lang.SecurityException: 
com.example.mytts was not granted  this permission: 
android.permission.WRITE_SETTINGS.
```

在这里，Write_setting有个坑，申请了write_setting权限后，也出现闪退的情况，异常为：
java.lang.IllegalArgumentException: You cannot keep your settings in the secure settings.

在android 6.0及以后，WRITE_SETTINGS权限的保护等级已经由原来的dangerous升级为signature，
这意味着我们的APP需要用系统签名或者成为系统预装软件才能够申请此权限，
并且还需要提示用户跳转到修改系统的设置界面去授予此权限

也就是说，要想申请该权限，apk必须要打包，签名打包。要签名打包，最喜欢的debug模式是不能申请该权限。













