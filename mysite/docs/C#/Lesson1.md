# 特殊文件夹
## Resources 资源文件夹
路径获取：  一般不获取，只能使用Resources相关API进行加载，如果硬要获取可以用工程路径拼接  
``` C# linenums="1"
print(Application.dataPath + "/Resources");
```
注意：此文件夹需要我们自己将创建  
作用： 

- 需要通过Resources相关API动态加载的资源需要放在其中
- 该文件夹下所有文件都会被打包出去
- 打包时Unity会对其压缩加密 :smiling_face_with_3_hearts:
- 该文件夹打包后只读 只能通过Resources相关API加载

## StreamingAssets 流动资源文件夹
路径获取：
``` C#
print(Application.streamingAssetsPath);
```
注意：此文件需要我们自己将创建 :smile::smiling_face_with_3_hearts:
作用：

- 打包出去不会被压缩加密，可以任由我们摆布
- 移动平台只读，PC平台可读可写
- 可以放入一些需要自定义动态加载的初始资源

n你好漂亮 霞鹜文楷