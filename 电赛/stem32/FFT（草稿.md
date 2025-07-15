```
#include "arm_math.h" // 引入DSP库头文件
#include <stdio.h>

#define FFT_SAMPLES   1024 // FFT采样点数，必须是2的幂

// 假设这是您找到或生成的原始数据
// float32_t signal_10khz_noise[FFT_SAMPLES] = { /* 数据点... */ };
// float32_t signal_1m_5m[FFT_SAMPLES] = { /* 数据点... */ };

// FFT输入输出缓冲区，长度为2*N，用于存放复数（实部、虚部交替）
static float32_t fft_input_output[FFT_SAMPLES * 2];

// 存放FFT计算出的幅值
static float32_t fft_magnitude[FFT_SAMPLES];

// --- 在您的main函数或一个专门的函数中执行以下代码 ---

void perform_fft(const float32_t* original_signal) {
    // 1. 准备FFT输入数据 (将实数信号转为复数形式)
    // 这是练习题中特别强调的一步
    for (int i = 0; i < FFT_SAMPLES; i++) {
        fft_input_output[2 * i] = original_signal[i]; // 实部：原始信号
        fft_input_output[2 * i + 1] = 0;              // 虚部：全部为0
    }

    // 2. 初始化基4的CFFT（复数FFT）实例
    arm_cfft_radix4_instance_f32 fft_instance;
    arm_cfft_radix4_init_f32(&fft_instance, FFT_SAMPLES, 0, 1); // 0表示FFT, 1表示正向排序

    // 3. 执行FFT计算 (结果将直接覆盖fft_input_output)
    arm_cfft_radix4_f32(&fft_instance, fft_input_output); [cite: 97]

    // 4. 计算FFT结果的模值（幅度）
    // pSrc是FFT结果，pDst是存放幅度的目标数组
    arm_cmplx_mag_f32(fft_input_output, fft_magnitude, FFT_SAMPLES); [cite: 99]

    // 5. [可选] 幅度转换
    // 为了方便观察，通常会对幅度进行归一化或转换为dB
    // 这里我们只取前一半的数据（N/2），因为对于实数输入，频谱是对称的
    // arm_max_f32(fft_magnitude, FFT_SAMPLES/2, &max_value, &max_index);
}
```