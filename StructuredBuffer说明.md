# StructuredBuffer说明

1. [StructuredBuffer测试报告 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/68888365)
2. 使用compute shader 需要OpenGL ES 3.1 及以上
3. ES 3.1有Shader Model 4.5和Shader Model 5.0两个版本，实际只有后者有效
4. 建议在使用时StructuredBuffer最好不超过24个，安卓机不同配置结果，支持在24-35不等
5. Metal环境  整个着色器阶段中或者一个ComputeShader中支持31个StructuredBuffer
6. 大部分安卓机只在Frag阶段支持StructuredBuffer



##### ComputeShader技术在Android上运行要求：Shader Model 5.0的设备（OpenGL ES 3.1及以上设备大部分满足此条件，少数3.1的设备不满足）。

##### ComputeShader技术在iOS上运行要求：系统软件最低为iOS 9（iPhone6之前的因缺乏设备未能测试，有需要的看官请自行测试）。

