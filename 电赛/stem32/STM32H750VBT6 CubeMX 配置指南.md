
#### 1. 新建工程与基础系统配置 (System Core)

1. **新建项目**:
    
    - 打开 STM32CubeMX，通过 `File -> New Project` 创建工程。
        
    - 在 Part Number 搜索框中输入 `STM32H750VBT6`，选中芯片并点击 `Start Project`。
        
2. **配置调试接口**:
    
    - 在左侧 `Categories` -> `System Core` -> `SYS` 中。
        
    - 将 `Debug` 选项设置为 `Serial Wire`。这是使用 ST-LINK 进行烧录和调试所必需的。
        
3. **配置时基 (Timebase)**:
    
    - 同样在 `SYS` 中，`Timebase Source` 保持默认为 `Systick` 即可。`HAL_Delay()` 函数会依赖这个时基。
        

#### 2. 时钟配置 (RCC)

1. **启用外部晶振**:
    
    - 在 `System Core` -> `RCC` (Reset and Clock Control) 中。
        
    - `High Speed Clock (HSE)`: 设置为 `Crystal/Ceramic Resonator`。
        
2. **配置系统主频**:
    
    - 点击顶部的 `Clock Configuration` 标签页。
        
    - **目标**: 将系统时钟（SYSCLK）配置到尽可能高，H750最高可达 **480 MHz**。
        
    - **设置**:
        
        - 在 `PLL Source Mux` 处选择 `HSE` 作为PLL的时钟源。
            
        - 调整 `PLL` 的各个分频和倍频系数（例如 `/M`, `xN`, `/P`, `/Q`, `/R`），直到 `HCLK` (To AHB bus...) 的频率显示为 **480 MHz**。
            
        - **注意**: 配置过程中，CubeMX 会自动检查各总线时钟是否超频（变红），请确保所有时钟都在其允许的最大频率范围内。
            

#### 3. 外设配置 (根据项目需求)

1. **配置 FMC (用于驱动并口 LCD)**:
    
    - 在 `Categories` -> `Computing` -> `FMC`。
        
    - **模式**: 在 `NOR/PSRAM/SRAM` 中选择一个Bank（通常是 `NOR/PSRAM 1`），将其 `Chip Select` 设置为 `NE1`（或根据您的硬件连接选择），并将模式设为 `LCD Interface`。
        
    - **参数**:
        
        - `Memory type`: `SRAM`。
            
        - `Data`: `16-bits`。
            
        - **Timing (时序)**: 这是**关键**。您必须根据您LCD模块所用控制芯片（如 ILI9341）的数据手册，结合当前HCLK的频率（480MHz）来计算并填写读写的时序参数（`Address setup time`, `Data setup time` 等）。**此步骤如果出错，LCD将无法正常工作。**
            
2. **配置 ADC (用于内置温度传感器)**:
    
    - 在 `Categories` -> `Analog` -> `ADC1` (或 ADC2/3)。
        
    - 在 `Parameter Settings` 中，找到 `ADC Settings` -> `Temperature Sensor Channel`，将其状态设置为 `Enabled`。
        
    - 其他ADC参数（如分辨率、对齐方式）可以暂时保持默认。
        
3. **配置 USART (用于 `printf` 调试)**:
    
    - 在 `Categories` -> `Connectivity` -> `USART1` (或您开发板上连接到调试串口的任意一个UART/USART)。
        
    - `Mode`: 设置为 `Asynchronous`。
        
    - `Parameter Settings`: `Baud Rate` 设置为一个常用值，如 `115200` Bits/s。
        

#### 4. MPU 配置 (内存保护单元 - 关键步骤!)

这是您需求文档中**特别强调**的一点，是 STM32H7 系列开发中非常重要且容易出错的地方。

1. **进入MPU设置**:
    
    - 在 `System Core` -> `MPU`。
        
2. **配置FMC内存区域**:
    
    - MPU默认可能是关闭的，请勾选 `Activate`。系统会自动生成几个默认的内存区域。
        
    - 我们需要为FMC所连接的外部LCD GRAM添加一个新的内存区域规则。点击 `Add`。
        
    - **配置参数**:
        
        - **Region Name**: 填一个描述性名称，如 `FMC_LCD_REGION`。
            
        - **Region Base Address**: `0x60000000` (这是FMC Bank1 NOR/SRAM的起始地址)。
            
        - **Region Size**: `16 MBytes` (覆盖FMC Bank1的全部地址空间)。
            
        - **Access Permission**: `Full Access`。
            
        - **Memory Type**: **`Strongly-ordered`** 或 **`Device`**。
            
        - **Cacheable**: **`Disable`**。
            
        - **Shareable / Bufferable**: `Disable`。
            
    - **原因**: 这个配置告诉CPU，`0x60000000` 开始的这块内存是外部设备，访问它的时候**不要使用Cache（高速缓存）**。这样可以确保CPU每次对LCD的写操作都直接写入到LCD，避免了因Cache导致的数据不一致问题（即花屏或无显示）。这是一个最安全、最保险的初始设置。
        

#### 5. 生成工程

1. **项目管理**:
    
    - 点击 `Project Manager` 标签页。
        
    - 设置 `Project Name` 和 `Project Location`。
        
    - `Toolchain / IDE`: 选择 `MDK-ARM`。
        
2. **生成代码**:
    
    - 点击 `GENERATE CODE`。