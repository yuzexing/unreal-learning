# InputBuffer

### 流程

1. GA 激活 蒙太奇
2. AnimNotifyState 开始通知，根据BufferInfo创建InputBufferClass
3. 将InputBuffer对象 加入到 输入缓冲组件中
4. 在输入事件中(IA)，调用输入缓冲组件的``ProcessInputAction``处理输入事件
5. 再在输入缓冲组件的``ProcessInputAction``中，从``ActiveInputbuffers``，找到对应IA的inputBuffer，调用其``ProcessInputAction``进行处理(易混淆)
6. 在BaseInputBuffer中，``ProcessInputAction``函数进行了，设置main Buffered Input和注册BufferInput，应该一个是按优先级一个是按顺序触发
