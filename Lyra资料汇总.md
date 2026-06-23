# UE5 Lyra 学习资料整理

> 本文整理我在学习 UE5 Lyra Starter Game 过程中参考过或计划参考的资料，主要面向想学习 Lyra、UE5 多人游戏架构、GAS、GameFeature、Experience、运动系统、UI、性能优化的开发者
>
> 这些资料并不代表完整学习路线，只是个人学习过程中的资料索引和经验记录
>
> 如果你也在学习 Lyra，欢迎补充更多资料或提交 PR

---

## 目录

* [一、Lyra 官方资料](#一lyra-官方资料)
* [二、GameFeature 相关资料](#二gamefeature-相关资料)
* [三、Experience 相关资料](#三experience-相关资料)
* [四、装备与库存系统](#四装备与库存系统)
* [五、运动系统与视角改造](#五运动系统与视角改造)
* [六、UI 改造](#六ui-改造)
* [七、Camera 改造](#七camera-改造)
* [八、性能优化资料](#八性能优化资料)
* [九、推荐 YouTube 博主与系列教程](#九推荐-youtube-博主与系列教程)

---

## 一、Lyra 官方资料

### 1. Lyra 官方讲座

* [Bilibili：Lyra 官方讲座 1](https://www.bilibili.com/video/BV16B4y197Zy)
* [Bilibili：Lyra 官方讲座 2](https://www.bilibili.com/video/BV1Ce4y1X7k5)

推荐理由：

这两个视频适合作为 Lyra 入门前的整体概览，帮助理解 Lyra 为什么不是一个普通的第三人称模板，而是一个包含 GameFeature、Experience、GAS、CommonUI、多人框架等系统的综合示例项目

---

### 2. Lyra 官方文档

* [Epic 官方文档：Lyra Sample Game](https://dev.epicgames.com/documentation/unreal-engine/lyra-sample-game-in-unreal-engine)

推荐理由：

官方文档是学习 Lyra 的基础资料，适合在阅读源码和修改功能时反复查询

建议重点关注：

* Lyra 的项目结构
* GameFeature Plugin
* Experience
* PawnData
* 输入系统
* GAS
* CommonUI
* CommonSession
* 武器、装备、库存系统

---

## 二、GameFeature 相关资料

### 官方讲座

* [Bilibili：GameFeature 官方讲座](https://www.bilibili.com/video/BV1j34y1B7Nf)

### 文字总结

* [知乎：GameFeature 文字总结](https://zhuanlan.zhihu.com/p/467236675)

### 群友学习笔记

* [知乎：GameFeature 学习笔记](https://zhuanlan.zhihu.com/p/2003472052393382919)

推荐理由：

GameFeature 是理解 Lyra 架构的关键入口之一
Lyra 里很多玩法内容并不是直接写在主项目里，而是通过 GameFeature Plugin 的方式组织和加载

学习重点：

* GameFeature Plugin 是什么
* GameFeatureData 的作用
* GameFeatureAction 如何添加组件、输入、Ability 和 UI
* 为什么 Lyra 把玩法拆到插件里
* 如何创建自己的 GameFeature Plugin

---

## 三、Experience 相关资料

### 社区教程

* [Epic Community：How to Create a New Game Feature Plugin and Experience in Lyra](https://dev.epicgames.com/community/learning/tutorials/rdW2/unreal-engine-how-to-create-a-new-game-feature-plugin-and-experience-in-lyra)

### YouTube 教程

* [YouTube：Lyra Experience 相关教程 1](https://www.youtube.com/watch?v=C-q3COu2r74)
* [YouTube：Lyra Experience 相关教程 2](https://www.youtube.com/watch?v=lhD9nZ2DqRc&t=4s)

### 文档总结

* [X157：Lyra Starter Game Documentation](https://x157.github.io/UE5/LyraStarterGame/)

推荐理由：

Experience 是 Lyra 里非常重要的概念
它决定一局游戏加载什么 GameFeature、PawnData、Ability、输入、UI 和游戏规则

学习重点：

* Experience 和 GameMode 的关系
* LyraExperienceDefinition
* LyraExperienceActionSet
* PawnData
* 如何创建一个新的 Experience
* 如何让自己的模式加载指定角色、输入和 Ability

---

## 四、装备与库存系统

### YouTube 系列教程

* [YouTube：Lyra Inventory / Equipment 系统教程](https://www.youtube.com/watch?v=F8zJQIxNo4E&list=PL5D8RLj0soskBNjookqshM4wWfBpEkO8s)

推荐理由：

Lyra 的装备和库存系统对理解“物品如何授予 Ability”“武器如何挂到角色身上”“装备实例如何管理状态”非常有帮助

学习重点：

* Inventory Item Definition
* Inventory Item Instance
* Equipment Definition
* Equipment Instance
* 装备如何授予 Gameplay Ability
* 装备如何生成 Actor
* 武器系统和装备系统的关系

---

## 五、运动系统与视角改造

### Lyra 动画适配官方博客

* [Epic 官方博客：Adapting Lyra Animation to Your UE5 Game](https://www.unrealengine.com/tech-blog/adapting-lyra-animation-to-your-ue5-game)

推荐理由：

适合学习如何把 Lyra 的动画系统和自己的角色项目结合
其中也涉及 Lyra 默认运动表现和动画系统的改造思路

---

### 视角与运动插件参考

* [Fab：支持 Lyra TPS 视角和自由视角的插件](https://www.fab.com/listings/682cfcc1-29a7-4d91-8076-f7f76c7acf20)

说明：

这个插件代码中有不少和 Lyra 相似的地方，支持 Lyra 的 TPS 视角和自由视角
可以作为学习视角切换、运动模式改造和代码组织方式的参考

---

### Character Movement 官方文档

* [Epic 官方文档：Understanding Networked Movement in the Character Movement Component](https://dev.epicgames.com/documentation/unreal-engine/understanding-networked-movement-in-the-character-movement-component-for-unreal-engine?application_version=5.6)

推荐理由：

如果你要在 Lyra 中修改角色移动、飞行、自定义 Movement Mode，尤其是涉及多人同步，就必须理解 Character Movement Component 的网络移动机制

---

### 联机移动预测

* [Epic Community：Best Practices for Networked Movement Abilities CMC](https://dev.epicgames.com/community/learning/tutorials/ywD1/unreal-engine-best-practices-for-networked-movement-abilities-cmc)

推荐理由：

联机移动预测是多人游戏中非常重要的理论基础
如果要做飞行、冲刺、特殊移动技能，建议认真学习这部分内容

学习重点：

* Character Movement Component
* Network Prediction
* Saved Move
* 自定义 Movement Mode
* 移动能力如何在多人环境下预测和同步

---

## 六、UI 改造

### UncleTron 教程

* [Epic Community：UncleTron 个人主页](https://dev.epicgames.com/community/profile/45AGD/UncleTron)

说明：

其中有增加体力条相关教程，并附带代码
适合参考如何在 Lyra 中扩展 HUD 和 UI 状态显示

---

### UI 官方讲座

* [Bilibili：UI 官方讲座](https://www.bilibili.com/video/BV1mT4y167Fm)

推荐理由：

尤其推荐后半部分的 UI 材质讲解
适合补充 UE UI 表现、材质和界面设计相关知识

---

### UI 讲解与改造教程

* [YouTube：Lyra UI 讲解和改造教程](https://www.youtube.com/watch?v=Pr_dBTd_UrY)

推荐理由：

适合学习 Lyra UI 的基本结构，以及如何对 UI 进行修改和扩展

学习重点：

* CommonUI
* HUD Layout
* Widget Extension
* UI 和 Gameplay Message 的关系
* 如何新增 HUD 信息
* 如何修改菜单和界面逻辑

---

## 七、Camera 改造

### Camera 系统讲解

* [YouTube：Lyra Camera 大致讲解](https://www.youtube.com/watch?v=IHnMhFv9GuY)

推荐理由：

适合初步理解 Lyra 的 Camera Mode 和 Camera Stack 思路
Lyra 的摄像机不是简单的 SpringArm，而是有自己的 Camera Mode 混合系统

---

### FPS Camera 改造

* [Epic Community：Unreal5Master - FPS Camera in Lyra Starter Game](https://dev.epicgames.com/community/learning/tutorials/RBpm/unreal-engine-unreal5master-fps-camera-in-lyra-starter-game)

推荐理由：

适合参考如何把 Lyra 的默认第三人称视角改造成 FPS Camera

学习重点：

* Camera Mode
* Camera Stack
* TPS Camera
* FPS Camera
* Camera 和 Pawn / Controller 的关系

---

## 八、性能优化资料

### 主循环介绍

* [知乎：UE 主循环介绍](https://zhuanlan.zhihu.com/p/225465983)

推荐理由：

适合理解 UE 每一帧中大概发生了什么
对后续学习 Stat、Unreal Insights 和性能优化有帮助

---

### 对象池讲座

* [Bilibili：对象池讲座](https://www.bilibili.com/video/BV14RU7BJEUk)

推荐理由：

适合用于优化大量投射物、特效、临时 Actor 的创建和销毁成本

---

### Unreal 性能优化学习路线

* [Epic Community：Unreal Performance Optimization Learning Path](https://dev.epicgames.com/community/learning/paths/Rkk/unreal-engine-unreal-performance-optimization-learning-path)

推荐理由：

官方性能优化学习路线，建议系统学习

---

### Unreal Fest 2023 优化讲座

* [Bilibili：UnrealFest 2023 性能优化讲座](https://www.bilibili.com/video/BV1Yj41147dh)

推荐理由：

适合了解 UE 项目中的常见性能瓶颈和优化思路

---

### Nanite 优化

* [YouTube：Nanite Optimization](https://www.youtube.com/watch?v=TMorJX3Nj6U)

推荐理由：

适合学习 Nanite 场景下的性能分析和优化方式

---

### Unreal Insights

* [YouTube：Unreal Insights - Unreal Fest Bali 2025](https://www.youtube.com/watch?v=HQLYkwoDoT4)

推荐理由：

Unreal Insights 是分析 UE 性能问题的重要工具
建议所有做真实项目的开发者都学习

---

### UFSH 2025 性能优化

* [Bilibili：性能优化 UFSH 2025](https://www.bilibili.com/video/BV125aGzVEGA)

---

### 碰撞优化

* [Bilibili：碰撞优化](https://www.bilibili.com/video/BV1FAiPBeEcJ)

推荐理由：

如果项目中有大量投射物、AI、移动单位，碰撞优化非常重要

---

### 性能优化总结

* [Bilibili：性能优化总结](https://www.bilibili.com/video/BV1k44mzWEvq)

---

### 骨骼优化

* [Epic Community：MetaHuman Skeletal Mesh Optimization Guide](https://dev.epicgames.com/community/learning/tutorials/2JVl/unreal-engine-metahuman-skeletal-mesh-optimization-guide)

推荐理由：

适合学习角色骨骼、MetaHuman 和 Skeletal Mesh 相关优化，尤其是metahuman的骨骼

---

## 九、推荐 YouTube 博主与系列教程

### LeafBranchGames

* [YouTube：LeafBranchGames - Lyra 核心部分讲解](https://www.youtube.com/watch?v=xy2J-KnuzQs&t=214s)

个人评价：

把 Lyra 的几个核心部分进行了讲解，并且会一步步带读代码
对于刚开始阅读 Lyra 源码的人非常有帮助

---

### NanceDevDiaries

* [YouTube：NanceDevDiaries 频道](https://www.youtube.com/@nancedevdiariesd)

个人评价：

声音很好听，教程讲解细致，适合慢慢跟着学习

---

### 其他 Lyra 系列教程

* [YouTube：Lyra 系列教程 1](https://www.youtube.com/playlist?list=PL3-o6vIW1dL3wtis-h8LZwmkntAvGo2cN)
* [YouTube：Lyra 系列教程 2](https://www.youtube.com/playlist?list=PLP0m9EE3InC7Byqvsm6WK9NVLkgb_RscR)

说明：

这些教程我目前还没有完整看过
但能够系统做成一个系列，本身就很有参考价值
后续可以根据需要选择性学习


欢迎大家补充更多 Lyra 相关资料，一起交流学习
