# Niagara优化

### Stat Commands

1. Stat NiagaraSystems 查看每一个Niagara system的消耗
2. Stat Niagara 查看总消耗，包含线程消耗，system路径
3. Stat NiagaraEmitters，查看每一个发射器的消耗

其中：system是主要组成部分，是一个niagarasystem的head?包含若干Emitter
1. System包含了User Parameters
2. wide scalablity
3. 是与游戏的接口

Emitter是由若干个模块组成的，包括粒子生成，粒子更新，渲染，模拟。
Emitter可以被继承，可以作为模板。
Emitter只能作为System的一部分存在，通过system与game world 交互

### 术语解释


1. CNC = Game Thread Concurrent：受粒子数量，发射器数量、模拟复杂度的影响
2. GT：Game Thread 受粒子数量，粒子复杂度，niagara数量影响
3. RT：RenderThread 渲染，
<img width="755" height="580" alt="image" src="https://github.com/user-attachments/assets/f19352fb-4ad0-4ed0-8862-36c489a8ccde" />

### NiagaraDebugger

### Unreal Insight与Niagara

开启statNamedEvents 才能查看到niagara的消耗

### niagara场景和对应优化方法

<img width="1625" height="1017" alt="image" src="https://github.com/user-attachments/assets/7fef6207-1af0-4f7c-8a8c-a2c95486ad93" />

