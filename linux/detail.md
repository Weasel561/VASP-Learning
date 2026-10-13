# Linux、Git 与 VASP 计算命令查阅笔记

适用对象：已经练习过 Day01–Day08、正在接触材料计算的初学者。整理日期：2026-10-13。

建议放入 GitHub 仓库的路径：`linux/detail.md`。

这份笔记是查阅手册，不是需要整篇粘贴执行的脚本。标为“已学”的内容来自此前练习；标为“后续查阅”的内容可以等实际遇到时再学习。示例中的练习数据都是人为构造的，不是真实计算结果。

## 目录

- [1. 使用环境、路径与文件位置](#environment)
- [2. 如何读懂一条命令](#syntax)
- [3. 目录查看与切换：已学](#navigation)
- [4. 文件和文件夹管理：已学](#files)
- [5. 写入、覆盖和追加：已学](#writing)
- [6. 查看文件内容：已学](#reading)
- [7. 搜索与统计：已学](#searching)
- [8. 管道、排序与去重：已学](#pipelines)
- [9. 权限与终端颜色：已接触，后续查阅](#permissions)
- [10. 查找文件、比较文件、查看容量：后续查阅](#inspection)
- [11. 文本提取与简单自动化：后续查阅](#automation)
- [12. VASP 文件与结果查看](#vasp)
- [13. 服务器连接、传输与作业管理：后续查阅](#server)
- [14. Git 保存、上传和同步](#git)
- [15. 常见报错与错误写法](#troubleshooting)
- [16. 一次完整的基础复习练习](#practice)
- [17. 按实际需求查命令](#lookup)
- [18. 官方参考资料](#references)

<a id="environment"></a>

## 1. 使用环境、路径与文件位置

### 1.1 三个环境不要混淆

| 环境 | 例子 | 说明 |
| --- | --- | --- |
| Windows 电脑 | `F:\MG\OUTCAR` | Windows 磁盘上的文件；可以用编辑器打开 |
| Codespaces | `/workspaces/VASP-Learning` | 远程 Linux 工作环境中的仓库文件夹 |
| 课题组服务器 | 由账号和服务器配置决定 | 用于实际计算的另一个环境，文件不会自动出现在 Codespaces 中 |

Windows PowerShell 中的某些命令与 Bash 同名，但含义和选项不一定相同。本文的 `ls -l`、`grep`、`cp -r` 等默认是在 Linux 的 Bash 终端中使用。

Codespaces、GitHub 和计算服务器不是同一个地方：

- Codespaces 提供工作环境。
- 仓库是工作环境中的一个文件夹。
- GitHub 保存已经提交并推送的仓库版本。
- 计算服务器运行任务；结果需要通过传输工具或其他方式取回。

### 1.2 打开终端与辨认环境

在此前的 VS Code/Codespaces 界面中，可按 **Ctrl + 反引号键** 打开或隐藏集成终端；反引号通常在数字 1 左边。也可以通过菜单“终端 → 新建终端”。

终端只是输入命令的界面，不决定命令在哪台机器执行。看远程连接状态、提示符，并用 `hostname` 与 `pwd` 核对。

### 1.3 此前使用的两个主要位置

```text
Codespaces 的文件系统
/
├── home
│   └── codespace                    ← 你的主目录，简写为 ~
│       └── linux_practice           ← 实际练习文件夹
│           ├── day01-03
│           ├── day04
│           ├── day05
│           ├── day06
│           ├── day07
│           ├── day08
│           └── practice_1
│
└── workspaces
    └── VASP-Learning                ← 仓库根目录
        └── linux
            ├── day01.md
            ├── day02.md
            ├── ...
            ├── day08.md
            └── half.md
```

这是此前环境的示意，不是目录一定始终存在的保证；当前情况用 `pwd` 和 `ls` 确认。

`linux/day04.md` 是笔记文件，`~/linux_practice/day04` 是练习文件夹。名称相似，不代表它们是同一个项目。

### 1.4 路径里的符号

| 符号或写法 | 含义 | 例子 |
| --- | --- | --- |
| `/` | 文件系统根目录；在路径中也用于分隔层级 | `/home/codespace` |
| `~` | 当前用户的主目录 | `~/linux_practice` |
| `.` | 当前目录 | `cp notes.txt ./backup/` |
| `..` | 上一级目录 | `cd ..` |
| 以 `/` 开头的路径 | 绝对路径，从系统根目录开始定位 | `/workspaces/VASP-Learning/linux` |
| 相对路径 | 根据当前目录定位 | `backup/notes.txt` |
| `~/…` | 经 Bash 展开后，从主目录定位 | `~/linux_practice/day08` |

`cd -` 中的 `-` 是 Bash 的特殊参数，表示上一次工作目录；它不是“上一级”的路径符号。

主目录 `~` 不一定在所有机器上都叫 `/home/codespace`。服务器上的主目录通常不同。[Bash 的主目录展开说明](https://www.gnu.org/software/bash/manual/bash.html#Tilde-Expansion)

### 1.5 新文件到底创建在哪里

假设：

```bash
pwd
```

显示：

```text
/home/codespace/linux_practice/practice_1
```

那么：

```bash
touch test.txt
```

会在这个目录创建 `test.txt`，完整路径为：

```text
/home/codespace/linux_practice/practice_1/test.txt
```

而：

```bash
echo "hello" > backup/test.txt
```

会写到当前目录下的 `backup/test.txt`；要求 `backup` 文件夹已经存在。

```bash
echo "hello"
```

只在终端显示文字，不创建文件。

关键区别：命令可以查看其他路径，但只有 `cd` 等改变工作目录的操作才会改变“你现在在哪里”。

<a id="syntax"></a>

## 2. 如何读懂一条命令

### 2.1 基本结构

```text
命令名称  选项或参数  操作对象
```

例如：

```bash
head -n 5 run.txt
```

| 部分 | 含义 |
| --- | --- |
| `head` | 查看开头内容的命令 |
| `-n` | 使用行数指定范围 |
| `5` | 行数取 5 |
| `run.txt` | 要读取的文件 |

```bash
cp notes.txt backup/
```

表示：调用 `cp`，从 `notes.txt` 复制到已有的 `backup` 文件夹。

### 2.2 空格、大小写和文件名

- 命令与参数之间用空格，例如 `ls -l`。
- `POSCAR` 和 `poscar` 在常见 Linux 文件系统上是不同名字。
- `run.txt` 和 `run.xtx` 是不同名字。
- `.md`、`.txt` 是命名习惯，不决定项目是否为文件夹。
- `mkdir day08.md` 创建的是名字叫 `day08.md` 的文件夹。
- 名字含空格时，把整个路径放入引号中。

```bash
cat "my notes.txt"
```

含义：读取名为 `my notes.txt` 的一个文件，而不是分别读取 `my` 和 `notes.txt`。

```bash
cd "$HOME/linux_practice"
```

含义：展开 `HOME` 为主目录，再进入其 `linux_practice` 子目录。

不要写 `cd "~/linux_practice"` 来期待主目录展开；双引号中的 `~` 不按这个方式展开。用 `cd ~/linux_practice` 或上面的 `"$HOME/…"`。

### 2.3 代码注释、提示符与输出

```bash
pwd    # 查看当前目录
```

`#` 后面是注释。为避免照抄混淆，也可以只输入 `pwd`。

终端常见提示符：

```text
@Weasel561 ➜ ~/linux_practice/day08 $
```

这表示终端正在等你输入，不是命令的一部分。不要把前面的用户名、路径、`$` 一起复制进去。

```text
new file: linux/day08.md
```

这是 Git 的状态输出，不是命令。输入它会得到 `command not found`。

### 2.4 同一个参数字母不一定表示同一个意思

| 写法 | 含义 |
| --- | --- |
| `grep -n` | 显示匹配行的原始行号 |
| `head -n 1` | 取第一行 |
| `sort -n` | 按数字大小排序 |
| `grep -c` | 统计匹配行数 |
| `uniq -c` | 统计每组相邻相同行的次数 |
| `sort -c` | 检查输入是否已排好序 |
| `cp -r` | 递归复制文件夹 |
| `sort -r` | 反转排序顺序 |

参数含义必须连着命令记，不能单独把 `-n` 或 `-c` 当成通用功能。

<a id="navigation"></a>

## 3. 目录查看与切换：已学

### 3.1 查看当前位置与目录内容

| 命令 | 每一句的含义 | 是否改变当前位置 |
| --- | --- | --- |
| `pwd` | 显示当前工作目录的完整路径 | 否 |
| `ls` | 列出当前目录的非隐藏项目 | 否 |
| `ls -l` | 用长格式显示类型、权限、所有者、大小、时间等 | 否 |
| `ls -1` | 每行只显示一个名字；这里是数字 1 | 否 |
| `ls -a` | 包含隐藏项目，也会显示 `.` 和 `..` | 否 |
| `ls -la` | 合并 `-l` 和 `-a`，详细显示全部项目 | 否 |
| `ls -lh` | 长格式，并用更容易读的容量单位显示大小 | 否 |
| `ls backup` | 列出指定 `backup` 文件夹里的内容 | 否 |
| `ls -ld backup` | 查看 `backup` 文件夹本身的属性 | 否 |
| `ls ~/linux_practice` | 查看主目录下练习文件夹中的项目 | 否 |
| `ls linux/day08.md` | 查看这个指定项目是否能被找到 | 否 |

`ls -l` 的 `l` 是字母，`ls -1` 的 `1` 是数字。

`ls` 没有输出，通常意味着没有它会显示的项目；检查隐藏项目要使用 `ls -a`。

目录在 `ls -l` 中显示的大小不是其全部内容的总容量；汇总容量用后面的 `du`。

### 3.2 切换目录

| 命令 | 含义 |
| --- | --- |
| `cd backup` | 进入当前目录中的 `backup` 子目录 |
| `cd ..` | 进入当前目录的父目录 |
| `cd ../..` | 连续向上两级 |
| `cd ~` | 回到自己的主目录 |
| `cd` | 不带参数时，通常与 `cd ~` 一样 |
| `cd /` | 进入系统根目录 |
| `cd -` | 返回上一次工作目录，并通常显示目的路径 |
| `cd ~/linux_practice` | 从任何当前位置进入主目录里的练习目录 |
| `cd /workspaces/VASP-Learning` | 进入这个绝对路径指定的仓库目录 |

`cd` 不会创建不存在的文件夹。

### 3.3 上一级和上一次的区别

```bash
cd ~/linux_practice/practice_1
cd /workspaces/VASP-Learning
cd -
```

最后回到 `practice_1`，因为它是上一次位置。再执行 `cd -`，通常在这两个位置之间切换。

如果在 `/workspaces/VASP-Learning` 执行：

```bash
cd ..
```

会进入 `/workspaces`，因为这是父目录。它不关心你上一次来自哪里。

### 3.4 文件系统根目录与仓库根目录

- 系统根目录是 `/`。
- 本例仓库根目录是 `/workspaces/VASP-Learning`。
- 文件夹 `linux` 是仓库根目录里的一个子目录。

“根目录”必须看语境。`cd /` 不会把你带到仓库根目录。

参考：[GNU 目录查看说明](https://www.gnu.org/software/coreutils/manual/coreutils.html#Directory-listing)、[Bash 内置命令说明](https://www.gnu.org/software/bash/manual/bash.html#Bourne-Shell-Builtins)。

<a id="files"></a>

## 4. 文件和文件夹管理：已学

本节示例假定在练习目录中执行。目标位置是否存在，会影响复制和移动的结果。

### 4.1 创建

| 命令 | 含义 |
| --- | --- |
| `mkdir backup` | 创建一个文件夹；同名项目已存在时通常报错 |
| `mkdir -p logs/run01` | 必要时逐级创建 `logs` 和 `run01`；目录已存在不报这个错误 |
| `touch notes.txt` | 文件不存在时创建空文件；存在时更新时间戳，不清空内容 |
| `touch a.txt b.txt` | 对两个指定文件分别执行 `touch` 操作 |

`touch` 不会自动创建父文件夹。`touch logs/run.txt` 要求 `logs` 已经存在。

### 4.2 复制文件

| 命令 | 含义与前提 |
| --- | --- |
| `cp notes.txt notes_copy.txt` | 在当前目录创建副本；源文件保留 |
| `cp notes.txt backup/` | 把文件复制进已经存在的 `backup` 文件夹 |
| `cp backup/notes.txt restored.txt` | 把子目录中的源文件复制出来，目标名字为 `restored.txt` |
| `cp -i notes.txt notes_copy.txt` | 目标文件已存在且将覆盖时，先询问 |
| `cp -p notes.txt notes_copy.txt` | 复制并尽量保留原文件的时间、权限等属性，受账号权限限制 |

结构：

```text
cp 源路径 目标路径
```

源在前，目标在后。目标是已有目录时，通常把文件放进去；目标是文件路径时，通常把内容写到这个目标文件。

普通 `cp` 可能覆盖已有目标文件。`-i` 更适合练习时需要确认覆盖的情况。

### 4.3 复制文件夹

```bash
cp -r backup backup_copy
```

含义：递归复制 `backup` 及其内部内容。这里假定 `backup_copy` 尚不存在。

```text
执行前：
backup/notes.txt

执行后：
backup/notes.txt
backup_copy/notes.txt
```

如果 `backup_copy` 已经是一个目录，重复执行同样的命令可能得到 `backup_copy/backup/…`，不能无条件认为每次都生成相同层级。

```bash
cp -a backup backup_copy
```

后续查阅：以归档方式复制，尽量保留权限、时间、符号链接等属性。也是复制，不是自动备份系统。

### 4.4 移动与重命名

| 命令 | 含义 |
| --- | --- |
| `mv notes_copy.txt saved.txt` | 将文件改名为 `saved.txt` |
| `mv saved.txt backup/` | 将文件移进已有的 `backup` 文件夹 |
| `mv backup backup_old` | 目标不存在时，将文件夹改名为 `backup_old` |
| `mv -i notes.txt saved.txt` | 若操作将覆盖已有目标，先询问 |

`mv` 没有“只给源路径就自动知道目的地”的功能。

```bash
mv backup
```

缺少目标，会报 `missing destination file operand`。

移动普通文件夹不需要 `mv -r`；GNU `mv` 没有这个用于递归移动的选项。

### 4.5 删除

| 命令 | 含义 | 注意事项 |
| --- | --- | --- |
| `rm restored.txt` | 删除指定普通文件 | 通常不进入回收站 |
| `rm -i restored.txt` | 删除前询问 | 输入 `y` 才确认 |
| `rmdir empty_dir` | 删除空文件夹 | 里面还有项目时拒绝删除 |
| `rm -r backup_copy` | 删除文件夹及其中内容 | 递归删除，先确认对象 |
| `rm -ri backup_copy` | 递归处理并逐项询问删除 | 仍是实际删除，不是预览 |

删除前可以查看：

```bash
pwd
ls -ld backup_copy
ls -la backup_copy
```

分别确认当前位置、目标类型、目标内部内容。不要把真实计算目录当成练习副本删除。

`rm` 不一定有可恢复副本。已提交文件可以借助 Git 历史恢复；未提交且没有备份的文件不能因此保证恢复。

### 4.6 此前漏步骤的例子

```bash
mkdir backup
touch notes.txt
cp notes.txt notes_copy.txt
cp notes.txt backup/
cp backup/notes.txt restored.txt
```

各句含义：

1. 创建 `backup` 文件夹。
2. 在当前目录创建 `notes.txt`。
3. 在当前目录复制一份 `notes_copy.txt`。
4. 把 `notes.txt` 复制进 `backup`。
5. 从 `backup` 中复制 `notes.txt` 出来，命名为 `restored.txt`。

漏掉第四句，第五句就可能找不到 `backup/notes.txt`。文件名正确，不代表文件已经位于你写的路径中。

参考：[GNU 文件操作说明](https://www.gnu.org/software/coreutils/manual/coreutils.html#Basic-operations)。

<a id="writing"></a>

## 5. 写入、覆盖和追加：已学

### 5.1 echo 与重定向

| 命令 | 含义 |
| --- | --- |
| `echo "Calculation started"` | 在终端输出文字，通常在末尾加换行 |
| `echo "Calculation started" > run.txt` | 将输出写入当前目录的 `run.txt`；不存在则创建，存在则清空后写入 |
| `echo "Step 1 finished" >> run.txt` | 在文件末尾追加这行；文件不存在时也可创建 |
| `echo "Warning" > logs/run.txt` | 写入当前目录下 `logs` 中的文件；`logs` 要已存在 |
| `echo "# 我的笔记" > notes.md` | 写入一行带 Markdown 一级标题标记的文字 |
| `echo "今天的内容" >> notes.md` | 给笔记追加正文，不自动变成标题 |
| `echo -n "hello"` | 输出文字但不自动添加末尾换行 |

`>` 与 `>>` 是 Shell 的重定向符号，不是 `echo` 独有的参数。`grep`、`sort` 等命令的输出也可以这样保存。

Markdown 标题来自 `# ` 这样的内容格式，不来自 `echo` 或 `.md` 后缀。

### 5.2 以三行日志为例

```bash
echo "Step 1 finished" > run.txt
echo "Warning: check input" >> run.txt
echo "Step 2 finished" >> run.txt
cat run.txt
```

按顺序：

1. 用第一句建立或覆盖日志。
2. 追加警告行。
3. 追加第二个完成记录。
4. 显示全部内容。

输出应为：

```text
Step 1 finished
Warning: check input
Step 2 finished
```

如果第二句也用 `>`，第一行会被覆盖，而不是保留下来。

### 5.3 seq 与 printf：已接触 / 后续查阅

```bash
seq 1 12 > numbers.txt
```

生成从 1 到 12 的整数，每个一行，将结果写入 `numbers.txt`。

```bash
printf '%s\n' "finished" "warning" "running" > status.txt
```

`printf` 按格式输出。这里 `%s` 表示字符串，`\n` 表示换行，三个字符串各占一行。对复杂转义和可移植脚本，`printf` 通常比不同环境中的 `echo` 行为更可预测。

### 5.4 同时保存标准输出和报错：后续查阅

终端输出常分成：

| 编号 | 名称 | 例子 |
| --- | --- | --- |
| 0 | 标准输入 | 管道送进命令的文字 |
| 1 | 标准输出 | 正常结果 |
| 2 | 标准错误 | 文件不存在等错误信息 |

```bash
cat missing.txt > output.txt 2> error.txt
```

正常输出写入 `output.txt`，错误信息写入 `error.txt`。如果 `missing.txt` 不存在，错误文件会记录原因；`output.txt` 仍可能已经被建立或清空。

```bash
cat notes.txt > combined.log 2>&1
```

先把标准输出指向 `combined.log`，再让标准错误跟随标准输出；两种输出写入同一文件。重定向从左到右处理，顺序会影响结果。

### 5.5 tee：一边显示一边保存

```bash
grep "finished" run.txt | tee finished.txt
```

把搜索结果交给 `tee`：终端显示一份，同时写入 `finished.txt`，覆盖已有文件。

```bash
grep "finished" run.txt | tee -a finished.txt
```

终端显示结果，并追加到 `finished.txt`，不清空已有内容。

### 5.6 不要让输出覆盖正在读取的源文件

错误示例：

```text
sort run.txt > run.txt
```

Shell 可能先清空右边的 `run.txt`，左边就读不到原内容了。

正确的另存写法：

```bash
sort run.txt > run_sorted.txt
```

GNU `sort` 还有可用于原地排序的 `-o` 选项，但入门练习先用另存文件，便于保留原始内容。

参考：[Bash 重定向说明](https://www.gnu.org/s/bash/manual/html_node/Redirections.html)、[GNU sort 说明](https://www.gnu.org/software/coreutils/manual/html_node/sort-invocation.html)。

<a id="reading"></a>

## 6. 查看文件内容：已学

### 6.1 cat、head、tail

| 命令 | 含义 |
| --- | --- |
| `cat run.txt` | 把整个文件内容显示到终端 |
| `cat -n run.txt` | 显示内容，并给每行加行号 |
| `cat a.txt b.txt` | 按顺序显示两个文件；不修改它们 |
| `head run.txt` | 默认显示前 10 行 |
| `head -n 1 run.txt` | 显示第一行 |
| `head -n 5 run.txt` | 显示前 5 行；文件不足 5 行时显示已有行 |
| `tail run.txt` | 默认显示最后 10 行 |
| `tail -n 1 run.txt` | 显示最后一行 |
| `tail -n 5 run.txt` | 显示最后 5 行 |
| `head -n -1 run.txt` | 在 GNU head 中显示“除最后一行外”的内容 |

`head -n 1` 与 `head -n -1` 不是同一个意思。在仅有两行时，二者结果可能恰巧一样；换成更多行就能看出区别。[GNU head 说明](https://www.gnu.org/s/coreutils/manual/html_node/head-invocation.html)

`cat run.txt` 根据当前位置找文件。若文件在子目录，应使用 `cat logs/run.txt`；或者先 `cd logs`，再 `cat run.txt`。

### 6.2 less：查看长文件

```bash
less OUTCAR
```

分页打开 `OUTCAR`，适合大量计算输出。进入 `less` 后的操作不是 Shell 命令：

| 按键 | 含义 |
| --- | --- |
| 空格 | 向下翻一页 |
| `b` | 向上翻一页 |
| `g` | 去开头 |
| `G` | 去末尾 |
| `/关键词`，回车 | 向后搜索 |
| `n` | 下一个匹配 |
| `N` | 上一个匹配 |
| `q` | 退出 less，返回 Shell |

例如输入 `/TOTEN` 再回车，搜索能量记录。按 `q` 才退出查看界面。

### 6.3 跟踪正在更新的文件

```bash
tail -f OSZICAR
```

先显示末尾若干行，再持续显示新追加的内容。适合在另一个终端监视正在计算的任务。

```bash
tail -n 5 -f OSZICAR
```

先显示最后 5 行，然后跟踪新内容。

```bash
tail -F OSZICAR
```

后续查阅：按文件名跟踪，并在文件暂时不存在或被替换时重试，和单纯 `-f` 的跟踪方式有所不同。

在监视终端按 `Ctrl+C`，中断的是当前前台 `tail` 监视命令；不等于取消另一个终端或队列中运行的计算。若当前前台运行的正是 VASP，按 `Ctrl+C` 的作用就不同。

参考：[GNU tail 说明](https://www.gnu.org/software/coreutils/manual/coreutils.html#tail-invocation)。

<a id="searching"></a>

## 7. 搜索与统计：已学

### 7.1 grep 的基本用法

```text
grep 选项 "搜索内容" 文件路径
```

| 命令 | 含义 |
| --- | --- |
| `grep "finished" run.txt` | 显示包含 `finished` 的整行 |
| `grep "finish" run.txt` | 默认搜索中，`finish` 也能匹配 `finished` 内的这段文字 |
| `grep -n "finished" run.txt` | 在匹配行前显示它在原文件中的行号 |
| `grep -i "warning" run.txt` | 忽略大小写，可匹配 `Warning` |
| `grep -in "warning" run.txt` | 同时忽略大小写、显示行号 |
| `grep -c "finished" run.txt` | 输出包含匹配内容的行数 |
| `grep -ic "warning" run.txt` | 忽略大小写并统计匹配行数 |
| `grep -v "warning" run.txt` | 显示不匹配该模式的行；默认仍区分大小写 |
| `grep -iv "warning" run.txt` | 忽略大小写，显示不含该内容的行 |

`grep -c` 统计的是匹配行数，不是单词出现的总次数。一行里出现两个 `finished`，通常仍只计一行。

### 7.2 后续会用到的 grep 选项

| 命令 | 含义 |
| --- | --- |
| `grep -F "a.b" notes.txt` | 按固定字符串找 `a.b`，不把点当正则符号 |
| <code>grep -E "warning&#124;error" run.txt</code> | 按扩展正则搜索；这里匹配 warning 或 error |
| <code>grep -inE "warning&#124;error" OUTCAR</code> | 忽略大小写，搜索两类文字，显示行号 |
| `grep -A 3 "TOTAL-FORCE" OUTCAR` | 显示匹配行及其后 3 行 |
| `grep -B 2 "TOTEN" OUTCAR` | 显示匹配行及其前 2 行 |
| `grep -C 2 "reached required accuracy" OUTCAR` | 显示匹配行及前后各 2 行 |
| `grep -n "ENCUT" INCAR` | 定位输入中包含 ENCUT 的行；注释也可能匹配 |

上表的正则“或”符号是普通 `|`，可以直接使用下面这个代码块：

```bash
grep -inE 'warning|error' OUTCAR
```

`|` 位于引号内时，是交给 `grep -E` 的正则内容；位于引号外时，通常是 Shell 管道。

关键词匹配不等于诊断结论。`error` 可能出现在“误差估计”的说明里；没有匹配行也不能证明计算完全正确，要读上下文。

### 7.3 wc：行数、单词数和字节数

| 命令 | 含义 |
| --- | --- |
| `wc -l run.txt` | 统计换行符数量，并显示文件名 |
| `wc -w run.txt` | 统计按空白分隔的单词数量 |
| `wc -c run.txt` | 统计字节数，不是中文字符数量 |
| <code>cat run.txt &#124; wc -l</code> | 用管道提供内容，统计换行符数；通常不显示原文件名 |

普通使用中把 `wc -l` 叫“统计行数”很方便，但严格说它统计换行符。最后一行没有结尾换行时，可能和眼睛看到的文本行数不同。

### 7.4 保存匹配内容与保存统计数字的区别

```bash
grep -i "warning" run.txt > warnings.txt
```

保存的是匹配到的原始行。

```bash
grep -ic "warning" run.txt > warning_count.txt
```

保存的是一个统计数字。

如果 `warning_count.txt` 内容是：

```text
1
```

那么：

```bash
cat warning_count.txt
wc -l warning_count.txt
```

第一句显示数字 `1`；第二句统计“存放这个数字的文件”有多少换行符，通常也是 1。即使文件内容改成 `100`，它仍可能只有一行。

### 7.5 没有匹配和执行失败不一样

GNU `grep` 的常见退出状态：

| 状态 | 意义 |
| --- | --- |
| 0 | 找到了匹配 |
| 1 | 没有匹配，通常不是命令语法错误 |
| 2 | 发生错误，例如文件不存在 |

`grep "warning" run.txt > warnings.txt` 没有找到匹配时，也可能把已有 `warnings.txt` 清空成空文件。这是重定向行为，不表示文件未保存。

参考：[GNU grep 手册](https://www.gnu.org/s/grep/manual/grep.html)、[grep 退出状态](https://www.gnu.org/s/grep/manual/html_node/Exit-Status.html)、[GNU wc 说明](https://www.gnu.org/software/coreutils/manual/coreutils.html#wc-invocation)。

<a id="pipelines"></a>

## 8. 管道、排序与去重：已学

### 8.1 管道的作用

```bash
grep "finished" run.txt | tail -n 1
```

从左到右：

1. `grep` 读取 `run.txt`，输出匹配行。
2. `|` 把这份标准输出交给 `tail`。
3. `tail -n 1` 取收到内容的最后一行。

这取的是搜索结果的最后一行，不一定是原文件的最后一行。

右边必须有命令。`sort run.txt | -c` 只有选项、没有命令，因此会找不到名叫 `-c` 的命令。

### 8.2 常见组合

| 命令 | 含义 |
| --- | --- |
| <code>grep "finished" run.txt &#124; head -n 1</code> | 取搜索结果的第一行 |
| <code>grep "finished" run.txt &#124; tail -n 1</code> | 取搜索结果的最后一行 |
| <code>grep "finished" run.txt &#124; wc -l</code> | 统计输出中的换行符数量，常用来统计匹配行数 |
| <code>grep -i "warning" run.txt &#124; wc -l</code> | 忽略大小写搜索后统计行数 |
| `grep "finished" run.txt > finished.txt` | 保存全部匹配行 |
| <code>grep "finished" run.txt &#124; tail -n 1 &gt; last_step.txt</code> | 保存搜索结果的最后一行 |
| <code>grep "finished" run.txt &#124; head -n 1 &gt; first_step.txt</code> | 保存搜索结果的第一行 |

表格中的竖线表示管道，真正输入时使用普通字符 `|`，不加反斜线。

### 8.3 sort 与 uniq

准备这个示例文件：

```bash
printf '%s\n' "finished" "warning" "finished" "running" "warning" "finished" > status.txt
```

它有 6 行：finished 3 行、warning 2 行、running 1 行。

| 命令 | 含义 |
| --- | --- |
| `sort status.txt` | 按当前语言环境的文本排序规则排列各行 |
| `sort -r status.txt` | 反转文本排序方向 |
| `sort -n numbers.txt` | 按开头数字的数值排序，默认从小到大 |
| `sort -nr numbers.txt` | 按数字从大到小排序 |
| `sort -c status.txt` | 检查是否已经排序；发现无序时报告，不替你排序 |
| `uniq status.txt` | 只合并相邻且相同的行 |
| <code>sort status.txt &#124; uniq</code> | 先把相同行放在一起，再去重 |
| <code>sort status.txt &#124; uniq -c</code> | 每组显示“次数＋内容” |
| `sort -u status.txt` | 排序并去除重复行；这是普通文本去重的另一种写法 |

`uniq` 只处理相邻重复行。两行相同内容中间夹着其他内容，直接 `uniq` 不会把它们合并。[GNU uniq 说明](https://www.gnu.org/s/coreutils/manual/html_node/uniq-invocation.html)

文本排序受语言环境影响；需要可重复的脚本排序规则时，可以查阅 `LC_ALL=C sort status.txt`。这只为该条命令指定 C 排序环境，不是重命名文件。

### 8.4 数字排序为什么要 -n

假设文件内容是：

```text
2
10
3
```

按文本比较，常见结果是 10、2、3；按数值比较应是 2、3、10。`sort -n` 用于后者。

### 8.5 完整的统计组合

```bash
sort status.txt | uniq -c > status_count.txt
cat status_count.txt
```

第一句：排序、统计重复次数、保存结果。第二句：查看保存的文件。

输出通常是：

```text
      3 finished
      1 running
      2 warning
```

```bash
sort -n status_count.txt
```

按开头次数从小到大，得到 1 running、2 warning、3 finished。

```bash
sort -nr status_count.txt
```

按次数从大到小。

```bash
sort status.txt | uniq -c | sort -nr | head -n 1 > most_common.txt
cat most_common.txt
```

顺序为：把相同行集中 → 统计次数 → 按次数降序 → 取第一行 → 保存 → 查看。

预期内容：

```text
      3 finished
```

如果最多次数并列，`head -n 1` 只会留下排序后的一个项目，不能称为“保留了所有并列第一”。

### 8.6 管道里的 0 不一定说明搜索结果确实为零

此前出现过这种情况：左边命令拼错，右边 `wc -l` 仍显示 0。原因是右边没有收到正常输出，不代表左边成功读取了文件。

Bash 默认以最后一个命令的退出状态作为管道状态。后续写脚本可以查阅：

```bash
set -o pipefail
```

含义：管道中某个命令非零退出时，不简单地把最后一个成功命令当成整条管道成功。注意 `grep` “无匹配”的状态也非零，因此仍要结合具体命令判断。

参考：[GNU sort 说明](https://www.gnu.org/software/coreutils/manual/html_node/sort-invocation.html)、[Bash 管道说明](https://www.gnu.org/software/bash/manual/bash.html#Pipelines)。

<a id="permissions"></a>

## 9. 权限与终端颜色：已接触，后续查阅

### 9.1 读取权限串

```bash
ls -ld day04 day04-folder-backup
```

查看两个目录本身的属性，而不是各自里面的文件列表。

示例：

```text
drwxr-xr-x
drwxrwxrwx
```

拆开读：

```text
d | rwx | r-x | r-x
    所有者  所属组  其他用户
```

| 符号 | 含义 |
| --- | --- |
| 开头 `d` | 文件夹 |
| 开头 `-` | 普通文件 |
| 开头 `l` | 符号链接 |
| `r` | 读取权限；对目录主要是列出条目 |
| `w` | 写权限；对目录主要是增删或改名其中条目，通常还需要 x |
| `x` | 对文件是执行权限；对目录是搜索、穿越权限 |
| `-` | 没有对应权限 |
| 权限串后面的 `+` | 常见 Linux 环境下提示还有 ACL 等额外访问控制信息 |

`x` 对目录不只是“能输入 cd”，还影响访问已知名字的内部条目。删除文件通常主要受父目录权限控制，不仅由该文件是否可写决定。

### 9.2 颜色只是提示

| GNU ls 常见默认显示 | 通常提示 |
| --- | --- |
| 蓝色文字 | 目录 |
| 绿色文字 | 有执行权限的普通文件 |
| 绿色背景 | 对“其他用户”开放写权限的目录类别，可能还区分 sticky 属性 |
| 青色文字 | 符号链接 |

实际配色可以配置。不能用颜色代替 `ls -l` 的类型和权限信息。[GNU 默认配色源码](https://raw.githubusercontent.com/coreutils/coreutils/master/src/dircolors.hin)

`backup` 只是名字，文件夹不会因为这个名字自动进行备份。

### 9.3 chmod：会改变权限，按需使用

| 命令 | 含义 |
| --- | --- |
| `chmod u+x run.sh` | 给文件所有者增加执行权限 |
| `chmod u-x run.sh` | 去掉所有者的执行权限 |
| `chmod u=rw,go=r notes.txt` | 所有者可读写，所属组和其他用户只读 |
| `chmod 644 notes.txt` | 同样设置为所有者读写，其他组只读 |
| `chmod 755 run.sh` | 所有者读写执行，所属组和其他用户读和执行 |

数字来自 `r=4、w=2、x=1`，相加得到每组权限。

权限不是“越开放越好”。看到绿色背景并不要求立即修改；要先理解共享目录的用途和服务器规则。`bash run.sh` 通常只需要可读取的脚本，不要求脚本文件本身已经有执行位。

<a id="inspection"></a>

## 10. 查找文件、比较文件、查看容量：后续查阅

### 10.1 find：找路径

| 命令 | 含义 |
| --- | --- |
| `find . -name 'OUTCAR'` | 从当前目录递归查找名字恰为 OUTCAR 的项目 |
| `find . -type f -name 'OUTCAR'` | 只查找普通文件 |
| `find . -type d -name 'backup'` | 只查找名字为 backup 的文件夹 |
| `find ~/linux_practice -type f -name '*.txt'` | 在练习目录中查找以 .txt 结尾的普通文件 |
| `find . -maxdepth 2 -type f` | GNU find 中，最多查到当前起点以下两级的普通文件 |

`.` 表示从当前目录开始，不代表查整台服务器。用引号包住 `'*.txt'`，让模式交给 `find`，而不是提前在当前目录展开。

`find` 找文件名和路径，`grep` 搜文件内容。[GNU Findutils 手册](https://www.gnu.org/software/findutils/manual/html_node/find_html/index.html)

### 10.2 diff：比较内容

```bash
diff notes.txt notes_copy.txt
```

显示两个文件内容的差别。没有输出通常表示两者内容相同。

```bash
diff -u notes.txt notes_copy.txt
```

显示带上下文的统一差异，方便读修改位置。常见 `-` 行属于旧文件，`+` 行属于新文件。

```bash
diff -u POSCAR CONTCAR
```

查看起始结构文件和末次结构文件的文字差异。格式变化、坐标表示变化也会产生文本差异；它不是自动完成物理结构等价性分析的工具。

### 10.3 file、du、df

| 命令 | 含义 |
| --- | --- |
| `file OUTCAR` | 根据文件内容等信息判断类型，例如文本 |
| `file charge-density.hdf5` | 判断它是否属于 HDF5 等二进制数据 |
| `du -sh .` | 汇总当前目录及内容占用的磁盘空间，使用易读单位 |
| `du -sh backup` | 汇总指定目录的占用 |
| `df -h .` | 显示当前路径所在文件系统的总容量和可用容量 |
| `ls -lh OUTCAR` | 查看一个文件的逻辑大小 |

`du` 主要看文件或目录用了多少空间；`df` 看所在文件系统还剩多少空间。配额限制可能比 `df` 显示的可用空间更严格。

不要用 `cat` 查看 WAVECAR、HDF5 等大型二进制文件；文本查看工具不会自动把它们解释成科学数据。

### 10.4 tar：打包、查看和解包

假设当前目录下已经有自己的结果副本 `calc_copy`：

```bash
tar -czf calc_copy.tar.gz calc_copy
```

将目录打包并用 gzip 压缩，写入 `calc_copy.tar.gz`。`-c` 创建归档，`-z` 使用 gzip，`-f` 后面指定归档文件名。原目录保留，同名归档可能被覆盖。

```bash
tar -tzf calc_copy.tar.gz
```

只列出归档内容。`-t` 是列出，不解包。

```bash
mkdir unpack_new
tar -xzf calc_copy.tar.gz -C unpack_new
```

先创建一个新的解包目录；创建成功后，`-x` 解包，`-C` 指定目的目录。解包后通常出现 `unpack_new/calc_copy/…`，而不是把原目录名自动去掉。旧目录中的同名文件可能被解包覆盖，因此使用新目录更容易核对。

只处理可信来源的归档，先查看内部路径与文件名。打包全部结果前，考虑大文件容量、所需保留的数据和数据许可。[GNU tar 手册](https://www.gnu.org/software/tar/manual/tar.html)

<a id="automation"></a>

## 11. 文本提取与简单自动化：后续查阅

这一节先查用，不需要今天掌握所有语法。使用自动化前，先在一个样例文件上验证输出。

### 11.1 sed：取指定行段、预览文字替换

```bash
sed -n '1,20p' POSCAR
```

`-n` 关闭默认自动打印；`1,20p` 表示只打印第 1–20 行。

```bash
sed -n '20,40p' OUTCAR
```

只显示第 20–40 行，适合读取特定上下文。

```bash
sed 's/warning/Warning/g' run.txt
```

在每一行中，把所有匹配的 `warning` 替换成 `Warning`，显示结果，不修改原文件。`s` 表示替换，`g` 表示每行内全部匹配。

```bash
sed 's/warning/Warning/g' run.txt > run_preview.txt
```

将替换结果另存，保留原文件。

GNU `sed -i` 会修改原文件，不能把它与“仅显示替换结果”的写法混为一谈。实际修改 INCAR 参数时，先确认匹配范围；有注释或多个同名参数时，简单替换可能改错位置。[GNU sed 手册](https://www.gnu.org/software/sed/manual/sed.html)

### 11.2 awk：按字段提取

假设 `status_count.txt` 中是“次数＋状态”：

```bash
awk '{print $1}' status_count.txt
```

逐行输出第一列。默认字段通常按空白分隔。

```bash
awk '{print $2}' status_count.txt
```

逐行输出第二列。

```bash
awk '{sum += $1} END {print sum}' status_count.txt
```

把每行第一列累加，最后显示总和。这里的变量 `sum` 是 awk 程序内部变量。

```bash
awk '/free[[:space:]]+energy[[:space:]]+TOTEN/ {print $5}' OUTCAR
```

对常见的 `free energy TOTEN = 数值 eV` 格式，提取第五列能量数值。先查看匹配行，确认列结构，再依赖字段号。

```bash
awk '/free[[:space:]]+energy[[:space:]]+TOTEN/ {print $5}' OUTCAR | tail -n 1
```

提取这类能量记录的最后一个数值。

`TOTEN` 是 VASP 输出中的总自由能，不应不加区分地拿它作为形成能、带隙或稳定性结论。不同能量定义需按目标选用。[VASP OSZICAR 能量说明](https://vasp.at/wiki/OSZICAR)

### 11.3 通配符：文件名匹配，不是内容搜索

| 写法 | 作用 |
| --- | --- |
| `ls *.txt` | 列出当前目录中匹配 .txt 后缀的非隐藏名字 |
| `ls day*.md` | 列出以 day 开头、以 .md 结尾的匹配名字 |
| `ls calc_*/OUTCAR` | 列出满足该路径模式的输出文件 |

`*` 通常表示任意长度的名字片段。Shell 一般先展开文件名匹配，再运行命令。没有匹配时，Bash 默认可能把模式原样传给命令，造成“文件不存在”的报错。

### 11.4 简单循环：批量查看，不批量启动计算

假设当前目录下有 `calc_A、calc_B、calc_C` 三个计算目录，并且各自有 OUTCAR：

```bash
for calc_dir in calc_A calc_B calc_C; do
    printf '%s\n' "$calc_dir"
    grep 'free.*TOTEN' "$calc_dir/OUTCAR" | tail -n 1
done
```

各句含义：

1. `for` 依次把三个目录名赋给 `calc_dir`。
2. `printf` 显示当前正在查看的目录名。
3. `grep` 与 `tail` 读取该目录中末次能量记录。
4. `done` 结束循环。

`"$calc_dir/OUTCAR"` 使用变量拼接路径，引号避免名称中空格导致参数被拆开。不存在的文件仍会报错，不能把空输出当作能量为零。

### 11.5 退出状态和 &&

```bash
echo $?
```

显示刚刚执行完的命令的退出状态。要紧跟被检查的命令，否则看到的是后来命令的状态。0 通常表示成功，非零的含义由命令决定。

```bash
cd ~/linux_practice/practice_1 && pwd
```

只有 `cd` 成功才执行 `pwd`。这有助于避免“切换目录失败，却继续在原目录操作”。

分号 `;` 是依次运行，不要求前一条成功。学习时逐行执行并检查输出更容易发现错误。

### 11.6 编辑器与命令帮助

| 命令 | 含义 |
| --- | --- |
| `nano notes.md` | 用 nano 编辑文件；要求已安装 nano |
| `bash script.sh` | 用 Bash 执行指定脚本，会真正运行其内容；先阅读脚本 |
| `./script.sh` | 执行当前目录的脚本，需要适当执行权限及解释器设置 |
| `man cp` | 查看 cp 的手册；通常按 q 退出分页器 |
| `cp --help` | 查看 GNU cp 自带的简要帮助 |
| `grep --help` | 查看 GNU grep 选项说明 |
| `help cd` | 查看 Bash 内置 cd 的帮助 |
| `history` | 查看当前 Shell 的命令历史，受历史配置影响 |
| `clear` | 清理终端显示；不删除文件，也不撤销已运行命令 |
| `command -v vasp_std` | 查找当前 Shell 能否找到 vasp_std，不启动它 |
| `type mpirun` | 查看 mpirun 是可执行文件、别名还是其他 Shell 定义 |

nano 中 `Ctrl+O` 写入保存，回车确认文件名，`Ctrl+X` 退出。也可以继续用 VS Code 编辑器；不必为了计算强制改用终端编辑器。

历史命令可能包含路径或敏感参数；发给别人之前查看内容，不把密码、令牌写进命令历史或仓库。

<a id="vasp"></a>

## 12. VASP 文件与结果查看

### 12.1 文件名和科学问题的关系

| 文件 | 作用 | 是否通常适合文本查看 |
| --- | --- | --- |
| `POSCAR` | 输入结构：晶胞、元素、数量、坐标，以及可能的移动限制 | 是 |
| `INCAR` | 任务、方法、算法与精度设置 | 是 |
| `KPOINTS` | 周期性电子计算的 k 点采样设置 | 是 |
| `POTCAR` | 按元素顺序组合的原子势数据 | 有文本内容，但不用全文打印或公开分发来学习 |
| `OUTCAR` | 详细参数、电子迭代、力、应力和运行信息 | 是，但可能很长 |
| `OSZICAR` | 简明的迭代和能量记录 | 是 |
| `CONTCAR` | 最后一步的结构，格式与 POSCAR 兼容 | 是 |
| `vasp.log` | 自己重定向保存的终端输出；名字可自定 | 取决于保存内容 |
| `WAVECAR` | 波函数相关的二进制数据 | 否 |
| `CHGCAR` | 电荷密度相关数据，可能较大 | 有可读格式，但不是只看几行就能分析电荷密度 |

输入四文件的用途和元素顺序见 [VASP 输入说明](https://vasp.at/wiki/Input)。`CONTCAR` 表示末次结构，不因文件存在就保证结构优化已收敛。[CONTCAR 说明](https://vasp.at/wiki/CONTCAR)

### 12.2 开始查看前确认目录

在 Linux 中进入那次实际计算目录后：

```bash
pwd
ls -lh
ls -l INCAR POSCAR KPOINTS POTCAR
```

分别显示当前位置、项目与大小、四个指定输入文件的属性。不能因为终端已打开就假定位置正确。

Windows 的 `F:\MG` 不会自动成为服务器上的路径；要在相应机器或环境中找到实际副本。

### 12.3 查看结构与任务设置

```bash
head -n 20 POSCAR
cat INCAR
cat KPOINTS
```

第一句看结构文件前 20 行；第二句看任务参数；第三句看采样设置。

POSCAR 常见开头依次包含：注释、缩放因子、三个晶胞向量、元素名称、原子数量、可选的 Selective dynamics、坐标类型和原子坐标。旧格式和不同写法会有区别，不要机械地把所有文件的固定行号都当成同一个参数。[POSCAR 说明](https://vasp.at/wiki/POSCAR)

```bash
grep -nE '^[[:space:]]*(IBRION|ISIF|NSW|EDIFF|EDIFFG|ENCUT|ISPIN)[[:space:]]*=' INCAR
```

定位这些以参数名开头的设置行，比简单搜索所有出现的字词更容易排除行首注释。但它仍不是完整 INCAR 解析器；缺省参数、同一行多设置和重复设置需要进一步核对 OUTCAR 中实际采用的值。

### 12.4 检查过程与结束标记

| 命令 | 用来查看什么 |
| --- | --- |
| `tail -n 20 OSZICAR` | 最后的电子迭代和结构步记录 |
| `grep 'F=' OSZICAR` | 常见结构迭代的能量汇总行 |
| `grep -c 'F=' OSZICAR` | 这类结构汇总行有几条；适用于确认过格式的本次优化 |
| `grep -F 'reached required accuracy' OUTCAR` | 是否有结构优化达到停止条件的标记 |
| `grep -F 'aborting loop because EDIFF is reached' OUTCAR` | 查找电子迭代达到 EDIFF 条件的标记 |
| `tail -n 40 OUTCAR` | 查看输出末尾，有无计时信息及结束上下文 |
| <code>grep -inE 'warning&#124;error' OUTCAR</code> | 定位相关文字，随后阅读上下文 |

注意：

- 固定结构的静态计算不必出现结构优化停止标记。
- 搜索到标记说明该类停止条件被触发，不说明所有可能的科学验证都完成了。
- 作业正常结束、电子收敛、结构收敛、截断能/k 点精度测试通过，是不同层次。
- `EDIFF` 控制电子迭代停止条件，不直接等于最终结果的绝对误差。[EDIFF 说明](https://vasp.at/wiki/EDIFF)

### 12.5 查看最后能量和压力

```bash
grep 'free.*TOTEN' OUTCAR | tail -n 1
```

取最后一条匹配的总自由能记录。它对应整次计算单元，不会自动变成每原子或每化学式能量。

```bash
grep -F 'external pressure' OUTCAR | tail -n 1
```

取最后一次压力记录。在 VASP 此处，`kB` 表示 kilobar（千巴），不是 Boltzmann 常数。1 kbar = 0.1 GPa。

```bash
grep -n -A 5 'TOTAL-FORCE' OUTCAR
```

显示每个力表标题及后面 5 行，帮助定位。它不保证展示完整力表，也不自动选择最后一张表。

### 12.6 读取原子受力的正确方式

力表中通常每个原子一行，前 3 列是位置，后 3 列是力分量：

```text
x  y  z  Fx  Fy  Fz
```

受力大小是：

```text
|F| = sqrt(Fx² + Fy² + Fz²)
```

判断最大受力，需要对相关原子的三个分量计算模长，再比较。不能只看 `Fx`，也不能把最显眼的一个分量直接当成所有原子的最大力。

有固定原子或 Selective dynamics 约束时，还要确认哪些方向实际参与优化。负的 `EDIFFG` 使用力条件停止结构优化，阈值取它的绝对值。[EDIFFG 说明](https://vasp.at/wiki/EDIFFG)

### 12.7 你提供的 Mg₄Ta₂O₉ 结果：与命令相连的实际例子

此前读取的文件为 Windows 副本 `F:\MG\INCAR`、`F:\MG\OSZICAR`、`F:\MG\OUTCAR`。以下是那次结果的记录，不是另一次新计算：

| 核对项 | 实际记录 | 能说明什么 |
| --- | --- | --- |
| 原子数量 | Mg 8、Ta 4、O 18，共 30 | 计算单元含 2 个化学式单元 |
| `IBRION = 2` | 结构优化算法 | 程序会按受力调整允许移动的原子位置 |
| `ISIF = 2` | 固定晶胞形状和体积 | 不进行完整晶格参数优化 |
| `NSW = 300` | 最大结构步数 | 不表示必须执行 300 步 |
| `EDIFFG = -0.05` | 力阈值 0.05 eV/Å | 用原子受力检查结构停止条件 |
| 实际结构评估 | 5 步 | 达到标准后提前结束 |
| 最后最大原子受力 | 约 0.0197 eV/Å | 低于本次设定的 0.05 阈值 |
| 最后 `TOTEN` | −233.73179072 eV | 整个 30 原子单元的总自由能记录 |
| 最后压力 | 43.09 kbar，约 4.31 GPa | 不是接近零压的晶胞 |
| 运行时间 | 2240.530 秒，约 37 分 21 秒 | 墙钟时间，不是所有并行进程 CPU 时间的总和 |

这些结论来自实际文件读取和最后力表的模长计算，不能只靠关键词搜索一条命令代替。

`ISIF = 2` 允许原子位置优化，却不改变晶胞形状和体积。因此“原子受力小”与“晶胞仍有残余压力”可以同时出现。是否应该进一步优化晶胞，取决于你的研究目标和结构来源，不是看到压力就直接修改参数重跑。[ISIF 说明](https://vasp.at/wiki/ISIF)

单个负的总能量不能证明材料稳定；确定结构稳定性、形成能或带隙等，需要不同的比较对象和计算任务。

### 12.8 启动命令：会真正消耗计算资源

```bash
mpirun -np 96 vasp_std
```

| 部分 | 含义 |
| --- | --- |
| `mpirun` | 启动 MPI 并行程序 |
| `-np 96` | 请求启动 96 个进程；不是计算精度，不一定等同于 96 个物理核心 |
| `vasp_std` | VASP 标准版可执行程序 |

此命令本身不规定“算结构优化还是其他任务”；实际任务由输入与运行环境决定。进程数、线程数和可用资源需要匹配服务器分配，进程越多不保证越快。[Open MPI 说明](https://docs.open-mpi.org/en/main/man-openmpi/man1/mpirun.1.html)、[VASP 可执行程序说明](https://vasp.at/wiki/Installing_VASP.6.X.X)

在已经允许直接运行、资源已分配的环境中，可以用：

```bash
mpirun -np 96 vasp_std > vasp.log 2>&1
```

含义：真正启动计算，并把终端的标准输出与标准错误合并写进 `vasp.log`。同名日志存在时会覆盖；VASP 本身也可能重写工作目录中的输出文件。这不是查阅结果的命令，也不是获取资源的队列命令。

本笔记不提供未经确认的通用 INCAR 参数模板。计算目的、结构约束、磁性、采样、截断能和目标精度，需要按具体任务选择。

<a id="server"></a>

## 13. 服务器连接、传输与作业管理：后续查阅

本节的 `your_user`、`server.example.org`、`/remote/calc` 是占位示例，要替换为自己的账号、服务器地址和真实路径。不要把这些示例当成已经存在的服务器。

### 13.1 ssh：连接远程机器

```bash
ssh your_user@server.example.org
```

通过 SSH 登录远程服务器。登录后输入的 `pwd`、`ls` 等查看的是远程机器，不是 Windows 本地磁盘。

```bash
ssh -p 2222 your_user@server.example.org
```

指定 SSH 端口为 2222；端口必须来自实际配置。

```bash
exit
```

退出当前远程 Shell，回到发起连接的环境。它不是可靠的作业取消指令，后台和队列任务需单独管理。

登录时输入密码通常不显示字符或星号，这是正常行为。首次连接时主机指纹需要核对实际服务器信息，不要仅因出现提示就认定连接可信。[OpenSSH 手册](https://man.openbsd.org/ssh.1)

### 13.2 scp：传文件

从发起连接的本地终端执行：

```bash
scp POSCAR your_user@server.example.org:/remote/calc/
```

把本地当前目录中的 `POSCAR` 复制到远程已存在的 `/remote/calc/`。

```bash
scp your_user@server.example.org:/remote/calc/OUTCAR .
```

把远程 `OUTCAR` 下载到本地当前目录；末尾 `.` 表示这里。

```bash
scp -r your_user@server.example.org:/remote/calc ./calc_copy
```

递归下载目录。目标层级会受 `calc_copy` 是否已存在影响，执行前确认；同名文件可能被覆盖。

```bash
scp -P 2222 POSCAR your_user@server.example.org:/remote/calc/
```

为 scp 指定端口。注意 `scp -P` 是大写 P，`ssh -p` 是小写 p。[scp 手册](https://man.openbsd.org/scp.1)

在 Windows PowerShell 中，如果 OpenSSH 客户端已经可用，可以使用本地 Windows 路径：

```powershell
scp "F:\MG\INCAR" your_user@server.example.org:/remote/calc/
```

这句在 Windows 本地运行，不在 Linux 服务器里运行。远程目录需要已存在且允许写入。

### 13.3 资源与进程查看

| 命令 | 含义与适用范围 |
| --- | --- |
| `hostname` | 查看当前机器名，帮助区分本地、登录节点与计算节点 |
| `whoami` | 查看当前用户账号 |
| `date` | 查看该环境的时间 |
| `free -h` | 在常见 Linux 系统上查看内存情况 |
| `nproc` | 查看当前环境报告的可用处理器数量；不保证就是队列授权资源数 |
| `top` | 交互查看进程和资源；按 q 退出 |
| `ps -u "$USER"` | 查看该机器上当前账号的进程 |
| `command -v mpirun` | 查找当前环境能否找到 mpirun |
| `command -v vasp_std` | 查找 VASP 可执行程序，不启动计算 |

在登录节点执行 `ps`，不一定能看到计算节点上的进程。队列系统分配的资源比机器硬件总数更直接决定作业能使用什么。

### 13.4 只有服务器使用 Slurm 时才适用

课题组是否使用 Slurm 目前尚未确认。也可能使用 PBS、LSF 或组内脚本，不能把这里的命令直接套上去。

| Slurm 命令 | 含义 | 是否改变作业状态 |
| --- | --- | --- |
| `squeue -u "$USER"` | 查看自己的当前队列作业 | 否 |
| `squeue -j 12345` | 查看示例编号 12345 的作业 | 否 |
| `scontrol show job 12345` | 查看该作业的详细信息 | 读取用途 |
| `sacct -j 12345` | 查询作业记账记录，是否可用依配置而定 | 否 |
| `sbatch run.sh` | 将脚本提交给队列，可能稍后才运行 | 是，提交真实任务 |
| `scancel 12345` | 取消该作业或向它发送默认取消操作 | 是，会中止任务 |

12345 是示例编号，必须换成自己的实际作业编号。`PD` 通常表示排队等待，`R` 表示正在运行。

`sbatch` 返回作业编号，只说明提交成功；不证明 VASP 已运行或收敛。`mpirun` 启动程序，`sbatch` 提交作业，两者用途不同。

官方资料：[Slurm 入门](https://slurm.schedmd.com/quickstart.html)、[squeue](https://slurm.schedmd.com/squeue.html)、[sbatch](https://slurm.schedmd.com/sbatch.html)、[scancel](https://slurm.schedmd.com/scancel.html)。

### 13.5 module：集群的软件环境

在安装了环境模块系统的服务器上：

| 命令 | 含义 |
| --- | --- |
| `module list` | 查看当前加载的软件模块 |
| `module avail` | 查看可用模块；输出可能很长 |
| `module load 模块名` | 加载实际指定的软件环境 |
| `module unload 模块名` | 卸载当前指定模块 |

模块名必须来自实际服务器，不能根据本笔记猜测版本。加载 VASP/MPI 环境可能由师姐提供的脚本完成，先确认服务器实际流程。

<a id="git"></a>

## 14. Git 保存、上传和同步

### 14.1 保存文件、提交版本、上传是三件事

```text
编辑器保存
    ↓
工作目录中有修改后的文件
    ↓ git add
暂存准备提交的内容
    ↓ git commit
本地仓库中形成一个版本记录
    ↓ git push
把本地提交推送到 GitHub
```

编辑器自动保存不等于 Git 自动提交，也不等于已上传 GitHub。

Git 只管理仓库范围内的文件。`~/linux_practice` 在本例仓库外，不会因为在 `VASP-Learning` 中执行 `git push` 就自动上传。

### 14.2 下载一个仓库副本：后续查阅

```bash
git clone https://github.com/Weasel561/VASP-Learning.git VASP-Learning-copy
```

把远程仓库克隆到当前目录下新建的 `VASP-Learning-copy`。目录必须适合创建，通常不能使用已有非空目录作为这个目标。它下载仓库提交与文件，不下载 Codespaces 主目录里的 `linux_practice`。

### 14.3 核对仓库位置和状态

| 命令 | 含义 |
| --- | --- |
| `git rev-parse --show-toplevel` | 显示当前所在 Git 仓库的顶层路径 |
| `git status` | 查看分支、工作区和暂存区的状态 |
| `git status --short` | 用简短格式查看变化 |
| `git branch --show-current` | 查看当前分支名称 |
| `git remote -v` | 查看配置的远程仓库名称和地址 |

`git status` 本身不提交、不上传。正常上传流程中可以最后检查；遇到分叉或冲突时，应按需要提前检查。

常见状态：

| 输出 | 意义 |
| --- | --- |
| `Untracked files` | 新文件还未被跟踪 |
| `Changes not staged for commit` | 修改尚未放入暂存区 |
| `Changes to be committed` | 暂存内容准备进入下一次提交 |
| `ahead … by 1 commit` | 本地比已知远程分支多一个提交 |
| `behind …` | 本地落后于已知远程分支 |
| `diverged` | 本地和已知远程有各自独有的提交 |
| `working tree clean` | 没有 Git 报告的未提交变化；被忽略文件仍可能存在 |
| `up to date with origin/main` | 相对本地当前知道的 origin/main 一致，不一定已经查询远程最新状态 |

`git fetch` 会更新本地对远程的认识；仅执行 `git status` 不会联网获取最新提交。

### 14.4 四个常用命令

以下假设文件确实已保存为 `linux/detail.md`：

```bash
git add linux/detail.md
```

把这个文件当前版本的改动放入暂存区。之后再修改它，需要再次 `git add` 才把新修改纳入提交。

```bash
git commit -m "添加 Linux、Git 与 VASP 命令查阅笔记"
```

根据暂存区创建本地提交。`-m` 后面是提交说明，不是文件名，也不会自动上传。

```bash
git push
```

把本地提交推向配置好的远程和上游分支。首次未建立上游的分支可能需要额外指定，但本例 main 已配置。

```bash
git status
```

检查剩余未提交修改，以及相对当前已知远程分支的状态。

`git add .` 会暂存当前目录及其下面的变化，包括可能不想上传的文件。初学时优先使用明确路径。

### 14.5 正常编辑和上传流程

```bash
cd /workspaces/VASP-Learning
git pull --ff-only
```

进入仓库，并在工作区适合更新、历史允许快进时同步远程。若同步报错，先处理原因，不把后面命令当成一定能接着执行的脚本。

然后在编辑器中创建或修改笔记，保存，再执行：

```bash
git add linux/detail.md
git commit -m "添加 Linux、Git 与 VASP 命令查阅笔记"
git push
git status
```

如果没有新的文件变化，`git commit` 不会凭空创建“空版本”；`nothing to commit` 通常表示没有可提交的内容。

### 14.6 删除仓库中的文件

假设 `linux/test-note.md` 是已经被 Git 跟踪的练习文件：

```bash
git rm linux/test-note.md
git commit -m "删除测试笔记"
git push
```

三句分别是：

1. 从工作目录删除文件，并将删除变化放入暂存区。
2. 在本地记录删除。
3. 推送这个版本变化。

也可以：

```bash
rm linux/test-note.md
git add linux/test-note.md
git commit -m "删除测试笔记"
git push
```

第一句只在工作目录删除；第二句将这一删除变化暂存。普通 `rm` 不会自动替你提交。

未跟踪的文件通常不能用 `git rm` 处理；删除它使用普通文件操作即可。已提交的旧版本仍在 Git 历史中，删除当前版本不等于清除全部历史。

### 14.7 查看差异和历史

| 命令 | 含义 |
| --- | --- |
| `git diff` | 查看工作区相对于暂存区的未暂存差异 |
| `git diff --staged` | 查看暂存区相对于当前提交的差异 |
| `git log --oneline -n 10` | 查看最近 10 条提交的简短记录 |
| `git log --oneline --graph --decorate --all -n 12` | 查看各引用附近的提交关系，帮助判断分叉 |
| `git show --stat HEAD` | 查看最新提交的文件变化概况 |
| `git fetch origin` | 更新远程跟踪信息；不等于把 main 和工作文件合并更新 |
| `git merge --ff-only origin/main` | 尝试把当前分支快进到已获取的 origin/main，分叉时拒绝 |

`git pull --ff-only` 包含获取远程变化并尝试快进的过程。它不能保证解决所有历史分叉。[Git pull 说明](https://git-scm.com/docs/git-pull)

### 14.8 你遇到过的分叉：查阅流程

发生 `push rejected (fetch first)` 时，常见原因是远程出现本地还没有的提交。不是重新 `commit` 一次就能解决。

先检查：

```bash
git status
git fetch origin
git log --oneline --graph --decorate --all -n 12
```

含义：查看本地未提交变化，获取远程状态，查看两边历史关系。

当工作区干净、本地独有提交尚未公开、准备采用 rebase 整合时，可以先保留一个本地引用：

```bash
git branch backup-before-sync
git rebase origin/main
```

第一句建立指向当前提交的备份分支；它不保存未提交的文件，也不拷贝目录。名称已存在时换一个新名字，不强制覆盖。

第二句以 `origin/main` 为基础重新应用本地独有提交。它会重写这些本地提交的编号，不适合不加判断地对已共享历史执行。

你曾看到：

```text
skipped previously applied commit
Successfully rebased and updated refs/heads/main.
```

当时表示本地提交的修改已被远程历史包含，重复修改被跳过，rebase 完成。不能因此推断所有以后的 rebase 都会无冲突。

完成后：

```bash
git status
```

确认是否还有本地领先提交。还有需要上传的提交时再 `git push`；如果已一致，就不需要制造一个新提交来“再上传一次”。

### 14.9 rebase 冲突时的三个操作

| 命令 | 意义 |
| --- | --- |
| `git status` | 查看正在冲突的文件和 rebase 状态 |
| `git rebase --continue` | 解决冲突并暂存后，继续 rebase |
| `git rebase --abort` | 中止并回到 rebase 开始前的状态 |

解决冲突的一般顺序是：阅读冲突文件 → 确定最终内容 → 保存 → `git add` 该文件 → `git rebase --continue`。可能打开提交信息编辑器，需要保存退出。

`git rebase --skip` 会跳过当前提交，不是通用的“忽略报错”按钮；可能放弃你想保留的改动。不要因为终端出现提示就随意使用。

官方说明：[git rebase](https://git-scm.com/docs/git-rebase)。

### 14.10 撤销暂存与撤销内容不是一回事

```bash
git restore --staged linux/detail.md
```

撤销暂存，通常保留工作目录里的内容，适合“暂存错文件”。

```bash
git restore linux/detail.md
```

用暂存区的版本恢复工作文件，可能丢弃尚未暂存的编辑内容。文件未被另行暂存时，常见结果是恢复已提交版本。执行前核对 `git diff` 和要保留的改动。

### 14.11 .gitignore：哪些文件不应随手 add

`.gitignore` 适合指定不跟踪的临时文件、机器生成的大型文件和本地私密信息。

针对 VASP 项目的示例规则：

```gitignore
WAVECAR
CHGCAR
POTCAR
tmp/
*.log
```

每句分别匹配波函数文件、电荷密度文件、原子势文件、临时目录以及日志后缀。是否忽略日志和电荷密度取决于实际数据管理策略，示例不是强制模板。

VASP 的 POTCAR 涉及许可和分发约束，公共仓库优先记录势的名称与版本、所用参数，而不上传完整许可数据；可参见 [Materials Project/pymatgen 的许可说明](https://github.com/materialsproject/pymatgen/blob/main/docs/installation.md)。密码、私钥和访问令牌也不写入仓库。

已经被 Git 跟踪的文件，不会因为后来写进 `.gitignore` 就自动从仓库历史或跟踪状态中消失。[gitignore 说明](https://git-scm.com/docs/gitignore)

<a id="troubleshooting"></a>

## 15. 常见报错与错误写法

| 报错或现象 | 常见原因 | 先做什么 |
| --- | --- | --- |
| `No such file or directory` | 路径错误、文件不存在、大小写错误、漏掉创建或复制步骤 | `pwd`，再查看源路径和父目录 |
| `cannot stat 'backup/notes.txt'` | 复制或移动的源路径找不到，或路径无法访问 | `ls -l backup/notes.txt`，再查权限和目录 |
| `command not found` | 命令拼错，或程序未安装/不在 PATH 中 | 检查拼写，用 `command -v` |
| `Permission denied` | 权限或其他访问控制不允许 | 查看 `ls -l`，确认账号与路径 |
| `missing destination file operand` | cp/mv 缺少目标参数 | 补全“源＋目标” |
| `File exists` | 要创建的同名项目已存在 | 用 `ls -ld` 确认它是什么 |
| `Directory not empty` | rmdir 的对象不是空目录 | 用 `ls -la` 查里面的项目 |
| `Not a directory` | 把普通文件当目录进入或作为路径父层级 | 用 `ls -ld` 查类型 |
| `sort: … disorder` | `sort -c` 检查到输入尚未排序 | 用普通 `sort` 显示排序结果 |
| `nothing to commit` | 没有准备提交的新变化 | 确认文件是否保存、是否 add、是否已提交 |
| `not a git repository` | 当前目录不在仓库内 | 进入仓库，用 `git rev-parse --show-toplevel` |
| `push rejected (fetch first)` | 远程已有本地没有的提交 | fetch、查看历史，再整合 |
| `Not possible to fast-forward` | 两边分叉，或无法满足快进条件 | 查看状态与历史，选择合适整合方式 |
| `ls` 没有内容 | 可能没有非隐藏项目 | `ls -la` |
| 管道最后显示 `0` | 可能确实没匹配，也可能前面命令失败 | 阅读错误输出，单独验证左侧命令 |

以前出现过的写法纠正：

| 错误或易混写法 | 正确理解 |
| --- | --- |
| `git ststus` | 应为 `git status` |
| `git commmit` | 应为 `git commit` |
| `grep ic "warning" run.txt` | 组合选项要写 `grep -ic …` |
| <code>sort status.txt &#124; -c</code> | 管道右侧缺少命令；统计用 `uniq -c` |
| `mv -r backup` | mv 不用 cp 的递归参数；还缺目标 |
| `mkdir day08.md` | 创建的是目录，不是 Markdown 文件 |
| `cat run.txt` 找不到日志 | 文件可能在 `logs/run.txt`，或当前目录不同 |
| `head -n -1` 当成取第一行 | GNU head 中实际是排除最后一行 |
| `tail -nf run.txt` | 不清晰且会错误解析；用 `tail -n 5 -f run.txt` |
| 把 `new file: …` 输入终端 | 这是状态输出，不是命令 |

错误通常在“名字、路径、前置步骤、参数所属命令”中。先定位这四项，比连续猜不同命令更有效。

<a id="practice"></a>

## 16. 一次完整的基础复习练习

这段只包含练习文件操作，不连接服务器、不启动 VASP、不提交 Git。每行都有注释，按顺序执行；遇到报错就先查看原因。

准备一个新目录：

```bash
mkdir -p ~/linux_practice
cd ~/linux_practice
mkdir command_review_01
cd command_review_01
pwd
```

各句含义：

1. 确保主目录中的练习父目录存在。
2. 进入练习父目录。
3. 新建本次目录；如果它已存在，换一个新名字，例如 command_review_02。
4. 进入本次新目录。
5. 核对当前位置。

如果第三句失败，不要继续把已有目录当成全新的练习目录。

### 16.1 创建、复制和移动

```bash
mkdir backup                         # 新建子目录
touch notes.txt                      # 创建空文件
echo "My Linux review" > notes.txt    # 写入并覆盖练习文件内容
cp notes.txt notes_copy.txt           # 复制到当前目录
cp notes.txt backup/                  # 复制进 backup
cp backup/notes.txt restored.txt      # 从 backup 复制出来
mv notes_copy.txt saved.txt           # 重命名副本
mv saved.txt backup/                  # 移动副本
cp -r backup backup_copy              # 目标不存在时复制整个目录
ls -l                                # 查看当前目录详情
ls backup                            # 查看 backup 内部项目
```

### 16.2 创建日志、查看和搜索

```bash
echo "Step 1 finished" > run.txt       # 建立日志第一行
echo "Warning: check input" >> run.txt # 追加第二行
echo "Step 2 finished" >> run.txt      # 追加第三行
echo "Calculation finished" >> run.txt # 追加第四行
cat -n run.txt                        # 显示四行并加行号
head -n 1 run.txt                     # 查看第一行
tail -n 1 run.txt                     # 查看最后一行
wc -l run.txt                         # 预期为 4
grep -n "finished" run.txt             # 预期匹配第 1、3、4 行
grep -ic "warning" run.txt             # 预期输出 1
grep "finished" run.txt | wc -l        # 预期输出 3
grep "finished" run.txt | tail -n 1 > last_step.txt # 保存最后一条完成记录
cat last_step.txt                     # 预期为 Calculation finished
```

### 16.3 排序、去重和保存

```bash
printf '%s\n' "finished" "warning" "finished" "running" "warning" "finished" > status.txt
sort status.txt                       # 按文字排序，显示 6 行
sort status.txt | uniq                # 去重后显示 3 种状态
sort status.txt | uniq -c > status_count.txt # 统计并保存
cat status_count.txt                  # 查看次数及状态
sort -n status_count.txt              # 次数从小到大
sort -nr status_count.txt             # 次数从大到小
sort status.txt | uniq -c | sort -nr | head -n 1 > most_common.txt # 保存最多的一项
cat most_common.txt                   # 预期为 3 finished，前面可能有空格
```

第一句 printf 的含义：按每个字符串一行，建立 6 行示例数据。不是从真实计算里判断出 finished 状态。

### 16.4 检查保留的练习结果

```bash
pwd
ls -l
ls backup
ls backup_copy
```

依次确认当前位置、当前目录文件、原备份目录、备份副本。可以保留它们作为下一次查阅样例。

若需要练删除，单独创建一个无内容的小目录：

```bash
mkdir empty_dir
rmdir empty_dir
```

第一句建立空目录，第二句删除它，不影响前面的练习结果。

<a id="lookup"></a>

## 17. 按实际需求查命令

| 我现在要做什么 | 优先查哪些命令 |
| --- | --- |
| 不知道自己在哪 | `pwd` |
| 不知道目录里有什么 | `ls`、`ls -la` |
| 找不到文件 | `pwd`、`ls 指定路径`、`find` |
| 想去练习目录 | `cd ~/linux_practice` |
| 想回上一级 | `cd ..` |
| 想回上一次位置 | `cd -` |
| 想创建文件夹 | `mkdir` |
| 想创建空文件 | `touch` |
| 想复制或改名 | `cp`、`mv` |
| 想看短文件 | `cat` |
| 想看大文件 | `less` |
| 想看开头或末尾 | `head`、`tail` |
| 想看计算更新 | `tail -f OSZICAR` |
| 想搜索关键词 | `grep` |
| 想数匹配行 | `grep -c` |
| 想数总行数 | `wc -l` |
| 想统计重复内容 | `sort`、`uniq -c` |
| 想保留结果 | `>`、`>>`、`tee` |
| 想比较两个版本 | `diff`、`git diff` |
| 想知道用了多少磁盘 | `du` |
| 想知道磁盘剩多少 | `df` |
| 想看这次算什么 | `cat INCAR`，然后理解参数 |
| 想看最终能量 | 搜索 OUTCAR 的 TOTEN，并核对能量定义 |
| 想判断计算成功 | 同时检查任务设置、电子/结构条件和输出结尾 |
| 想从服务器取结果 | `scp` 或组里规定的传输工具 |
| 想查队列作业 | 确认调度器后，使用对应查询命令 |
| 想上传笔记 | `git add`、`git commit`、`git push` |
| 想检查上传状态 | `git status`，必要时先 `git fetch` |

建议现在熟悉：路径、pwd、ls、cd、mkdir、touch、cp、mv、cat、head、tail、less、echo、grep、wc、sort、uniq、管道与重定向，以及 Git 基本流程。

后续按需求查：find、diff、du、df、chmod、sed、awk、循环、ssh、scp、服务器模块和队列命令。不需要先把全部命令背会，才能理解材料计算。

命令负责操作和提取；科学判断负责解释。原始文件、设置、结构来源和比较对象都要保留，不能只抄一个最终数字。

<a id="references"></a>

## 18. 官方参考资料

遇到不同版本或不同服务器行为，以该环境实际帮助和手册为准。以下资料用于核对语义，笔记示例为针对当前学习情况编写：

- [GNU Coreutils 手册](https://www.gnu.org/software/coreutils/manual/coreutils.html)：文件管理、查看、统计、排序、权限和容量命令。
- [Bash 手册](https://www.gnu.org/software/bash/manual/bash.html)：目录切换、主目录展开、引号、管道、变量和重定向。
- [GNU grep 手册](https://www.gnu.org/s/grep/manual/grep.html)：搜索、选项、正则和退出状态。
- [GNU Findutils 手册](https://www.gnu.org/software/findutils/manual/html_node/find_html/index.html)：路径查找。
- [GNU sed 手册](https://www.gnu.org/software/sed/manual/sed.html)：按行查看、替换与原地修改的区别。
- [GNU awk 手册](https://www.gnu.org/software/gawk/manual/gawk.html)：字段与文本处理。
- [Git 命令参考](https://git-scm.com/docs)：提交、同步、差异、rebase 与 ignore。
- [OpenSSH ssh 手册](https://man.openbsd.org/ssh.1)、[scp 手册](https://man.openbsd.org/scp.1)：远程登录和文件传输。
- [Slurm 官方入门](https://slurm.schedmd.com/quickstart.html)：仅在服务器实际使用 Slurm 时查阅。
- [Open MPI mpirun 手册](https://docs.open-mpi.org/en/main/man-openmpi/man1/mpirun.1.html)：并行进程启动。
- [VASP 输入说明](https://vasp.at/wiki/Input)、[输出说明](https://vasp.at/wiki/Output)：各输入输出文件的用途。
- [VASP POSCAR](https://vasp.at/wiki/POSCAR)、[CONTCAR](https://vasp.at/wiki/CONTCAR)、[OSZICAR](https://vasp.at/wiki/OSZICAR)：结构和迭代记录。
- [VASP ISIF](https://vasp.at/wiki/ISIF)、[EDIFF](https://vasp.at/wiki/EDIFF)、[EDIFFG](https://vasp.at/wiki/EDIFFG)：优化变量和停止条件。

本文件用于学习和查阅，不代表已对课题组服务器配置、全部命令版本或材料模型作了完整验证。

