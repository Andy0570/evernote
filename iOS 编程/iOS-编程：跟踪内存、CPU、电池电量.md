![图片来自Pixabay](http://upload-images.jianshu.io/upload_images/2648731-95c0724fac3b812d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 跟踪可用和已用的内存方法

> 💡在出现低内存警告时对应用进行埋点，包括内存的使用及统计信息，并在应用重新运行时将这些信息上报给服务器。使用这些信息来识别出应用发生内存溢出的常见场景和边缘情况。

#### 示例代码

```objective-c
// HPMemoryAnalyzer.h
#import <Foundation/Foundation.h>

/**
 跟踪可用和已用的内存
 */
@interface HPMemoryAnalyzer : NSObject

vm_size_t getUsedMemory();
vm_size_t getFreeMemory();

@end

// HPMemoryAnalyzer.m
#import "HPMemoryAnalyzer.h"
#import <mach/mach.h>

vm_size_t getUsedMemory() {
    task_basic_info_data_t info;
    mach_msg_type_number_t size = sizeof(info);
    kern_return_t kerr = task_info(mach_task_self(), TASK_BASIC_INFO, (task_info_t)&info, &size);
    
    if (kerr == KERN_SUCCESS) {
        return info.resident_size;
    }else {
        return 0;
    }
}

vm_size_t getFreeMemory() {
    mach_port_t host = mach_host_self();
    mach_msg_type_number_t size = sizeof(vm_statistics_data_t) / sizeof(integer_t);
    vm_size_t pagesize;
    vm_statistics_data_t vmstat;
    
    host_page_size(host, &pagesize);
    host_statistics(host, HOST_VM_INFO, (host_info_t)&vmstat, &size);
    
    return vmstat.free_count * pagesize;
}

@implementation HPMemoryAnalyzer

@end
```

另外，[YYKit](https://github.com/ibireme/YYKit) 框架中也集成了关于获取应用内存使用量、CPU使用量的相关方法：

```objective-c
// UIApplication+YYAdd.h

/// Current thread real memory used in byte. (-1 when error occurs)
@property (nonatomic, readonly) int64_t memoryUsage;

/// Current thread CPU usage, 1.0 means 100%. (-1 when error occurs)
@property (nonatomic, readonly) float cpuUsage;


// UIApplication+YYAdd.m
- (int64_t)memoryUsage {
    struct task_basic_info info;
    mach_msg_type_number_t size = sizeof(info);
    kern_return_t kern = task_info(mach_task_self(), TASK_BASIC_INFO, (task_info_t)&info, &size);
    if (kern != KERN_SUCCESS) return -1;
    return info.resident_size;
}

- (float)cpuUsage {
    kern_return_t kr;
    task_info_data_t tinfo;
    mach_msg_type_number_t task_info_count;
    
    task_info_count = TASK_INFO_MAX;
    kr = task_info(mach_task_self(), TASK_BASIC_INFO, (task_info_t)tinfo, &task_info_count);
    if (kr != KERN_SUCCESS) {
        return -1;
    }
    
    thread_array_t thread_list;
    mach_msg_type_number_t thread_count;
    
    thread_info_data_t thinfo;
    mach_msg_type_number_t thread_info_count;
    
    thread_basic_info_t basic_info_th;
    
    kr = task_threads(mach_task_self(), &thread_list, &thread_count);
    if (kr != KERN_SUCCESS) {
        return -1;
    }
    
    long tot_sec = 0;
    long tot_usec = 0;
    float tot_cpu = 0;
    int j;
    
    for (j = 0; j < thread_count; j++) {
        thread_info_count = THREAD_INFO_MAX;
        kr = thread_info(thread_list[j], THREAD_BASIC_INFO,
                         (thread_info_t)thinfo, &thread_info_count);
        if (kr != KERN_SUCCESS) {
            return -1;
        }
        
        basic_info_th = (thread_basic_info_t)thinfo;
        
        if (!(basic_info_th->flags & TH_FLAGS_IDLE)) {
            tot_sec = tot_sec + basic_info_th->user_time.seconds + basic_info_th->system_time.seconds;
            tot_usec = tot_usec + basic_info_th->system_time.microseconds + basic_info_th->system_time.microseconds;
            tot_cpu = tot_cpu + basic_info_th->cpu_usage / (float)TH_USAGE_SCALE;
        }
    }
    
    kr = vm_deallocate(mach_task_self(), (vm_offset_t)thread_list, thread_count * sizeof(thread_t));
    assert(kr == KERN_SUCCESS);
    
    return tot_cpu;
}
```

除了 **UIApplication+YYAdd** 中这两个方法，你会发现 **UIDevice+YYAdd** 中还有更丰富的方法，包括获取设备信息、磁盘空间、内存信息、CPU信息学。

### 电池电量与代码感知

![](http://upload-images.jianshu.io/upload_images/2648731-f48c8f5f394f63b0.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


一个智能的应用会考虑到电池的电量和自身的状态，从而决定是否要真正执行资源密集消耗型的操作。另外一个有价值的点是对充电的判断，确定设备是否处于充电状态。
例如：每次更新iOS系统时，系统会判断当前设备电量是否低于50%，如果低于这个值，系统是不会执行更新系统的任务的，它会提示用户先充电之后再更新系统。

#### 示例代码

传入执行特定操作需要的最低电量级别。该级别是浮点数，范围在 0～100 之间（100表示电池完全充满） 
```
-(BOOL)shouldProceedWithMinLevel:(int)minLevel
{
	UIDevice *device = [UIDevice currentDevice];
	device.batteryMonitoringEnabled = YES;

	UIDeviceBatteryState state = device.batteryState;
	if(state == UIDeviceBatteryStateCharging || state == UIDeviceBatteryStateFull) {
		return YES;
	}

	int batteryLevel = (int) (device.batteryLevel * 100);
	if(batteryLevel >= minLevel) {
		return YES;
	}
	return NO;
}
```

### 谨慎使用电量的最佳实践

* 最小化硬件使用。换句话说，尽可能晚地与硬件打交道，并且一旦完成任务立即结束使用。
* 在进行密集型任务前，检查电池电量和充电状态。
* 在电量低时，提示用户是否确定要执行任务，并在用户同意后再执行。
* 或提供设置的选项，允许用户定义电量的阈值，以便在执行密集型操作前提示用户。

### 参考

* Gaurav Vaish. 高性能iOS应用开发 [M]. 北京：人民邮电出版社，2017. 66-67
