# Blinn-Phong 反射模型

### 整体结构

<img width="894" height="481" alt="image" src="https://github.com/user-attachments/assets/3ea112c4-775b-4ef7-af6a-571ab59537d0" />

环境光 + 漫反射 + 镜面反射 = 布灵冯反射模型

### 环境光

布灵冯反射模型中假设环境光是常数``Ia``

### 漫反射

前提：
1. 对于一个局部的点来说，可以考虑他是平面
2. 漫反射后光向四周均匀反射
3. 光的能量守恒（按光能量圈理解，光圈面积越小，单位面积上的光能量越大，反之则越小）
4. 平面法线与光的方向同向时，接收能量的有效面积越大，反之则越小

最终计算公式：

其中系数``kd``向量表示平面对于``rgb``的反射系数[0,1]，1表示不吸收，0表示全部吸收能量

<img width="756" height="385" alt="image" src="https://github.com/user-attachments/assets/d6b216c3-5f03-4146-a563-1760f330c1ba" />

### 镜面反射

前提：
1. 取决于观测点与光线反射方向之间夹角
2. 也可以采用平面法线``n``与光线方向``l``与观察方向``v``的角平分向量去估计上述关系
3. 因为角平分向量比光线反射方向更好计算

最终计算公式:

其中：
1. 光的能量遵守能量守恒
2. Ks表示光的反射系数
3. 指数``p``可以控制观测到镜面反射的角度，当``p``越大，越难观测到高光

<img width="779" height="394" alt="image" src="https://github.com/user-attachments/assets/8ad5fddf-5b73-4f7c-8f92-92991279ee2e" />

#### 镜面反射的参数变化：

<img width="888" height="658" alt="image" src="https://github.com/user-attachments/assets/cac131c2-825c-45e9-b40e-af20bb74deff" />


