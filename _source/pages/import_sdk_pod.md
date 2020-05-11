# Cocoapods 导入 SDK
推荐使用 `Cocoapods` 集成 SDK。 `Cocoapods` 提供了一个简单的依赖管理系统，避免手动导入产生的错误。

<br/>

在 `Podfile` 文件中，添加以下行：
```ruby
pod 'ElingIM'
```

<br/>

进入到 `Podfile` 所在的目录，执行以下命令：
```zsh
pod install
```

执行完命令后，打开工程文件运行即可。