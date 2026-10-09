# Day08：排序、去重与重复次数统计

## 1. 排序
```bash
sort status.txt
```
按文本顺序排列，让相同内容相邻。只显示结果，不修改原文件。

## 2. 去重
```bash
sort status.txt | uniq
```
uniq 只合并连续相同的行，因此通常先排序。

## 3. 统计重复次数并保存
```bash
sort status.txt | uniq -c > status_count.txt
cat status_count.txt
```
本次结果：
```text
4 finished
2 warning
```

## 4. 按次数排序
```bash
sort -n status_count.txt
sort -nr status_count.txt
```
-n：按数字大小排序。
-r：反转顺序。
-nr：按数字从大到小排序。

## 5. 找出出现次数最多的状态
```bash
sort status.txt | uniq -c | sort -nr | head -n 1 > most_common.txt
cat most_common.txt
```
处理顺序：排序 → 统计次数 → 按次数降序 → 取第一行 → 保存。

## 6. 容易混淆的地方
- sort -c：检查输入是否已经排好序。
- uniq -c：统计每组连续相同行的次数。
- head -n 1：取第一行。
- 参数必须结合具体命令理解。
- 管道 | 把输出交给下一个命令。
- > 把输出写入文件；文件已存在时覆盖原内容。
