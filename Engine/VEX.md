# VEX



## 常用（标准）属性

### 	Vector

​	@P, @accel, @center, @dPdx, @dPdy, @dPdz, @Cd, @N, @scale, @force, @rest, @torque, @up, @uv, @v

### 	Vector4 

​	@backtrack, @orient, @rot

### int

​	@id, @ix, @iy, @iz, @nextid, @pstate, @resx, @resy, @resz, @ptnum, @vtxnm, @primnum, @numpt, @numvtx, @numprim, @group_*

### 	string

​	@name, @instance

## 全局变量

####  __$__开头为全局变量

__仅部分全局变量能直接获取__

##### 常用

$E	自然对数

$F	整数帧

$FPS	帧速率

$PI	圆周率



__其他无法直接$获取的变量__

| @Time        当前时间 | @Frame        当前帧 | @Timeinc        1/$FPS，当前帧的时间增量 |
| --------------------- | -------------------- | ---------------------------------------- |
|                       |                      |                                          |



## 类型赋值

​	i@ -> int

​	f@ -> float

​	u@ -> vector2

​	v@ -> vector3

​	p@ -> vector4

​	2@ -> matrix2

​	3@ -> matrix3

​	4@ -> matrix4

​	s@ -> string

​	[]@表示数组 -> i[]@	f[]@	3[]@	s[]@



## 属性创建/修改/读取

#### 创建

非Houdini标准数据创建需指定属性

​	·	@Cd = {1,0,0}	(Cd是标准数据)

​	·	__v__@a = {0,1,0}	(a不是标准数据)	-->	缩写创建

​	·	__vector__ @b = {0,1,0}	-->	全称创建

#### 修改

_直接以string带引号使用，不需要@_

​	addpointattrib(0, "Cd", {0,0,0})

​	addvertexattrib()

​	addprimattrib()

​	adddetailattrib()

​	setpointattrib(0, "Cd", 3, {0,0,0})具体看官方文档

​	setvertexattrib()

​	setprimattrib()

​	sdeettailattrib()



#### 读取

​	读取___非houdini标准数据___且该数据___不是在当前节点创建___时需指定属性，否则默认浮点



## 指定获取

·	使用opinput获取输入端属性

​	opinput关键字：@opinput1_Cd.y 获取输入端1的Cd.y属性（__输入端从0开始__）

​	获取非Houdini标准属性需标明属性类型：v@opinput1_custom.y

​	

·	使用Function获取属性

​	vector color = point(1, 'Cd', @ptnum);

​	prim()、vertex()、detail()……看文档

·	使用Function修改属性，参见__属性创建/修改/读取__

·	__体素相关__:使用 _@体素节点名_获取体素密度，使用VolumeWrangle逐体素操作



## 读取节点参数

