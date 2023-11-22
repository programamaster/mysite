# Input鼠标键盘输入
输入相关内容肯定是写在Update中的
## 鼠标输入
### 鼠标在屏幕位置
屏幕坐标的原点是在屏幕的左下角，往右是X轴正方向，往上时Y轴正方向，返回值时Vector3，但是只有x和y有值z一直是0，是因为屏幕本来就是2D的不存在Z轴。
``` C#
print(Input.mousePosition);
```
### 检测鼠标输入
鼠标按下相关检测能够实现的功能：  
1. 可以做发射子弹  
2. 可以控制摄像机转动  
> 0左键 1右键 2中键
```
//只要按下的这一瞬间 进入一次
if( Input.GetMouseButtonDown(0) )
{
    print("鼠标左键按下了");
}
```

## 小练习
``` C#
//ws键 控制位移
// 这公式 是 ： 前进方向 * 速度 * 时间 * 当前是否移动（-1~1 相当于 正向还是反向的感觉 不按就不动 0）
this.transform.Translate(Vector3.forward * moveSpeed * Time.deltaTime * Input.GetAxis("Vertical"));

//ad键 控制 左右转向
// 这公式 是 ： 转动的轴 * 速度 * 时间 * 当前是否移动（-1~1 相当于 正向还是反向的感觉 不按就不动 0）
this.transform.Rotate(Vector3.up * rotateSpeed * Time.deltaTime * Input.GetAxis("Horizontal"));

//鼠标左右移动控制炮台的转向
// 这公式 是 ： 转动的轴 * 速度 * 时间 * 当前是否移动（-1~1 相当于 正向还是反向的感觉 不按就不动 0）
head.Rotate(Vector3.up * headRotateSpeed * Time.deltaTime * Input.GetAxis("Mouse X"));
```