# Unity协程
官方文档：[协程](https://docs.unity.cn/cn/2021.1/Manual/Coroutines.html)  
官方技术专栏：[深入理解协程](https://developer.unity.cn/projects/61c80ba3edbc2a244ffbcf25)  
CSDN：[Unity 协程(Coroutine)原理与用法详解](https://blog.csdn.net/xinzhilinger/article/details/116240688)
## 什么是协程
协同程序简称协程，它是“假”的多线程，它不是多线程。  
主要作用： 将代码分时执行，不卡主线程，简单理解，是把可能会让主线程卡顿的耗时的逻辑分时分步执行。  
主要使用场景：异步加载文件，异步下载文件，场景异步加载，批量创建时防止卡顿。  
tip：新开一个线程是独立的一个管道，和主线程并行执行，新开一个协程是在原线程之上开启，进行逻辑分时分步执行。
## 协程的使用
继承MonoBehavior的类都可以开启协程函数  
### 第一步：申明协程函数  
协程函数2个关键点   
1. 返回值为IEnumerator类型及其子类  
2. 函数中通过 yield return 返回值; 进行返回  

### 第二步：开启协程函数
``` c#
Coroutine c1 = StartCoroutine( MyCoroutine(1, "123") );
```
### 第三步：关闭协程
``` c#
//关闭所有协程
StopAllCoroutines();

//关闭指定协程
StopCoroutine(c1);
```
## yield return 不同内容的含义（Unity定的规则）
``` c#
yield return null; // 下一帧再执行后续代码
yield return 0; //下一帧再执行后续代码
yield return 6;//(任意数字) 下一帧再执行后续代码
yield break; //直接结束该协程的后续操作
yield return asyncOperation;//等异步操作结束后再执行后续代码
yield return StartCoroution(/*某个协程*/);//等待某个协程执行完毕后再执行后续代码
yield return WWW();//等待WWW操作完成后再执行后续代码
yield return new WaitForEndOfFrame();//等待帧结束,等待直到所有的摄像机和GUI被渲染完成后，在该帧显示在屏幕之前执行
yield return new WaitForSeconds(0.3f);//等待0.3秒，一段指定的时间延迟之后继续执行，在所有的Update函数完成调用的那一帧之后（这里的时间会受到Time.timeScale的影响）;
yield return new WaitForSecondsRealtime(0.3f);//等待0.3秒，一段指定的时间延迟之后继续执行，在所有的Update函数完成调用的那一帧之后（这里的时间不受到Time.timeScale的影响）;
yield return WaitForFixedUpdate();//等待下一次FixedUpdate开始时再执行后续代码
yield return new WaitUntil()//将协同执行直到 当输入的参数（或者委托）为true的时候....如:yield return new WaitUntil(() => frame >= 10);
yield return new WaitWhile()//将协同执行直到 当输入的参数（或者委托）为false的时候.... 如:yield return new WaitWhile(() => frame < 10);
```
## 协程受对象和组件失活销毁的影响
协程开启后,脚本组件和物体销毁，协程不执行。  
物体失活协程不执行，脚本组件失活协程执行。

> !!! 小结
>  1. Unity支持多线程，只是新开线程无法访问主线程中Unity相关内容，一般主要用于进行复杂逻辑运算或者网络消息接收等等。（注意：Unity中的多线程一定记住关闭）
> 2. 协同程序不是多线程，它是将线程中逻辑进行分时执行，避免卡顿
> 3. 继承MonoBehavior的类都可以使用协程
> 4. 开启协程方法、关闭协程方法
> 5. yield return 返回的内容对于我们的意义
> 6. 协程只有当组件单独失活时不受影响，其它情况协程会停止

## 协程的本质
协程可以分成两部分  
1. 协程函数本体：协程本体就是一个能够中间暂停返回的函数
2. 协程调度器：协程调度器是Unity内部实现的，会在对应的时机帮助我们继续执行协程函数

Tip：Unity只实现了协程调度部分，协程的本体本质上就是一个 C#的迭代器方法

## 协程本体是迭代器方法的体现
### 协程函数本体
如果我们不通过开启协程方法执行协程Unity的协程调度器是不会帮助我们管理协程函数的，但是我们可以自己执行迭代器函数内容。
``` C#
IEnumerator Test()
{
    print("第一次执行");
    yield return 1;
    print("第二次执行");
    yield return 2;
    print("第三次执行");
    yield return "123";
    print("第四次执行");
    yield return new TestClass(10);
}

//MoveNext返回值代表着是否到了结尾（这个迭代器函数 是否执行完毕）
while(ie.MoveNext())
{
    print(ie.Current);
}
```  
### 协程调度器
继承MonoBehavior后开启协程，相当于是把一个协程函数（迭代器）放入Unity的协程调度器中帮助我们管理进行执行，具体的yield return后面的规则，也就是Unity定义的一些规则。  
简化理解 **迭代器函数**，C#看到迭代器函数和yield return语法糖，就会把原本是一个的函数 变成"几部分"，我们可以通过迭代器从上到下遍历这"几部分"进行执行，就达到了将一个函数中的逻辑分时执行的目的。  
 **协程调度器** 就是利用迭代器函数返回的内容来进行之后的处理，比如Unity中的协程调度器，根据yield return 返回的内容决定了下一次在何时继续执行迭代器函数中的"下一部分"，理论上来说，我们可以利用迭代器函数的特点自己实现协程调度器来取代Unity自带的调度器。

> !!! 小结
> 协程的本质：C#的迭代器函数"分步执行"的特点 + 协程调度逻辑实现的一套分时执行函数的规则。

## 自己简单实现Unity的协程调度器
(每隔1s执行迭代器函数中的一部分逻辑)
``` C# linenums="1"
public class YieldReturnTime
{
    //记录迭代器
    public IEnumerator ie;
    //记录下次执行的时间点
    public float time;
}

public class CoroutineMgr : MonoBehaviour
{
    private static CoroutineMgr instance;
    public static CoroutineMgr Instance => instance;

    //申明存储迭代器函数对象的容器
    private List<YieldReturnTime> list = new List<YieldReturnTime>();

    void Awake()
    {
        instance = this;
    }

    public void MyStartCoroutine(IEnumerator ie)
    {
        //来进行 分步走 分时间执行的逻辑
        //传入一个 迭代器函数返回的结构 那么应该一来就执行它
        //一来就先执行第一步 执行完了 如果返现 返回的true 证明 后面还有步骤
        if(ie.MoveNext())
        {
            //判断 如果yield return返回的是 数字 是一个int类型 那就证明 是需要等待n秒继续执行
            if(ie.Current is int)
            {
                //按思路 应该把 这个迭代器函数 和它下一次执行的时间点 记录下来
                //然后不停检测 时间 是否到达了 下一次执行的 时间点 然后就继续执行它
                YieldReturnTime y = new YieldReturnTime();
                //记录迭代器接口
                y.ie = ie;
                //记录时间
                y.time = Time.time + (int)ie.Current;
                //把记录的信息 记录到数据容器当中 因为可能有多个协程函数 开启 所以 用一个 list来存储
                list.Add(y);
            }
        }
    }

    void Update()
    {
        //为了避免在循环的时候 从列表里面移除内容 我们可以倒着遍历
        for (int i = list.Count - 1; i >= 0; i--)
        {
            //判断 当前该迭代器函数 是否到了下一次要执行的时间
            //如果到了 就需要执行下一步了
            if( list[i].time <= Time.time  )
            {
                if(list[i].ie.MoveNext())
                {
                    //如果是true 那还需要对该迭代器函数 进行处理
                    //如果是 int类型 证明是按秒等待
                    if(list[i].ie.Current is int)
                    {
                        list[i].time = Time.time + (int)list[i].ie.Current;
                    }
                    else
                    {
                        //该list 只是存储 处理时间相关 等待逻辑的 迭代器函数的 
                        //如果是别的类型 就不应该 存在这个list中 应该根据类型把它放入别的容器中
                        list.RemoveAt(i);
                    }
                }
                else
                {
                    //后面已经没有可以等待和执行的了 证明已经执行完毕了逻辑
                    list.RemoveAt(i);
                }
            }
        }
    }
}
```
调用自定义协程
``` C#
void Start()
{
    CoroutineMgr.Instance.MyStartCoroutine(MyTest());
}

IEnumerator MyTest()
{
    print("1");
    yield return 1;//如果是用自带的 开启 那么数字代表等待1帧继续执行之后的内容
    print("2");
    yield return 2;
    print("3");
    yield return 3;
    print("4");
}
```