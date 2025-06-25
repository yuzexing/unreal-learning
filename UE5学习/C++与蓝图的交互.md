# C++与蓝图的交互


### C++函数蓝图可调用

通过宏：
UFUNCTION(BlueprintCallable)

调用者：C++，蓝图

### C++函数定义/调用,由蓝图实现


通过宏：
UFUNCTION(BlueprintImplementableEvent)

.h文件中声明，蓝图中定义，不在.cpp文件实现

调用者：C++

### C++函数与蓝图共同实现

通过宏：
UFUNCTION(BlueprintNativeEvent)
void func();

不用定义为虚函数
C++的覆盖方式func_Implementation
C++调用方式obj->Execute_func(obj, param);
先执行蓝图，蓝图通过call parent 调用C++对应函数
若无蓝图实现，则调用对应C++函数

调用者：C++

### C++关联蓝图中定义的组件

通过：BindWidget、BindWidgetOptional。
关键：名称需相同
场景：C++访问Widget Blueprint中的组件

代码实例：
```
UCLASS()
class MYGAME_API UMyUserWidget : public UUserWidget
{
    GENERATED_BODY()

protected:
    virtual void NativeConstruct() override;

    UPROPERTY(meta=(BindWidget))
    UProgressBar* HealthBar;
};
```



