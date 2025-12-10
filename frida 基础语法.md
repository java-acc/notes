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
	
	// 枚举所有类 (找hook点)
	Java.enumerateLoadedClasses({
	    onMatch: function (name) {
	        if (name.indexOf("login") !== -1) {
	            console.log(name);
	        }
	    },
	    onComplete: function () {}
	});
	

	var clazzA = Java.use("com.xxx.Test");
	// ============================实例============================
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
	// 重载方法
	A.test.overload('int', 'java.lang.String').implementation = function (i, s) {
	    return 0;
	};
	
	// ============================静态============================
	// hook 静态方法
	A.staticMethod.implementation = function () {
	    return 1;
	};
	// 调用静态方法
	A.staticMethod(args)
	
	// 调用静态属性
	A.STATIC_FIELD.value = xxx;
	var f = A.someField.value;
	A.someField.value = null;
}
```