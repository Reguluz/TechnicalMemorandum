[Unity - Manual: Draw call batching (unity3d.com)](https://docs.unity3d.com/2018.4/Documentation/Manual/DrawCallBatching.html)

## Static Batching

#### 原理

把共享材质的静态物体的顶点信息合并在一起并直接转换到世界空间进行储存

#### 条件

静态物体

#### 优化内容

减少DC调用间渲染状态切换的次数，让Command Buffer能够缓存绘制命令，对流程进行加速

#### 缺陷

内存占用和打包体积增大，并不会减少DC数量（如果显示状态不变有可能显示为降低了DC数量，实际不会）

#### 导致失效

__改变材质球__：改变Renderer.material的信息导致产生新的material instance

#### 导致中断

__有穿插__：位置不相邻且穿插不同材质的物体渲染

__Lightmap__：拥有不同Lightmap信息的部分



## Dynamic Batching

#### 原理

把共享材质的动态物体的顶点信息转换到世界空间并使用单个DC统一绘制

#### 条件

面数较少的动态物体

#### 优化内容

减少DC调用次数

#### 缺陷

有一定的CPU开销

#### 导致失效

__改变材质球__：改变Renderer.material或者改变其变量导致产生新的material instance

__数据量超限__：顶点信息数据量不能超过900个Vector4 / 面数大于900

__Lightmap__：拥有不同Lightmap信息的部分

#### 导致中断

__有穿插__：位置不相邻且穿插不同材质的物体渲染

__次要方案__：优先参与Static Batching和GPU Instancing

__镜像变换__： Transform.scale < 0

__多渲染__：使用Multi-pass渲染，需要切换渲染状态

**特殊情况：同参数Shadow Caster Pass即使不是同一材质也可批处理



## GPU Instancing

#### 原理

相同材质球相同Mesh信息的对象，其所有信息被保存在显存的Constant Buffer，选其中一个实例进入渲染流程，当在执行DrawCall操作后，从显存中取出实例的部分共享信息与从GPU常量缓冲器中取出对应对象的相关信息一并传递到下一渲染阶段，与此同时，不同的着色器阶段可以从缓存区中直接获取到需要的常量，不用设置两次常量。

#### 条件

相同材质球相同Mesh信息

#### 优化内容

减少同信息的反复读取，部分信息存储在GPU缓存中进行计算，降低带宽开销，降低DC

#### 缺陷

Constant Buffer不得大于64K

#### 导致失效

__改变材质球__：改变Renderer.material或者改变其变量导致产生新的material instance

__数据量超限__：Constant Buffer超过64k

__镜像变换__： Transform.scale < 0

__多光源__：需要超过一盏实时光的计算

#### 导致中断

__有穿插__：位置不相邻且穿插不同材质的物体渲染

__数据量超限__：一个批次超过125个物体会被分批

__镜像变换__： Transform.scale < 0

__次要方案__：优先参与Static Batching



## SRP Batcher

#### 原理

对象所有信息被保存在显存的"Per Object" GPU Buffer，选其中一个实例进入渲染流程，当在执行DrawCall操作后，从显存中取出实例的部分共享信息与从GPU常量缓冲器中取出对应对象的相关信息一并传递到下一渲染阶段，与此同时，不同的着色器阶段可以从缓存区中直接获取到需要的常量，不用设置两次常量。

#### 条件

物体的**Shader中变体**一致

#### 优化内容

减少同信息的反复读取，降低DC的成本（不降低DC）

#### 缺陷

Constant Buffer不得大于64K

#### 导致失效

__动态Mesh__：粒子或蒙皮网格不支持SRP Batch

__Shader差异__：并未使用同一变体

__有穿插__：位置不相邻且穿插不同材质的物体渲染