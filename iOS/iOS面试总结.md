1、为什么说Objective-C是一门动态的语言？

Objective-C 可以通过Runtime 这个运行时机制，在运行时动态的添加变量、方法、类等

2、设计模式MVC、MVVM、MVP

3、为什么代理要用weak？代理的delegate和dataSource有什么区别？block和代理的区别?

* 通常一个对象的代理是会直接持有该对象的，用weak可以避免循环引用



* dataSource是数据源,里面的东西都跟内容有关，协议里面的主要是cell的构造函数，各种属性。这里面可以设置cell的属性与赋值。一般delegate与dataSource伴生，这时数据源处理的数据就是Delegate中发送委托的类中的数据，并通过Datasource发送给接受委托的类。
* delagate是委托，在OC中则是一个类委托另一个类实现某个方法。当一个对象接受到某个事件或者通知的时候， 会向它的Delegate对象查询它是否能够响应这个事件或者通知，如果可以这个对象就会给它的Delegate对象发送一个消息
* delegate 数据由对象流向代理delegate控制的是UI，是上层的东西；而datasource控制的是数据。他们本质都是回调，只是回调的对象不同





4、属性的实质是什么？包括哪几个部分？属性默认的关键字都有哪些？@dynamic关键字和@synthesize关键字是用来做什么的？

实质：实例变量+存取方法

组成部分：原子性+读写权限+内存管理语义+方法名

默认关键字：atomic、strong、readwrite

@dynamic：告诉编译器不要自动创建实现属性所需的实例变量和存取方法，运行期可以找到

@synthesize：指定实例变量的名字

在对象内部尽量直接访问实例变量，初始化方法中应该总是直接访问实例变量

因为，子类有可能会覆写set方法，在特殊的场景下抛异常，而基类的默认初始化方法可能会设置成比较通用的值，这是如果用set方法来设值就会调用子类的设置方法而抛出异常

5、属性的默认关键字是什么？

atomic、strong、readwrite

6、NSString为什么要用copy关键字，如果用strong会有什么问题？（注意：这里没有说用strong就一定不行。使用copy和strong是看情况而定的）

* 因为父类指针可以指向子类对象,使用 copy 的目的是为了让本对象的属性不受外界影响,使用 copy 无论给我传入是一个可变对象还是不可对象,我本身持有的就是一个不可变的副本.
* 如果我们使用是 strong ,那么这个属性就有可能指向一个可变对象,如果这个可变对象在外部被修改了,那么会影响该属性.

copy 此特质所表达的所属关系与 strong 类似。然而设置方法并不保留新值，而是将其“拷贝” (copy)。 当属性类型为 NSString 时，经常用此特质来**保护其封装性**，因为传递给设置方法的新值有可能指向一个 NSMutableString 类的实例。这个类是 NSString 的子类，表示一种可修改其值的字符串，此时若是不拷贝字符串，那么设置完属性之后，字符串的值就可能会在对象不知情的情况下遭人更改。所以，这时就要拷贝一份“不可变” (immutable)的字符串，确保对象中的字符串值不会无意间变动。只要实现属性所用的对象是“可变的” (mutable)，就应该在设置新属性值时拷贝一份。

7、如何令自己所写的对象具有拷贝功能?

（1）遵守NSMutableCopying 协议，实现 mutableCopyWithZone: 方法

（2）归档：归档解档过程中，会在内存产生临时副本

8、深复制、浅复制可变集合类 和 不可变集合类的 copy 和 mutablecopy有什么区别？如果是集合是内容复制的话，集合里面的元素也是内容复制么？

copy返回不可变对象，mutablecopy返回可变对象

非集合类对象：不可变 copy 浅复制，其他深复制；

集合类对象：不可变对象copy浅复制  其他单层深复制

注意：不是所有的对象都支持copy、mutablecopy，遵守NSCopying 协议的类可以发送copy消息，遵守NSMutableCopying 协议的类才可以发送mutableCopy消息

9、为什么IBOutlet修饰的UIView也适用weak关键字？

当我们将控件拖到Storyboard上，相当于新创建了一个对象，而这个对象是加到视图控制器的view上，view有一个**subViews**属性，这个属性是一个数组，里面是这个view的所有子view，而我们加的控件就位于这个数组中，那么说明，实际上我们的控件对象是属于**view**的，也就是说view对加到它上面的控件是强引用。当我们使用Outlet属性的时候，我们是在viewController里面使用，而这个Outlet属性是有view来进行强引用的，我们在viewController里面**仅仅是对其使用，并没有必要拥有它，所以是weak的**。

