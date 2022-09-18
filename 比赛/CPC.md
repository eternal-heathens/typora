

### Blender整体架构图



![image-20220829224744760](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220829224744760.png)



https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html



![image-20220830233738153](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220830233738153.png)





![image-20220830234417769](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220830234417769.png)



### 路径跟踪渲染流程

![image-20220831233700051](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831233700051.png)

![image-20220831233648991](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831233648991.png)



![image-20220831233820188](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831233820188.png)



![image-20220831234034061](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831234034061.png)



![image-20220831234049420](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831234049420.png)



### BVH构建

![image-20220831234406631](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831234406631.png)



### BVH 遍历

![image-20220831234526768](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831234526768.png)



![image-20220831234814810](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831234814810.png)



### 从核加速方案



![image-20220831235805415](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831235805415.png)



![image-20220831235824592](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831235824592.png)



![image-20220901000255315](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220901000255315.png)

DMA 传输和LDM共享空间使用注意从核执行代码不要太多，避免堆栈调用过多占用LDM



![image-20220901000458003](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220901000458003.png)