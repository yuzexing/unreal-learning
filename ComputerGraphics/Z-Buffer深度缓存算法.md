# Z-Buffer深度缓存算法

光栅化之后，三角形可以被显示在屏幕上，如果多个三角形之间存在z方向上的遮挡，该怎么办？

使用深度缓存算法，在绘制时，
1. 维护一个zbuffer的二维数组，其中点初始化为无限大
2. 遍历三角形中的像素点，判断z是否比zbuffer中的z值小，小则绘制
3. 将点绘制道framebuffer中

时间复杂度O(nm)，空间复杂度O(nm)，n,m分别为屏幕宽高

<img width="622" height="395" alt="image" src="https://github.com/user-attachments/assets/90ce2021-40c7-487a-9b09-73ba05d2c293" />

<img width="674" height="397" alt="image" src="https://github.com/user-attachments/assets/1df59d3e-829f-4487-83f8-d353ccc8a34e" />