如果将weak改为strong，也是没有问题的，并不会造成强引用循环。当viewController的指针指向其他对象或者为nil，这个viewController销毁，那么对控件就少了一个强引用指针。然后它的view也随之销毁，那么subViews也不存在了，那么控件就又少了一个强引用指针，如果没有其他强引用，那么这个控件也会随之销毁。

* 如果IBOutlet对象是nib/storyboard scene的拥有者（File’s owner）所直接持有的对象，那么很显然拥有者必须`直接拥有`对象的指针，因此属性应设置为strong。而其他的IBOutlet对象的属性需要设置为weak，因为拥有者并不需要`直接拥有`它们的指针。举例来说，UIViewController的view属性是strong，因为controller要直接拥有view。而添加到view上的subviews，作为IBOutlet只需要设置为weak就可以了，因为他们不是controller直接拥有的。直接拥有subviews的是controller的view，ARC会帮助管理内存。
* 控制器需要直接控制某一个子视图并且将子视图添加到其他的view tree上去，此时需要strong。

10、nonatomic和atomic的区别？atomic是绝对的线程安全么？为什么？如果不是，那应该如何实现？

 * nonatomic：非原子性
 * atomic：原子性，相当于@synchronized （该属性）只是对该属性的读写的原子性，不是绝对的线程安全，比如对集合类对象的操作。atomic要比nonatomic慢大约20倍

实现线程安全：加锁  https://bestswifter.com/ios-lock/

性能由高到低：

* OSSpinLock：自旋锁，低优先级线程拿到锁时，高优先级线程进入忙等状态，消耗大量 CPU 时间，从而导致低优先级线程拿不到 CPU 时间，也就无法完成任务并释放锁。这种问题被称为优先级反转。适合临界区执行时间比较短情况
* dispatch_semaphore：锁的粒度更大，得不到锁时线程阻塞进入睡眠状态
* pthread_mutes：互斥锁，和信号量的实现原理类似，多种类型，可以支持递归锁等，因此在申请加锁时，需要对锁的类型加以判断，这也就是为什么它和信号量的实现类似，但效率略低的原因。
* NSLock：在内部封装了一个 `pthread_mutex`，属性为 `PTHREAD_MUTEX_ERRORCHECK`，它会损失一定性能换来错误提示。而且会经过OC的方法调用，有微弱的性能损失(方法缓存)
* NSCondition：通过条件变量(condition variable) `pthread_cond_t` 来实现的。条件变量有点像信号量，提供了线程阻塞与信号机制，因此可以用来阻塞某个线程，并等待某个数据就绪，随后唤醒线程，比如常见的生产者-消费者模式。在等待某个条件达成时自身要进行睡眠或阻塞，避免忙等待带来的不必要消耗。`NSCondition` 其实是封装了一个互斥锁和条件变量， 它把前者的 `lock` 方法和后者的 `wait/signal` 统一在 `NSCondition` 对象中，暴露给使用者。它的加解锁过程与 `NSLock` 几乎一致，理论上来说耗时也应该一样(实际测试也是如此)。在图中显示它耗时略长，我猜测有可能是测试者在每次加解锁的前后还附带了变量的初始化和销毁操作。
* pthread_mutes（recursive）
* NSRecursiveLock：内部封装的 `pthread_mutex_t` 对象，类型为 `PTHREAD_MUTEX_RECURSIVE`。
* NSConditionLock：`NSConditionLock` 借助 `NSCondition` 来实现，它的本质就是一个生产者-消费者模型。“条件被满足”可以理解为生产者提供了新的内容。`NSConditionLock` 的内部持有一个 `NSCondition` 对象，以及 `_condition_value` 属性，在初始化时就会对这个属性进行赋值
* @synchronized：后面需要紧跟一个 OC 对象，它实际上是把这个对象当做锁来使用。这是通过一个哈希表来实现的，OC 在底层使用了一个互斥锁的数组(你可以理解为锁池)，通过对对象去哈希值来得到对应的互斥锁

11、UICollectionView自定义layout如何实现？



12、用StoryBoard开发界面有什么弊端？如何避免？

