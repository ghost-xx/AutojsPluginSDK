# Auto.js-Plugin-SDK0.2


#插件配置# 
1. 新建PluginHelloWorld文件，继承于

```
Plugin.public class PluginHelloWorld extends Plugin {

    public PluginHelloWorld(Context context, Context selfContext, Object runtime, Object topLevelScope) {
        super(context, selfContext, runtime, topLevelScope);
    }

    // 返回插件的JavaScript胶水层代码的assets目录路径
    @Override
    public String getAssetsScriptDir() {
        return "plugin-helloworld";
    }

    // 插件public API，可被JavaScript代码调用
    public String getStringFromJava() {
        return "Hello, Auto.js!";
    }

    // 插件public API，可被JavaScript代码调用
    public void say(String message) {
        getSelfContext().startActivity(new Intent(getSelfContext(),  HelloWorldActivity.class)
                .putExtra("message", message)
                .addFlags(Intent.FLAG_ACTIVITY_NEW_TASK));
    }
}
```
# 2. 新增MyPluginRegistry文件，继承于

```
PluginRegistry:public class MyPluginRegistry extends PluginRegistry {

    static {
        // 注册默认插件
        registerDefaultPlugin(new PluginLoader() {
            @Override
            public Plugin load(Context context, Context selfContext, Object runtime, Object topLevelScope) {
                return new PluginHelloWorld(context, selfContext, runtime, topLevelScope);
            }
        });
    }
}

```
在AndroidManifest.xml中配置以下meta-data, name为"org.autojs.plugin.sdk.registry"，value为MyPluginRegistry的包名。

```
  <application
        ...>

        <meta-data
            android:name="org.autojs.plugin.sdk.registry"
            android:value="org.autojs.plugin.sdk.demo.MyPluginRegistry" />

        <activity
            android:name=".HelloWorldActivity"
            android:exported="true" />

        <service
            android:name=".HelloworldPluginService"
            android:exported="true" />
    </application>
    
 ```
# 3. 编写JavaScript胶水层

在assets的相应目录(由Plugin.getAssetsScriptDir返回)中添加index.js文件，用于作为胶水层导出插件API。
```
module.exports = function (plugin) {
    let runtime = plugin.runtime;
    let scope = plugin.topLevelScope;

    function helloworld() {
    }

    helloworld.stringFromJava = plugin.getStringFromJava();

    helloworld.say = function (message) {
        plugin.say(message);
    }

    return helloworld;
}
```
# 4. 在Auto.js Pro中调用编译插件为apk(assembleDebug/assembleRelease)，安装到设备上。在Auto.js Pro中使用以下代码调用：let helloworld = $plugins.load("org.autojs.plugin.sdk.demo");
```
console.log(helloworld.stringFromJava);
helloworld.say("Hello, Auto.js Pro Plugin");
```


