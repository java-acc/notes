> hook `Java`层代码都写在`Java.perform`中

``` javascript
setImmediate(function () {
    Java.perform(function () {
        console.log('[*] Frida Attached');
    });
});
```

##### 延迟加载
``` javascript
setTimeout(function () {
    Java.perform(function () {
        console.log("delay hook");
    });
}, 5000);
```

##### Hook Java层
``` javascript
Java.perform(function () {
	Java.enumerateClassLoaders({
	    onMatch: function (loader) {
	        try {
	            if (loader.findClass("com.xxx.ClassName")) {
		            // 切换为对应的ClassLoader 应对split dex / 动态 dex
	                Java.classFactory.loader = loader;
	                console.log("loader found");
	            }
	        } catch (e) {}
	    },
	    onComplete: function () {}
	});
}
```