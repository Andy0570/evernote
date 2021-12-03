### Runtime

* [Objective-C Runtime 大佬系列文章整理](https://kangzubin.com/objc-runtime-articles/)
* [神经病院 Objective-C Runtime 入院第一天 —— isa 和 Class](https://github.com/halfrost/Halfrost-Field/blob/master/contents/iOS/ObjC/objc_runtime_isa_class.md)
* [神经病院 Objective-C Runtime 住院第二天 —— 消息发送与转发](https://github.com/halfrost/Halfrost-Field/blob/master/contents/iOS/ObjC/objc_runtime_objc_msgsend.md)
* [神经病院 Objective-C Runtime 出院第三天 —— 如何正确使用 Runtime](https://github.com/halfrost/Halfrost-Field/blob/master/contents/iOS/ObjC/how_to_use_runtime.md)
* [Objc Runtime](https://github.com/ming1016/study/wiki/Objc-Runtime)
* [神经病院 objc runtime 入院考试](http://blog.sunnyxx.com/2014/11/06/runtime-nuts/)
* [重识 Objective-C Runtime - Smalltalk 与 C 的融合](http://blog.sunnyxx.com/2016/08/13/reunderstanding-runtime-0/)
* [重识 Objective-C Runtime - 看透 Type 与 Value](http://blog.sunnyxx.com/2016/08/13/reunderstanding-runtime-1/)
* [从 NSObject 的初始化了解 isa](https://github.com/draveness/analyze/blob/master/contents/objc/%E4%BB%8E%20NSObject%20%E7%9A%84%E5%88%9D%E5%A7%8B%E5%8C%96%E4%BA%86%E8%A7%A3%20isa.md)
* [深入解析 ObjC 中方法的结构](https://github.com/draveness/analyze/blob/master/contents/objc/%E6%B7%B1%E5%85%A5%E8%A7%A3%E6%9E%90%20ObjC%20%E4%B8%AD%E6%96%B9%E6%B3%95%E7%9A%84%E7%BB%93%E6%9E%84.md)
* [Objc 对象的今生今世](https://github.com/halfrost/Halfrost-Field/blob/master/contents/iOS/ObjC/objc_life.md)
* [深入理解 Objective-C：Category](https://tech.meituan.com/2015/03/03/diveintocategory.html)
* [iOS 开发之 Runtime 常用示例总结](https://www.cnblogs.com/ludashi/p/6294112.html)
* [翻译 - 为什么 objc_msgSend 必须用汇编实现](http://tutuge.me/2016/06/19/translation-why-objcmsgsend-must-be-written-in-assembly/)

### Aspect 面向切片编程

[Aspects](https://github.com/steipete/Aspects) 是一个轻量级的面向切面编程的库。它能允许你在每一个类和每一个实例中存在的方法里面加入任何代码。可以在以下切入点插入代码：before (在原始的方法前执行) / instead (替换原始的方法执行) / after (在原始的方法后执行，默认)。通过 Runtime 消息转发实现 Hook。Aspects 会自动的调用 super 方法，使用 method swizzling 起来会更加方便。






* [iOS 如何实现 Aspect Oriented Programming](https://github.com/halfrost/Halfrost-Field/blob/master/contents/iOS/Aspect/ios_aspect.md)
* [从 Aspects 源码中我学到了什么？](https://lision.me/aspects/)



