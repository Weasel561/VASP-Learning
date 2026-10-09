# Day 1～7：Linux 基础阶段总结

整理日期：2026-10-09  
来源：[Day 1](day01.md) · [Day 2](day02.md) · [Day 3](day03.md) · [Day 4](day04.md) · [Day 5](day05.md) · [Day 6](day06.md) · [Day 7](day07.md)

这七天的学习主线是：确认位置 → 管理文件 → 查看内容 → 写入文字 → 搜索 → 用管道处理结果 → 统计与保存。本文合并重复知识，保留常用例子和易错点，方便集中复习。

## 1. 先确认位置，再写路径

```bash
pwd       # 当前目录的完整路径
ls        # 当前目录有哪些内容
ls -l     # 详细信息，l 是小写字母
ls -1     # 每行显示一个名字，1 是数字
ls -a     # 包括隐藏文件
```

```bash
cd 文件夹名          # 进入当前目录中的文件夹
cd ..                # 返回上一级
cd ~                 # 回到主目录
cd ~/linux_practice  # 进入主目录里的练习目录
cd /                 # 到根目录
cd -                 # 回到上一次所在的目录
```

本次 Codespaces 中，`~` 代表 `/home/codespace`。不管当前在哪里，`cd ~/linux_practice` 都指向同一个练习目录。

假设结构如下：

```text
day06/
└── logs/
    └── run.txt
```

| 当前所在位置 | 查看同一个文件 |
|---|---|
| day06 | `cat logs/run.txt` |
| day06/logs | `cat run.txt` |

`/` 分隔路径中的名字；相对路径从当前位置出发。只写文件名，表示当前目录里的文件。已经在某个目录里，再执行 `cd 同名目录`，会尝试进入其同名子目录。

操作前先问：**我在哪里？来源在哪里？目标在哪里？**

## 2. 创建、复制、移动与删除

以下示例按顺序在一个新练习目录中执行，目标名字尚未存在：

```bash
mkdir backup                  # 创建文件夹
touch notes.txt               # 创建空文件；已有文件则更新时间
cp notes.txt notes_copy.txt    # 当前目录中复制一份
cp notes.txt backup/          # 复制到已有文件夹
cp backup/notes.txt restored.txt  # 从文件夹复制到当前目录
mv notes_copy.txt saved.txt    # 改名
mv saved.txt backup/          # 移入已有文件夹
cp -r backup backup_copy      # 复制整个文件夹
rm restored.txt              # 删除指定文件
```

| 操作 | 顺序 | 结果 |
|---|---|---|
| cp | 来源 → 目标 | 原文件保留，目标多一份副本 |
| mv | 来源 → 目标 | 转移位置或改名，原位置或旧名字不再保留 |
| rm | 指定文件 | 删除文件 |

关键点：

- `touch` 创建文件，`mkdir` 创建文件夹；名字带 `.txt` 也不能决定类型。
- 复制文件夹需要 `-r`。
- 目标只写一个新文件名，就在当前目录创建副本。
- 目标目录已存在时，`cp -r backup archive` 会把来源复制到目标目录里面；目标不存在时，则创建名为 `archive` 的副本。
- 复制、移动可能覆盖已有目标文件，先核对名字。
- `rm` 通常不经过回收站，删除前确认路径。

## 3. 查看全部、开头、末尾与大文件

```bash
cat run.txt          # 全部内容
cat -n run.txt       # 全部内容和行号
head -n 3 run.txt    # 前 3 行
tail -n 3 run.txt    # 最后 3 行
less run.txt        # 分页查看
```

在 `less` 中：方向键移动，空格翻页，输入 `/关键词` 后回车搜索，按 `q` 退出。

```bash
tail -f run.txt
tail -n 5 -f run.txt
```

实时查看追加内容；第二条先显示末尾 5 行再跟踪。按 `Ctrl+C` 退出。

`head -n 1` 表示第一行；`head -n -1` 在本次 Linux 环境中表示除了最后一行以外的所有行，二者不能混淆。

## 4. 写入文字：覆盖与追加

```bash
echo "Calculation started"             # 显示在终端
echo "Calculation started" > run.txt   # 写入文件
echo "Step 1 finished" >> run.txt      # 追加一行
seq 1 12 > number.txt                  # 生成 1～12，每个数字一行
```

| 符号 | 文件不存在时 | 文件已存在时 |
|---|---|---|
| > | 创建文件 | 覆盖原内容 |
| >> | 创建文件 | 保留原内容，末尾追加 |

双引号括住包含空格的文字；文件会保存实际输入的内容，拼错的词不会自动纠正。

普通 `echo` 在输出末尾换行；`echo -n` 不换行，后续追加文字可能接在同一行上。

## 5. grep：筛选包含关键词的行

