根据修改时间批量改名
```
$i=1; Get-ChildItem -File -Filter *.mp4 | Sort-Object LastWriteTime | ForEach-Object {
  Rename-Item $_ -NewName ("lecture{0}.mp4" -f $i++)
}

```
![[Pasted image 20250723204931.png]]
