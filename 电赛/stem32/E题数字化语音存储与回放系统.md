- **RCC/SYS/Clock**:
    
    - `HSE`: Crystal/Ceramic Resonator
        
    - `SYS`: Debug -> Serial Wire
        
    - `Clock Configuration`: HCLK 设置为 72 MHz
        
- **Analog -> ADC1**:
    
    - `IN1`: **勾选** (会自动分配到`PA1`)
        
    - `External Trigger Conversion Source`: **Timer 3 Trigger Out Event**
        
    - `DMA Settings` -> `Add` -> `ADC1`:
        
        - `Mode`: **Circular**
            
        - `Data Width`: **Half Word** (外设和内存都是)
            
- **Analog -> DAC**:
    
    - `Mode` -> `OUT1`: **勾选** (会自动分配到`PA4`)
        
    - `Parameter Settings` -> `Output Buffer`: **Enable**
        
    - `Trigger`: **Timer 2 Trigger Out Event**
        
    - `DMA Settings` -> `Add` -> `DAC_CH1`:
        
        - `Mode`: **Circular**
            
        - `Direction`: **Memory to Peripheral**
            
        - `Data Width`: Memory `Byte`, Peripheral `Byte` (**8-bit right aligned**)
            
- **Timers -> TIM3 (用于ADC触发)**:
    
    - `Parameter Settings`: `Prescaler = 89`, `Counter Period = 99` (产生8kHz频率)
        
    - `Trigger Output (TRGO) Parameters` -> `Trigger Event Selection`: **Update Event (UE)**
        
- **Timers -> TIM2 (用于DAC触发)**:
    
    - `Parameter Settings`: `Prescaler = 89`, `Counter Period = 99` (产生8kHz频率)
        
    - `Trigger Output (TRGO) Parameters` -> `Trigger Event Selection`: **Update Event (UE)**
        
- **Connectivity -> SPI2 (用于SD卡)**:
    
    - `Mode`: **Full-Duplex Master**
        
    - 在`Pinout`视图中，将`PB12`手动设置为`GPIO_Output`用作片选(CS)。
        
- **System Core -> NVIC (中断控制器)**:
    
    - `DMA1 channel 1 global interrupt`: **勾选Enabled** (这是ADC1对应的DMA通道)
        
    - `DMA1 channel 3 global interrupt`: **勾选Enabled** (这是DAC1对应的DMA通道)