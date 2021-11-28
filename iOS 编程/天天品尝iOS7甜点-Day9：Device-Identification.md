### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 9 :: Device Identification](http://blog.kingiol.com/2014/01/10/ios7-day-by-day-day9-device-identification/)



### Vendor Identification

`UIDevice`中的`identifierForVendor`方法是最接近替代`uniqueIdentifier`的另外一个方法，它返回`NSUUID`。在相同的设备中相同供应商的app共享一个UUID。不同的供应商在同一个设备上面将会返回不同的`identifierForVendor`值，就像相同供应商在不同设备上面一样。

对于开发者来说，这个值提供了和原来相似的功能，而且没有用户隐私的问题。

但是美中不足的是**如果用卸载了供应商下面所有的app，这个id就会被销毁，重新安装之后就会生成一个新的供应商ID。**

```objective-c
NSString *identifeir = [[[UIDevice currentDevice] identifierForVendor] UUIDString];
```



### Advertising Identification

`ADSupprt`模块包含了`ASIdentifierManager`类，它有一个`advertisingIdentifier`方法。它返回一个`NSUUID`可以用来达到追踪广告的目的。

还有一个方法`avertisingTrackingEnable`.它返回一个BOOL类型的数据用来指定是否用户允许进行广告追踪。

```objective-c
@import AdSupport;
// ...

NSString *identifier = [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];

NSString *isEnabled = [[ASIdentifierManager sharedManager] isAdvertisingTrackingEnabled] ? @"YES" : @"NO";
```



### Network Identification

当 `uniqueIdentifier` 被弃用了，通过使用MAC地址变得很流行。一个MAC地址是恒定不变的且唯一的，可以用来跟踪用户。但是在iOS7中Apple对这个也添加了限制，所以实际返回的MAC地址为: 02:00:00:00:00:00. 关闭这个“漏洞”，将会推动开发者运用Apple提供的优先方法来进行获得设备标识.



```objective-c
#include <sys/socket.h>
#include <sys/sysctl.h>
#include <net/if.h>
#include <net/if_dl.h>

//...

- (NSString *)getMacAddress {
    int                 mgmtInfoBase[6];
    char                *msgBuffer = NULL;
    NSString            *errorFlag = NULL;
    size_t              length;
    
    // Setup the management Information Base (mib)
    mgmtInfoBase[0] = CTL_NET;        // Request network subsystem
    mgmtInfoBase[1] = AF_ROUTE;       // Routing table info
    mgmtInfoBase[2] = 0;
    mgmtInfoBase[3] = AF_LINK;        // Request link layer information
    mgmtInfoBase[4] = NET_RT_IFLIST;  // Request all configured interfaces
    
    // With all configured interfaces requested, get handle index
    if ((mgmtInfoBase[5] = if_nametoindex("en0")) == 0)
        errorFlag = @"if_nametoindex failure";
    // Get the size of the data available (store in len)
    else if (sysctl(mgmtInfoBase, 6, NULL, &length, NULL, 0) < 0)
        errorFlag = @"sysctl mgmtInfoBase failure";
    // Alloc memory based on above call
    else if ((msgBuffer = malloc(length)) == NULL)
        errorFlag = @"buffer allocation failure";
    // Get system information, store in buffer
    else if (sysctl(mgmtInfoBase, 6, msgBuffer, &length, NULL, 0) < 0)
    {
        free(msgBuffer);
        errorFlag = @"sysctl msgBuffer failure";
    }
    else
    {
        // Map msgbuffer to interface message structure
        struct if_msghdr *interfaceMsgStruct = (struct if_msghdr *) msgBuffer;
        
        // Map to link-level socket structure
        struct sockaddr_dl *socketStruct = (struct sockaddr_dl *) (interfaceMsgStruct + 1);
        
        // Copy link layer address data in socket structure to an array
        unsigned char macAddress[6];
        memcpy(&macAddress, socketStruct->sdl_data + socketStruct->sdl_nlen, 6);
        
        // Read from char array into a string object, into traditional Mac address format
        NSString *macAddressString = [NSString stringWithFormat:@"%02X:%02X:%02X:%02X:%02X:%02X",
                                      macAddress[0], macAddress[1], macAddress[2], macAddress[3], macAddress[4], macAddress[5]];
        
        // Release the buffer memory
        free(msgBuffer);
        
        return macAddressString;
    }
    
    return errorFlag;
}
```



### 关于 identity

关于 UUID （设备唯一标识符）我之前也有研究过，可以戳这里：

[iOS—获取设备型号和App版本号等信息](http://www.jianshu.com/p/40b9f8ec1c78)

得到的最终方案是： `UIDevice`中的`identifierForVendor`方法配合 keychain（钥匙串）存储标识符。
