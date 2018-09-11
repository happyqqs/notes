# 函数式编程

编程范式：

1. 命令式编程：面向计算机硬件的抽象，命令式程序就是一个冯诺依曼的指令序列
2. 函数式编程：面向数学的抽象，将计算描述为一种**表达式求值**，一句话，函数式程序就是一个**表达式**。
3. 逻辑式编程：

函数：指的是数学中的函数，而不是计算机中的函数

思想：

通过函数描述我们要解决的问题和解决问题的方案，**把运算过程尽量写成一系列嵌套的函数调用**

函数和 integers, booleans, or structs 等类型一样，本身可以作为变量、返回值、参数

和指令式编程相比，函数式编程强调函数的计算比指令的执行重要。

和过程化编程相比，函数式编程里函数的计算可随时调用。



函数式编程有5个鲜明的特点

1. **函数是第一类对象**：与 integers, booleans, or structs 等类型
2. **只用表达式，不用语句**：每一步都是单纯的运算，而且都有返回值
3. **没有副作用**：函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。
4. **不修改状态**：状态不能保存在变量中，而是保存在参数中
5. **引用透明性**：如果提供同样的输入，那么函数总是返回同样的结果，也就是说表达式的值不依赖于可以改变值的全局状态。



优点：函数式编程的好处主要是不可变性带来的，没有可变的状态，没有副作用，引用透明；易于单元测试和并发

1. 代码简洁，减少了代码的重复
2. 接近自然语言，容易理解
3. 更方便的代码管理：每一个函数都可以被看做独立单元，很有利于进行单元测试（unit testing）和除错（debugging），以及模块化组合
4. 易于并发编程：
5. 代码的热升级：函数式编程没有副作用，只要保证接口不变，内部实现是外部无关的。所以，可以在运行状态下直接升级代码，不需要重启，也不需要停机



由于变量不可变，纯函数式编程无法实现循环，只能使用递归来解决迭代问题



函数式语言特性

* 高阶函数：参数为函数或返回值为函数的函数，可以将复用的粒度降低到函数级别，提供一种函数级别上的依赖注入机制
* 偏应用函数：
* 柯里化
* 闭包



惰性求值：将表达式赋值给变量时并不计算表达式的值，而是在变量第一次被使用时才进行计算

# 响应式编程

响应式编程是一种面向数据流和变化传播的编程范式

在维基百科中有一个这样的例子：

> 在命令式编程环境中，a = b + c 表示将表达式的结果赋给a，而之后改变b或c的值不会影响a。但在响应式编程中，a的值会随着b或c的更新而更新。
>
> Excel就是响应式编程的一个例子。单元格可以包含字面值或类似”=B1+C1″的公式，而包含公式的单元格的值会依据其他单元格的值的变化而变化 。



#### 函数响应式编程

**函数响应式编程**是种编程范式。它是通过构建函数操作数据序列，然后对这些序列做出响应的编程方式。它结合了**函数式编程**以及**响应式编程**。

**函数式编程**可以很方便的来操作序列，响应式编程用于对这个序列做出相应

例如：可以把一个按钮的点击事件看做一个序列

```swift
// 假设用户在进入页面到离开页面期间，总共点击按钮 3 次

// 按钮点击序列
let taps: Array<Void> = [(), (), ()]

// 每次点击后弹出提示框
taps.forEach { showAlert() }
```

这样的处理是理想状态下的函数式编程，但是这个序列里面的事件（点击按钮）是异步产生的，传统的序列无法描述这种元素异步产生的情况。为了解决这个问题，于是就产生了**可被监听的序列**`Observable<Element>`。它也是一个序列，只不过这个序列里面的元素可以是同步产生的，也可以是异步产生的:

```swift
// 按钮点击序列
let taps: Observable<Void> = button.rx.tap.asObservable()

// 每次点击后弹出提示框
taps.subscribe(onNext: { showAlert() })
```

这里 `taps` 就是按钮点击事件的序列。然后我们通过弹出提示框，来对每一次点击事件做出响应。这种编程方式叫做**响应式编程**。我们结合**函数式编程**以及**响应式编程**就得到了**函数响应式编程**：

```swift
passwordOutlet.rx.text.orEmpty
    .map { $0.characters.count >= minimalPasswordLength }
    .bind(to: passwordValidOutlet.rx.isHidden)
    .disposed(by: disposeBag)
```

我们通过不同的构建函数，来创建所需要的数据序列。最后通过适当的方式来响应这个序列。这就是**函数响应式编程**。



## RxSwift



##### iOS中常用的消息通知方式

* **Target-Action**：addTarget与action方法分离在两处处理
* **代理**：声明协议，代理属性设置，代理方法实现
* **闭包回调**：需要处理错误，解析数据
* **通知**：添加观察者、移除观察者、收到通知的处理方法
* **KVO**：添加观察者、移除观察者、观察到变化的处理方法

