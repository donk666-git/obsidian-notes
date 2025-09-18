版芯：160mm×247mm，即页边距上、下、左、右各25 mm。
```
% --- 依赖宏包 ---
\usepackage[
    a4paper,
    top=25mm,
    bottom=25mm,
    left=25mm,
    right=25mm
]{geometry}
```

页码：页码用阿拉伯数字连续编页，字号与正文字体相同，页底居中，数字两侧用圆点或一字横线修饰，如- 1 -。
```
\fancyfoot[C]{- \thepage\ -}
```
c是中间，默认字体随正文