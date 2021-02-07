### 角色特有光照的饱和度平衡

​	不同**材质**对应不同固定饱和度系数，整体饱和度由**<span style='color:#ffff00'>StaticCharacterLightSaturation</span>**控制（1是完全灰度）

### 角色特有补光计算

​	光照方向固定为屏幕空间的(0.6667, -0.6667, -0.3333)，光源颜色为**<span style='color:#ffff00'>StaticCharacterLightColor</span>**，结果叠加至Diffuse

### 卡通次表面法线收束（仅皮肤和头发（后者不太明显））

​	非金属表面的物体，混合贴图_U的R通道作用会由metallic贴图变为**Bunching指示贴图**，该帖图完全白色的部分，其顶点法线在后续计算中会在屏幕空间偏移至正朝向摄像机（屏幕空间的(0,0,-1)），全黑色部分保持原有法线方向，其强度由**<span style='color:#ffff00'>NormalBunchingStr</span>**控制

### 角色特有阴影  ·  混合

​	阴影灯方向为**<span style='color:#ffff00'>SpecialCharacterShadowLightDirVS</span>**（相对屏幕空间固定），结果按**<span style='color:#ffff00'>ShadowBalance</span>**与平行光阴影混合。阴影按**材质**和**<span style='color:#ffff00'>ShadowRampBalance</span>**会有不同程度偏移（降低阴影对面部结果的影响）

​	特有阴影最大值为**<span style='color:#ffff00'>SpecialShadowMin</span>**（效果几乎忽略不计）

### 角色特有阴影  ·  重映射

​	由**<span style='color:#ffff00'>ShadowAttenRamp</span>**和**<span style='color:#ffff00'>ShadowAttenRampLevel</span>**对阴影进行重映射以定制模糊阴影边界

### 次表面颜色叠加

​	由三盏方向接近的灯**<span style='color:#ff0000'>NormalDotDir1/2/3</span>**混合计算得出**（已直接全部替换为平行光）**并叠加第一层边界光**<span style='color:#ffff00'>EdgeLightColor1</span>**，与次表面高光贴图_S相关，使用CodeVein特有的EnvBRDF算法，强度受**<span style='color:#ffff00'>SubsurfaceColorIntensity</span>**控制

### 拆分光影响

​	光可由**<span style='color:#ffff00'>DirLightSaturation</span>**控制饱和度，由**<span style='color:#ffff00'>DirLightIntensity</span>**控制强度，头发材质只收到其他材质一半强度的光影响

### 描边  ·  蒙板

IcbDir 3 / 4 / 5，EdgeWidth，OutlineSizeY，EdgeRange

### 描边  ·  流光

IcbDir 1 / 2，edgeMul

### 描边

EdgeHighlightDepthRange，EdgeMin，OutlineSizeX，EdgeLightColor1，EdgeLightColor2

### 后处理混合

PosRange负责范围



其他参数可以不动或者忽略，意义不大或是有其他系统替代



## 问题？

1.次表面（皮肤）不受环境光（SH），因此没有后期做处理的时候（远距离）阴影部分近乎全黑

2.可能UE4的深度图并非线性，导致原有算法描边在不同距离区间可能产生（中远距离明显）的错误效果（宽度过大），可以调整参数缓解但不能解决

3.有几个调节饱和度的参数可能可以忽略

4.可能有bloom的计算内容被夹在里面