以下后续例子统一使用 Day 7 的四行日志：

```text
Step 1 finished
Warning: check input
Step 2 finished
Calculation finished
```

```bash
grep "finished" run.txt       # 显示包含 finished 的整行
grep -n "finished" run.txt    # 匹配内容和原文件行号
grep -i "warning" run.txt     # 忽略大小写
grep -in "warning" run.txt    # 忽略大小写并显示行号
```

最后一条输出：

```text
2:Warning: check input
```

对于目前练习的普通文字，包含关键词就可以匹配，不要求整行或整个单词完全相同。因此搜索 `finish` 也会匹配 `finished`；搜索 `finished` 不会匹配只有 `finish` 的内容。

默认区分大小写；没有匹配时，普通 `grep` 通常没有输出。`-in` 等同于 `-i -n`，不能写成 `-i n`。

## 6. 管道：把输出交给下一条命令

```bash
grep "finished" run.txt | head -n 1
grep "finished" run.txt | tail -n 1
```

流程：`grep` 输出匹配行 → `|` 传递输出 → 右边命令继续处理。

第一条取匹配结果的第一行，得到 `Step 1 finished`；第二条取匹配结果的最后一行，得到 `Calculation finished`。

右边处理的是**搜索结果**，不是直接读取原文件的开头或末尾。

```bash
grep "finished" run.txt > finished.txt
grep "finished" run.txt | tail -n 1 > last_step.txt
```

第一条保存全部匹配结果；第二条保存最后一条匹配结果。输入日志保持原内容。

| 符号 | 输出交给谁 |
|---|---|
| 管道 `\|` | 右边的命令 |
| `>` | 右边的文件，覆盖写入 |
| `>>` | 右边的文件，末尾追加 |

不要把输出写回正在读取的同一个文件，例如 `grep "finished" run.txt > run.txt`，因为重定向会先清空原文件。

## 7. 统计：文件行数与匹配行数

```bash
wc -l run.txt                       # 4 run.txt
grep -c "finished" run.txt           # 3
grep -ic "warning" run.txt           # 1
grep "finished" run.txt | wc -l      # 3
grep -i "step" run.txt | wc -l       # 2
```

`wc -l` 严格来说统计换行符；今天用 `echo` 写出的每行都有结尾换行。`grep -c` 统计匹配行数，同一行出现两次关键词也只计一行。

| 命令 | 得到什么 |
|---|---|
| cat -n | 全部内容及行号 |
| grep -n | 匹配内容及原行号 |
| grep -c | 匹配行数 |
| wc -l | 输入内容中的换行符数量 |

不要用 `grep -c "finished" run.txt | wc -l` 统计匹配行数：左边只输出一行数字，右边会得到 1。

## 8. 保存统计结果

```bash
grep -c "finished" run.txt > finished_count.txt
grep -i "step" run.txt | wc -l > step_count.txt
grep -i "warning" run.txt | wc -l > warning_count.txt
cat finished_count.txt
cat step_count.txt
cat warning_count.txt
```

分别保存数字 3、2、1。

`wc -l finished_count.txt` 统计的是结果文件本身有几行，不是把文件里的数字当作数量。

组合思路：

```text
搜索得到文字行 → 管道传给统计命令 → 得到数字 → 重定向保存
```

## 9. 集中检查易错点

- 命令与参数间留空格：`ls -a`、`head -n 3 run.txt`。
- 选项前带横线：`-ic`，不是 `ic`。
- `ls -l` 是字母 l，`ls -1` 是数字 1。
- 文件名逐字核对：`run.txt` 与 `run.xtx` 不同。
- 先确认当前位置，再决定是否加文件夹路径。
- 复制和移动都是来源在前、目标在后。
- 同样的选项在不同命令中可能含义不同，例如 `echo -n` 不换行，`grep -n` 显示行号。
- `>` 会覆盖，`>>` 才是追加。
- 左边命令失败时，右边 `wc -l` 仍可能显示 0；看到数字也要检查是否有报错。
- 搜索无匹配时使用 `>`，仍会创建或清空目标文件。

## 10. 自测题

1. 当前在练习目录，如何查看 logs/run.txt 的前 3 行？
2. 如何把这个文件复制到当前目录，副本叫 copy.txt？
3. 如何把 copy.txt 改名为 saved.txt，再移入已有的 backup 文件夹？
4. 如何在日志末尾追加 All done，保留原内容？
5. 如何忽略大小写搜索 warning，并显示原行号？
6. 如何只保存 finished 的最后一条匹配结果？
7. 如何用管道统计 Step 的匹配行数，并保存为 step_count.txt？
8. 为什么 grep -c 的结果再传给 wc -l，通常得到 1？

复习时先独立写出命令；不熟悉管道时，先运行左边搜索，再连接右边处理，最后加保存。
