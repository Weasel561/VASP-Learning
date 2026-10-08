# Day 6：相对路径、管道与搜索结果保存

日期：2026-10-08
练习目录：/home/codespace/linux_practice/day06

## 1. 根据当前位置写路径

练习结构：

```text
day06/
└── logs/
    └── run.txt
```

在 day06 中查看文件：

```bash
cat logs/run.txt
```

进入 logs 后查看同一个文件：

```bash
cd logs
cat run.txt
cd ..
```

`/` 分隔路径中的名字。
相对路径从当前目录出发，先用 pwd 确认位置。

## 2. 准备模拟计算记录

在 day06 中执行：

```bash
mkdir logs
echo "Step 1 finished" > logs/run.txt
echo "Warning: check input" >> logs/run.txt
echo "Step 2 finished" >> logs/run.txt
cat -n logs/run.txt
```

以上是模拟文字，不代表实际运行了计算。
`>` 覆盖写入，`>>` 追加写入。

## 3. 搜索匹配行

```bash
grep "finished" logs/run.txt
```

结果：

```text
Step 1 finished
Step 2 finished
```

## 4. 管道：把输出交给下一条命令

```bash
grep "finished" logs/run.txt | head -n 1
```

先搜索，再取搜索结果的第一行。

```bash
grep "finished" logs/run.txt | tail -n 1
```

先搜索，再取搜索结果的最后一行。

右边的 head 或 tail 处理的是左边传来的搜索结果，
不是直接查看原文件。原文件内容不会改变。

## 5. 保存搜索结果

```bash
grep "finished" logs/run.txt > finished.txt
cat finished.txt
```

将全部匹配行写入当前目录的 finished.txt。

保存最后一条匹配结果：

```bash
grep "finished" logs/run.txt | tail -n 1 > last_step.txt
```

保存第一条匹配结果：

```bash
grep "finished" logs/run.txt | head -n 1 > first_step.txt
```

## 6. 忽略大小写、显示行号并保存

```bash
grep -in "warning" logs/run.txt > warnings.txt
cat warnings.txt
```

结果：

```text
2:Warning: check input
```

- `-i`：忽略大小写。
- `-n`：显示匹配行在原文件中的行号。
- `-in` 等同于 `-i -n`。

## 7. 符号对比

| 符号 | 输出交给谁 | 效果 |
|---|---|---|
| `|` | 右边的命令 | 继续处理输出 |
| `>` | 右边的文件 | 不存在则创建，存在则覆盖 |
| `>>` | 右边的文件 | 在末尾追加 |

综合流程：

```text
grep 搜索 → 管道传递 → head/tail 取行 → 重定向保存
```

## 8. 今天的易错点

- run.txt 写成 run.xtx：文件名不同，系统找不到文件。
- `head -n 1` 表示第一行。
- `head -n -1` 表示除了最后一行以外的所有行。
  今天只有两条匹配结果，两种写法碰巧输出相同，但含义不同。
- 搜索小写 warning 时，要加 -i 才能匹配大写 Warning。
- 没有匹配结果时使用 >，仍可能创建或清空目标文件。
- 不要把输出重定向到正在读取的原文件，
  例如不要使用 `grep "finished" logs/run.txt > logs/run.txt`，
  这会先清空原文件。

## 9. 本次练习文件

```text
day06/
├── logs/
│   └── run.txt
├── finished.txt
├── first_step.txt
├── last_step.txt
└── warnings.txt
```

## 10. 下次复习重点

1. 根据当前位置写文件路径。
2. 区分原文件的末尾与搜索结果的末尾。
3. 独立组合 grep、管道和 head/tail。
4. 区分 |、> 和 >>。
5. 核对选项、文件名和数字。
