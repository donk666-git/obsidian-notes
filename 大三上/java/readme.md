新建类，集成ai，编译调试
环境选择优势

- jdk17：最新的 LTS 版本，现在被越来越多公司当作默认选择。现在行业主流基本是 **Java 17 LTS**，因为它支持新语法（`switch` 新写法、record、sealed class 等），同时能跑 Spring Boot 3。
- java 21 是最新 LTS，但学习资料还没那么多，入门用它会卡壳。
- **Spring Boot 3.x** 要求 **Java 17+**，不支持 8/11 了。

```
C:\Program Files\Java\jdk-17
```
![[Pasted image 20250918125900.png]]


*关于vscode*

1.自动补全
### tab自动补全
![[Pasted image 20250918130531.png]]
## copilot补全
![[Pasted image 20250918131724.png]]

2.ai集成
阿里云的LINGMA 和github的 copilot

3.快捷键 创建java项目

4.自动编译
![[Pasted image 20250918132949.png]]
#### **方法 1：创建一个 Java 项目（推荐）**

1. 打开 VSCode → **命令面板**（`Ctrl+Shift+P`）→ 输入 `Java: Create Java Project` → 回车
		快捷键ctrl+1
    ![[Pasted image 20250918125900.png]]
2. 选择 **No Build Tools**（简单项目，不用 Maven/Gradle）
    
3. 选择项目路径，例如：`D:/Coding/java/HelloWorldProject`
    
4. VSCode 会自动生成：
    
    `HelloWorldProject/ └─ src/     └─ App.java`
    
5. 把 `HelloWorld.java` 移到 `src` 目录下
    
6. 打开 `HelloWorld.java` → 右键 → **Run Java**
    

---

#### **方法 2：单文件快速运行（适合测试）**

- 文件名必须和类名一致，例如：
    

`HelloWorld.java → public class HelloWorld`

- 打开文件 → 右键 → **Run Java**
    
- 适合写简单程序，但某些高级功能（调试、Maven/Gradle 支持）无法使用。