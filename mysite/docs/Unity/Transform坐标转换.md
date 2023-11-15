## 世界坐标转本地坐标
世界坐标系转本地坐标系,可以帮助我们大概判断一个相对位置,例如一个物体大概在玩家的什么位置 

世界坐标系的点转换为相对本地坐标系的点(受到缩放影响)
``` C#
print("转换后的点 " + this.transform.InverseTransformPoint(Vector3.forward));
```
世界坐标系的方向转换为相对本地坐标系的方向  
不受缩放影响
``` C#
print("转换后的方向" + this.transform.InverseTransformDirection(Vector3.forward));
```
受缩放影响
``` C#
print("转换后的方向" + this.transform.InverseTransformVector(Vector3.forward));
```
## 本地坐标转世界坐标
例如,现在玩家要在自己面前的n个单位前放一团火，我不用关心世界坐标系，通过相对于本地坐标系的位置，转换为世界坐标系的点，进行特效的创建或者攻击范围的判断。 

世界坐标系的方向转换为相对本地坐标系的方向(受到缩放影响)
```  c#
print("本地转世界点" + this.transform.TransformPoint(Vector3.forward));
```
本地坐标系的方向转换为相对世界坐标系的方向  
不受缩放影响
``` C#
print("本地转世界方向" + this.transform.TransformDirection(Vector3.forward));
```
受缩放影响
``` C#
print("本地转世界方向" + this.transform.TransformVector(Vector3.forward));
```