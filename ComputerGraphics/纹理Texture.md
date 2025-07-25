# 纹理Texture

### 纹理的作用

控制像素点对光的反射能力，也就是控制每一个像素点的颜色

前提：所有三维物体的表面都可以被拉成一个平面

### UV是什么？

UV是[0,1]的纹理坐标，模型中三角形的每一个顶点都有一个[u,v]坐标，通过[u,v]坐标可以在纹理中找到对应的展示区域

<img width="600" height="423" alt="image" src="https://github.com/user-attachments/assets/37b889ae-a132-4ea4-b3bc-3cbf4ade1d7b" />

### 如何将模型中的三角形顶点，与纹理的uv坐标关联？

模型设计师、艺术家负责或者由软件生成


### 纹理过小

含义：
当多个屏幕像素(pixcel)对应一个纹理像素(texel)时，即纹理分辨率不足。

处理方式：

1. 取最近纹理像素作为屏幕像素（多个屏幕像素对应一个纹理像素）
2. 取最近的四个纹理像素进行双线性插值
3. 取最近的十六个纹理像素进行插值

#### 双线性插值：

对于矩形网格内的四个点顶点，
1. 先在x方向上做两次插值
2. 再在y方向上做一次插值
3. 顺序任意，一共三次线性插值

### 纹理过大

当一个像素对应多个纹理时，即屏幕分辨率不足

产生的问题：
1. 摩尔纹

解决办法：
1. 超采样(supersampling，增加采样点，将多个采样点的平均值作为像素的颜色)

超采样代价较高，通过空间换时间，给定一个像素可以查周围的纹理像素的平均值(范围查询)


方法：
1. mipmap（方形查询）
2. Anisotropic Filtering（各向异性过滤）
3. EWA filtering 

### mipmap原理

进行快速的、近似的、正方形范围查询

分块：
<img width="662" height="451" alt="image" src="https://github.com/user-attachments/assets/bb6e13af-e65f-4a5b-8c76-909380ee1dd4" />

额外空间大小：1/3

#### 原理：
1. 首先纹理分级存储，提前计算好每块纹理的平均值
2. 计算一个像素点所占用的纹理大小
3. 根据占用大小查询所在的纹理层级(Level0 / Level1....)
4. 查询纹理层级中的坐标的平均值

#### 带来的问题：

随着场景深度的增加，纹理产生了离散的纹理过渡（例如同一块墙体颜色骤变）

解决办法：在不同层级之间进行线性插值(现在纹理层内部插值，再将不同层级之间的插值结果进行线性插值)

可以实现对任意纹理层级的范围查询

<img width="621" height="463" alt="image" src="https://github.com/user-attachments/assets/ec9dc9de-fcb1-4262-9877-da7329a020fe" />


### Anisotropic Filtering（各向异性过滤）

实现对矩形的范围查询，可以对方形进行查询

空间存储增加三倍(极限趋近于三倍)

<img width="679" height="228" alt="image" src="https://github.com/user-attachments/assets/830628c3-7744-4eb4-bad7-d7ef6f4a8e0a" />

### 法线贴图

在着色器中直接用这些扰动后的法线替代模型原生法线，进行更精细的光照计算，让平面表面呈现出复杂凹凸感

优点：
1. 不增加模型网格数量
2. 效率高

缺点：
1. 在模型边缘处容易产生瑕疵（边缘是无法展示出凹凸的）
2. 需要使用三通道存储法线信息

> 同理有高度图，使用一个通道存储高度，需要外的计算产生得出法线

### 位移贴图

会对三角形顶点产生真实的影响，从而产生凹凸效果

局限性：
1. 要求三角形顶点数量满足位移贴图的要求

### 环境贴图

预先采样或渲染得到的全景**光照纹理**，一般采用立方体贴图。

因为球面贴图边缘会有问题。

<img width="597" height="374" alt="image" src="https://github.com/user-attachments/assets/0b72a8cf-6e9f-4a1c-9a5f-6caa6130f2c5" />


