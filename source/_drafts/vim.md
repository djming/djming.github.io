***_arguments:450: _vim_files: function definition file not found***

如果

``` zsh
rm ~/.zcompdump*
exec zsh
```
无效，再试试

```
rm ~/.antigen/.zcompdump*
exec zsh
```
