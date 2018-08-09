# APP启动过程

![avatar](https://mmbiz.qpic.cn/mmbiz_png/foPACGrddJ0cHJzFKTLj6Try0K3vqtFQOcR1RfWGcDoTN2wLGBXkJSynty02w1sD6FCy2nHAQiaGMibAiaNDAFpwQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

![avatar](https://mmbiz.qpic.cn/mmbiz_png/foPACGrddJ0cHJzFKTLj6Try0K3vqtFQuDsfLplFoqNIYicsiaJ2Sv520DxYZpjLbqIF2z9m09oGFnKjzWQu4msg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

## pre-main阶段

####  耗时的影响因素：

1. 动态库加载越多，启动越慢。
2. ObjC类越多，方法和属性越多，启动越慢。
3. 可执行文件越大启动越慢。
4. C的constructor函数越多，启动越慢。
5. C++静态对象越多，启动越慢。
6. ObjC的+load越多，启动越慢。

#### 优化

* 减少依赖不必要的库，不管是动态库还是静态库；如果可以的话，把动态库改造成静态库；如果必须依赖动态库，则把多个非系统的动态库合并成一个动态库；
* 检查下 framework应当设为optional和required，如果该framework在当前App支持的所有iOS系统版本都存在，那么就设为required，否则就设为optional，因为optional会有些额外的检查； 
* 合并或者删减一些OC类和函数；关于清理项目中没用到的类，使用工具AppCode代码检查功能，查到当前项目中没有用到的类（也可以用根据linkmap文件来分析，但是准确度不算很高）；有一个叫做[FUI](https://github.com/dblock/fui)的开源项目能很好的分析出不再使用的类，准确率非常高，唯一的问题是它处理不了动态库和静态库里提供的类，也处理不了C++的类模板。
* 删减一些无用的静态变量，
* 删减没有被调用到或者已经废弃的方法，方法见http://stackoverflow.com/questions/35233564/how-to-find-unused-code-in-xcode-7和https://developer.Apple.com/library/ios/documentation/ToolsLanguages/Conceptual/Xcode_Overview/CheckingCodeCoverage.html
* 将不必须在+load方法中做的事情延迟到+initialize中，尽量不要用C++虚函数(创建虚函数表有开销)
* 类和方法名不要太长：iOS每个类和方法名都在__cstring段里都存了相应的字符串值，所以类和方法名的长短也是对可执行文件大小是有影响的；因还是object-c的动态特性，因为需要通过类/方法名反射找到这个类/方法进行调用，object-c对象模型会把类/方法名字符串都保存下来；
* 用dispatch_once()代替所有的 attribute((constructor)) 函数、C++静态对象初始化、ObjC的+load函数；
* 在设计师可接受的范围内压缩图片的大小，会有意外收获。压缩图片为什么能加快启动速度呢？因为启动的时候大大小小的图片加载个十来二十个是很正常的，图片小了，IO操作量就小了，启动当然就会快了，比较靠谱的压缩算法是TinyPNG。

## main阶段

1. 减少启动初始化的流程，能懒加载的就懒加载，能放后台初始化的就放后台，能够延时初始化的就延时，不要卡主线程的启动时间，已经下线的业务直接删掉； 
2. 优化代码逻辑，去除一些非必要的逻辑和代码，减少每个流程所消耗的时间； 
3. 启动阶段使用多线程来进行初始化，把CPU的性能尽量发挥出来； 
4. 使用纯代码而不是xib或者storyboard来进行UI框架的搭建，尤其是主UI框架比如TabBarController这种，尽量避免使用xib和storyboard，因为xib和storyboard也还是要解析成代码来渲染页面，多了一些步骤； 



注意：

* 并不是什么任务都适合放子线程，有些任务在主线程大概10ms，放到子线程需要几百ms，因为某些任务内部可能会用到UIKit的api，又或者某些操作是需要提交到主线程去执行的，关键是要搞清楚这个任务里边究竟做了啥，有些SDK并不见得适合放到子线程去初始化，需要具体情况具体去测试和分析。