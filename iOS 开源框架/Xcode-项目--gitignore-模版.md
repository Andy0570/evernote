```bash
# Xcode
#
# gitignore contributors: remember to update Global/Xcode.gitignore, Objective-C.gitignore & Swift.gitignore

## Build generated  —构建产生的目录和文件
build/
DerivedData/

## Various settings  —各种设置
*.pbxuser
!default.pbxuser
*.mode1v3
!default.mode1v3
*.mode2v3
!default.mode2v3
*.perspectivev3
!default.perspectivev3
xcuserdata/

## Other  -其它
*.moved-aside
*.xccheckout
*.xcscmblueprint

## Obj-C/Swift specific  -Obj-C/Swift 相关的特定文件
*.hmap
*.ipa
*.dSYM.zip
*.dSYM

# CocoaPods  -CocoaPods 第三方Pods库目录
#
# We recommend against adding the Pods directory to your .gitignore. However
# you should judge for yourself, the pros and cons are mentioned at:
# https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control

Pods/

# Carthage  —Carthage 构建目录
#
# Add this line if you want to avoid checking in source code from Carthage dependencies.
# Carthage/Checkouts

Carthage/Build

# fastlane  -fastlane 构建产生的文件
#
# It is recommended to not store the screenshots in the git repo. Instead, use fastlane to re-generate the
# screenshots whenever they are needed.
# For more information about the recommended setup visit:
# https://docs.fastlane.tools/best-practices/source-control/#source-control

fastlane/report.xml
fastlane/Preview.html
fastlane/screenshots
fastlane/test_output

# Code Injection  -代码注入工具产生的目录及文件
#
# After new code Injection tools there's a generated folder /iOSInjectionProject
# https://github.com/johnno1962/injectionforxcode

iOSInjectionProject/
```

### .gitignore 忽略规则配置说明

通配符 | 描述
-------------- | -------------- 
`/` | 目录
`*` | 通配多个字符
`?` | 通配单个字符
`[]` | 包含单个字符的匹配列表
`!` | 不忽略（跟踪）匹配到的文件或目录
`/mtk/` | 过滤整个文件夹
`*.zip` | 过滤所有.zip文件
`/mtk/do.c` | 过滤某个具体文件

### 参考

* [https://www.gitignore.io/api/objective-c](https://www.gitignore.io/api/objective-c)
