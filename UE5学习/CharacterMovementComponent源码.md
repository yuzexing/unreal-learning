# CharacterMovementComponent源码


### PerformMovement函数

#### BasedMovement 是什么？

> 在MaybeUpdateBasedMovement这里提到，BaseMovement是基座平台，例如可移动的平台，需要将角色的Transform先保持与基座平台的同步，通过调用 UpdateBasedMovement 实现

#### bClientUpdating 是什么？

> 在``Prepare Root Motion``处，进行了!bClientUpdating的判断
> 
> bClientUpdating 代表在客户端预测失败，使用服务端通过网络传输过来的Savemoves进行更新矫正。
> 
> 含义是否正在应用服务器状态还原 + SavedMove 重放



#### 关于性能优化的点：
1. FScopedCapsuleMovementUpdate 创建了一个新的移动范围，在此范围内移动的传播可以推迟到最外层不延迟更新的范围结束。该范围内的移动会避免更新，如 UpdateBounds（）、OnUpdateTransform（）、UpdatePhysicsVolume（）、UpdateChildTransforms（） 等，直到移动被提交
2. 总的来说是一种批量更新的操作，减少更新广播次数，减少检测次数
