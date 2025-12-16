# CharacterMovementComponent源码


### PerformMovement函数

关于性能优化的点：
1. FScopedCapsuleMovementUpdate 创建了一个新的移动范围，在此范围内移动的传播可以推迟到最外层不延迟更新的范围结束。该范围内的移动会避免更新，如 UpdateBounds（）、OnUpdateTransform（）、UpdatePhysicsVolume（）、UpdateChildTransforms（） 等，直到移动被提交
2. 总的来说是一种批量更新的操作，减少更新广播次数，减少检测次数
