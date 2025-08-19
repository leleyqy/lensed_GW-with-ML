*lensed GW + ML.ipynb*

此jupyter notebook用于生成ET探测器的unlensed及lensed GW数据（whitened）

必要package：
- astropy
- bilby
- numpy
- pandas

运行完毕会得到文件：

*lens_params.csv*：透镜参数，包含关键字 mu_0, mu_1, t_d

*source_samples.csv*：源参数，包含引力波15个参数的关键字

*lensed_index.csv*：透镜源index，包含关键字 lensed_index

*lensed_source_samples.csv*：透镜源参数，包含引力波15个参数的关键字

*unlensed_events_data.npz*：非透镜化GW数据(whitened)，包含关键字data_strain, time_array, optimal_SNR

*lensed_events_data.npz*：透镜化GW数据(whitened)，包含关键字data_strain, time_array, optimal_SNR


以下序号对应于notebook内单元格

# 0. 参数设置
所有可以更改的参数均在这个单元设置，其他单元按顺序依次直接运行即可。

## 0.0 随机种子
设置随机数种子

## 0.1 源参数
设置单元格1.1中波源参数的采样范围，产生n_samples个GW波源

- n_samples: 总样本个数

- z：波源红移 (建议范围 0 < z_s <= 2)

- d_L：波源光度距离

- m：波源质量（建议范围 10 - 70）

- time：波形到达时间

- 其余参数使用默认即可

## 0.2 透镜参数
从n_samples个波源中，随机挑选n_lens个，作为被透镜化的GW波源。如果不需要生成透镜化数据，设为0即可
这里并未考虑真实物理情景，仅给出了一个真实建模情况下放大率和时延的大致参考范围，也可根据实际需要调整参数上下限以及其分布。运行此单元格，所有透镜参数（共n_lens组）保存至文件 
*lens_params.csv*

- n_lens: 透镜化样本个数( <=n_samples). 

- mu_0：第一个像的放大率( mu_0 > 2 )

- mu_1：第二个像的放大率

- t_d：第二个像与第一个像的到达时间差( t_d > 0 )

## 0.3 GW波形参数
设置波形的采样频率以及数据时长

- sampling_frequency: 采样频率(建议 >=4096Hz)

- duration: 数据时长(建议 >16s,太短可能无法容纳GW波形。如果实在需要更短的data,可以增大minimum_frequency来缩短GW波形的持续时间，避免报错)

- minimum_frequency: 引力波波形的起始频率(建议20Hz-40HZ)


# 1. Source generation

## 1.1 All GW sources
使用bilby内置先验分布对GW参数进行采样，产生n_samples个源
运行此单元格，保存所有波源参数至文件 
*source_samples.csv*

## 1.2 Lensed GW source
从n_samples个波源中，随机挑选n_lens个，作为被透镜化的GW波源
运行此单元格，得到需要透镜化的波源(第一个像的参数),并保存其在所有波源中的index至文件
*lensed_index.csv*

## 1.3 All lensed GW source
透镜产生双像，所以n_lens个透镜会产生 2 * n_lens个像，相当于有 2 * n_lens组源的参数和data
运行此单元格，产生第二个像的源参数（t_2=t_1+t_d，其余参数保持一致）,保存所有透镜化GW源的参数至文件
*lensed_source_samples.csv*


# 2. Lensed waveform
透镜化波形（一键运行即可，生成data时需要）


# 3. GW data

## 3.1 Unlensed GW data
直接运行此单元格,得到所有非透镜化GW事件（共n_samples-n_lens组）的时域数据(whitened)。保存t,data,SNR至文件 
*unlensed_events_data.npz*

## 3.2 Lensed event data
### 3.2.1 First image data
直接运行此单元格,得到透镜化GW的第一个像的时域数据

### 3.2.2 Second image data
直接运行此单元格,得到透镜化GW的第二个像的时域数据

### 3.3.3 All lensed GW data
合并image_1和image_2的data数据（共2 * n_lens组）,保存至文件 
*lensed_events_data.npz*