## 文本查找
```
grep "resolve.sh" ./resolve_all.sh
```
- `grep`: 文本搜索工具，全称是 **Global Regular Expression Print**，用于从文本中查找匹配的字符串。
    
- `"resolve.sh"`: 要查找的字符串内容，表示你想找含有 `resolve.sh` 这个字符串的行。
    
- `./resolve_all.sh`: 被查找的文件，表示当前目录下的 `resolve_all.sh` 文件。

		grep -i   忽略大小写（不区分大小写匹配）
		grep -n   显示匹配的行号（定位在哪一行）
		grep -v   显示不包含该字符串的行（反向匹配）
		grep -r "resolve.sh" ./scripts/    会在 `scripts/` 目录下所有文件中查找
		grep -l "resolve.sh" *.sh   只列出含有 `resolve.sh` 的 `.sh` 文件名
		grep -x   只匹配整个行正好是 `resolve.sh` 的情况，其他部分多一个字符都不匹配
