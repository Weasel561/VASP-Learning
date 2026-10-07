# Day 1：Linux 基础操作

## 确认位置与查看目录

```bash
pwd       # 显示当前目录的完整路径
ls        # 查看当前目录内容
ls -l     # 显示详细信息，小写字母 l
ls -1     # 每行显示一个名字，数字 1
ls -a     # 包括隐藏文件
```

## 切换目录

```bash
cd 文件夹名  # 进入当前目录中的文件夹
cd ..        # 返回上一级目录
cd ~         # 回到主目录；本次 Codespaces 中为 /home/codespace
cd /         # 到根目录
cd -         # 回到上一次所在的目录
```

## 易错点

- 命令和选项之间需要空格，如 `ls -a`，不能写成 `ls-a`。
- `ls -l` 显示详情，`ls -1` 每行显示一个名字。
- 已经在某个目录里，再写 `cd 同名目录` 会尝试进入其同名子目录。

## 原学习感想

today is the second day i learn linux, i feel better than yesterday.
