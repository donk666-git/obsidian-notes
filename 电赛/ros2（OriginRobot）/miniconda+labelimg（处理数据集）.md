**激活环境**

- - **打开一个新的 "Anaconda Prompt"**（(base) C:\Users\Zwk>cd /d "D:\labelImg-master"）
	
    
    - 从Windows的“开始”菜单中，找到并打开 "Anaconda Prompt (miniconda3)"。这是一个专门配置好的终端，比PowerShell更适合操作conda。
        
- **重建纯净环境（如果需要）** 为了确保万无一失，我们再操作一遍。
    
    PowerShell

	```
	cd /d "D:\labelImg-master"
	```
    ```
    conda deactivate
    conda env remove -n label_env
    conda create -n label_env python=3.10
    ```
    
    在每个提示处按 `y`。
    
- **激活环境**
    
    PowerShell
    
    ```
    conda activate label_env
    ```
    
- **使用conda-forge渠道安装（最关键的一步）**
    
    PowerShell
    
    ```
    conda install -c conda-forge labelimg
    ```
    ```

**运行LabelImg**

- 安装完成后，直接在终端输入以下命令即可启动`labelimg`的图形界面：
    
    PowerShell
    
    ```
    labelimg
    ```

### 标注图片并存入`dataset`目录

现在，您已经可以开始为您的数字识别项目进行标注了。

1. **准备文件目录**
    
    - 在您电脑的任意位置（例如D盘），创建一个项目总文件夹，例如 `yolo_digits_project`。
        
    - 在其中，创建我们之前讨论过的 `dataset` 目录结构：
        
        ```
        D:/yolo_digits_project/
        └── dataset/
            ├── images/
            │   ├── train/  <-- 把80%的原始数字图片放这里
            │   └── val/    <-- 把20%的原始数字图片放这里
            └── labels/
                ├── train/  <-- 标注文件会自动生成在这里
                └── val/    <-- 标注文件会自动生成在这里
        ```
        
2. **开始标注**
    
    - **打开图像目录**：在`labelimg`的图形界面中，点击左侧的 "Open Dir" 按钮，选择您存放原始图片的文件夹（例如 `D:/yolo_digits_project/dataset/images/train`）。
        
    - **切换标注格式**：**（非常重要！）** 点击左侧的 "PascalVOC" 按钮，将其切换为 **"YOLO"** 格式。这样它才会生成YOLO训练所需的 `.txt` 文件。
        
    - **创建矩形框**：点击 "Create RectBox" 按钮（或按快捷键 `W`），在图片中的数字上拖拽鼠标画一个框。
        
    - **输入标签**：画完框后会弹出一个对话框，让您输入类别名称。例如，如果框住的是数字"1"，您就输入"1"。
        
    - **保存**：点击 "Save" 按钮（或按快捷键 `Ctrl+S`）。`labelimg`会自动在您指定的输出目录（下面会讲）下创建一个与图片同名的 `.txt` 标注文件。
        
    - **设置自动保存目录**：在`labelimg`界面点击 "Change Save Dir"，将保存目录设置为对应的 `labels` 文件夹（例如 `D:/yolo_digits_project/dataset/labels/train`）。这样，您标注 `images/train` 里的图片时，标签文件就会自动存放到 `labels/train` 中。