* 多人合作冲突：合理地划分功能模块和每个开发者负责的部分，不同时修改同一个 View Controller 的内容，共用的部分，比如 IB 的版本，系统的版本等可以通过统一开发成员的环境来避免冲突。
* 属性设置复杂：不会随着程序状态改变的内容直接在SB设置，其他，字体颜色这样的设置使用自定义的子类，来统一设置字体或者颜色这些属性或者为目标 view 的类型添加像是 `style` 属性，然后使用 runtime attribute 来设置
* 难以继承和重用：

13、进程和线程的区别？同步异步的区别？并行和并发的区别？

进程

* 资源（CPU、内存等）分配的基本单位，有自己独立的地址空间，每启动一个进程，系统就会为它分配地址空间，建立数据表来维护代码段、堆栈段和数据段
* 多进程更健壮，一个进程挂掉不会影响其他的进程

线程

* 调度的基本单位，对应一个任务，更轻量，易创建和撤销，线程间难同步，同一进程内资源共享
* 多线程程序只要有一个线程挂掉，那么整个进程也会挂掉

同步

* 没有得到结果之前，调用不返回

异步

* 立即返回，通知或回调结果

并行

* 两个或者多个事件在同一时刻发生

并发

* 伪并行，两个或多个事件在同一时间间隔发生

14、线程通信？

- 1个线程传递数据给另1个线程
- 在1个线程中执行完特定任务后，转到另1个线程继续执行任务
- iOS线程间的通信，实际上是各种输入源，触发Runloop去处理对应的事件

（1）NSThread

```objective-c
// 回到主线程执行
[self performSelectorOnMainThread:@selector(showImage:) withObject:image waitUntilDone:YES];
// 回到XX线程执行
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait;
// waitUntilDone的含义:
// 如果传入的是YES: 那么会等到主线程中的方法执行完毕, 才会继续执行下面其他行的代码
// 如果传入的是NO: 那么不用等到主线程中的方法执行完毕, 就可以继续执行下面其他行的低吗
```

（2） GCD

（3）NSOperation

第一种方法：创建一个新的队列，在新的队列添加任务，任务中去其他队列

```objective-c

    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    [queue addOperationWithBlock:^{
        NSURL *url  = [NSURL URLWithString:@"http://imgcache.mysodao.com/img2/M04/8C/74/CgAPDk9dyjvS1AanAAJPpRypnFA573_700x0x1.JPG"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        UIImage *image = [UIImage imageWithData:data];

        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            self.imageView.image = image;
        }];
    }];
```

第二种方法：添加依赖

```objective-c
	NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 2.添加一个操作下载第一张图片
    __block UIImage *image1 = nil;
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSURL *url  = [NSURL URLWithString:@"http://imgcache.mysodao.com/img2/M04/8C/74/CgAPDk9dyjvS1AanAAJPpRypnFA573_700x0x1.JPG"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        image1 = [UIImage imageWithData:data];
    }];

    // 3.添加一个操作下载第二张图片
    __block UIImage *image2 = nil;
     NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSURL *url  = [NSURL URLWithString:@"http://imgcache.mysodao.com/img1/M02/EE/B5/CgAPDE-kEtqjE8CWAAg9m-Zz4qo025-22365300.JPG"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        image2 = [UIImage imageWithData:data];
    }];
    // 4.添加一个操作合成图片
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        UIGraphicsBeginImageContext(CGSizeMake(200, 200));
        [image1 drawInRect:CGRectMake(0, 0, 100, 200)];
        [image2 drawInRect:CGRectMake(100, 0, 100, 200)];
        UIImage *res = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();

        // 5.回到主线程更新UI
        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            self.imageView.image = res;
        }];
    }];

    // 6.添加依赖

    [op3 addDependency:op1];
    [op3 addDependency:op2];

    // 7.添加操作到队列中
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
```



15、GCD的一些常用的函数？（group，barrier，信号量，线程同步）

Serial Dispatch Queue：串行队列，使用一个线程，等待现在执行中的任务处理结束才会继续处理下一个任务

Concurrent Dispatch Queue：并行队列，使用多个线程，

