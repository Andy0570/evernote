[PINCache](https://github.com/pinterest/PINCache) 是一个适用于 iOS，tvOS 和 OS X 的快速、无死锁的并行对象缓存框架。

[PINCache](https://github.com/pinterest/PINCache) 是 [TMCache](https://github.com/TumblrArchive/TMCache) 项目的一个分支，它对 [TMCache](https://github.com/TumblrArchive/TMCache) 进行了重构，以修复大量使用导致的死锁问题。它是一个键/值存储，设计用于持久化临时对象，这些临时对象的重现成本很高，比如下载的数据或处理速度慢的结果。它由两个自相似存储组成，一个在内存中（`PINMemoryCache`），另一个在磁盘上（`PINDiskCache`），都由 GCD 支持，可以安全地从多个线程同时访问。在 iOS 系统中，当应用程序收到内存警告或进入后台时，`PINMemoryCache` 会自行清除。而存储在 `PINDiskCache` 中的对象会保留下来，直到你自己手动或通过设置字节或缓存时间限制来删除缓存。

`PINCache` 和  `PINDiskCache` 接受任何遵守 `<NSCoding>` 协议的对象。设置缓存的方式如下：

**Objective-C**

```objectivec
UIImage *img = [[UIImage alloc] initWithData:data scale:[[UIScreen mainScreen] scale]];
[[PINCache sharedCache] setObject:img forKey:@"image" block:nil]; // 立即返回
```
**Swift**
```swift
let img = UIImage(data: data, scale:UIScreen.main.scale)
PINCache.shared().setObject(img, forKey: "img")
```

取出缓存的方式如下：

**Objective-C**
```objectivec
[[PINCache sharedCache] objectForKeyAsync:@"image" block:^(PINCache *cache, NSString *key, id object) {
    UIImage *image = (UIImage *)object;
    NSLog(@"image scale: %f", image.scale);
}];
```
**Swift**
```swift
PINCache.shared().object(forKey: "image") { (cache, key, object) in
    if let image = object as? UIImage {
        print("image scale: %f", image.scale)
    }
}
```

`PINMemoryCache` 和 `PINDiskCache` 都使用锁来保护读和写。[PINCache](https://github.com/pinterest/PINCache) 对它们进行协调，使添加到内存中的对象在后台安全地写入磁盘的同时，可以立即被其他线程使用。这两个缓存都是 [PINCache](https://github.com/pinterest/PINCache) 的公共属性，所以如果需要的话，可以很容易地分别操作其中一个或另一个。

集合对象也可以使用。得益于 `NSKeyedArchiver` 的魔力，一个集合中重复的对象只占用磁盘上的一个空间：

**Objective-C**
```objective-c
NSArray *images = @[ image, image, image ];
[[PINCache sharedCache] setObject:images forKey:@"images"];
NSLog(@"3 for the price of 1: %d", [[[PINCache sharedCache] diskCache] byteCount]);
```
**Swift**
```swift
// In Swift, Array, String, and Dictionary are all value types.
let images = [image, image, image] as NSArray // Cast to NSArray
PINCache.shared.setObject(images, forKey: "images")
print("3 for the prices of 1: %d", PINCache.shared.diskCache.byteCount)
```

## 安装

### 手动安装

[Download the latest tag](https://github.com/pinterest/PINCache/tags) and drag the `PINCache` folder into your Xcode project.

Install the docs by double clicking the `.docset` file under `docs/`, or view them online at [cocoadocs.org](http://cocoadocs.org/docsets/PINCache/)

### Git Submodule

    git submodule add https://github.com/pinterest/PINCache.git
    git submodule update --init

### CocoaPods

Add [PINCache](http://cocoapods.org/?q=name%3APINCache) to your `Podfile` and run `pod install`.

### Carthage

Add the following line to your `Cartfile` and run `carthage update --platform ios`. Then follow [this instruction of Carthage](https://github.com/carthage/carthage#adding-frameworks-to-unit-tests-or-a-framework) to embed the framework.

```github "pinterest/PINCache"```

## Requirements

__PINCache__ requires iOS 8.0, tvOS 9.0, watchOS 2.0 or OS X 10.8 and greater.

## Contact

[Garrett Moon](mailto:garrett@pinterest.com)

## License

Copyright 2013 Tumblr, Inc.
Copyright 2015 Pinterest, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. [See the License](LICENSE.txt) for the specific language governing permissions and limitations under the License.