RxSwift可以以一种简便优雅的方式实现iOS中传统的消息通知

此外RxSwift还可以很方便地实现以下功能

1. 多个任务之间有依赖关系
2. 等待多个并发任务完成后处理结果



##### RxSwift的优点

* 复合：Rx 就是复合的代名词
* 复用：易复合
* 清晰：声明都是不可变更的
* 易用：它抽象的了异步编程，使我们统一了代码风格
* 稳定：Rx 是完全通过单元测试的



#### 数据绑定（订阅）

将**可被监听的序列**绑定到**观察者**上

举个例子：

在iOS中给一个imageView设置图片我们通常会这么做

```swift
let image: UIImage = UIImage(named: ...)
imageView.image = image
```

而通过绑定的方式将一个图片序列 **“同步”** 到`imageView`上。这个序列里面的图片可以是**异步**产生的。这里定义的 `image` 就是上图中蓝色部分（**可被监听的序列**），`imageView.rx.image`就是上图中橙色部分（**观察者**）。而这种 **“同步机制”** 就是**数据绑定（订阅）**。

```swift
let image: Observable<UIImage> = ...
image.bind(to: imageView.rx.image)
```



### RxSwift核心

#### 1. Observable - 可被监听的序列

Observable可以用于描述元素异步产生的序列。

所有的事物都可以看作是序列：温度、网络请求返回的JSON，任务回调……

##### 创建序列

框架已经帮我们创建好了许多常用的序列。例如：`button`的点击，`textField`的当前文本，`switch`的开关状态，`slider`的当前数值等等

自定义序列的创建：

创建一个`[0, 1, ... 8, 9]` 的序列

```swift
let numbers: Observable<Int> = Observable.create { observer -> Disposable in

    observer.onNext(0)
    observer.onNext(1)
    observer.onNext(2)
    observer.onNext(3)
    observer.onNext(4)
    observer.onNext(5)
    observer.onNext(6)
    observer.onNext(7)
    observer.onNext(8)
    observer.onNext(9)
    observer.onCompleted()

    return Disposables.create()
}
```

创建序列最直接的方法就是 `Observable.create`，然后在构建函数里面描述元素的产生过程。`observer.onNext(0)` 就代表产生了一个元素，他的值是 `0`。后面又产生了 9 个元素分别是 `1, 2, ... 8, 9` 。最后，用 `observer.onCompleted()` 表示元素已经全部产生，没有更多元素了。

你可以用这种方式来封装功能组件，例如，闭包回调：

```swift
typealias JSON = Any

let json: Observable<JSON> = Observable.create { (observer) -> Disposable in

    let task = URLSession.shared.dataTask(with: ...) { data, _, error in

        guard error == nil else {
            observer.onError(error!)
            return
        }

        guard let data = data,
            let jsonObject = try? JSONSerialization.jsonObject(with: data, options: .mutableLeaves)
            else {
            observer.onError(DataError.cantParseJSON)
            return
        }

        observer.onNext(jsonObject)
        observer.onCompleted()
    }

    task.resume()

    return Disposables.create { task.cancel() }
}
```



在闭包回调中，如果任务失败，就调用 `observer.onError(error!)`。如果获取到目标元素，就调用 `observer.onNext(jsonObject)`。由于我们的这个序列只有一个元素，所以在成功获取到元素后，就直接调用 `observer.onCompleted()` 来表示任务结束。最后 `Disposables.create { task.cancel() }` 说明如果数据绑定被清除（订阅被取消）的话，就取消网络请求。

这样一来我们就将传统的闭包回调转换成序列了。然后可以用 `subscribe` 方法来响应这个请求的结果：

```swift
json
    .subscribe(onNext: { json in
        print("取得 json 成功: \(json)")
    }, onError: { error in
        print("取得 json 失败 Error: \(error.localizedDescription)")
    }, onCompleted: {
        print("取得 json 任务成功完成")
    })
    .disposed(by: disposeBag)
```

这里`subscribe`后面的`onNext`,`onError`, `onCompleted` 分别响应我们创建 json 时，构建函数里面的`onNext`,`onError`, `onCompleted` 事件。我们称这些事件为 **Event**:

#### Event

```swift
public enum Event<Element> {
    /// Next element is produced.
    case next(Element)

    /// Sequence terminated with an error.
    case error(Swift.Error)

    /// Sequence completed successfully.
    case completed
}
```

#####  特征序列（语法糖）

Single：一个比较常见的例子就是执行 HTTP 请求，然后返回一个**应答**或**错误**。

- 发出一个元素，或一个 `error` 事件
- 不会共享状态变化

Completable：适用于那种你只关心任务是否完成，而不需要在意任务返回值的情况。它和 `Observable<Void>` 有点相似。

