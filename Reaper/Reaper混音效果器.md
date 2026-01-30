# Reaper混音效果器

### 平衡 EQ

用于对特定频率的声音进行调整(增加或减少分贝(dB))

类型：Low Shelf，Hight Shelf，Band，Low Pass，Hight Pass等

Low Shelf：对低于指定频率的部分调整
Hight Shelf：对高于指定频率的部分调整
Band：对于指定频率的指定宽度进行调整
Low Pass：低通滤波器
Hight Pass：高通滤波器

### 压缩 Comp

将高于阈值的声音进行压缩，可以用于将低音和高音统一大小

Attack：声音到达阈值后，进入压缩的时间
Release：声音低于阈值后，推出压缩的时间
Ratio：重要参数，将超出阈值的声音，按多少的比例进行压缩，例如：4:1->超出4dB则压缩至1dB。1:1则压缩器不工作。
Auto-Makeup：对**整体**的声音进行补偿，让压缩后的声音听起来和压缩前一样响，但动态更平稳


### 删除噪音 ReaFir

对于捕捉噪音部分的特征，使用Subtract，将噪音特征进行删除

属于"破坏性降噪"

### 调整音高 PitchShift

Semintone: 半音
Cent: 分，等于1/100个半音
Octave: 八度；一个八度等于12个半音；两个音之间差八度，也就是频率翻倍，和谐音，差

魔鬼音：两个音相差6个半音是魔鬼音，听起来不和谐；12个半音是八度，八度的一半是魔鬼音

Shift FullRange可以设定浮点数，不同于Shift Semitones，只能是整的8度
Shift cents：用于细粒度的调整
Shift FullRange：用于粗粒度的调整


Formant Shift：保证音调的情况下，调整共振峰，例如调整声道的大小，
1. 越大则模拟更短的声道，更偏向小孩或女性，声音偏细，偏亮。
2. 越小则模拟更长的声道，例如巨人或男人，声音更低沉，厚重



### 空间混响 Verbate

Wet：混响后的声音
Dry：原声
Dampening：阻尼，阻尼越小代表表面越光滑，回响越明显；进阶：阻尼主要吃掉的是高频，低阻尼，例如浴室，有高频回声；高阻尼，比如有窗帘，高频被吸收
Room Size：房屋大小，房屋越大，声音越空旷
Stero Width：如果使用Mino输出，则最好设置为0；如果是立体音，可以使用该参数增加空间感。
Initial Delay：回响启动的延迟时间，用于保证原声先出来，

### 限制器 ReaLimit

防止爆音，一般加载Master track上，降低即将到来的大音量







