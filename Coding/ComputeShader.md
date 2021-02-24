# ComputeShader

### 传值

1. 新建数据结构体

   ```C#
   public struct Property
   {
   	...
   }
   ```

2. 新建结构体列表

   ``` C#
   public NativeArray<Property> propertiesList;
   ```

3. 初始化和消除（以ECS系统写法为例，下同）

   ```C#
   static int maxCount;
   
   void OnStartRunning()
   {
       //新建
   	propertiesList = new NativeArray<Property>(maxCount, Allocator.Persistent)
   }
   
   void OnStopRunning()
   {
   	propertiesList.Dispose();   
   }
   ```

4. 运行赋值与回传

   数据修改部分（来自C#部分，按Update周期执行）：

   ```C#
   
   void OnUpdate()
   {
   	for(int index=0; index<maxCount; index++)
       {
           var intervalProperty = propertiesList[Index];
           //赋值
           propertiesList[index] = intervalProperty;
       }
   }
   ```

   

   数值传递部分(来自RenderFeature部分，按Excute周期执行)

   ```C#
   ComputeBuffer propertyBuffer;
   ComputeShader cs;
   int propertyDataID =  Shader.PropertyToID("properties");
   
   void Excute(CommandBuffer cmd, ScriptableRenderContext context)
   {
   	//清除数据
   	if(propertyBuffer !=null)
       {
       	propertyBuffer.Dispose();
           propertyBuffer.Release();
       }
       //判断长度
       bool hasData = false;
       int intervalLength = Math.Min(propertiesList.Count, maxCount+1);
       hasData = intervalLength > 0 ? true : false;
       
       if(hasData)
       {
           //重建Buffer
           propertyBuffer = new ComputeBuffer(intervalLength, Marshal.SizeOf(typeof(Property)));
           //直接全部读取
           NativeArray<Property> intervalProperties = propertiesList.GetSubArrary(0， propertiesList.Count);
           //数据传递给Buffer
           propertyBuffer.SetData(intervalProperties);
       }else{
           //即使没数据也要有1条否则出问题
           propertyBuffer = new ComputeBuffer(1, Marshal.SizeOf(typeof(Property)));
       }
       
       //寻找ComputerShader中需要使用的Kernel
       int kernel = cs.FindKernel("ComputeKernel");
       cmd.SetComputeBufferParam(cs, kernel, propertyDataID, propertyBuffer)
       context.ExecuteCommandBuffer(cmd);
       cmd.Clear();
       
   }
   ```

   

5. ComputeShader设置

   ``` HLSL
   StructuredBuffer<Property> properties;
   RWStructuredBuffer<float3> propertiesForShader;
   struct csInput
   {
   	uint3 GroupID           : SV_GroupID;           // 3D index of the thread group in the dispatch.
   	uint3 GroupThreadID     : SV_GroupThreadID;     // 3D index of local thread ID in a thread group.
   	uint3 DispatchThreadID  : SV_DispatchThreadID;  // 3D index of global thread ID in the dispatch.
   	uint  GroupIndex        : SV_GroupIndex;        // Flattened local index of the thread within a thread group.
   }
   
   [numthreads(8,8,1)]
   void ComputeKernel(csInput cs_IDs)
   {
   	//计算结果（example）
   	propertiesForShader[id] = properties[cs_IDs.DispatchThreadID]+-*/;
   }
   ```

   

6. Shader设置

   ```
   StructuredBuffer<float3> propertiesForShader;
   
   
   half4 frag()
   {
   	o.pos = propertiesForShader;
   }
   
   ```

   

7. __矩阵渲染例子__

   ```C#
   渲染 resolution*resolution个mesh，使用 material,整体使用bounds做AABB
   Bounds bounds = new Bounds(Vector3.zero/*ObjectSpace中心*/, Vector3.one * (2f + 2f / resolution)/*Size*/);
   Graphics.DrawMeshInstancedProcedural(mesh, 0, material, bounds,resolution * resolution);
   ```

   

   #### Tips

   1. Shader种获取到的StructuredBuffer的名字必须和compute shader中对应需要获取的buffer名字一致
   2. DispatchThreadID.x为总长ID，无视groups不需要额外计算

   

   





​	

