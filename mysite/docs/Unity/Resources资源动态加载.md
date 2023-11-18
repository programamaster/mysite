# Resources资源动态加载 

## Resources资源动态加载的作用  
1. 通过代码动态加载Resources文件夹下指定路径资源
2. 避免繁琐的拖曳操作

## 资源同步加载

### 普通方法  
#### 不指定类型
``` C# 
// 第一步：要去加载预设体的资源文件(本质上 就是加载 配置数据 在内存中)
Object obj = Resources.Load("Cube");
// 第二步：如果想要在场景上 创建预设体 一定是加载配置文件过后 然后实例化
Instantiate(obj);
```
#### 指定类型 
``` C# 
TextAsset ta = Resources.Load("Tex/TestJPG", typeof(TextAsset)) as TextAsset;
```
#### 同名所有资源
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
### 泛型方法
``` C# 
TextAsset ta2 = Resources.Load<TextAsset>("Tex/TestJPG");
```
Tip：多次Resources.Load()方法加载同一个资源文件，只会加载一次(Unity会有一个缓存区，会把这个内容存到缓存区中)，不会重复加载,所以内存的空间不会被浪费，但是会浪费性能（加载时会去寻找缓存区中有无此资源）

## 资源异步加载 
### 异步加载是什么？ 
加载过大的资源可能会造成程序卡顿，卡顿的原因就是从硬盘上把数据读取到内存中需要进行计算，越大的资源耗时越长，所以Unity提供了异步加载资源的方法，内部新开一个线程进行资源加载，从而使主线程不会卡顿。
### 异步加载资源的方法
Tip：异步加载 不能马上得到加载的资源 至少要等一帧
#### 通过事件监听异步加载
``` C# 
//这句代码 你可以理解 Unity 在内部 就会去开一个线程进行资源下载
ResourceRequest rq = Resources.LoadAsync<Texture>("Tex/TestJPG");
//马上进行一个 资源下载结束 的一个事件函数监听
rq.completed += LoadOver;
print(Time.frameCount);

private void LoadOver(AsyncOperation rq)
{
    print("加载结束");
    //asset 是资源对象 加载完毕过后 就能够得到它
    tex = (rq as ResourceRequest).asset as Texture;
    print(Time.frameCount);
}


private void OnGUI()
{
    //当tex等于null的时候说明资源还没有加载完毕
    if( tex != null)
        GUI.DrawTexture(new Rect(0, 0, 100, 100), tex);
}
```
Tip：print(Time.frameCount); 这句话的作用是打印当前帧数，因为异步加载资源的时候，资源加载的完成事件是在加载完成后才会执行，所以打印当前帧数会发现不在同一帧。
#### 通过协程异步加载
``` C# 
StartCoroutine(Load());
IEnumerator Load()
{
    //迭代器函数 当遇到yield return时  就会 停止执行之后的代码
    //然后 协程协调器 通过得到 返回的值 去判断 下一次执行后面的步骤 将会是何时
    ResourceRequest rq = Resources.LoadAsync<Texture>("Tex/TestJPG");
    print(Time.frameCount);
    //Unity 自己知道 该返回值 意味着你在异步加载资源 
    yield return rq;
    //Unity 会自己判断 该资源是否加载完毕了 加载完毕过后 才会继续执行后面的代码
    tex = rq.asset as Texture;
    print(Time.frameCount);
}


IEnumerator Load2()
{
    ResourceRequest rq = Resources.LoadAsync<Texture>("Tex/TestJPG");
    print(Time.frameCount);
    //判断资源是否加载结束
    while(!rq.isDone)
    {
        //打印当前的 加载进度 
        //该进度 不会特别准确 过渡也不是特别明显
        print(rq.progress);
        yield return null;
    }
    yield return rq;
    tex = rq.asset as Texture;
    print(Time.frameCount);
}
```
### 总结
事件监听异步加载：写法简单，但只能在资源加载结束后进行处理，一般用于单一资源加载。  

协程异步加载：写法稍微麻烦，并且可以处理没有加载完成的逻辑，还可以多个资源配合加载且同时处理，一般用于进度条更新。