# UE模块构建

[官方文档](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-modules)

### 私有模块和共有模块的区别

区别通常出现在当前模块需要被其他模块引用时，例如A->B->C，A引用B，B引用C。

当B在公开的头文件中引入了C，且A引入了B的头文件，则需要将C加入B的public依赖中，否则会出现链接错误。

### 设置为私有模块的优势

通常情况下，将依赖模块设置为私有依赖(条件允许的情况下)，会提高编译速度：

1. C++在编译时，只会增量编译，而不是全局重新编译
2. 所以当被修改的头文件所关联的翻译单元越少，则需重新编译的翻译单元则会少，所以速度越快


图示：

<img width="471" height="295" alt="image" src="https://github.com/user-attachments/assets/2ba0b7d6-8b82-4b3d-8fe5-25a2b52dd408" />

解释：
1. C++的编译是以翻译单元为最小单元
2. 对于Parent Module .cpp来说，其引用了Your Module中的头文件，且该头文件没有发生变化，则不会重新编译
3. Your Module .cpp文件中引入了child Module中的头文件，则该头文件发生变化，则需要重新编译
4. 对比：如果Parent Module (.cpp/.h)也引入了child Moudle中的头文件(需要加入Your Moudle的公开依赖)，则会导致YourMoudle和ParentMoudle都重新编译
