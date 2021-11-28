* **GitHub地址**：[**XcodeSourceEditorExtension-Alignment**](https://github.com/tid-kijyun/XcodeSourceEditorExtension-Alignment)
* **star**：100+



> 💡 这是一个 Xcode 8 插件，以下是我对官方 README.md 文档的翻译。

# 对齐 

这是一个 Xcode 代码编辑器扩展，用于对齐属性声明。

![c](https://cloud.githubusercontent.com/assets/1665104/16102266/ae4a0b24-33a8-11e6-943e-9a00eb6e9802.gif)

## 安装：
从 Mac App Store 下载安装：

[![download_on_the_mac_app_store_badge_us-uk_165x40](https://cloud.githubusercontent.com/assets/1665104/19629909/de5c7a64-99b9-11e6-88fc-2a39c8849920.png)](https://itunes.apple.com/us/app/alignment-for-xcode/id1168397789?ls=1&mt=12)

![system preferences](https://cloud.githubusercontent.com/assets/1665104/19801236/5dc1dfec-9d39-11e6-8dc3-8cff3d7e9921.png)

![extensions](https://cloud.githubusercontent.com/assets/1665104/19801299/a45e026e-9d39-11e6-8cec-a82f30aecd9a.png)

## 使用:

1. 打开 Xcode 8
2. 选中你的代码
3. 选择菜单 `Editor > Alignment`


## 设置:
![screen shot 2017-02-09 at 17 09 15](https://cloud.githubusercontent.com/assets/1665104/22777668/521450de-eef7-11e6-98a3-82fe4e1b9188.png)
* Align assignment  ——对齐属性
    选中后，对齐属性功能启用。

  ```
  let value = 0
  let i = 0
  ```

  ```
  let value = 0
  let i     = 0
  ```

* Align type declaration  ——对齐类型声明
  选中后，对齐类型声明功能启用。

  ```
  func sort(from: Int,
            to: Int) {
  }
  ```

  ```
  func sort(from : Int,
            to   : Int) {
  }
  ```

## 无需安装即可使用:

1. 安装 Xcode 8
2. 运行 Xcode 8 并安装其他系统组件
3. 打开 Xcode 8 中的项目然后运行扩展
4. 选择App并且运行: Xcode 8
5. 选择你的代码
6. 选择菜单 `Editor > Alignment`

许可:
=================
The MIT License. See the LICENSE file for more infomation.
