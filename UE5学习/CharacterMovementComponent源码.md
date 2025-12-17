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


### SavedMove代码

#### 流程

1. 客户端发起
2. 服务段接受，验证，发送校验信息给客户端
3. 客户端校正或者直接清除待验证的SavedMove

#### 注意点

1. 通过Character发送RPC，避免CMC的组件RPC开销


#### 函数调用过程

1. 客户端用户产生输入事件
2. 客户端CMC Tick事件，消费输入事件
3. 进行SavedMove回放
4. 对于 自主代理和自主控制的Character 且 1.本地控制 2.模拟物理的没有controller的character 3.播放RootMotion的没有Controller的Character
5. 进行``ControlledCharacterMove``，如果是服务器上直接``PerformMovement``;客户端本地的则``ReplicateMoveToServer``
6. 在本地的``ReplicateMoveToServer``中，执行了 ``PerformMovement``
7. 对于服务端的模拟代理，1. 更新BasedMovement，不然可能跟不上基座。 2. 平滑展示客户端位置
8. 对于 模拟代理，进行模拟tick ``SimulatedTick``
9. 
10. 客户端CMC: ControlledCharacterMove -> 服务器上的直接``PerformMovement``;本地的则``ReplicateMoveToServer``
11. CMC::ReplicateMoveToServer -> CMC::CallServerMovePacked -> CMC::ServerMovePacked_ClientSend
12. CMC::ServerMovePacked_ClientSend -> Character::ServerMovePacked_Implementation -> CMC::ServerMovePacked_ServerReceive ->


#### ClientUpdatePositionAfterServerUpdate 是什么？

``ClientUpdatePositionAfterServerUpdate``是做服务端矫正后的重播，通过``MoveAutonomous``在服务端提供的起点，重新快速的走一遍SavedMove的数据，对client来说是一瞬间的事
GetPredictionData_Client_Character 是什么？



