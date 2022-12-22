---
{"dg-publish":true,"permalink":"//bash/"}
---


来自 [Bash add pause in shell script with bash pause command - nixCraft](https://www.cyberciti.biz/tips/linux-unix-pause-command.html) 的方法

```bash
read -p "Press any key to resume ..."

## Bash add pause prompt for 5 seconds ##
read -t 5 -p "I am going to wait for 5 seconds only ..."
```

由此实现的一个 `pause` 函数

```bash
function pause(){
	read -s -n 1 -p "Press any key to continue . . ."
	echo ""
}
```

解释：

> We can pass the `-t` option to the read command to set time out value.
> 
> By passing the `-s` we can ask the read command not to echo input coming from a terminal/keyboard (不显示你后面的输入)
> 
> `-n nchars`  return after reading NCHARS characters rather than waiting for a newline, but honor a delimiter if fewer than NCHARS characters are read before the delimiter (如果用 `-N` 就会忽略 delimiter )

#zsh #shell 的 `read` 命令使用方式不同，bash 中 `read -p "str"` 在 zsh 中的等价操作是 `read "?str"` ，`-n` 也变为 `-k` 。

更多相关信息参考 [ZSH: Read command fails within bash function "read:1: -p: no coprocess" - Super User](https://superuser.com/a/556006) 。