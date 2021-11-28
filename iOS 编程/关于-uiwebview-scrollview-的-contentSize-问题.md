动态改变 webview 的 frame 以达到 webview 刚好填充contentSize的效果
```
// 在Y方向给contentSize足够的空间
_webView.scrollView.contentSize = CGSizeMake(self.view.bounds.size.width, MAXFLOAT);
// 加载本地HTML数据
[_webView loadHTMLString:html baseURL:nil];

// web视图加载完成
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation {
    // 在加载完毕之后获取webview内容所需要的空间
    CGSize size = [webView sizeThatFits:CGSizeMake(self.view.bounds.size.width, MAXFLOAT)];
    CGRect frame = CGRectMake(0, 0, size.width, size.height);
    webView.frame = frame;
    // 调整好frame之后再调整contentSize
    webView.scrollView.contentSize = CGSizeMake(0, 0);
}
```
