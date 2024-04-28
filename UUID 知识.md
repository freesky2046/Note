## UUID历史

### iOS 6.0

`uniqueIdentifier`作为手机唯一标识，苹果感觉这样会泄露用户隐藏，废弃，使用`identifierForVendor`代替

但发现卸载后再重装，就会改变，不能当做手机的唯一标识使用。程序员们想到了使用WiFi的mac地址来作为手机的唯一标识，很欢乐的使用着

### iOS 7.0

苹果再一次无情的封杀mac地址，程序员们想到了用广告标示符`advertisingIdentifier`作为手机唯一的标识.

只要用户重置了广告标示符以后，还是会获得一个新的广告标识.最终使用KeyChain来保存获取到的唯一标示符呢，这样以后即使APP删了再装回来，也可以从KeyChain中读取回来

### FCUUID

`FCUUID` 就是基于钥匙串的原理，虽然用户抹掉整个手机的内容时，这个唯一标识也会发生变动，但这也可以满足大部分app的使用场景了。

`FCUUID` 提供了很多级别的方法，`uuidForDevice`可以做为 `uuid`唯一标识使用

```swift
// 每次运行应用都会变
+(NSString *)uuid;

//changes each time (no persistent), but allows to keep in memory more temporary uuids
+(NSString *)uuidForKey:(id<NSCopying>)key;

// 每次运行应用都会变
+(NSString *)uuidForSession;

// 重新安装的时候会变
+(NSString *)uuidForInstallation;

// 卸载后重装会变
+(NSString *)uuidForVendor;

// 抹掉iPhone的时候才会变，适合做唯一标识
+(NSString *)uuidForDevice;
```