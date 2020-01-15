1. **Cocopods原理解析:** 

   - 1.当使用 CocoaPods 导入私有库时，CocoaPods 先是根据:git => 'https://github.com/xiaofei86/LPLineChartView.git'找到对应的 Git 仓库，然后根据:tag => '1.0.0'定位到对应 tag 的提交（如果没有注明 Pod 依赖库版本则定位到最后一次的提交） 

   - 2.然后在这次提交中检索到后缀为“.podspec”的文件（文件可以随便命名）。找到 Podspec 文件后先要验证 [s.name](http://s.name/) 是否与Podfile中的一致，如果不一致则 install 时会报错“[!] Unable to find a specification for 'LPLineChartView'.” 

   - 3.验证成功后，就会根据 Podspec 中的 s.source_files 找到需要导入代码文件，并通过其他的数据找到对应的配置文件或资源文件等。

   - 4.最后，将其下载到本地项目中

     

2. **集成原理** 

   1. Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个.a文件即可
   2. 对于资源文件，CocoaPods 提供了一个名为 [Pods-resources.sh](http://pods-resources.sh/) 的 bash 脚本，该脚本在每次项目编译的时候都会执行，将 Pods 依赖库的各种资源文件复制到目标目录中。CocoaPods 还通过一个名为 Pods.xcconfig 的文件来在编译时设置所有的依赖和参数。 
   3. ![](../技术图片/192842B3-FE28-48C0-99D0-3CB3335F6985.png)

3. **Podfile.lock 文件**

   1. 执行完 pod install 之后，CocoaPods会生成一个名为 Podfile.lock 的文件
   2. Podfile.lock 文件最大得用处在于多人开发。团队中的某个人执行完 pod install 命令后，生成的 Podfile.lock 文件就记录下了当时最新 Pods 依赖库的版本，这时团队中的其它人 check 下来这份包含 Podfile.lock 文件的工程以后，再去执行 pod install 命令时，获取下来的 Pods 依赖库的版本就和最开始用户获取到的版本一致。
   3. 如果没有 Podfile.lock 文件，后续所有用户执行 pod install 命令都会获取最新版本的 LPLineChartView，这就有可能造成同一个团队使用的依赖库版本不一致，这对团队协作来说绝对是个灾难！
   4. 解决方案:如果团队想使用当前最新版本的 LPLineChartView 依赖库，有两种方案：1.更改 Podfile，使其指向最新版本的 LPLineChartView 依赖库；2.执行 pod update 命令；鉴于 Podfile.lock 文件对团队协作如此重要，所以应该加入到版本控制里面（This file should always be kept under version control）。

4. **命令总结**

   1. CocoaPods 在执行 pod install 和 pod update 时，会默认先更新一次 Podspec 索引。使用 --no-repo-update 参数可以禁止其做索引更新操作

      > ```objective-c
      > pod install --no-repo-update
      > pod update --no-repo-update
      > ```

   2. 查看细节 => 了解底层运行过程最简单的方式

      >```objective-c
      >pod install --verbose
      >pod update --verbose
      >```

5. **管理Pods依赖库版本**

   1. pod 'LPLineChartView'                //不显式指定依赖库版本，表示每次都获取最新版本
      pod 'LPLineChartView', '2.0'        //只使用2.0版本
      pod 'LPLineChartView', '> 2.0'      //使用大于2.0的版本
      pod 'LPLineChartView', '>= 2.0'     //使用大于或等于2.0的版本
      pod 'LPLineChartView', '< 2.0'      //使用小于2.0的版本
      pod 'LPLineChartView', '<= 2.0'     //使用小于或等于2.0的版本
      pod 'LPLineChartView', '~> 0.1.2'   //使用大于等于0.1.2但小于0.2的版本
      pod 'LPLineChartView', '~> 0.1'     //使用大于等于0.1但小于1.0的版本
      pod 'LPLineChartView', '~> 0'        //使用大于0的版本，和什么都不写效果相同
      


[参考文档连接 --作者：xuyafei86](作者：xuyafei86链接：https://www.jianshu.com/p/fb202af858fd来源：简书著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。)

