原文：[Podspec Syntax Reference](https://guides.cocoapods.org/syntax/podspec.html#specification)



# Specification

Specification 描述了 Pod 库的版本信息。它描述了源码应该从哪里获取、需要使用哪些文件、应用编译设置以及其他一般元数据细节，如其名称、版本和描述。

可以通过 `pod spec create` 命令生成一个规范的 Specification 文件。

Specification  DSL 提供了极大的灵活性和动态性。此外，DSL 采用的是约定俗成的配置方式，因此可以非常简单。

```bash
Pod::Spec.new do |spec|
  spec.name         = 'Reachability'
  spec.version      = '3.1.0'
  spec.license      = { :type => 'BSD' }
  spec.homepage     = 'https://github.com/tonymillion/Reachability'
  spec.authors      = { 'Tony Million' => 'tonymillion@gmail.com' }
  spec.summary      = 'ARC and GCD Compatible Reachability Class for iOS and OS X.'
  spec.source       = { :git => 'https://github.com/tonymillion/Reachability.git', :tag => 'v3.1.0' }
  spec.source_files = 'Reachability.{h,m}'
  spec.framework    = 'SystemConfiguration'
end
```

或者可以非常详细：

```bash
Pod::Spec.new do |spec|
  spec.name          = 'Reachability'
  spec.version       = '3.1.0'
  spec.license       = { :type => 'BSD' }
  spec.homepage      = 'https://github.com/tonymillion/Reachability'
  spec.authors       = { 'Tony Million' => 'tonymillion@gmail.com' }
  spec.summary       = 'ARC and GCD Compatible Reachability Class for iOS and OS X.'
  spec.source        = { :git => 'https://github.com/tonymillion/Reachability.git', :tag => 'v3.1.0' }
  spec.module_name   = 'Rich'
  spec.swift_version = '4.0'

  spec.ios.deployment_target  = '9.0'
  spec.osx.deployment_target  = '10.10'

  spec.source_files       = 'Reachability/common/*.swift'
  spec.ios.source_files   = 'Reachability/ios/*.swift', 'Reachability/extensions/*.swift'
  spec.osx.source_files   = 'Reachability/osx/*.swift'

  spec.framework      = 'SystemConfiguration'
  spec.ios.framework  = 'UIKit'
  spec.osx.framework  = 'AppKit'

  spec.dependency 'SomeOtherPod'
end
```



# Root specification

root 规范存储了一个库的特定版本的信息。

这组中的属性只能写在 'root' 规范上，而不能写在 'sub-specifications' 上。

这组中列出的属性是一个 podspec 唯一需要的属性。

其他组的属性是为了完善 podspec 而提供的，遵循的是一种约定俗成的配置方式。一个 root 规范可以直接或通过 "sub-specifications" 来描述这些属性。



## `name` required

Pod 仓库的名字

### 示例

```bash
spec.name = 'AFNetworking'
```

它也是你在命令行中执行 `pod search` 命令搜索 Pod 库时的关键词，这里一定要和 `.podspec` 的名称一样，否则会报错；



## `version` required

Pod 仓库的版本号，Cocoapods 遵循 [语义化版本规范](https://semver.org/lang/zh-CN/)。

### 示例

```bash
spec.version = '0.0.1'
```

Pod 最新的版本。



## `swift_versions`

支持的 Swift 版本。CocoaPods 将版本 "4"  视为 "4.0"，而不是 "4.1" 或 "4.2"。

注意：Swift 编译器大多接受主要版本，有时也会接受次要版本。尽管 CocoaPods 允许指定次要版本或补丁版本，但 Swift 编译器可能不会完全认可它。

### 示例

```bash
spec.swift_versions = ['3.0']
```

```bash
spec.swift_versions = ['3.0', '4.0', '4.2']
```

```bash
spec.swift_version = '3.0'
```

```bash
spec.swift_version = '3.0', '4.0'
```



## `cocoapods_version`

支持的 CocoaPods 版本，比如某个属性，是某个 CocoaPods 版本以上才有的，这个时候进行依赖安装，就需要指定版本。

### 示例

```bash
spec.cocoapods_version = '>= 0.36'
```



## `authors` required

库（library ）维护者的姓名和电子邮件地址，而不是 Podspec 维护者的姓名和电子邮件地址。

### 示例

```bash
spec.author = 'Darth Vader'
```

```bash
spec.authors = 'Darth Vader', 'Wookiee'
```

```bash
spec.authors = { 'Darth Vader' => 'darthvader@darkside.com',
                 'Wookiee'     => 'wookiee@aggrrttaaggrrt.com' }
```



## `social_media_url`

Pod 维护者的社交账号 URL，CocoaPods 的网络服务可以使用这个。

例如，@CocoaPodsFeed 通知将通过 Twitter 处理（缩短描述），如果URL 是相对于 Twitter 的。这不一定非要是 Twitter 的 URL，但只有 Twitter 账号才可以接收 @CocoaPodsFeed 通知。


### 示例

```bash
spec.social_media_url = 'https://twitter.com/cocoapods'
```

```bash
spec.social_media_url = 'https://groups.google.com/forum/#!forum/cocoapods'
```



## `license` required

Pod 仓库的许可证

除非源文件中包含一个名为 `LICENSE.*` 或 `LICENCE.*` 的文件，否则必须指定许可证文件的路径或通常用于许可证类型通知的完整文本。如果指定了一个许可证文件，它必须是没有文件扩展名的，或者是 txt、md 或 markdown 格式中的一种文件类型。

CocoaPods 使用这些信息来生成确认文件（markdown和plist），这些文件可用于最终应用程序的确认部分。

### 示例

```bash
spec.license = 'MIT'
```

```bash
spec.license = { :type => 'MIT', :file => 'MIT-LICENSE.txt' }
```

```bash
spec.license = { :type => 'MIT', :text => <<-LICENSE
                   Copyright 2012
                   Permission is granted to...
                 LICENSE
               }
```

### 支持的 keys：

`:type`

`:file`

`:text`




## `homepage` required

Pod 主页的 URL。

### 示例

```bash
spec.homepage = 'http://www.example.com'
```



## `source` required

库被检索的位置

### 示例

> 使用 tag 标签指定 Git 源。这也是大多数 OSS 播客的工作原理。

```bash
spec.source = { :git => 'https://github.com/AFNetworking/AFNetworking.git',
                :tag => spec.version.to_s }
```

> 使用前缀为 'v' 的 tag 标签和子模块。

```bash
spec.source = { :git => 'https://github.com/typhoon-framework/Typhoon.git',
                :tag => "v#{spec.version}", :submodules => true }
```

> 使用带有 tag 标签的 Subversion。

```bash
spec.source = { :svn => 'http://svn.code.sf.net/p/polyclipping/code', :tag => '4.8.8' }
```

> 使用与规范的语义版本字符串版本相同的 Mercurial。

```bash
spec.source = { :hg => 'https://bitbucket.org/dcutting/hyperbek', :revision => "#{s.version}" }
```

> 使用 HTTP 下载压缩文件的代码。它支持 zip、tgz、bz2、txz 和 tar。

```bash
spec.source = { :http => 'http://dev.wechatapp.com/download/sdk/WeChat_SDK_iOS_en.zip' }
```

> 使用 HTTP 下载文件，并使用哈希算法来验证下载。它支持 sha1 和 sha256。

```bash
spec.source = { :http => 'http://dev.wechatapp.com/download/sdk/WeChat_SDK_iOS_en.zip',
                :sha1 => '7e21857fe11a511f472cfd7cfa2d979bd7ab7d96' }
```

### 支持的 keys

`:git => :tag, :branch, :commit, :submodules`

`:svn => :folder, :tag, :revision`

`:hg => :revision`

`:http => :flatten, :type, :sha256:, :sha1, :headers`



## `summary` required

对 Pod 的简短描述（最多 140 个字符）。

描述应该简短而丰富。它描述 Pod 的 tag 行信息，没有必要说明该 Pod 是一个 library（他们总是这样）。

摘要应适当大写，并包含正确的标点符号。


### 示例

```bash
spec.summary = 'Computes the meaning of life.'
```



## `description`

比摘要更详细的 Pod 描述。


### 示例

```bash
spec.description = <<-DESC
                     Computes the meaning of life.
                     Features:
                     1. Is self aware
                     ...
                     42. Likes candies.
                   DESC
```



## `screenshots`

展示 Pod 的 URL 图片列表。适用于面向 UI 的库。CocoaPods 推荐使用 gif 格式图片。


### 示例

```bash
spec.screenshot  = 'http://dl.dropbox.com/u/378729/MBProgressHUD/1.png'
```

```bash
spec.screenshots = [ 'http://dl.dropbox.com/u/378729/MBProgressHUD/1.png',
                     'http://dl.dropbox.com/u/378729/MBProgressHUD/2.png' ]
```




## `documentation_url`

可选的 Pod 文档的 URL，它将被 CocoaPods 的 web 属性所尊重。将其留空将默认为 CocoaDocs 为您的库生成的URL。

### 示例

```bash
spec.documentation_url = 'http://www.example.com/docs.html'
```



## `prepare_command`

Pod 下载完成后执行的 bash 脚本。该命令可用于创建、删除和修改任何下载的文件，并将在收集规范的其他文件属性的任何路径之前运行。

该命令在 Pod 被清理和 Pods 项目创建之前执行。工作目录是 Pod 的根目录。

如果 pod 安装时有 `:path` 选项，则不会执行此命令。

### 示例

```bash
spec.prepare_command = 'ruby build_files.rb'
```

```bash
spec.prepare_command = <<-CMD
                        sed -i 's/MyNameSpacedHeader/Header/g' ./**/*.h
                        sed -i 's/MyNameOtherSpacedHeader/OtherHeader/g' ./**/*.h
                   CMD
```



## `static_framework`

表示，如果指定了 `use_frameworks!`，pod 应该包含一个静态库框架。


### 示例

```bash
spec.static_framework = true
```

## `deprecated`

库是否已被弃用。


### 示例

```bash
spec.deprecated = true
```

## `deprecated_in_favor_of`

这个已经被废止的 Pod 库被取而代之的名字。


### 示例

```bash
spec.deprecated_in_favor_of = 'NewMoreAwesomePod'
```



# Platform

Specification 应指明支持该库的平台和相应的部署目标。

如果没有在 subspec 中定义，该组的属性将继承上一层级的值。

## `platform`

该 Pod 库支持的平台。留空表示该 Pod 库在所有平台上都支持，当支持多个平台时，应该使用下面的 `deployment_target` 代替。

### 示例

```bash
spec.platform = :osx, '10.8'
```

```bash
spec.platform = :ios
```

```bash
spec.platform = :osx
```



## `development_target`

支持平台的最低部署目标。

与 `platform` 属性相反，`deployment_target` 属性允许指定该 pod 支持的多个平台——也就是说，你可以为每个平台分别指定不同的部署目标。

### 示例

```bash
spec.ios.deployment_target = '6.0'
```

```bash
spec.osx.deployment_target = '10.8'
```



# Build settings

在这组中列出了与构建环境配置有关的属性，这些属性应该被用来构建库。

如果在 subspec 中没有定义，则该组的属性将继承父属性的值。



## `dependency`

任何对其他 Pods 或对 "sub-specification" 的依赖。

依赖关系可以指定版本。建议使用乐观的版本指示器 ~>，因为它可以很好地控制版本，而不会有太多限制。例如，~> 1.0.1 相当于 >= 1.0.1 与 < 1.1 相结合。同理，~> 1.0可以匹配1.0、1.0.1、1.1，但不会升级到2.0。

过度限制依赖性的 Pods 会限制它们与其他 Pods 的兼容性。


### 示例

```bash
spec.dependency 'AFNetworking', '~> 1.0'
```

```bash
spec.dependency 'AFNetworking', '~> 1.0', :configurations => ['Debug']
```

```bash
spec.dependency 'AFNetworking', '~> 1.0', :configurations => :debug
```

```bash
spec.dependency 'RestKit/CoreData', '~> 0.20.0'
```

```bash
spec.ios.dependency 'MBProgressHUD', '~> 0.5'
```



## `info_plist`

要添加到生成的 `Info.plist` 中的键值对。

这些值将与 CocoaPods 生成的默认值合并，覆盖任何重复的值。

对于库规范，对于使用框架集成的库，这些值将被合并到生成的 Info.plist 中。对于静态库来说，它不会有任何影响。

不支持子规格（除应用程序和测试规格外）。

对于应用程序规范，其值将被合并到应用程序主机的 Info.plist 中。

对于测试规范，其值将被合并到测试捆绑的 Info.plist 中。


### 示例

```bash
spec.info_plist = {
  'CFBundleIdentifier' => 'com.myorg.MyLib',
  'MY_VAR' => 'SOME_VALUE'
}
```

## `requires_arc`

`requires_arc` 允许你指定哪些 source_files 使用 ARC。这可以是支持 ARC 的文件，也可以是 `true` 表示所有的源文件都使用 ARC。

不使用 ARC 的文件会有 `-fno-objc-arc` 编译器标志。

这个属性的默认值是 `true`。

### 默认值

```bash
spec.requires_arc = true
```


### 示例

```bash
spec.requires_arc = false
```

```bash
spec.requires_arc = 'Classes/Arc'
```

```bash
spec.requires_arc = ['Classes/*ARC.m', 'Classes/ARC.mm']
```



## `frameworks`

用户的 target 需要链接的系统框架列表。


### 示例

```bash
spec.ios.framework = 'CFNetwork'
```

```bash
spec.frameworks = 'QuartzCore', 'CoreData'
```



## `weak_frameworks`

用户 target 需要 **weakly** （弱引用）链接的框架列表。


### 示例

```bash
spec.weak_framework = 'Twitter'
```

```bash
spec.weak_frameworks = 'Twitter', 'SafariServices'
```



## `libraries`

用户的 target（应用程序）需要链接到的系统库列表。


### 示例

```bash
spec.ios.library = 'xml2'
```

```bash
spec.libraries = 'xml2', 'z'
```



## `compiler_flags`

传递给编译器的标志列表。


### 示例

```bash
spec.compiler_flags = '-DOS_OBJECT_USE_OBJC=0', '-Wno-format'
```



## `pod_target_xcconfig`

要添加到最终**私有** pod 目标 xcconfig 文件的任何标志。


### 示例

```bash
spec.pod_target_xcconfig = { 'OTHER_LDFLAGS' => '-lObjC' }
```



## `user_target_xcconfig` - 不推荐使用

指定要添加到最终聚合目标 xcconfig 文件的标志，该文件将非覆盖和继承的构建设置传播到集成用户目标。

**不建议**使用此属性，因为Pods不应该污染用户项目的构建设置，这会导致冲突。

取多个值的构建设置的多个定义将被合并。用户在自定义构建设置和只取一个值的构建设置的冲突定义上会被警告。

通常 clang 编译器标志或预编译器宏定义如果在用户目标中导入 pod 时是需要的，就会进入这里。请注意，这不仅会影响你的pod的公共界面的编译器视图，而且会影响所有其他集成的pod。你应该始终选择 `pod_target_xcconfig`，它可以包含同样的设置，但只影响编译你的pod目标时的工具链。


### 示例

```bash
spec.user_target_xcconfig = { 'MY_SUBSPEC' => 'YES' }
```



## `prefix_header_contents` - 不推荐使用

在 Pod 项目的 prefix header 中注入的任何内容。

**不建议**使用这个属性，因为 Pods 不应该污染其他库或用户项目的 prefix header。


### 示例

```bash
spec.prefix_header_contents = '#import <UIKit/UIKit.h>'
```

```bash
spec.prefix_header_contents = '#import <UIKit/UIKit.h>', '#import <Foundation/Foundation.h>'
```



## `prefix_header_file` - 不推荐使用

注入到 Pod 项目的 prefix header 中的 prefix header 文件的路径。

`false` 表示不生成默认的 CocoaPods prefix header 文件，`true` 是默认的，表示应该生成默认的 CocoaPods prefix header 文件。

**不建议**使用文件路径选项，因为 Pods 不应该污染其他库或用户项目的前缀头。


### 示例

```bash
spec.prefix_header_file = 'iphone/include/prefix.pch'
```

```bash
spec.prefix_header_file = false
```



## `module_name`

为本规范生成的 framework/clang 模块使用的名称，而不是默认的名称（如果设置了header_dir，则为规范名称）。


### 示例

```bash
spec.module_name = 'Three20'
```



## `header_dir`

存储头文件的目录，这样就不会破坏 include。


### 示例

```bash
spec.header_dir = 'Three20Core'
```



## `header_mappings_dir`

保存页眉文件的文件夹结构的目录。如果没有提供，头文件将被扁平化。


### 示例

```bash
spec.header_mappings_dir = 'src/include'
```



## `script_phases`

这个属性允许定义一个脚本阶段，作为编译Pod的一部分来执行。与准备命令不同的是，脚本阶段作为 `xcodebuild` 的一部分执行，它们也可以利用编译期间设置的所有环境变量。

一个Pod可以提供多个脚本阶段来执行，它们将按照声明的顺序添加，并考虑到它们的执行位置设置。

注释 为了提供所有脚本阶段内容的可见性和意识，如果您的 pod 包含任何脚本阶段，在安装时将向用户发出警告。


### 示例

```bash
spec.script_phase = { :name => 'Hello World', :script => 'echo "Hello World"' }
```

```
spec.script_phase = { :name => 'Hello World', :script => 'echo "Hello World"', :execution_position => :before_compile }
```

```
spec.script_phase = { :name => 'Hello World', :script => 'puts "Hello World"', :shell_path => '/usr/bin/ruby' }
```

```
spec.script_phase = { :name => 'Hello World', :script => 'echo "Hello World"',
  :input_files => ['/path/to/input_file.txt'], :output_files => ['/path/to/output_file.txt']
}
```

```
spec.script_phase = { :name => 'Hello World', :script => 'echo "Hello World"',
  :input_file_lists => ['/path/to/input_files.xcfilelist'], :output_file_lists => ['/path/to/output_files.xcfilelist']
}
```

```
spec.script_phases = [
    { :name => 'Hello World', :script => 'echo "Hello World"' },
    { :name => 'Hello Ruby World', :script => 'puts "Hello World"', :shell_path => '/usr/bin/ruby' },
  ]
```



# File patterns

Podspecs 应该位于存储库的根目录下，文件的路径也应该相对于存储库的根目录来指定。File patterns 不支持遍历父目录（...）。File patterns 可以包含以下通配符模式。

---

## Pattern: `*`

匹配任何文件。可受 glob 中其他值的限制。

* `*` 将匹配任何文件
* `c*` 将匹配任何以 `c` 开头的文件
* `*c` 将匹配任何以 `c` 结尾的文件
* `*c*` 将匹配所有含有 `c` 的文件（包括开头或结尾处）。

相当于 regexp 中的 `/.*/x`。

**注意**，这不会匹配类似 Unix 的隐藏文件（dotfiles）。为了将这些文件包含在匹配结果中，你必须使用类似 `{*,.*}` 的东西。

---
## Pattern: `**`

递归匹配目录。

---
## Pattern: `?`

匹配任意一个字符。相当于 regexp 中的 `/.{1}/`。

---
## Pattern: `[set]`

匹配字符集中的任何一个字符。

与 Regexp 中的字符集完全相同，包括集合否定(`[^a-z]`)。

---
## Pattern: `{p,q}`

匹配文字 p 或文字 q。

匹配的字元长度可以超过一个字符。可以指定两个以上的字符。

相当于 regexp 中的模式交替。

---

## Pattern: `\`

忽略下一个元字符。

---

## 示例

考虑到这些要在 [JSONKit](https://github.com/johnezang/JSONKit) 的源根中进行评估。

```bash
"JSONKit.?"    #=> ["JSONKit.h", "JSONKit.m"]
"*.[a-z][a-z]" #=> ["CHANGELOG.md", "README.md"]
"*.[^m]*"      #=> ["JSONKit.h"]
"*.{h,m}"      #=> ["JSONKit.h", "JSONKit.m"]
"*"            #=> ["CHANGELOG.md", "JSONKit.h", "JSONKit.m", "README.md"]
```





## `source_files`

需要包含的源文件。


### 示例

```bash
spec.source_files = 'Classes/**/*.{h,m}'
```

```bash
spec.source_files = 'Classes/**/*.{h,m}', 'More_Classes/**/*.{h,m}'
```



## `public_header_files`

用作公共头的文件模式列表。

这些模式与源文件相匹配，以包含将暴露在用户项目中的头文件，并从这些文件中生成文档。当库被构建时，这些头文件将出现在构建目录中。如果没有指定公共头文件，那么 `source_files` 中的所有头文件都被认为是公共的。


### 示例

```bash
spec.public_header_files = 'Headers/Public/*.h'
```



## `private_header_files`

用来标记私有文件模式列表。

这些模式与公共头文件（或者如果没有指定公共头文件，则与所有头文件）进行匹配，以排除那些不应该暴露在用户项目中的头文件，也不应该被用来生成文档。当库被构建时，这些头文件将出现在构建目录中。

没有被列为 public 或 private 的头文件将被视为 private，但另外也不会出现在构建目录中。


### 示例

```bash
spec.private_header_files = 'Headers/Private/*.h'
```



## `vendored_frameworks`

源文件相关联的 framework


### 示例

```bash
spec.ios.vendored_frameworks = 'Frameworks/MyFramework.framework'
```

```
spec.vendored_frameworks = 'MyFramework.framework', 'TheirFramework.framework'
```



## `vendored_libraries`

源文件相关联的 libraries




### 示例

```bash
spec.ios.vendored_library = 'Libraries/libProj4.a'
```

```
spec.vendored_libraries = 'libProj4.a', 'libJavaScriptCore.a'
```



## `resource_bundles`

该属性允许定义资源包的名称和文件，这些资源包应该为Pod构建。它们被指定为一个哈希，其中键代表资源包的名称，值是它们应该包含的文件模式。

为了将 Pod 构建为静态库，官方强烈建议使用此属性来管理资源文件，因为使用 `resources` 属性可能会发生名称冲突。

资源文件 bundle 的名称至少应包括 Pod 的名称，以最大程度地减少名称冲突的可能性。

为了在每个平台上提供不同的资源，必须使用命名间隔捆绑。


### 示例

```bash
spec.ios.resource_bundle = { 'MapBox' => 'MapView/Map/Resources/*.png' }
```

```
spec.resource_bundles = {
    'MapBox' => ['MapView/Map/Resources/*.png'],
    'MapBoxOtherResources' => ['MapView/Map/OtherResources/*.png']
  }
```



## `resources`

应该复制到目标 bundle 中的资源列表。

为了将 Pod 构建为静态库，官方建议是使用 `resource_bundle`，因为使用 `resources` 属性可能会发生名称冲突。此外，使用此属性指定的资源将直接复制到客户端目标，因此 Xcode 不会对其进行优化。


### 示例

```bash
spec.resource = 'Resources/HockeySDK.bundle'
```

```bash
spec.resources = ['Images/*.png', 'Sounds/*']
```



## `exclude_files`

从其他文件模式中排除的文件模式列表。


### 示例

```bash
spec.ios.exclude_files = 'Classes/osx'
```

```bash
spec.exclude_files = 'Classes/**/unused.{h,m}'
```



## `preserve_paths`

任何在下载后不应该被删除的文件。

默认情况下，CocoaPods 会删除所有不符合任何其他文件模式的文件。


### 示例

```bash
spec.preserve_path = 'IMPORTANT.txt'
```

```
spec.preserve_paths = 'Frameworks/*.framework'
```



## `module_map`

将此 Pod 集成为框架时应使用的模块映射文件。

默认情况下，CocoaPods 基于规范中的公共头创建模块映射文件。


### 示例

```bash
spec.module_map = 'source/module.modulemap'
```



# Subspecs

一个库可以指定对另一个库、另一个库的子库或其自身的子库的依赖。



## `subspec`

代表库中一个模块的 specification。

Subspecs 参与了一个双重层次结构。

一方面，一个 specification 会自动继承它的所有 "sub-specifications" 作为依赖关系（除非指定了一个默认的子规范）。

另一方面，一个 "sub-specification" 继承了父规范的属性值，因此可以在祖规范中指定属性的共同值。

虽然在实践中听起来很复杂，但这意味着 subspecs 一般都能达到你所期望的效果。

```bash
pod 'ShareKit', '2.0'
```

安装 ShareKit 包含的所有框架，如 `ShareKit/Evernote`，`ShareKit/Facebook` 等，因为它们被定义为 subspecs。

```bash
pod 'ShareKit/Twitter',  '2.0'
pod 'ShareKit/Pinboard', '2.0'
```



安装 ShareKit 时只需要 `ShareKit/Twitter`、`ShareKit/Pinboard`的源文件。请注意，在这种情况下，要编译的 "sub-specifications"需要源文件、依赖关系和根规范定义的其他属性。CocoaPods 很聪明，可以处理任何由重复属性引起的问题。



### 示例

> 具有不同源文件的子模块。

```bash
subspec 'Twitter' do |sp|
  sp.source_files = 'Classes/Twitter'
end

subspec 'Pinboard' do |sp|
  sp.source_files = 'Classes/Pinboard'
end
```

> 引用对其他子模块的依赖关系的子模块。

```bash
Pod::Spec.new do |s|
  s.name = 'RestKit'

  s.subspec 'Core' do |cs|
    cs.dependency 'RestKit/ObjectMapping'
    cs.dependency 'RestKit/Network'
    cs.dependency 'RestKit/CoreData'
  end

  s.subspec 'ObjectMapping' do |os|
  end
end
```

> 嵌套子模块

```bash
Pod::Spec.new do |s|
  s.name = 'Root'

  s.subspec 'Level_1' do |sp|
    sp.subspec 'Level_2' do |ssp|
    end
  end
end
```



## `requires_app_host`

测试规范是否需要应用主机来运行测试。这只适用于测试规范。



### 示例

```
test_spec.requires_app_host = true
```



## `app_host_name`

必要时，作为 app host 的应用规范。



## `scheme`

指定本规范要使用的配置方案。



### 示例

```
spec.scheme = { :launch_arguments => ['Arg1'] }
```

```
spec.scheme = { :launch_arguments => ['Arg1', 'Arg2'], :environment_variables => { 'Key1' => 'Val1'} }
```

### 支持的 keys：

`:launch_arguments`

`:environment_variables`

`:code_coverage`



## `test_spec`

代表库的测试规范。在这里，你可以将所有的测试与测试依赖关系一起放在 podspec 中。

### 示例

```bash
Pod::Spec.new do |spec|
  spec.name = 'NSAttributedString+CCLFormat'

  spec.test_spec do |test_spec|
    test_spec.source_files = 'NSAttributedString+CCLFormatTests.m'
    test_spec.dependency 'Expecta'
  end
end
```



## `app_spec`

代表该库的应用程序规范。在这里，你可以将你的podspec的所有应用程序的源文件和应用程序的依赖关系放在一起。



### 示例

```bash
Pod::Spec.new do |spec|
  spec.name = 'NSAttributedString+CCLFormat'

  spec.app_spec do |app_spec|
    app_spec.source_files = 'NSAttributedString+CCLFormat.m'
    app_spec.dependency 'AFNetworking'
  end
end
```




## `default_subspecs`

一个子规格名称的数组，这些子规格应该被用作首选的依赖关系。如果没有指定，则规范要求所有的子规范作为依赖关系。

你可以使用值 `:none` 来指定编译这个pod不需要任何子规格，所有子规格都是可选的。

默认情况下，一个 Pod 应该提供完整的库。用户可以在知道自己的需求后，微调自己的依赖性，并排除不需要的子规范。因此，很少需要这个属性。它的目的是用来选择一个默认值，如果有 "子规范 "提供了其他不兼容的实现，或者排除很少需要的模块（特别是当它们触发对其他库的依赖时）。



### 示例

```bash
spec.default_subspec = 'Core'
```

```bash
spec.default_subspecs = 'Core', 'UI'
```

```bash
spec.default_subspecs = :none
```



