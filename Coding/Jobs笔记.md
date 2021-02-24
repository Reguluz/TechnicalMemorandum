# Jobs笔记

1. Jobs无法与objects共用
2. Jobs仅支持简单的值和结构体
3. 数组需要使用NativeArray替代Array。NativeArray是直接指向本机内存的
4. NativeArray需要释放（NativeArray.Dispose())，记得在OnDisable逐个释放
5. Jobs的执行需要一个继承job接口的结构类型，示例版本使用Burst1.4.3，常用接口名为IJobFor（此接口通常用于__for__ loops）
6. IJobFor需要一个Excute方法，传入for循环次数变量，此方法必须是public的
7. job.Schedule(迭代次数，顺序依赖)替代for循环，使其自动执行，该函数跟踪返回一个JobHandle值（每执行一次都会返回），可以使用.Complete延迟该操作至schedule全部完成后再返回
8. 使用Mathematics包内函数替换原有Vector和quaternion相关操作（否则burst优化效率会下降）

