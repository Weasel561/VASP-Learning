# Day 7：统计行数与管道复习

日期：2026-10-09
练习目录：~/linux_practice/day07

## 1. 主目录与路径

`~` 表示主目录，本次环境中是 /home/codespace。

```bash
cd ~/linux_practice
```

无论当前在哪里，都可以进入主目录里的练习文件夹。

## 2. 统计文件行数

```bash
wc -l run.txt
```

本次结果：4 run.txt。
-l 中是小写字母 l。

严格来说，wc -l 统计换行符数量；
今天用 echo 创建的每一行都有结尾换行。

## 3. 统计匹配行数

```bash
grep -c "finished" run.txt
```

结果：3。

-c 统计匹配行数，同一行出现两次关键词也只算一行。

```bash
grep -ic "warning" run.txt
```

忽略大小写并统计匹配行数，结果：1。

## 4. 显示内容与统计数量的区别

| 命令 | 显示什么 |
|---|---|
| cat -n run.txt | 全部内容及行号 |
| grep -n "finished" run.txt | 匹配内容及原行号 |
| grep -c "finished" run.txt | 匹配行数 |

## 5. 用管道统计搜索结果

```bash
grep "finished" run.txt | wc -l
grep -i "step" run.txt | wc -l
```

分别输出 3 和 2。

流程：grep 输出匹配行 → 管道传给 wc → 统计行数。

不要写成：

```bash
grep -c "finished" run.txt | wc -l
```

因为 grep -c 只输出一行数字，wc -l 会得到 1。

## 6. 保存统计结果

```bash
grep -c "finished" run.txt > finished_count.txt
grep -i "step" run.txt | wc -l > step_count.txt
grep -i "warning" run.txt | wc -l > warning_pipe_count.txt
```

`|` 把输出交给命令。
`>` 把输出写入文件，已有内容会被覆盖。

## 7. 易错点

- 选项前需要横线：-ic，不能写成 ic。
- 文件名必须一致，如 step_count.txt。
- grep 拼错时，右边 wc 仍可能输出 0；
  看到数字也要检查是否有报错。
- wc -l count.txt 统计的是结果文件有几行，
  不是读取里面的数字作为数量。

## 8. 下次复习重点

先分别运行搜索和统计，再组合管道；
熟悉后添加 > 保存结果。