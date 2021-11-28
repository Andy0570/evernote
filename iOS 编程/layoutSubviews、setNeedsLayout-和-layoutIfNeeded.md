### layoutSubviews 方法

布局子视图。

```objectivec
- (void)layoutSubviews;
```

> 此方法的默认实现在 iOS 5.1 及更早的版本中什么都不会做。否则，默认实现使用你已设置的任何约束来决定任何子视图的大小和位置。
>
> 子类可以根据需要覆盖此方法，以执行更精确的子视图布局。**只有当子视图的自动缩放（autoresizing）和基于约束的行为（constraint-based behaviors）不能满足你想要的布局行为时，你才应该覆盖此方法。**你可以覆写（重载）该方法，并在该方法中直接布局子视图。
>
> 你不应该直接调用这个方法。如果你想在下一次系统绘图更新之前强制布局更新，调用 `setNeedsLayout` 方法来完成。如果你想立即更新视图的布局，请调用 `layoutIfNeeded` 方法。
>
> ——摘自 [Apple Documentation > ... > UIView > layoutSubviews](https://developer.apple.com/documentation/uikit/uiview/1622482-layoutsubviews?language=objc)


### setNeedsLayout 方法

使当前布局失效，并**在下一个更新周期内触发布局更新**。

```objectivec
- (void)setNeedsLayout;
```

> 当你想要调整视图中子视图的布局时，在你的应用程序的主线程中调用这个方法。这个方法会记下请求并立即返回。因为这个方法不会强制立即更新，而是等待下一个更新周期，所以你可以在任何一个视图被更新之前，用它来失效多个视图的布局。这种行为允许你将所有的布局更新合并到一个更新周期，这通常对性能更好。
>
> ——摘自 [Apple Documentation > ... > UIView > setNeedsLayout](https://developer.apple.com/documentation/uikit/uiview/1622601-setneedslayout?language=objc)


### layoutIfNeeded 方法

如果当前布局正在等待更新，调用此方法会**立即更新子视图布局**。

```objectivec
- (void)layoutIfNeeded;
```


> 使用此方法可强制视图立即更新其布局。使用自动布局（Auto Layout）时，布局引擎会根据需要更新视图的位置，以满足约束的变化。将接收到消息的视图作为根视图，此方法将从根视图开始布局视图子树。如果当前没有布局在等待更新，则该方法退出，也不会修改布局或调用任何布局相关的回调。
>
> —— 摘自 [Apple Documentation > ... > UIView > layoutIfNeeded](https://developer.apple.com/documentation/uikit/uiview/1622507-layoutifneeded?language=objc)


### requiresConstraintBasedLayout 方法

返回一个布尔值，表示接收者是否依赖于基于约束的布局系统。

```objectivec
@property(class, nonatomic, readonly) BOOL requiresConstraintBasedLayout API_AVAILABLE(ios(6.0));
```
如果视图在窗口中必须使用基于约束的布局才能正常运行时，返回 `YES`，否则返回 `NO`。

当有人试图使用基于约束的布局（constraint-based layout）时，基于 约束的布局会“懒惰”地参与其中（例如，为一个视图添加约束）。 如果你在 `-updateConstraints` 方法中完成了所有的约束设置，如果没有人做约束，你甚至可能永远不会触发` updateConstraints` 消息。 要解决这个鸡和蛋的问题，如果你的窗口需要使用基于约束的布局，那么覆盖这个方法，并返回 `YES`。

如果自定义视图不能正确使用 autoresizing 自动调整大小，则应覆盖此方法，并返回 `YES`，即：

```objectivec
+ (BOOL)requiresConstraintBasedLayout {
    return YES;
}
```


### translatesAutoresizingMaskIntoConstraints 方法

返回一个布尔值，决定是否将视图的 autoresizing mask 转换为 Auto Layout 约束，默认值为 `YES`。

```objectivec
@property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints API_AVAILABLE(ios(6.0)); // Default YES
```

> 如果此属性的值为 "YES"，系统将创建一组约束，这些约束将重复视图的 autoresizing mask 所指定的行为。这也允许您使用视图的 `frame`、 `bounds` 或 `center` 属性来修改视图的大小和位置，使您可以在自动布局中创建静态的、基于 frame 的布局。
> 
> 请注意，autoresizing mask 的约束完全指定了视图的大小和位置；因此，您不能添加额外的约束来修改此尺寸或位置而不引入冲突。如果要使用 Auto Layout 来动态计算视图的大小和位置，必须将此属性设置为 `NO`，然后为视图提供一个不含糊、不冲突的约束集。
> 
> 默认情况下，该属性对于你在程序上创建的任何视图都被设置为 `YES`。如果你在 Interface Builder 中添加视图，系统会自动将此属性设置为 `NO`。
> —— 摘自 [Apple Documentation > ... > UIView > translatesAutoresizingMaskIntoConstraints](https://developer.apple.com/documentation/uikit/uiview/1622572-translatesautoresizingmaskintoco?language=objc)

默认情况下，视图的 autoresizing mask 会生成完全确定视图位置的约束。这使得自动布局系统能够跟踪通过手写视图布局的 frame（例如，通过 `-setFrame:` 方法手动设置视图的 `frame` 属性）。

**当你通过代码方式使用自动布局（Auto Layout）来添加自己的约束并定位视图时，你必须将此属性设置为 `NO`**。而在 Interface Builder 中是自动为你做这件事的。

另外，在使用 [Masonry](https://github.com/SnapKit/Masonry) 框架实现 Auto Layout 自动布局约束时，会自动为你将 `translatesAutoresizingMaskIntoConstraints` 属性设置为 `NO`。








### 参考

* [When is layoutSubviews called?](https://stackoverflow.com/questions/728372/when-is-layoutsubviews-called)
* [layoutSubviews 的调用机制](http://hongchaozhang.github.io/blog/2017/03/28/when-is-layoutsubviews-called/)
* [什么时候应该重写 viewDidLayoutSubviews?](http://sharonhu1990.github.io/%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E5%BA%94%E8%AF%A5%E9%87%8D%E5%86%99viewDidLayoutSubviews.html)