* dispatch_queue_create:创建dispatch_queue_t，需要手动释放，默认优先级
* dispatch_set_target_queue：变更优先级
* dispatch_after：在指定时间后**追加**处理，有延迟
* dispatch_group_notify:保证一组任务全部执行结束后才会执行后面的处理
* dispatch_barrier_async：等之前的任务都处理结束才会追加该任务，等该任务处理结束之后才会执行后续操作
* dispatch_sync：同步派发，将指定block追加到指定队列中，等待该任务处理结束，才能返回，注意死锁
* dispatch_async：异步派发，将指定block追加到指定队列中，不等待该任务处理结束，直接返回
* dispatch_apply：按指定的次数将指定Block同步追加到指定队列
* dispatch_suspend/dispatch_resume:挂起/恢复指定队列，只会挂起尚未执行的任务
* dispatch_semaphore：信号量，同步机制
* dispatch_once：单例机制，注意死锁
* dispatch_io

16、如何使用队列来避免资源抢夺？



17、数据持久化的几个方案（fmdb用没用过）

* plist:通过XML文件的方式保存在Documents目录中，只能保存OC封装的数据类型(string,array,dictionary,data,number,date)
* nsuserdefault：偏好设置，key-value的方式存取
* NSKeyedArchiver：遵从NSCoding协议
* Keychain :保存在沙盒外，可以在应用之间共享数据，key-value的方式存取，增删改查

上述所有存储方法，都是覆盖存储。如果想要增加一条数据就必须把整个文件读出来，然后修改数据后再把整个内容覆盖写入文件。所以它们都不适合存储大量的内容。

https://www.jianshu.com/p/7616cbd72845

#### SQLite3



#### FMDB



#### CoreData



#### 沙盒

iOS程序默认情况下只能访问程序的目录,这个目录就是沙盒。 沙盒的目录结构如下：

```
"应用程序包"
Documents
Library
    Caches
    Preferences
tmp
```

* 应用程序包：存放的是应用程序的源文件：资源文件和可执行文件

  ```objective-c
  NSString *path = [[NSBundle mainBundle] bundlePath];
  ```

* `Documents:`比较常用的目录，itune同步会同步这个文件夹的内容，适合存储重要的数据

  ```objective-c
  NSString *path = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
  ```

* `Library/Caches`: **iTunes**不会同步此文件夹，适合存储体积大，不需要备份的非重要数据。

  ```objective-c
  NSString *path  = NSSearchPathForDirectoriesInDomains(NSCachesDirectory,NSUserDomainMask, YES).firstObject;
  ```

* `Library/Preferences`: **iTunes**同步该应用时会同步此文件夹中的内容，通常保存应用的设置信息。

* `tmp`itunes不会同步，系统可能在应用没运行时就删除该目录的文件，适合存临时文件，用完就删除：

  ```objective-c
  NSString *path  = NSTemporaryDirectory();
  ```

18、说一下AppDelegate的几个方法？从后台到前台调用了哪些方法？第一次启动调用了哪些方法？从前台到后台调用了哪些方法？

* 第一次启动:didFinish，didBecomeActive
* 从前台到后台：willResignActive，didEnterBackground
* 从后台到前台：willEnterForeground，didBecomeActive

```objective-c
//1.当程序第一次运行并且将要显示窗口的时候执行，在该方法中我们完成的操作
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
//2.程序进入后台的时候首先执行程序将要取消活跃该方法
- (void)applicationWillResignActive:(UIApplication *)application
//3.该方法当应用程序进入后台的时候调用
- (void)applicationDidEnterBackground:(UIApplication *)application
//4.当程序进入将要前台的时候调用 当应用在后台状态，将要进行动前台运行状态时，会调用此方法。 如果应用不在后台状态，而是直接启动，则不会回调此方法。
- (void)applicationWillEnterForeground:(UIApplication *)application
//5.应用程序已经变得活跃（应用程序的运行状态）
 - (void)applicationDidBecomeActive:(UIApplication *)application
//6.当程序将要退出的时候调用，如果应用程序支持后台运行，该方法被applicationDidEnterBackground:替换
- (void)applicationWillTerminate:(UIApplication *)application
```

19、NSCache优于NSDictionary的几点？

* 系统资源将要耗尽时，可以自动删减缓存，先行删减最久未使用的对象
* NSCache不会拷贝键，而是保留，在键不支持copy的情况下使用比较方便
* 线程安全
* 开发者可以操控删减内容的时机：指定对象总数和总开销

20、知不知道Designated Initializer？使用它的时候有什么需要注意的问题？

