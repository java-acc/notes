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
	

	var clazzA = Java.use("com.xxx.Test");
	
	// 构造函数 有参构造
	A.$init.overload('java.lang.String').implementation = function (s) {
	    console.log("init:", s);
	    return this.$init(s);
	};
	// 构造函数 无参构造
	A.$init.overload().implementation = function () {
	    return this.$init();
	};
	// 普通方法
	clazzA.test.implementation = function (a, b) {
	    console.log(a, b);
	    return this.test(a, b);
	};
	
	// hook 重载方法
	A.test.overload('int', 'java.lang.String').implementation = function (i, s) {
	    return 0;
	};
}
```