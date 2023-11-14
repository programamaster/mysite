# Resources资源动态加载 

## Resources资源动态加载的作用  
1. 通过代码动态加载Resources文件夹下指定路径资源
2. 避免繁琐的拖曳操作

## 资源同步加载 普通方法
不指定类型
``` C# 
// 第一步：要去加载预设体的资源文件(本质上 就是加载 配置数据 在内存中)
Object obj = Resources.Load("Cube");
// 第二步：如果想要在场景上 创建预设体 一定是加载配置文件过后 然后实例化
Instantiate(obj);
```
指定类型 
``` C# 
TextAsset ta = Resources.Load("Tex/TestJPG", typeof(TextAsset)) as TextAsset;
```
同名所有资源
``` C#  
Object[] objs = Resources.LoadAll("Tex/TestJPG");
foreach (Object item in objs)
{
    if (item is Texture)
    { 

    }
    else if(item is TextAsset)
    {

    }
}
```
## 资源同步加载 泛型方法
``` C# 
TextAsset ta2 = Resources.Load<TextAsset>("Tex/TestJPG");
```
Tip：多次Resources.Load()方法加载同一个资源文件，只会加载一次(Unity会有一个缓存区，会把这个内容存到缓存区中)，不会重复加载,所以内存的空间不会被浪费，但是会浪费性能（加载时会去寻找缓存区中有无此资源）

## 资源异步加载 
， 