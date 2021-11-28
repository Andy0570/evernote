### UIRefreshControl
示例代码
```
@implementation MyViewController
{
    UIRefreshControl * refreshControl;
}

- (void)viewDidLoad {
    [super viewDidLoad];
        
    // 刷新控件
    refreshControl = [[UIRefreshControl alloc] init];
    [refreshControl addTarget:self action:@selector(refreshCallback) forControlEvents:UIControlEventValueChanged];
    [self.tableView addSubview:refreshControl];
    
    [self prepareDemoData];
}

#pragma mark - IBActions

// 刷新控件触发方法
-(void) refreshCallback
{
    // Run job...
    [_tableView reloadData];
    [refreshControl endRefreshing];
}
```

### 推荐阅读
* [Medium：Loading Indicators](https://medium.com/@ann.green/loading-indicators-e9d9ac9680b)
