![[d8e2a10d2d4b3e0cba762b185cd1bb02_ 1.png]]
```
wget https://mirrors.zju.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
```
bash M
```
#### 先手动初始化 conda 环境（推荐）

运行：
复制编辑

```
~/miniconda3/bin/conda init bash
````

然后执行：
复制编辑
```
source ~/.bashrc
```


之后 `conda` 命令就能正常使用了，试试：

bash

复制编辑

`conda --version`
```
conda create -n yolov8 python=3.9 -y
```

```
conda activate yolov8
```

```
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```
安装yolov8
```
pip install ultralytics
```
安装labelimg
```
pip install labelimg
```

## labelimg标注修改
#### 方法一：手动修改 (适用于少量文件)

如果只有几个文件标错了，最直接的方法就是手动打开这些 `.txt` 标签文件进行修改。 在 `34_rotated_2.txt` 文件中，您需要把第一行的第一个数字从 `0` 改成 `3`，第二行的第一个数字从 `1` 改成 `4`。

**修改前:**

```
0 0.389766...
1 0.77095...
```

**修改后:**

```
3 0.389766...
4 0.77095...
```

#### 方法二：使用 VS Code 批量查找和替换 (推荐)

如果您的错误是有规律的（例如，您把**所有**的数字‘3’都标成了`0`，**所有**的数字‘4’都标成了`1`），那么使用VS Code的批量替换功能最方便。

1. 在VS Code的左侧文件浏览器中，右键点击 `labels` 文件夹，选择 “**在文件夹中查找**”(Find in Folder)。
    
2. 在查找框中，开启“**使用正则表达式**”模式（通常是一个 `.*` 图标）。
    
3. **分步替换**：
    
    - **第一步：把所有错误的`0`改成`3`**
        
        - 查找框输入: `^0` (注意：`^`代表行首，`0`后面有一个空格，确保只匹配类别ID)
            
        - 替换框输入: `3` (同样，`3`后面有一个空格)
            
        - 点击全部替换按钮。
            
    - **第二步：把所有错误的`1`改成`4`**
        
        - 查找框输入: `^1`
            
        - 替换框输入: `4`
            
        - 点击全部替换按钮。
            

**请注意：** 这个方法的前提是您的错误有全局统一的规律。如果您的错误是杂乱无章的，请看方法三。

#### 方法三：使用Python脚本一键修改 (最强大灵活)

如果错误的规律很复杂，或者文件数量巨大，写一个Python脚本是最好的选择。这个脚本可以根据您定义的规则，自动修改所有标签文件。

下面是一个示例脚本，您可以根据自己的情况修改它。

**使用方法：**

1. 将下面的代码保存为一个Python文件，例如 `fix_labels.py`。
    
2. 修改脚本中的 `labels_train_dir` 和 `labels_val_dir` 路径，使其指向您存放标签的 `train` 和 `val` 文件夹。
    
3. 在 `class_id_map` 字典中定义好**错误的ID**到**正确的ID**的映射关系。
    
4. 运行这个脚本 (`python fix_labels.py`)。
    

Python

```
import os
import shutil

# --- 您需要修改的配置 ---

# 1. 设置您的标签文件夹路径
# 注意：路径要使用您在WSL中的路径
labels_train_dir = '/home/user/datasets/digits/labels/train'  # 示例路径，请替换成您的训练集标签路径
labels_val_dir = '/home/user/datasets/digits/labels/val'      # 示例路径，请替换成您的验证集标签路径

# 2. 定义错误ID到正确ID的映射规则
# 格式： '错误的ID': '正确的ID'
class_id_map = {
    '0': '3',  # 假设您把所有 '3' 都标成了 '0'
    '1': '4',  # 假设您把所有 '4' 都标成了 '1'
    # 如果还有其他错误，继续在这里添加，例如： '2': '5'
}

# 3. 是否创建备份 (强烈建议开启)
create_backup = True

# --- 脚本主程序 ---

def fix_labels_in_directory(directory, mapping):
    if not os.path.isdir(directory):
        print(f"警告：目录不存在，已跳过: {directory}")
        return

    if create_backup:
        backup_dir = directory + '_backup'
        if not os.path.exists(backup_dir):
            shutil.copytree(directory, backup_dir)
            print(f"已创建备份目录: {backup_dir}")

    print(f"\n开始处理目录: {directory}")
    modified_files_count = 0
    for filename in os.listdir(directory):
        if filename.endswith(".txt"):
            filepath = os.path.join(directory, filename)
            new_lines = []
            file_changed = False
            
            with open(filepath, 'r') as f:
                for line in f:
                    parts = line.strip().split()
                    if not parts:
                        continue
                    
                    original_class_id = parts[0]
                    if original_class_id in mapping:
                        correct_class_id = mapping[original_class_id]
                        parts[0] = correct_class_id
                        new_lines.append(" ".join(parts) + "\n")
                        file_changed = True
                    else:
                        new_lines.append(line)
            
            if file_changed:
                with open(filepath, 'w') as f:
                    f.writelines(new_lines)
                modified_files_count += 1
                print(f"  - 已修改文件: {filename}")

    print(f"处理完成! 共修改了 {modified_files_count} 个文件。")

if __name__ == "__main__":
    fix_labels_in_directory(labels_train_dir, class_id_map)
    fix_labels_in_directory(labels_val_dir, class_id_map)
    print("\n所有标签修改完毕!")
```

**总结建议：** 如果您的数据集不大，推荐使用**方法二**，它既快速又直观。如果数据集非常大或者错误规律复杂，**方法三**是您最好的选择。