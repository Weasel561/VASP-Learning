# Day 2：文件与文件夹管理

## 常用命令

```bash
mkdir backup              # 创建文件夹
touch notes.txt           # 创建空文件；文件已存在时更新时间
cp notes.txt copy.txt      # 复制文件
cp -r backup backup_copy  # 复制文件夹及其内容；此例目标尚不存在
mv copy.txt saved.txt     # 重命名
mv saved.txt backup/      # 移动到已有文件夹
rm notes.txt              # 删除文件
```

## 命令对比

| 命令 | 用途 | 原位置 |
|---|---|---|
| `cp 来源 目标` | 复制 | 原文件保留 |
| `mv 来源 目标` | 移动或改名 | 原位置或旧名字不再保留 |
| `rm 文件` | 删除 | 文件被删除 |

## 易错点

- `touch` 创建空文件，`mkdir` 创建文件夹；文件名带 `.txt` 也不能决定类型。
- 复制文件夹要加 `-r`。
- 来源在前，目标在后；目标已存在时可能覆盖文件，执行前核对名字。
- `rm` 通常不经过回收站，删除前确认路径。
