# UE5Foot_IK

双脚向下射线检测获取offset，将较小的offset作用于盆骨。

核心在于先作用于ik_bone，用于ik计算

最好需要差值过渡（如果不呢）


### aim math

Aim Math 节点的主要功能是将骨骼的某个本地轴（Primary Axis）对齐到一个目标方向（Target Direction），同时通过次级轴（Secondary Axis）控制骨骼的翻滚（Roll）或朝向稳定性

参数解释：
Primary Axis（主轴）：指定骨骼的哪个本地轴需要对齐到目标方向。

Secondary Axis（次轴）：用于控制骨骼在对齐主轴后，绕主轴的旋转，确保骨骼的朝向稳定。

Target Kind（目标类型）：可以是 Direction（方向）或 Location（位置）。

Direction：直接指定一个方向向量。

Location：指定一个目标位置，系统会计算从骨骼当前位置指向目标位置的方向向量。

Target Space（目标空间）：定义目标方向或位置的空间，通常为 World（世界空间）或 Bone（骨骼空间）。
