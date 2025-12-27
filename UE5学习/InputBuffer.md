# InputBuffer

### 流程

1. GA 激活 蒙太奇
2. AnimNotifyState 开始通知，根据BufferInfo创建InputBufferClass
3. 将InputBuffer对象 加入到 输入缓冲组件中
4. 在输入事件中(IA)，调用输入缓冲组件的``ProcessInputAction``处理输入事件
5. 再在输入缓冲组件的``ProcessInputAction``中，从``ActiveInputbuffers``，找到对应IA的inputBuffer，调用其``ProcessInputAction``进行处理(易混淆)
6. 在BaseInputBuffer中，``ProcessInputAction``函数进行了，设置main Buffered Input和注册BufferInput，应该一个是按优先级一个是按顺序触发


### BUG

问题描述
1. 需要在蒙太奇被中断的事件中，将InputBuffer清空，这些处理原本是在NotifyEnd处理，但是需要兼容被中断的情况
2. 蒙太奇被中断的事件，目前被分配在OnMontageEnd事件中，通过bInterupted区分
3. 但是这个蒙太奇结束事件，并不是在被中断的一瞬间，就执行，而是会在整个蒙太奇播放完(推测)，进行执行，所以在切换到下一个Montage的时候，会在NotifyBegin的之后，再次触发MontageEnd事件
4. MontageEnd事件会清理在NotifyBegin中产生的InputBuffer对象，所以会导致输入缓冲的bug
