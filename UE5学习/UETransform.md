# UETransform

### Transform

基于某一个坐标系的变换，可以相对父级节点的空间坐标系的变换，也可以是一个相当于世界坐标的空间变换

### Transform Compose

1. 对于单个Transform的顺序：先缩放，再旋转，最后平移
2. 对于多个Transform Compose之间的顺序：先A、再B、最后C（A·B·C）


[变换顺序参考](https://rodolphe-vaillant.fr/entry/145/unreal-engine-c-tmap-doc-sheet-1)

### Transform inverse

常用于求两个两个Transform之间的``delta``，差异，等价于矩阵减法

也可以适用于：求父节点相对于直接子节点的Transform

