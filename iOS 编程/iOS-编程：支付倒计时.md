* 15分钟支付倒计时

```
// 支付倒计时
- (void)secondsCountDown:(UILabel *)label {
    __block int timeout = 900;
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_source_t _timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
    dispatch_source_set_timer(_timer, dispatch_walltime(NULL, 0), 1.0*NSEC_PER_SEC, 0);//每秒执行
    dispatch_source_set_event_handler(_timer, ^{
        if (timeout<=0) {
            dispatch_source_cancel(_timer);
            dispatch_async(dispatch_get_main_queue(), ^{
                // 隐藏密码输入界面，禁用支付按钮
                [MBProgressHUD showMessage:@"付款超时，请返回重新选择座位"];
            });
        }else {
            int minutes = timeout/60;
            int seconds = timeout%60;
            NSString *strTime = [NSString stringWithFormat:@"%d 分 %d 秒", minutes, seconds];
            dispatch_async(dispatch_get_main_queue(), ^{
                label.text = [NSString stringWithFormat:@"请在 %@ 内完成付款，晚了座位就没有了哦",strTime];
            });
            timeout--;
        }
    });
    dispatch_resume(_timer);
}
```

### 参考
* [iOS中常用的验证码倒计时, 支付半小时倒计时.](http://www.cnblogs.com/siwenss/p/6428903.html)
