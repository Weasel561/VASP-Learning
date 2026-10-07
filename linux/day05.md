# Day 5：写入文字与搜索文件内容

日期：2026-10-07  
练习目录：/home/codespace/linux_practice/day05

## 1. 用 echo 显示文字

```bash
echo "Calculation started"
```

将文字显示在终端，不会自动保存到文件。
双引号用来括住包含空格的文字。

## 2. 覆盖写入与追加写入

```bash
echo "Calculation started" > run.txt
```

`>`：文件不存在时创建文件；已存在时覆盖原内容。

```bash
echo "Step 1 finished" >> run.txt
```

`>>`：保留原内容，在末尾追加；文件不存在时也会创建。

| 写法 | 效果 |
|---|---|
| `echo "文字"` | 显示在终端 |
| `echo "文字" > run.txt` | 覆盖写入文件 |
| `echo "文字" >> run.txt` | 追加到文件末尾 |

今天容易混淆：一个 `>` 是覆盖，两个 `>>` 是追加。

## 3. 查看文件内容

```bash
cat run.txt
cat -n run.txt
```

`cat` 显示全部内容。
`cat -n` 显示全部内容，并加上行号。

今天最终确认的文件内容：

```text
1  Calculation started
2  Step 1 finished
3  Warning check input
4  Step 2 finished
5  Calculation finish
6  All done
```

这里的数字是查看时显示的行号，不属于文件中的文字。

## 4. 用 grep 筛选包含关键词的行

基本顺序：

```text
grep "关键词" 文件路径
```

例如：

```bash
grep "finished" run.txt
```

输出：

```text
Step 1 finished
Step 2 finished
```

grep 按条件筛选行，显示匹配的整行，不会修改文件。

对于今天使用的普通文字，包含关键词就能匹配，
不要求整行或整个单词完全等于关键词。

## 5. 包含匹配：finish 与 finished

```bash
grep -n "finish" run.txt
```

输出：

```text
2:Step 1 finished
4:Step 2 finished
5:Calculation finish
```

因为 finished 中包含 finish，所以这三行都会匹配。

反过来，搜索 finished 不会匹配第 5 行的 finish，
因为那里缺少 ed。

## 6. 显示匹配行的原行号

```bash
grep -n "Step" run.txt
```

输出：

```text
2:Step 1 finished
4:Step 2 finished
```

`-n` 显示匹配行在原文件中的行号。

| 命令 | 显示范围 |
|---|---|
| `cat -n run.txt` | 全部行及其行号 |
| `grep -n "Step" run.txt` | 包含 Step 的行及其原行号 |

筛选条件由引号里的关键词决定。

## 7. 忽略大小写

```bash
grep "warning" run.txt
```

默认区分大小写，因此不会匹配文件里的 Warning。
没有匹配时通常没有输出，这不代表文件被删除了。

```bash
grep -i "warning" run.txt
```

`-i` 忽略大小写，可以匹配 Warning。

## 8. 组合选项

下面两种写法效果相同：

```bash
grep -in "step" run.txt
grep -i -n "step" run.txt
```

同时忽略大小写并显示行号。

组合写成 `-in` 时，i 和 n 之间不加空格。
分开写时，每个选项都带横线：`-i -n`。

## 9. 同一个选项在不同命令中含义不同

| 命令 | 含义 |
|---|---|
| `cat -n run.txt` | 给全部输出行编号 |
| `grep -n "Step" run.txt` | 显示匹配行的原行号 |
| `head -n 3 run.txt` | 查看前 3 行 |
| `echo -n "All done"` | 输出末尾不换行 |

逐行追加日志时，使用普通 echo，
避免后续文字接在上一行末尾。

## 10. 今天的出错点与改正

- 把 grep 拼成 gerp：命令名必须准确。
- 把 run.txt 拼成 run.xtx：文件名不同就找不到。
- 输入 finish 而不是 finished：文件会保存实际输入的文字。
- 搜索 finish 时，误以为只会找到完整单词 finish：
  实际也会匹配包含它的 finished。
- 混淆 > 和 >>：覆盖与追加的效果不同。
- 把 echo 的 -n 当作显示行号：选项含义取决于命令。

## 11. 下次复习重点

1. 不看笔记，说出 > 和 >> 的区别。
2. 独立写出追加一行文字的命令。
3. 使用 grep 搜索并显示原行号。
4. 使用 -i 忽略大小写。
5. 执行前核对命令拼写、关键词和文件名。
