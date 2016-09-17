# 高德地图使用cocoapods No such file or directory错误
高德地图使用`cocoapods`出现 No such file or directory错误
```
Errno::ENOENT - No such file or directory - /Users/newuser/Documents/umu/ios_fast_dealer/Pods/AMapSearch/AMapSearchKit.framework/Resources
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:905:in `stat'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:905:in `symbolic_modes_to_i'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:926:in `fu_mode'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:1000:in `block (2 levels) in chmod_R'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:1460:in `preorder_traverse'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:998:in `block in chmod_R'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:997:in `each'
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/fileutils.rb:997:in `chmod_R'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/installer.rb:117:in `block in prepare'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/user_interface.rb:140:in `message'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/installer.rb:116:in `prepare'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/installer.rb:103:in `install!'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/command/project.rb:71:in `run_install_with_update'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/command/project.rb:156:in `run'
/Users/newuser/.gem/ruby/2.0.0/gems/claide-0.9.1/lib/claide/command.rb:312:in `run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/lib/cocoapods/command.rb:48:in `run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.38.2/bin/pod:44:in `<top (required)>'
/Applications/AppCode.app/Contents/bin/pod:6:in `load'
/Applications/AppCode.app/Contents/bin/pod:6:in `<main>'
```
```
――― TEMPLATE END ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――

[!] Oh no, an error occurred.

Search for existing GitHub issues similar to yours:
https://github.com/CocoaPods/CocoaPods/search?q=No+such+file+or+directory+-+%2FUsers%2Fnewuser%2FDocuments%2Fumu%2Fios_fast_dealer%2FPods%2FAMapSearch%2FAMapSearchKit.framework%2FResources&type=Issues
```

这个问题以前没有出现过,在pod 的版本是`cocoapods 0.38.2`的时候就无法找到高德地图在github上面的仓库地址
把pod降级为`0.38.1`即可：
>gem uninstall cocoapods

```
	Successfully uninstalled cocoapods-0.38.2
```
>gem install cocoapods -v 0.38.1

```
	Successfully installed cocoapods-0.38.1
```
运行`pod update`不再报错

已经提交issues https://github.com/CocoaPods/CocoaPods/issues/4153

@segiddins 回复说：This has been fixed in the 0.39 betas.

