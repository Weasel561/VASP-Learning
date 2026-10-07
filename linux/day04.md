# Day 4：Linux 指令复习
日期：2026-10-06

## 1. 确认位置与切换目录

```bash
pwd           # 显示当前目录的完整路径
ls            # 查看当前目录的内容
ls -l         # 查看详细信息，小写字母 l
ls -1         # 每行显示一个名字，数字 1
ls -a         # 包括隐藏文件
cd ~          # 回到主目录
cd backup     # 进入当前目录中的 backup 文件夹
cd ..         # 返回上一级目录
```

注意：已经在 review1 中时，cd review1 会尝试进入里面另一个
叫 review1 的文件夹，不是留在原地。

## 2. 创建文件与文件夹

```bash
mkdir backup      # 创建文件夹
touch notes.txt   # 创建空文件；已有文件则更新时间
```

文件类型取决于创建方式，不能只看名字：
mkdir a.txt 创建的仍然是文件夹。

## 3. 文件复制

基本顺序：

```text
cp 来源 目标
```

```bash
cp notes.txt notes_copy.txt
```

复制当前目录的文件，并给副本起新名字。

```bash
cp number.txt backup/
```

复制到已有的 backup 文件夹，保持文件名不变。

```bash
cp backup/check.txt example.txt
```

从 backup 中复制文件，在当前目录创建 example.txt。

cp 复制后，原文件仍然保留。
目标文件已存在时，普通 cp 可能覆盖它，执行前要核对名字。

## 4. 移动与重命名

```bash
mv notes_copy.txt saved.txt   # 重命名
mv saved.txt backup/         # 移入文件夹
```

基本顺序仍然是：来源在前，目标在后。

mv 后，文件不再位于原位置，内容仍然保留在目标位置。

## 5. 当前目录与相对路径

假设文件结构是：

```text
review1/
└── backup/
    └── check.txt
```

在 review1 中，需要写：

```bash
cat backup/check.txt
```

进入 backup 后，可以直接写：

```bash
cat check.txt
```

路径从当前所在目录出发。
只写文件名，表示当前目录里的文件。
backup/check.txt 表示 backup 文件夹里面的 check.txt。

Linux 路径使用正斜杠 /，不是反斜杠 \。

## 6. 生成和查看内容

```bash
seq 1 12 > number.txt      # 生成 1～12 并写入文件
cat number.txt            # 显示全部内容
head -n 3 number.txt      # 查看前 3 行
tail -n 4 number.txt      # 查看最后 4 行
tail -n 2 backup/number.txt
```

`>` 会在文件不存在时创建文件，存在时覆盖原内容。
`>>` 用于追加内容。

命令、选项、行数和路径之间要用空格分开：

```text
head  -n  3  backup/number.txt
```

## 7. 分页查看

```bash
less number_copy.txt
```

进入后：
- 方向键移动。
- /8 然后回车：搜索 8。
- q：退出。

## 8. 复制整个文件夹

```bash
cp -r backup backup_copy
cp -r backup archive
```

-r 用于连同文件夹内部内容一起复制。

今天执行时，目标文件夹还不存在，因此创建了副本文件夹。
如果目标目录已经存在，复制结果可能是在里面增加 backup 子目录，
所以执行前要检查目标是否存在。

## 9. 删除文件

```bash
rm notes.txt
```

删除前核对文件名，rm 通常不会放进回收站。

## 10. 今天容易出错的地方

- mkdir 创建文件夹，touch 创建空文件。
- ls -l 的 l 是字母，ls -1 的 1 是数字。
- txt、xtx 等拼写不同，就是不同的名字。
- 数字与路径之间不能漏空格。
- cp 和 mv 都是先来源，再目标。
- 文件移动后，要使用它的新路径。

操作前先问自己：
1. 我现在在哪里？
2. 来源文件在哪里？
3. 我要把它放在哪里？
