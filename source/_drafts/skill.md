### Vim tab出现 _arguments:450: _vim_files: function definition file not found

如果

``` zsh
rm ~/.zcompdump*
exec zsh
```
无效，再试试

``` zsh
rm ~/.antigen/.zcompdump*
exec zsh
```

### android studio提速

 1. Windows在as目录下的studio64.exe.vmoptions，mac与linux在as目录的studio64.vmoptions中修改`-Xmx内存m`可修改AS最大消耗内存。
 2. 项目的gradle.property添加`org.gradle.jvmargs=-Xms内存m`修改编译所用内存。
 3. `org.gradle.parallel=true` 使用并行编译

### oh my zsh 启动提速
``` zsh
sudo rm /private/var/log/asl/*.asl
```
