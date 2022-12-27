---
{"dg-publish":true,"permalink":"/Python/re 库的基本使用/"}
---


## 补充内容：断言(assertion)

基本的断言类型有 positive/negative lookahead/lookbehind assertion 四种。这几种经常用到。

- **前视断言 lookahead assertion** ：`(?=somestrings)` 匹配的结果只包括后面的内容为 `somestrings` 的字符串。例如 `Isaac (?=Asimov)` 将会匹配 `Isaac ` (注意有空格)，仅当其后紧跟  `Asimov` 。
- **否定型前视断言 negative lookahead assertion** ：`(?!somestrings)` 类似前视断言，例如，`Isaac (?!Asimov)` 将会匹配 `Isaac `，仅当它后面不是 `Asimov`。
- **肯定型后视断言 positive lookbehind assertion** ：`(?<=somestrings)` 匹配的结果只包括前面有 `somestrings` 的字符串。例如 `(?<=abc)def` 将会对目标字符串 `abcdef` 匹配 `def` 。注意内部表达式（匹配的内容）必须是固定长度的，就是 `abc` 或 `a|b` 是允许的，但是 `a*` 和 `a{3,4}` 不可以。
- **否定型后视断言 negative lookbehind assertion** ：`(?<!somestrings)`匹配的结果只包括前面不是 `somestrings` 的字符串。类似于肯定型后视断言，内部表达式（匹配的内容）必须是固定长度的。

## 创建正则表达式 pattern

通过 `re.compile(r"<regex>")` 创建 pattern 。

## 匹配目标字符串

匹配方式有多种，这里介绍最常用的 4 种，分别是：`match, search, findall, finditer` 。

### `match`

`match` 只从字符串开头进行检查是否匹配，没有匹配则会返回 `None` 。

``` python
pattern = re.compile(r"<regex>")
result = pattern.match("<string>")
if result==None:
	print("Not found")
else:
	print(result.group(0))
```

### `search`

`search` 会在字符串中搜索，找到 **第一个** 匹配对象，没有匹配则会返回 `None` 。

``` python
pattern = re.compile(r"<regex>")
result = pattern.search("<string>")
if result==None:
	print("Not found")
else:
	print(result.group(0))
```

### `findall`

`findall` 从左至右扫描 string ，以字符串列表或字符串元组列表的形式，按找到的顺序返回 pattern 在 string 中的所有非重叠匹配。空匹配也包括在结果中。

``` python
re.findall(r'\bf[a-z]*', 'which foot or hand fell fastest')
# ['foot', 'fell', 'fastest']

re.findall(r'(\w+)=(\d+)', 'set width=20 and height=10')
# [('width', '20'), ('height', '10')]
```

^7a002f

### `finditer`

从左到右扫描 string ，返回保存匹配对象的迭代器，其中包括 pattern 在 string 里所有的非重叠匹配。匹配按顺序排列。空匹配也包含在结果里。

``` python
# pattern = re.compile(...)
results = pattern.finditer("<string>")
for res in results:
	print(res.group(0))
```

## 使用 `group`

当我们要匹配的字符串可以分为几个部分，我们还需要同时提取这几个部分时，可以通过 `group` 来执行。比如下面的例子。

``` python
	  m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
	  
	  m.group(0)       # The entire match
	  # 'Isaac Newton'
	  
	  m.group(1)       # The first parenthesized subgroup.
	  # 'Isaac'
	  
	  m.group(2)       # The second parenthesized subgroup.
	  # 'Newton'
	  
	  m.group(1, 2)    # Multiple arguments give us a tuple.
	  # ('Isaac', 'Newton')
```

`group(0)` 包含的是整个匹配的字符串，从 `1` 开始就是正则表达式中第几个圆括号包含的匹配内容。

## 其他注意的内容

`match, search` 等函数一般都有更简单的调用方式，也就是直接像 [[Python/re 库的基本使用#^7a002f\|上面的例子]] 一样写 `match(<regex>, <string>)` 。但是这样是我不推荐的，每次调用 `match` 正则引擎都会重新编译一次给定的 `<regex>` 字符串。不如先用 `compile` 再调用这个 pattern 做匹配。