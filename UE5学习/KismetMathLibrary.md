# KismetMathLibrary

### MakeRotFromX(Vector) : FRotator

返回一个欧拉角，表示从X轴旋转到目标向量 需要经过的欧拉角

### NormalizedDeltaRotator(R1, R2) : FRotator

1. 计算两个欧拉角之间的差值
2. 对结果欧拉角标准化（固定到[-180, 180]之间，也就是说获取最小的旋转角度）
3. 返回结果
