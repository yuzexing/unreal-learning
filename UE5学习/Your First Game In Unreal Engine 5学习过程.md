# Your First Game In Unreal Engine 5学习过程

学习过程中的困惑：遇到很多类不知道为什么作者要使用他，也不知道他的作用，如果作者能够详细的说明每一个选择的理由就最好了。

学习思路：遇到问题解决问题，遇到使用的API则去文档查询API的作用，如果文档也不清晰，则查询AI。

问题一：UVs的作用


问题二：过程中遇到Axis and action mappings are now deprecated, please use Enhanced input Actions and input Mapping Context instead。

使用[Enhanced Input System](https://www.bilibili.com/video/BV1GEypYKERz/?spm_id_from=333.337.search-card.all.click&vd_source=16b822060153afb61a028379bf440fe8)

Enhanced input Actions and input Mapping Context和Axis and Action mapping的最大区别：前者是基于数据资产（data asset)的，而后者是将定义放在配置文件中（deafultInput.ini）

优势：
1. Enhanced input actions 类似于旧的axis and action mapping，但是通过数据资产管理。
2. Input Mapping Context 支持动态的上下文切换，更加灵活的控制操作映射。