- 发出零个元素
- 发出一个 `completed` 事件或者一个 `error` 事件
- 不会共享状态变化

Maybe：介于 Single和 Completable之间，适用于可能需要发出一个元素，又可能不需要发出的情况

- 发出一个元素或者一个 `completed` 事件或者一个 `error` 事件
- 不会共享状态变化

Driver：一个精心准备的特征序列。它主要是为了简化 UI 层的代码。不过如果你遇到的序列具有以下特征，你也可以使用它：

- 不会产生 `error` 事件
- 一定在 `MainScheduler` 监听（主线程监听）
- 共享状态变化

ControlEvent：专门用于描述 **UI** 控件所产生的事件，它具有以下特征：

- 不会产生 `error` 事件
- 一定在 `MainScheduler` 订阅（主线程订阅）
- 一定在 `MainScheduler` 监听（主线程监听）
- 共享状态变化

#### 2. Observer-观察者

观察者是用来监听事件，然后对这个事件作出响应。响应事件的都是观察者

##### 创建观察者

框架已经帮我们创建好了许多常用的**观察者**。例如：`view` 是否隐藏，`button` 是否可点击， `label` 的当前文本，`imageView` 的当前图片等等。

创建自定义观察者：

```swift
tap.subscribe(onNext: { [weak self] in
    self?.showAlert()
}, onError: { error in
    print("发生错误： \(error.localizedDescription)")
}, onCompleted: {
    print("任务完成")
})
```

创建**观察者**最直接的方法就是在 `Observable` 的 `subscribe` 方法后面描述，事件发生时，需要如何做出响应。而**观察者**就是由后面的 `onNext`，`onError`，`onCompleted`的这些闭包构建出来的。

##### 特征观察者

AnyObserver：可以用来描叙任意一种观察者

Binder：适用于UI观察者（只处理next事件，更新UI的操作需要在主线程执行）

- 不会处理错误事件
- 确保绑定都是在给定Scheduler 上执行（默认 **MainScheduler**）

#### 3.  Observable & Observer-既是可被监听的序列也是观察者

在我们所遇到的事物中，有一部分非常特别。他们既是**可被监听的序列**也是**观察者**。

例如：`textField`的当前文本。它可以看成是由用户输入，而产生的一个**文本序列**。也可以是由外部文本序列，来控制当前显示内容的**观察者**。有许多 UI 控件都存在这种特性，例如：`switch`的开关状态，`segmentedControl`的选中索引号，`datePicker`的选中日期等等

##### 辅助类型

AsyncSubject：

* **AsyncSubject** 将在源 `Observable` 产生完成事件后，发出最后一个元素（仅仅只有最后一个元素），如果源 `Observable` 没有发出任何元素，只有一个完成事件。那 **AsyncSubject** 也只有一个完成事件。
* 它会对随后的观察者发出最终元素。如果源 `Observable` 因为产生了一个 `error` 事件而中止， **AsyncSubject** 就不会发出任何元素，而是将这个 `error` 事件发送出来。

PublishSubject：

* **PublishSubject** 将对观察者发送订阅后产生的元素，而在订阅前发出的元素将不会发送给观察者。如果你希望观察者接收到所有的元素，你可以通过使用 `Observable` 的 `create` 方法来创建 `Observable`，或者使用ReplaySubject。
* 如果源 `Observable` 因为产生了一个 `error` 事件而中止， **PublishSubject** 就不会发出任何元素，而是将这个 `error` 事件发送出来。

ReplaySubject：

* 对观察者发送全部的元素，无论观察者是何时进行订阅的
* 这里存在多个版本的 **ReplaySubject**，有的只会将最新的 n 个元素发送给观察者，有的只会将限制时间段内最新的元素发送给观察者
* 如果把 **ReplaySubject** 当作观察者来使用，注意不要在多个线程调用 `onNext`, `onError` 或 `onCompleted`。这样会导致无序调用，将造成意想不到的结果

BehaviorSubject：





## ReactiveCocoa



> 可以把信号想象成水龙头，只不过里面不是水，而是玻璃球(value)，直径跟水管的内径一样，这样就能保证玻璃球是依次排列，不会出现并排的情况(数据都是线性处理的，不会出现并发情况)。水龙头的开关默认是关的，除非有了接收方(subscriber)，才会打开。这样只要有新的玻璃球进来，就会自动传送给接收方。可以在水龙头上加一个过滤嘴(filter)，不符合的不让通过，也可以加一个改动装置，把球改变成符合自己的需求(map)。也可以把多个水龙头合并成一个新的水龙头(combineLatest:reduce:)，这样只要其中的一个水龙头有玻璃球出来，这个新合并的水龙头就会得到这个球。

## EasyReact

