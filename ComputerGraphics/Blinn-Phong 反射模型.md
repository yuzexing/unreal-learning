# Blinn-Phong 反射模型

### 整体结构

<img width="894" height="481" alt="image" src="https://github.com/user-attachments/assets/3ea112c4-775b-4ef7-af6a-571ab59537d0" />

环境光 + 漫反射 + 镜面反射 = 布灵冯反射模型






前提：
1. 对于一个局部的点来说，可以考虑他是平面
2. 暂时不考虑周围的环境光对其的影响
3. 漫反射后光向四周均匀反射
4. 光的能量守恒（按光能量圈理解，光圈面积越小，单位面积上的光能量越大，反之则越小）
5. 平面法线与光的方向同向时，接收能量的有效面积越大，反之则越小

最终计算公式：

其中系数``kd``向量表示平面对于``rgb``的反射系数[0,1]，1表示不吸收，0表示全部吸收能量

<img width="650" height="459" alt="image" src="https://github.com/user-attachments/assets/c7d2cc29-62dd-415d-84fa-6144146e2f83" />
