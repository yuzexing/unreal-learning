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