![avatar](https://images.cnblogs.com/cnblogs_com/smileEvday/806954/o_initializerDelegation01_2x.png)

* 指定初始化方法  ：通常接收最多的初始化参数。其他initializer则调用它即可,不需要重复写相关代码。这个模式保证了所有的初始化方法都正确地初始实例变量。
* 便利初始化方法

OC规范

* 子类如果有指定初始化函数，那么指定初始化函数实现时必须调用它的直接父类的指定初始化函数。
* 如果子类有指定初始化函数，那么便利初始化函数必须调用**自己**的其它初始化函数(包括指定初始化函数以及其他的便利初始化函数)，不能调用super的初始化函数
* 如果子类提供了指定初始化函数，那么一定要实现所有父类的指定初始化函数
* NSCoding
  * 如父类没有实现NSCoding协议，那么应该调用父类的指定初始化函数。
  * 如果父类实现了NSCoing协议，那么子类的 initWithCoder: 的实现中需要调用父类的initWithCoder:方
  * 实现NSCoding协议的时候，我们可以显示的声明 **initWithCoder:** 为指定初始化函数(一个类可以有多个指定初始化函数，比如UIViewController)即可完美解决问题，既满足了指定初始化函数的三个规则，又满足了NSCoding协议的三条原则。

Swift规范

* 指定初始化方法必须先初始化该类自己定义的存储属性，再使用super来调用父类的指定初始化器，跌倒过来编译器会报错。
* 便利初始化方法必须先调用其他的初始化方法之后，再赋类的存储属性值
* 便利初始化方法如果重载了父类的指定初始化方法，则必须使用override修饰符

21、实现description方法能取到什么效果？

* NSLog(@"%@", objectA);这会自动调用objectA的description方法来输出ObjectA的描述信息. 
* description方法默认返回对象的描述信息(默认实现是返回类名和对象的内存地址) 
* description方法是基类NSObject 所带的方法,因为其默认实现是返回类名和对象的内存地址, 这样的话,使用NSLog输出OC对象,意义就不是很大,因为我们并不关心对象的内存地址,比较关心的是对象内部的一些成变量的值。因此,会经常重写description方法,覆盖description方法的默认实现

22、objc使用什么机制管理对象内存？

* MRC(manual retain-release)手动内存管理
* ARC(automatic reference counting)自动引用计数，引用计数为0时对象就会被释放
* Garbage collection (垃圾回收)。但是iOS不支持垃圾回收

**中级**

1、block的实质是什么？一共有几种block？都是什么情况下生成的？

带有自动变量（局部变量）的匿名函数

实质：对象

![avatar](http://www.devtalking.com/postImages/block-struct.jpg)

- `isa`指针：指向表明该block类型的类。
- `flags`：按bit位表示一些block的附加信息，比如判断block类型、判断block引用计数、判断block是否需要执行辅助函数等。
- `reserved`：保留变量，我的理解是表示block内部的变量数。
- `invoke`：函数指针，指向具体的block实现的函数调用地址。
- `descriptor`：block的附加描述信息，比如保留变量数、block的大小、进行`copy`或`dispose`的辅助函数指针。
- `variables`：因为block有闭包性，所以可以访问block外部的局部变量。这些`variables`就是复制到结构体中的外部局部变量或变量的地址。



类型：

- `_NSConcreteGlobalBlock`类型的block要么是空block，要么是不访问任何外部变量的block。在内存的全局区。
- `_NSConcreteStackBlock`类型的block有闭包行为，也就是有访问外部变量，并且该block只且只有有一次执行，因为栈中的空间是可重复使用的，所以当栈中的block执行一次之后就被清除出栈了，所以无法多次使用。
- `_NSConcreteMallocBlock`类型的block有闭包行为，并且该block需要被多次执行。当需要多次执行时，就会把该block从栈中复制到堆中，供以多次执行。

2、为什么在默认情况下无法修改被block捕获的变量？ __block都做了什么？

默认情况下block对捕获的变量进行的是值拷贝

__block对捕获的变量进行了一个封装，指针拷贝

3、模拟一下循环引用的一个情况？block实现界面反向传值如何实现？

A持有Block，Block内部捕获了A

页面A推出页面B，页面B向A传值title

A向B传入一个Block，Block体是A对该值得处理，参数是需要传的值

B调用Block

4、objc在向一个对象发送消息时，发生了什么？

5、什么时候会报unrecognized selector错误？iOS有哪些机制来避免走到这一步？

消息接收者找不到对应的@selector()方法.

```objective-c
+ (BOOL)resolveInstanceMethod:(SEL)sel; / + (BOOL)resolveClassMethod:(SEL)sel;
- (id)forwardingTargetForSelector:(SEL)aSelector;
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector;
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```

6、能否向编译后得到的类中增加实例变量？能否向运行时创建的类中添加实例变量？为什么？

不能，能

- 编译后的类已经注册在runtime中,类结构体中的objc_ivar_list实例变量的链表和instance_size实例变量的内存大小已经确定,runtime会调用class_setvarlayout或class_setWeaklvarLayout来处理strong weak引用.所以不能向存在的类中添加实例变量
- 运行时创建的类是可以添加实例变量,调用class_addIvar函数.但是得在调用objc_allocateClassPair之后,objc_registerClassPair之前,原因同上

7、runtime如何实现weak变量的自动置nil？

runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指向的对象内存地址作为 key，当此对象的引用计数为0的时候会 dealloc，假如 weak 指向的对象内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

8、给类添加一个属性后，在类结构体里哪些元素会发生变化？

* `long instance_size`：实例的内存大小
* `objc_ivar_list *ivars`: 属性列表
* `objc_method_list **methodLists` ：属性的存取方法

9、isa指针？（对象的isa，类对象的isa，元类的isa都要说）

10、类方法和实例方法有什么区别？

* 类方法，也称静态方法，指的是用static关键字修饰的方法。此方法属类本身的方法，不属于类的某一个实例
* 类方法与实例无关。实例方法与实例相关

使用

* 如果需要访问或者修改某个实例的成员变量时，将该方法定义成实例方法。类方法正好相反，它不需要访问或者修改某个实例的成员变量
* 类方法一般用于实现一些工具方法，比如对某个对象进行扩展，或者实现单例
* 如果一个方法与他所在类型的实例无关，那么它就应该是静态的，决不会有人把它写成实例方法。所以所有的实例方法都与实例有关，既然与实例有关，那么创建实例就是必然的步骤，没有麻烦简单一说。实际上上你可以把所有的实例方法都写成静态的，将实例作为参数传入即可

11、介绍一下分类，能用分类做什么？内部是如何实现的？它为什么会覆盖掉原来的方法？

* 扩展基础类库的方法
* 多人协作
* 

12、运行时能增加成员变量么？能增加属性么？如果能，如何增加？如果不能，为什么？

能增加属性，不能增加成员变量（编译的时候已经确定）

增加属性：关联对象

13、objc中向一个nil对象发送消息将会发生什么？（返回值是对象，是标量，结构体）

什么都不会发生,返回值为 nil 缺省值， 缺省值

14、 苹果是如何实现autoreleasepool的？https://draveness.me/autoreleasepool

- 自动释放池是由 `AutoreleasePoolPage` 以双向链表的方式实现的
- 当对象调用 `autorelease` 方法时，会将对象加入 `AutoreleasePoolPage` 的栈中
- 调用 `AutoreleasePoolPage::pop` 方法会向栈中的对象发送 `release` 消息



**高级** 

1、UITableview的优化方法（缓存高度，异步绘制，减少层级，hide，避免离屏渲染）

* cell重用
* 缓存高度
* 在heightForRowAtIndexPath:中尽量不使用cellForRowAtIndexPath:，如果你需要用到它，只用一次然后缓存结果
* 尽量少用addView给Cell动态添加View，可以初始化时就添加，然后通过hide来控制是否显示
* 尽量少用或不用透明图层
* 复杂界面手写代码实现
* 图片资源不要缩放

2、有没有用过运行时，用它都能做什么？（交换方法，创建类，给新创建的类增加方法，改变isa指针）

3、看过哪些第三方框架的源码？都是如何实现的？（如果没有，问一下多图下载的设计）

4、SDWebImage的缓存策略？

5、AFN为什么添加一条常驻线程？

* AFN 的做法是把网络请求的发起和解析都放在同一个子线程中进行，但由于子线程默认不开启 runloop，它在运行完所有代码后退出线程。而网络请求是异步的，这会导致获取到请求数据时，线程已经退出，代理方法没有机会执行。因此，AFN 的做法是使用一个 runloop 来保证线程不死，能保证代理方法的执行。同时，避免每次请求都创建新的线程。

- 由于NSUrlSession参考了AF的2.x的优点，自己维护了一个线程池，做Request线程的调度与管理，所以在AF3.x中，没有了常驻线程，都是用的时候run,结束的时候stop。

6、KVO的使用？实现原理？（为什么要创建子类来实现）

*  isa-swizing，隐式创建子类，让实例对象的 isa 指针指向该子类。
* 重写该子类的 setKey: 方法实现在设置值之前和之后通知观察者的功能

7、KVC的使用？实现原理？（KVC拿到key以后，是如何赋值的？知不知道集合操作符，能不能访问私有属性，能不能直接访问_ivar）

KVC的定义都是对`NSObject`的扩展来实现的(NSKeyValueCoding扩展)

#### 设值

当调用`setValue：属性值 forKey：@”name“`的代码时，底层的执行机制如下：

* 程序优先调用`setName:属性值`方法，代码通过`setter`方法完成设置。
* 如果没有找到`setName：`方法,再调用`setIsName:属性值`方法
* 如果仍然没找到，KVC机制会检查`+ (BOOL)accessInstanceVariablesDirectly`方法
  * 如果返回NO，跳到最后一步，可以禁止直接访问实例变量
  * 如果返回YES，继续查找实例变量
* 按照 `_name,` `_isName`, `name`,`isName` 的顺序查找实例变量。
* 如果上面列出的方法或者成员变量都不存在，系统将会执行该对象的`setValue：forUndefinedKey：`方法，默认是抛出异常。

#### 取值

当调用`valueForKey：@”name“`的代码时，KVC对`key`的搜索方式不同于`setValue：属性值 forKey：@”name“`，其搜索方式如下：

- 首先按`get<Key>`,`<key>`,`is<Key>`的顺序方法查找`getter`方法，找到的话会直接调用。如果是`BOOL`或者`Int`等值类型， 会将其包装成一个`NSNumber`对象。
- 如果上面的`getter`没有找到，KVC则会查找`countOf<Key>`,`objectIn<Key>AtIndex`或`<Key>AtIndexes`格式的方法。如果`countOf<Key>`方法和另外两个方法中的一个被找到，那么就会返回一个可以响应`NSArray`所�有方法的代理集合(它是`NSKeyValueArray`，是`NSArray`的子类)，调用这个代理集合的方法，或者说给这个代理集合发送属于`NSArray`的方法，就会以`countOf<Key>`,`objectIn<Key>AtIndex`�或`<Key>AtIndexes`这几个方法组合的形式调用。还有一个可选的`get<Key>:range:`方法。所以你想重新定义KVC的一些功能，你可以添加这些方法，需要注意的是你的方法名要符合KVC的标准命名方法，包括方法签名。
- 如果上面的方法没有找到，那么会同时查找`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`格式的方法。如果这三个方法都找到，那么就返回一个可以响应`NSSet`所的方法的代理集合，和上面一样，给这个代理集合发`NSSet`的消息，就会以`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`组合的形式调用。
- 如果还没有找到，再检查类方法`+ (BOOL)accessInstanceVariablesDirectly`,如果返回YES(默认行为)，那么和先前的设值一样，会按`_<key>,_is<Key>,<key>,is<Key>`的顺序搜索成员变量名，这里不推荐这么做，因为这样直接访问实例变量破坏了封装性，使代码更脆弱。如果重写了类方法`+ (BOOL)accessInstanceVariablesDirectly`返回NO的话，那么会直接调用`valueForUndefinedKey:` 
- 还没有找到的话，调用`valueForUndefinedKey:`



**项目经验** 

1、有已经上线的项目么？

2、项目里哪个部分是你完成的？（找一个亮点问一下如何实现的）

3、开发过程中遇到过什么困难，是如何解决的？

**学习能力** 

1、遇到一个问题完全不能理解的时候，是如何帮助自己理解的？举个例子？

2、有看书的习惯么？最近看的一本是什么书？有什么心得？

3、有没有使用一些笔记软件？会在多平台同步以及多渠道采集么？（如果没有，问一下是如何复习知识的）

4、有没有使用清单类，日历类的软件？（如果没有，问一下是如何安排，计划任务的）

5、平常看博客么？有没有自己写过？（如果写，有哪些收获？如果没有写，问一下不写的原因）

6、有关技术类的问题可以在评论区留言，我重点说一下这轮面试的心得和体会。