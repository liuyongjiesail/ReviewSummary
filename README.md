# ReviewSummary

iOS Objective-C review summary of knowledge

- 数据结构

- - 图

  - - <https://www.cnblogs.com/polly333/p/4760275.html>

    - 由顶点集合和边集合构成

    - 有向图和无向图

    - 有向图有度的概念

    - - 入度
      - 出度

    - 带权值的图叫做网

    - - 铁路网

    - 存储结构

    - - 邻接矩阵

      - - 一个一维数组存储顶点信息，一个二维数组存储边或弧的信息

      - 邻接表

      - - 一维数组存储顶点信息，一个链表存储边或者弧的信息

      - 十字链表

      - 邻接多重表

- 框架模式

- - <https://github.com/livoras/blog/issues/11>

  - MVC

  - - 依赖关系：把应用程序分成View、Model层，再加一个Controller层，C的职责为进行Model和View之间的协作（路由、输入预处理等）的应用逻辑；Model进行处理业务逻辑。

    - 调用关系

    - - View是把控制权叫移给Controller，Controller执行应用程序相关的应用逻辑（对来自View数据进行预处理、决定调用哪个Model的接口等等）
      - Controller操作Model，Model执行业务逻辑对数据进行处理。但不会直接操作View，可以说它是对View无知的
      - View和Model的同步消息时通过观察者模式进行，而同步操作是由View自己请求Model的数据然后对视图进行更新

    - 通俗理解

    - - Controller其实是不知道View的存在的，View有事件处理，就代理给Controller，Controller可以直接操作Model，而同时View是依赖Model的变化的，Model发生变化，通过观察者模式通知View，View发生变化，同时同一个Model可以更新多个View
      - 数据的流向是一个环形。View—> Controller —> Model - - -> View

    - 优缺点

    - - 优点

      - - 把业务逻辑和展示逻辑分离，模块化程度高。且当应用逻辑需要变更的时候，不需要变更业务逻辑和展示逻辑，只需要Controller换成另外一个Controller就行了（Swappable Controller）
        - 观察者模式可以做到多视图同时更新

      - 缺点

      - - Controller测试困难。因为视图同步操作是由View自己执行，而View只能在有UI的环境下运行。在没有UI环境下对Controller进行单元测试的时候，应用逻辑正确性是无法验证的：Model更新的时候，无法对View更新操作进行断言
        - View无法组件化。View是强依赖特定的Model的，如果需要把这个View抽出来作为另一个应用的程序可复用的组件就困难了。因为不同的程序的Domain Model是不一样的

  - MVP

  - - 两种模式

    - - Passive View

      - - 依赖关系：把MVC模式中的Controller换成了Presenter，MVP打破了View原来对Model的依赖，其余的依赖关系和MVC模式一致

        - 调用关系

        - - View不再负责同步的逻辑，而是有Presenter赋值。Presenter中既有应用程序的逻辑也有同步逻辑
          - View需要提供操作界面的接口给Presenter进行调用
          - 对比在MVC中，Controller是不能操作View的，View也没有提供相应的接口；而在MVP当中，Presenter可以操作View，View需要提供一组对界面操作的接口给Presenter进行调用；Model仍然通过事件官博自己的变更，但有Presenter监听而不是View

        - 通俗理解

        - - Presenter是一个中间者，控制着View的变化和Model的变化。假如用户点击了View，把这个事件交给Presenter处理，Presenter通过该事件处理相应的Model的变化，Presenter又依赖Model的变化，此时Presenter接收到Model变化时通过View提供的接口来更新View。Presenter负责了所有的逻辑，Model也负责了一些。
          - 数据流向: View <—> Presenter <—> Model

        - 优缺点

        - - 优点

          - - 便于测试。Presenter对View是通过接口进行，在对Presenter进行不依赖UI环境的单元测试的时候。可以通过Mock一个View对象，这个对象只需要实现了View的接口即可。然后依赖注入到Presenter中，单元测试的时候就可以完整的测试Presenter应用逻辑的正确性
            - View可以进行组件化。在MVP当中，View不依赖Model。这样就可以让View从特定的业务场景中脱离出来，可以说View可以做到对业务完全无知。它只需要提供一系列接口提供给上层操作。这样就可以做到高度可复用的View组件

          - 缺点

          - - Presenter中除了应用逻辑以外，还有大量的View->Model，Model->View的手动同步逻辑，造成Presenter比较笨重，维护起来会比较困难

      - Supervising Controller

      - - MVP的Passive View模式，该模式下View非常Passive，它几乎什么都不知道，Presenter让它干什么它就干什么。而Supervising Controller模式中，Presenter会把一部分简单的同步逻辑交给View自己去做，Presenter只负责比较复杂的、高层次的UI操作，所以可以把它看成一个Supervising Controller

  - MVVM

  - - 依赖关系：MVVM的依赖关系和MVP依赖，只不过是把P换成了VM

    - 调用关系

    - - MVVM的调用关系和MVP一样。但是，在ViewModel当中会有一个叫Binder，或者是Data-binding engine的东西。以前全部由Presenter负责的View和Model之间数据同步操作交由给Binder处理。你只需要在View的模版语法当中，指令式地声明View上的显示的内容是和Model的哪一块数据绑定的。当ViewModel对进行Model更新的时候，Binder会自动把数据更新到View上去，当用户对View进行操作（例如表单输入），Binder也会自动把数据更新到Model上去。这种方式称为：Two-way data-binding，双向数据绑定。可以简单而不恰当地理解为一个模版引擎，但是会根据数据变更实时渲染。
      - 通俗理解： MVVM把View和Model的同步逻辑自动化了。以前Presenter负责的View和Model同步不再手动地进行操作，而是交由框架所提供的Binder进行负责。只需要告诉Binder，View显示的数据对应的是Model哪一部分即可

    - 通俗理解

    - - View和Model通过ViewModel做了一个双向绑定的Binder，View发生变化，通过这个Binder，把相应数据动更新Model上，当然，如果ViewModel更新了Model，通过这个Binder会自动吧数据更新到View上
      - 数据流向  View <—> ViewModel <—> Model

    - 优缺点

    - - 优点

      - - 提高可维护性。解决了MVP大量的手动View和Model同步的问题，提供双向绑定机制。提高了代码的可维护性
        - 简化测试。因为同步逻辑是交由Binder做的，View跟着Model同时变更，所以只需要保证Model的正确性，View就正确。大大减少了对View同步更新的测试

      - 缺点

      - - 过于简单的图形界面不适用，或说牛刀杀鸡
        - 对于大型的图形应用程序，视图状态较多，ViewModel的构建和维护的成本都会比较高
        - 数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的

- 设计模式

- - <http://www.cnblogs.com/shuigu/p/6908520.html>

  - 策略模式

  - - 定义了算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。

  - 观察者模式

  - - 定义了对象间的一种一对多的组合关系。以便一个对象的状态发生变化时，所有依赖于他的对象都得到通知并自动更新。

  - 装饰模式

  - - 动态地将责任附加到对象上。若要扩展功能，装饰者提供了比继承更有弹性的替代方案。

  - 工厂模式

  - - 定义一个用于创建对象的接口，让子类决定实例化哪一个类。使一个类的实例化延迟到子类

  - 抽象工厂模式

  - - 提供一个创建一系列相关或者互相依赖对象的接口，而无需指定它们具体的类

  - 单例模式

  - - 保证一个类仅有一个实例，并提供一个访问 它的全局访问点

  - 构造者模式

  - - 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示

  - 原型模式

  - - 用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象

  - 适配器模式

  - - 将一个类的接口转换为客户希望的另外一个接口，Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作

  - 外观模式

  - - 为子系统中一组不同的接口提供统一的接口
    - 比如组装一辆车，需要很多参与者，老板找了一个经理，老板只要安排经理，经理安排这些参与者，这里的经理就是一个外观。

  - 桥接模式

  - - 将抽象部分与它的实现部分分离，使它可以独立变更
    - 不同框架类之间通过桥接访问

  - 代理模式

  - - 为其他对象提供一种代理以控制对这个对象的访问

  - 组合模式

  - - 将对象组合成树形结构以表示“部分-整体”的层次结构

  - 享元模式

  - - 运用共享技术有效地支持大量细粒度的对象
    - TableView cell的复用就是使用这种模式

  - 模板方法模式

  - - 定义一个操作中算法的骨架，而将一些步骤延迟到子类。模板方法使子类可以重定义算法的某些特定步骤而不改变算法的结构

  - 命令模式

  - - 将请求封装成一个对象，从而可用不同的请求对客户端进行参数化，对请求排队或记录请求日志，以及支持可撤销的操作

  - 中介者模式

  - - 用中介对象封装一系列的对象交互。中介者使得各对象不需要显示地相互引用，从而使其耦合松散，而且可以独立的改变它们之间的交互。
    - 页面路由  中间件

  - 迭代器模式

  - - 提供一种方法顺序的访问一个聚合对象的各个元素，而又不暴露该对象内部表示

  - 访问者模式

  - - 表示一个作用于某对象结构中的各元素的操作。它让我们可以在不改变各元素的类的前提下定义作用于这些元素的新操作

  - 责任链模式

  - - 使多个对象都有机会处理请求，从而避免请求的发送者与接受者之前的耦合关系。将这个对象连成一条链传递该请求，直到有一个对象处理它为止。
    - 类似响应者链模式

  - 备忘录模式

  - - 在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原先保存的状态

  - 状态模式

  - - 允许一个对象在其内部状态改变时改变他的行为。对象看起来似乎改变了它的类

  - 解释器模式

  - - 给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子

- OC中关联对象的原理

- -  <http://blog.leichunfeng.com/blog/2015/06/26/objective-c-associated-objects-implementation-principle/>

  - objc_setAssociatedObject

  - - 用于给对象添加关联对象，传入 nil 则可以移除已有的关联对象

  - objc_getAssociatedObject

  - - 用于获取关联对象

  - objc_removeAssociatedObjects

  - - 用于移除一个对象的所有关联对象

  - 一个对象的所有关联对象是在这个对象被释放时调用的objc_removeAssociatedObjects 函数中被移除的。

  - AssociationsManager (全局类)

  - AssociationsHashMap (带自旋锁的哈希表，避免数据竞争，全局静态变量)

  - - 数据结构：key为对象地址，value为ObjectAssociationsMap。

  - ObjectAssociationsMap  (C++ Map，一个对象有一个map)

  - - 数据结构：key为唯一的地址，一般是方法名的取地址，value 为 ObjcAssociation。

  - ObjcAssociation (C++类，具体的关联对象类)

  - - 数据结构：_Policy 为关联策略，_value为关联对象值，两个实例变量。

- weak的实现原理

- - <https://www.jianshu.com/p/dce1bec2199e>
  - 数据结构就是一个全局的哈希表
  - 对象释放的时候，通过对象地址找到哈希表中的弱引用数组，遍历释放，并置为nil
  - 每个对象有一个SideTable实例，存储了一个自旋锁，一个引用计数表和一个weak表，相当于每个对象都引用了一个全局weak_table的引用
  - 对象的地址作为key，value为指向该对象的所有弱引用指针数组
  - initWeak
  - storeWeak
  - 大量使用weak会造成应用性能降低，因为weak实现有一系列的查表，插入操作，还是很耗时的，如果大量使用，会造成CPU使用增加

- OC引用计数原理

- - <http://yulingtianxia.com/blog/2015/12/06/The-Principle-of-Refenrence-Counting/#isa-%E6%8C%87%E9%92%88%EF%BC%88NONPOINTER-ISA%EF%BC%89>

  - 数据结构

  - - isa指针存储引用计数
    - SideTable管理引用计数

- Autorelease

- - <http://blog.sunnyxx.com/2014/10/15/behind-autorelease/>

  - 一个线程对应一个自动释放池，对应一个runLoop，主线程的自动释放池是main函数主动添加的，当一个对象使用Autorelease修饰的使用，就自动加入了当前这个释放池，在runLoop迭代中会调用pop方法，释放掉自动释放池中的autorelease对象，pop方法需要传入一个哨兵对象，自动释放池是可以嵌套的，只是多了一个哨兵对象而已

  - 数据结构（栈区）

  - - AutoreleasePool 没有数据结构，是一个结构体，它是很多个Page对象使用双向链表的数据结构串起来的

    - - AutoreleasePoolPage(很多个)

      - - 4096字节，一页虚拟内存的大小
        - 56 bit 用来存储page的成员变量，其他用来存储autorelease对象
        - magic 完整性校验
        - parent指针
        - child指针
        - next
        - 哨兵对象
        - 当前线程Thread

  - 系统的Block数组遍历方法内部会自动添加一个自动释放池，可以解决大量临时对象导致内存飙升问题，for循环和forin 需要手动添加自动释放池

- RunLoop

- - <https://www.cnblogs.com/kenshincui/p/6823841.html>

  - 事件驱动的一个循环

  - 事件种类（六种）

  - - Observer事件
    - Block事件
    - Main_dispatch_Queue事件
    - Timer事件
    - Source0事件
    - Source1事件

  - 伪过程（一个循环）

  - -  **__CFRunLoopDoObservers**(**kCFRunLoopBeforeTimers**)
    - ** __CFRunLoopDoObservers**(**kCFRunLoopBeforeSources**)
    - ** __CFRunLoopDoBlocks**()
    - ** __CFRunLoopDoSource0**()
    - ** CheckIfExistMessagesInMainDispatchQueue**()
    - ** __CFRunLoopDoObservers**(**kCFRunLoopBeforeWaiting**)
    - ** var wakeUpPort = SleepAndWaitForWakingUpPorts**()
    - **  __CFRunLoopDoObservers**(**kCFRunLoopAfterWaiting**)
    - ** if **(**wakeUpPort == timerPort**)** {__CFRunLoopDoTimers**()**}**
    -  **if **(**wakeUpPort == mainDispatchQueuePort**)** { __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__**()**}**
    - ** else {__CFRunLoopDoSource1**()**}**
    - ** __CFRunLoopDoBlocks**()

  - Mode

  - - Common

    - - NSDefaultRunLoopMode
      - NSTrackingRunLoopMode

    - Private 系统控制

    - - UIInitializationRunLoopMode
      - GSEventReceiveRunLoopMode

- Runtime

- - <http://yulingtianxia.com/blog/2014/11/05/objective-c-runtime/>

  - Runtime 基本是用 C 和汇编写的，铸就了OC动态语言的特性。因为Objc是一门动态语言，所以它总是想办法把一些决定工作从编译连接推迟到运行时。也就是说只有编译器是不够的，还需要一个运行时系统 (runtime system) 来执行编译后的代码

  - 数据结构

  - - SEL
    - id
    - Class
    - class_data_bits_t
    - class_ro_t
    - class_rw_t
    - Category
    - Method
    - Ivar
    - objc_property_t
    - protocol_t
    - IMP

- 消息发送与转发

- - 发送与转发流程

  - - 发送消息

    - - objc_msgSend

      - objc_msgSend_stret

      - objc_msgSendSuper

      - objc_msgSendSuper_stret

      - objc_msgSend_fpret

      - 通过获取方法地址 IMP 可以避开消息绑定而直接获取方法的地址并调用方法。这种做法很少用，除非是需要持续大量重复调用某方法的极端情况，避开消息发送泛滥而直接调用该方法会更高效

      - - NSObject类中有个methodForSelector:实例方法，你可以用它来获取某个方法选择器对应的IMP
        - methodForSelector:方法是由 Cocoa 的 Runtime 系统提供的，而不是 Objc 自身的特性

    - 动态方法解析

    - - 给程序员一次动态添加方法的机会
      - resolveInstanceMethod
      - resolveClassMethod

    - 重定向

    - - 偷梁换柱，让其他对象去处理
      - \- (id)forwardingTargetForSelector:(SEL)aSelector
      - \+ (id)forwardingTargetForSelector:(SEL)aSelector

    - 转发

    - - 如果动态方法解析返回NO，消息转发会直接触发
      - methodSignatureForSelector：用于生产NSInvocation对象
      - forwardInvocation:

  - 可以实现多继承的效果

  - 使轻量级对象代表重量级对象

  - - <https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW11>

  - 尽管转发很像继承，但是NSObject类不会将两者混淆

- Category

- - <https://tech.meituan.com/DiveIntoCategory.html>

  - 作用

  - - 声明私有方法

    - - 比如我们用了一个pod第三方库，有个私有方法我们想直接用，可以创建一个分类声明这个私有方法

    - 可以把一个大的类的不同实现分开在几个不同的文件里

    - 模拟多继承

    - 把framework的私有方法公开

    - - 同第一个

  - 数据结构

  - - 结构体

  - 如何加载的

  - - dyld加载的

  - load方法调用

  - - 根据编译顺序

  - 方法覆盖

  - - 加入了本来方法列表的前面

  - 关联对象

- TableView卡顿优化

- - <https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/>

  - 首先明确造成卡顿的原因是什么，一个内容显示到屏幕上都经历了什么呢？当VSync信号到来的时候，App主线程开始在CPU中计算显示内容，比如视图创建、布局计算、图片解码、本文绘制等。随后CPU会将计算好的内容提交给GPU，由GPU进行变换、合成、渲染。随后GPU会把渲染结果提交到帧缓存区去，等待下次VSync信号到来的时候显示到屏幕上。由于垂直同步的机制，如果在一个 VSync 时间内，CPU 或者 GPU 没有完成内容提交，则那一帧就会被丢弃，等待下一次机会再显示，而这时显示屏会保留之前的内容不变。这就是界面卡顿的原因。1秒显示60次

  - CPU的优化（对象复用、避免多次调整布局、异步计算布局、异步绘制）

  - - 对象的创建  - 避免使用Storyboard，推迟创建，对象复用
    - 对象调整 -  避免调整视图层次、添加和删除
    - 对象销毁 - 后台线程中去销毁
    - 布局计算 - 尽量在后台线程中计算好布局、并且对布局进行缓存，一次性调整好对应属性，而不是多次
    - AutoLayout - 避免使用，frame布局或者 ComponentKit、AsyncDisplayKit
    - 文本计算 - CoreText 对文本异步绘制
    - 文本渲染 - CoreText 对文本异步绘制
    - 图片的解码 - 后台线程先把图片绘制到 CGBitmapContext 中，然后从 Bitmap 直接创建图片
    - 图像的绘制 - 异步绘制

  - GPU的优化（避免大量图片同时显示、视图层级复杂和离屏渲染）

  - - 纹理的渲染 - 尽量减少在短时间内大量图片的显示，尽可能将多张图片合成为一张进行显示

    - 视图的混合 - 尽量减少视图数量和层次，并在不透明的视图里标明 opaque 属性以避免无用的 Alpha 通道合成。当然，这也可以用上面的方法，把多个视图预先渲染为一张图片来显示。

    - 图形的生成 - 把需要显示的图形在后台线程绘制为图片，避免使用圆角、阴影、遮罩等属性。

    - - 离屏渲染的定义

      - - 指的是GPU在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作

      - 为什么会触发离屏渲染

      - -  当使用圆角，阴影，遮罩的时候，图层属性的混合体被指定为在未预合成之前不能直接在屏幕中绘制，所以就需要屏幕外渲染被唤起。
        - 屏幕外渲染并不意味着软件绘制，但是它意味着图层必须在被显示之前在一个屏幕外上下文中被渲染（不论CPU还是GPU）

  - 优化历程

  - - 预排版 - 提前计算布局 cellLayout
    - 预渲染 - 避免离屏渲染，后台线程绘制内容
    - 异步绘制 - 文本异步绘制
    - 全局并发控制 - 控制并发线程数量
    - 更高效的异步图片加载 - 使用CALayer.contents 添加图片
    - 列表中有不少视觉元素并不需要触摸事件，这些元素可以用 ASDK 的图层合成技术预先绘制为一张图
    - 减少每个 Cell 内图层的数量，用 CALayer 替换掉 UIView
    - 目前每个 Cell 的类型都是相同的，但显示的内容却各部一样，比如有的 Cell 有图片，有的 Cell 里是卡片。把 Cell 按类型划分，进一步减少 Cell 内不必要的视图对象和操作，应该能有一些效果

  - Instruments的GPU Driver能够实时查看到 CPU 和 GPU 的资源消耗

- 界面渲染流程

- - <http://blog.handy.wang/blog/2015/10/03/uiviewyu-calayerxie-zuo-xuan-ran-jie-mian-de-guo-cheng/?nsukey=M6GrXP%2B3pLH1tCM%2FWKHpNIjtQ72dYxOMcQWzqP9aoznuOvQqyO91Rj9%2B0uRpryZ9sVSaxAEfrP5MizA052EtNuj5ZzKvbJmVvgmEPvkzTZ5tvVAGq525RoaImu8EuNs3lzhhRybb8DlSwF5FHBch1SJceXp%2BKCE8sYhdzL%2FJnl51Z2O1CQj%2FdQBLPistdVoCuh49Wgdb18PO%2FnH3JvlkfQ%3D%3D>

  - 渲染框架

  - - UIKit Framework

    - - 负责界面展示、事件响应、驱动RunLoop运行和与系统内核通信的数据

    - QuartzCore Framework 和 CoreAnimation

    - - 特指CoreAnimation，图形处理和视频图像处理的能力，CoreAnimation通过OpenGLES做的，负责把CoreGraphics创建的数据模型真正显示屏幕上

    - CoreGraphics Framework

    - - 负责创建显示到屏幕上的数据模型，比如path绘制、阴影效果等等其他显示属性

  - 触发渲染的方式

  - - Observer

    - - CoreAnimation 会有一个Observer 监听RunLoop Waiting 和 Exit 状态的
      - 一个控制器LoadView时，Runloop处于Waiting时会回调这个Observer，当下一轮runLoop执行到__CFRunLoopDoObservers，Observer的callBack事件会触发CoreAnimation进一步调用渲染

    - Source

    - - 在控制器上添加一个按钮，当点击这个按钮的时候改变背景色。点击时，Runloop 被iOS系统传递来的点击事件唤醒并由Source1处理（__IOHIDEventSystemClientQueueCallback），并且在下一个RunLoop里在__CFRunLoopDoSources0时由Source0转发给UIApplication（_UIApplicationHandleEventQueue，这里会涉及一个响应者链），从而通过Source0的事件队列来调用CoreAnimation来进一步渲染

  - 渲染的过程（Runloop触发CoreAnimation渲染后）

  - - 调用CA::Transaction::commit() ();来创建CATransaction，然后进一步调用-[CALayer drawInContext:] （CPU）
    - 回调CALayer的Delegate(UIView)，问UIView没有需要画的内容，即回调到drawRect:方法（CPU）
    - 在drawRect:方法里可以通过CoreGraphics函数或UIKit中对CoreGraphics封装的方法进行画图操作，这些画图的操作内容都是以Off-Screen离屏(广义的离屏，因为没有在GPU中进行)方式进行画图（CPU）
    - 无论是有UIView参与的或是直接采用CALayer渲染的操作都会体现在CALayer上
    - CoreAnimation(CALayer)把它的内容转成位图(纹理)，然后通过OpenGLES把位图内容传送到GPU的帧缓冲区（GPU）
    - 等到由iOS显示屏时钟信号驱动的VSync信号来临时，则把GPU帧缓冲区里的内容显示到iOS显示屏上

- 响应者链

- - <https://www.jianshu.com/p/6ff87b3ab2cb>

  - 生成事件

  - - runloop的过程这里不续诉了，runloop把事件加入UIApplication事件队列中后，然后在队列中取出最前面的事件交给window处理

  - 查找第一响应者

  - - 利用类似二分法的方式查找。window调用hitTest:withEvent:方法，紧接着调用pointInside:withEvent:方法，如果返回的是true，那么widow循环它的子视图调用hitTest:withEvent:方法，如果pointInside:withEvent:返回false，hitTest:withEvent:返回nil，如果返回true，继续逐级调用，直到touch发生的位置，这个视图就是要找的view。hitTest:withEvent:返回这个view，直到把此对象依次向上返回到Application则处理结束

  - 处理事件

  - - 当Application就知道了第一响应对象后，就会把事件交给第一响应对象来处理，如果第一响应对象能顺利处理事件，则整个响应结束，但是第一响应对象如果无法处理事件，就会把事件传递给下一个响应对象（nextResponder），一直沿着响应链向上回溯
    - View —> Superview —> ViewController —> Window —> UIApplication —> 丢弃

- KVC

- - <https://www.jianshu.com/p/b9f020a8b4c9>

  - 键值编码，可以允许开发者通过Key名直接访问对象的属性。

  - NSObject的扩展NSObject(NSKeyValueCoding)

  - API

  - - \- (nullable id)valueForKey:(NSString *)key
    - \- (void)setValue:(nullable id)value forKey:(NSString *)key
    - \- (nullable id)valueForKeyPath:(NSString *)keyPath
    - \- (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath
    - …

  - 设值

  - - 优先调用setter方法，setKey
    - 检查+ (BOOL)accessInstanceVariablesDirectly方法有没有返回YES，返回YES
    - 搜索 _key 的成员变量
    - 搜索* *_isKey 成员变量
    - 搜索 key 的成员变量
    - 搜索 isKey 的成员变量
    - 最后未搜索到，则会调用setValue：forUndefinedKey：方法
    - 如果开发者想让这个类禁用KVC里，那么重写+ (BOOL)accessInstanceVariablesDirectly方法让其返回NO即可，这样的话如果KVC没有找到set<Key>:属性名时，会直接调用setValue：forUndefinedKey：方法

  - 取值

  - - 按照getKey，key，isKey的顺序查找getter方法
    -  …
    -  …
    - 检查+ (BOOL)accessInstanceVariablesDirectly方法有没有返回YES，返回YES
    - 搜索 _key 的成员变量
    - 搜索* *_isKey 成员变量
    - 搜索 key 的成员变量
    - 搜索 isKey 的成员变量
    - 最后未搜索到，则会调用valueForUndefinedKey：方法
    - 如果开发者想让这个类禁用KVC里，那么重写+ (BOOL)accessInstanceVariablesDirectly方法让其返回NO即可，这样的话如果KVC没有找到get<Key>:属性名时，会直接用valueForUndefinedKey：方法

  - 使用KeyPath

  - - 通过键值路径寻找，使用点来分割key

  - 处理异常

  - - 处理nil异常

    - - setNilValueForKey: 这个方法默认是抛出异常，所以一般而言最好还是重写这个方法

      - - \- (void)setNilValueForKey:(NSString *)key

    - 处理UndefinedKey异常

    - - 对于不存在的key操作，会报错forUndefinedKey发生崩溃，重写forUndefinedKey方法避免崩溃

      - - \- (id)valueForUndefinedKey:(NSString *)key
        - \- (void)setValue:(id)value forUndefinedKey:(NSString *)key

  - 处理集合

  - - 简单集合运算符

    - - @avg， @count ， @max ， @min ，@sum

    - 对象运算符 （返回值都是NSArray）

    - - @distinctUnionOfObjects

      - - 去重后的

      - @unionOfObjects

      - - 未去重

  - 处理字典

  - - 模型字典的转换

    - - \- (NSDictionary<NSString *, id> *)dictionaryWithValuesForKeys:(NSArray<NSString *> *)keys;
      - \- (void)setValuesForKeysWithDictionary:(NSDictionary<NSString *, id> *)keyedValues;

  - 应用

  - - 动态地取值和设值

    - 访问和修改私有变量

    - Model和字典的转换

    - 修改一些控件的内部属性

    - 方便的操作集合

    - 将一个数组中的元素修改返回另一个数组

    - - NSArray *arrStr = @[@"english",@"franch",@"chinese"];NSArray *arrCapStr = [arrStr valueForKey:@"capitalizedString"];for(NSString *str in arrCapStr) {NSLog(@"%@",str); } 

- KVO

- - <https://www.jianshu.com/p/b9f020a8b4c9>

  - KVO是基于KVC实现的

  - 观察者模式的一种实现

  - -  \- (void)addObserver:(NSObject*)observer forKeyPath:(NSString*)keyPath options:(NSKeyValueObservingOptions)options context:(void*)context; 
    - \- (void)removeObserver:(NSObject*)observer forKeyPath:(NSString*)keyPath;

  - 手动实现KVO或者禁用KVO

  - - \- (void)willChangeValueForKey:(NSString *)key
    - \- (void)didChangeValueForKey:(NSString *)key
    - \+ (BOOL) automaticallyNotifiesObserversForKey:(NSString *)key

  - 实现键值依赖观察

  - KVO是同步的

  - KVO的实现

- 多线程
  https://juejin.cn/post/6844903581967974413#heading-29

- - pthread

  - - <https://www.jianshu.com/p/cbaeea5368b1>

    - pthread 是一套通用的多线程的 API，可以在Unix / Linux / Windows 等系统跨平台使用，使用 C 语言编写，需要程序员自己管理线程的生命周期，使用难度较大，我们在 iOS 开发中几乎不使用 pthread，但是还是来可以了解一下的

    - 创建线程方法

    - - pthread_create(&thread, NULL, run, NULL);

      - - 第一个参数&thread是线程对象，指向线程标识符的指针
        - 第二个是线程属性，可赋值NULL
        - 第三个run表示指向函数的指针(run对应函数里是需要在新线程中执行的任务)
        - 第四个是运行函数的参数，可赋值NULL

    - 相关方法

    - - pthread_create() 创建一个线程
      - pthread_exit() 终止当前线程
      - pthread_cancel() 中断另外一个线程的运行
      - pthread_join() 阻塞当前的线程，直到另外一个线程运行结束
      - pthread_attr_init() 初始化线程的属性
      - pthread_attr_setdetachstate() 设置脱离状态的属性（决定这个线程在终止时是否可以被结合）
      - pthread_attr_getdetachstate() 获取脱离状态的属性
      - pthread_attr_destroy() 删除线程的属性
      - pthread_kill() 向线程发送一个信号

  - NSThread

  - - <https://www.jianshu.com/p/cbaeea5368b1>

    - NSThread 是苹果官方提供的，使用起来比 pthread 更加面向对象，简单易用，可以直接操作线程对象。不过也需要需要程序员自己管理线程的生命周期(主要是创建)，我们在开发的过程中偶尔使用 NSThread。比如我们会经常调用 [NSThread currentThread] 来显示当前的线程信息

    - 创建、启动线程

    - - 先创建、再启动

      - - NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run) object:nil];
        - [thread start]

      - 创建并自动启动

      - - [NSThread detachNewThreadSelector:@selector(run) toTarget:self withObject:nil]

      - 隐私创建并自动启动

      - - [self performSelectorInBackground:@selector(run) withObject:nil]

    - 线程相关方法

    - - \+ (NSThread *)mainThread;
      - \- (BOOL)isMainThread;
      - \+ (BOOL)isMainThread;
      - \+ (NSThread *)currentThread;
      - \- (void)setName:(NSString *)n;
      - \- (NSString *)name;

    - 线程状态控制方法

    - - \- (void)start;
      - \+ (void)sleepUntilDate:(NSDate *)date;
      - \+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
      - \+ (void)exit;

    - 线程间的通信

    - - 开启一个子线程下载图片，下载完成后回到主线程设置图片

      - -  [NSThread detachNewThreadSelector:@selector(downloadImage) toTarget:self withObject:nil];
        -  \- (void)downloadImage 方法一些了操作后 [self performSelectorOnMainThread:@selector(refreshOnMainThread:) withObject:image waitUntilDone:YES];
        - \- (void)refreshOnMainThread:(UIImage *)image {}

    - 线程安全和线程同步

    - - 加个 互斥锁 @synchronized (self) {}

    - 线程的状态转换

    - - New          新建
      - Runnable  就绪
      - Blocked    阻塞
      - Running    运行
      - Dead         退出

  - GCD

  - - <https://www.jianshu.com/p/2d57c72016c6>

    - 优点

    - - 用于多核的并行运算
      - 自动利用更多的CPU(双核、四核)
      - 自动管理线程的生命周期（创建、调度、销毁）
      - 不用编写任何线程管理代码

    - 组合方式

    - - 同步 + 并发

      - - 不会开启新线程，串行执行任务

      - 同步 + 串行

      - - 不会开启新线程

      - 异步 + 并发

      - - 开启新线程，并发执行任务

      - 异步 + 串行

      - - 开启一条新线程，串行执行任务

    - 线程间的通信

    - - 异步线程执行完任务，回到主线程执行任务

    - 栅栏方法 **dispatch_barrier_async**

    - - 异步执行两组操作，一组操作执行完后，再执行另一组操作
      - 任务**1**、**2**、**3  barrier  **任务** 4**、**5**、**6**

    - 延时执行方法 **dispatch_after**

    - - 几秒后执行

    - 一次性代码（只执行一次）**dispatch_once**

    - - 单例

    - 快速迭代方法** dispatch_apply**

    - - 串行队列中与**for**循环一样
      - 并发队列中可以并发执行，效率更高
      - 开始代码** —>  **并发完成后**  —> **结束代码**  **同步操作

    - 队列组** dispatch_group**

    - - 分别异步执行两个耗时任务，当两个耗时任务都执行完毕后在回到主线程执行任务

      - **dispatch_group_notify**

      - - 任务1 ：dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0) 
        - 任务2 ： dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)
        - dispatch_group_notify(group, dispatch_get_main_queue()

      - 暂停 **dispatch_group_wait**

      - - 暂停当前线程（阻塞当前线程）
        - 任务1
        - 任务2
        - dispatch_group_wait(group, DISPATCH_TIME_FOREVER)
        - 下一步代码

      - **dispatch_group_enter**、**dispatch_group_leave**

      - - dispatch_group_enter(group);

        - dispatch_async(queue, ^{   任务1

        - -  dispatch_group_leave(group);

        - dispatch_group_enter(group);

        - dispatch_async(queue, ^{   任务2

        - - dispatch_group_leave(group);

        - dispatch_group_notify(group, dispatch_get_main_queue(), ^{

    - 信号量 **dispatch_semaphore**

    - - 保持线程同步，将异步执行任务转换同步执行任务

      - - AFNetworking 中 AFURLSessionManager.m 里面的 tasksForKeyPath: 方法。通过引入信号量的方式，等待异步执行任务结果，获取到 tasks，然后再返回该 tasks。

      - 保证线程安全，为线程加锁

      - - 售票系统

        - - 多个队列售卖票
          - 卖票的方法开始** **
          - dispatch_semaphore_wait(semaphoreLock, DISPATCH_TIME_FOREVER);
          - 卖票
          - dispatch_semaphore_signal(semaphoreLock);
          - 卖票方法结束

  - NSOperation 和 NSOperationQueue

  - - <https://www.jianshu.com/p/4b1d77054b35>
    - ​

- iOS签名原理

- - <http://www.cocoachina.com/cms/wap.php?action=article&id=19427>

  - 非对称加密

  - - 选两个质数p和q，相乘得出一个大整数n，例如 p = 61，q = 53， n = 3233

    - 选1~n间随便一个质数e，例如 e = 17

    - 经过一系列数学公式，算出一个数字d（欧拉函数 和 模反元素 所得），满足

    - - 通过 n 和 e 这两个数据一组数据惊醒数学运算后，可以通过 n 和 d 去反解运算，反过来也可以
      - 如果知道 n 和 e ， 要推导出d，需要知道 p 和 q ，也就是需要把 n 因数分解

    - （n,e）公钥

    - （n,d）私钥

  - 数字签名

  - - 基于非对称加密实现的

    - 加密：数据&MD5 —> 摘要&公钥加密 —> 签名

    - 验证：接收到 数据 和 签名

    - - 数据&MD5 —> 摘要
      - 签名&私钥 —> 摘要
      - 比较摘要就能确保数据来自于正确的人且未被篡改

  - 确保从App Store安装

  - - 苹果自己生成 私钥 和 公钥
    - 私钥放在苹果后台
    - 公钥放在iOS系统内
    - 上传APP到App Store，后台用私钥签名这个app
    - iOS下载安装时，公钥解密签名 对比 就能判断是否来自App Store

  - 开发时确保安装

  - - 本地电脑生成一对公钥和私钥
    - 苹果后台生成一对公钥和私钥
    - 电脑的公钥要上传到苹果后台，苹果的私钥去签名公钥，公钥和签名一起形成一个证书
    - 证书、appID、权限配置、设备列表这些数据使用苹果的私钥签名，签名后的数据和签名一起形成了 Provisioning Profile 文件
    - 开发是，编译一个APP，用本地的私钥对APP签名，签名、pp文件、app数据形成了embedded.mobileprovision，安装到手机上
    - iOS系统的苹果公钥去验证签名就可以了

- APP启动过程

- - <https://techblog.toutiao.com/2017/01/17/iosspeed/>

  - <https://www.jianshu.com/p/0858878e331f>

  - <https://blog.cnbluebox.com/blog/2017/06/20/dyldyu-objc/>

  - pre-main (dyld：二进制加载器)

  - - 加载过程

    - - dylib loading

      - - 加载动态库，找到其Mach-O文件，打开并读取验证有效性，找到代码签名注册到内核，最后对动态库的每个segment调用mmap()

      - rebase/binding

      - - 动态库加载完成之后，它们处于相互独立的状态，需要绑定起来。在动态库加载过程中，系统为了安全考虑，引入了ASLR技术和代码签名。由于ASLR的存在，镜像（Image，包括可执行文件、动态库和bundle）会在随机的地址上加载，和之前指针指向的地址（preferred_adress）会有一个偏差，dyld需要修正这个偏差，来指向正确的地址。
        - rebase在前，bind在后，rebase做的是将镜像读入内存，修正镜像内部的指针，性能消耗主要在IO
        - bind做的是查询符号表，设置指向镜像外部的指针，性能消耗主要在CPU计算

      - Objc setup

      - - OC的runtime需要维护一张类名与类的方法列表的全局表。
        - 对所有声明过的OC类，将其注册到这个全局表中（class registration），
        - 将category的方法插入到类的方法列表中（category registration）
        - 检查每个selector的唯一性（selector uniquing）

      - initializer

      - - OC类调用+ load方法
        - C++的构造函数
        - 非基本类型的C++静态全局变量的创建

      - runtime方法生效

      - - 可执行文件中和动态库所有的符号(Class，Protocol，Selector，IMP，…)都已经按格式成功加载到内存中，被 runtime 所管理，再这之后，runtime 的那些方法(动态添加 Class、swizzle 等等才能生效)

    - 优化思路

    - - 移除不需要用到的动态库
      - 移除不需要用到的类或者方法
      - 合并功能类似的类和扩展
      - 删减一些无用的静态变量
      - 尽量避免在+load方法里执行的操作，可以推迟到+initialize方法中

  - main之后（我们重点优化的地方）

  - - didFinishLaunchingWithOptions 开始执行

    - - 初始化第三方SDK
      - 配置APP运行需要的环境
      - 自己的一些工具类的初始化
      - …

    - 优化思路

    - - 必须第一时间启动的

      - - 日志、统计等

      - 如果有广告页，有些功能可以放在广告也的viewDidAppear

      - - 项目配置、环境配置、用户信息的初始化、推送、IM等

      - 不那么必须的，可以放在首页展示成功后

      - - 其他SDK和配置事件

      - 广告页展示的时候，预先渲染主控制器的数据

- Block

- - <https://www.jianshu.com/p/9ff40ea1cee5>

  - 本质是一个OC对象

  - 与函数指针的区别

  - - 函数指针是在编译链接是已经确定好的代码块
    - block是一个OC对象，可以接受消息
    - 函数里只能访问全局变量，而block代码块不光能访问全局变量，还拥有当前栈内存和堆内存的可读性
    - 从内存的角度看，函数指针只不过是指向代码区的一段可执行代码，而block实际上是程序运行过程中在栈内存动态生成的对象，可以向其发送copy消息将block对象拷贝到堆内存，以延长其生命周期

  - __forwarding 指针，使用__block修饰的变量以后，会生成一个新的变量，这是结构体，有isa指针，forwarding指针，新变量 —>__forwarding —>原变量，其实修改值，修改的是通过这个指针去修改的原变量。

  - 与Delegate使用上的区别

  - - <https://www.zhihu.com/question/29023547>
    - 性能上：block比较耗性能，因为他是一个对象，释放和销毁都比较耗性能，而delegate只保存了一个指针。
    - 使用上：block一对一，delegate一般用于一对多

- GET和POST区别

- - <https://www.cnblogs.com/logsharing/p/8448446.html>

  - 表面上

  - - GET在浏览器回退时是无害的，而POST会再次发送请求
    - GET产生的URL地址可以被Bookmark(网页书签的意思)，而POST不可以
    - GET请求会被浏览器主动cache，而POST不会，除非手动设置
    - GET请求只能进行url编码，而POST支持多种编码方式
    - GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留
    - GET请求在URL中传送的参数是有长度限制的，而POST没有
    - 对参数的数据类型，GET只接受ASCII字符，而POST没有限制
    - GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息
    - GET参数通过URL传递，POST放在Request body中

  - 本质上

  - - 本质上并没有什么区别，底层都是TCP，但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同
    - GET产生一个TCP数据包，POST产生两个TCP数据包
    - GET，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）
    - POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）

- iOS中的锁

- - <https://www.jianshu.com/p/6c8bf19eb10d>

  - NSLock

  - - ​

  - NSRecursiveLock

  - NSCondition

  - NSConditionLock

  - pthread_mutex

  - - 互斥锁

  - pthread_rwlock

  - - 读写锁

  - POSIX Conditions

  - OSSpinLock

  - - 自旋锁

  - os_unfair_lock

  - dispatch_semaphore

  - - 信号量

  - @synchronized

  - - 互斥锁

- 加密算法相关

- - <https://blog.csdn.net/Yj_sail/article/details/52988925>

  - 编码

  - - Base64

    - - <https://www.cnblogs.com/diligenceday/p/6002382.html>
      - 编码的目的是把不可见字符转换成可见字符进行传输
      - 字符 A-Z、a-z、0-9、+ 和 / 64个可打印字符，= 来补位
      - 索引表
      - 字符串 —> ASCII —> 8bit —> 6bit —> 十进制 —> 对应编码

  - 摘要算法

  - - MD5

    - - 输入不定长度信息，输出固定长度128-bits的算法
      - MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过一系列的处理后，算法的输出由4个32位分组组成，将这个四个32位分组级联后将生成128位散列值
      - 基本运算方式为求余、取余、调整长度、与链接变量进行循环运算

    - SHA

    - - 运算方式与MD5一致，只是数据块位数和得到的散列值位数不一样

  - 对称加密

  - - AES

    - - 128位区块长度，密码长度为128、192、256

      - AES加密过程是在一个4x4的字节矩阵上运行的，这个矩阵又称为“体（state）”，其初值就是一个明文区块（矩阵中一个元素大小就是明文区块中的一个Byte），加密时，各轮AES加密循环（除最后一轮）均包含四个步骤

      - - AddRoundKey：矩阵中的每一个字节都与该次回合密钥（round key）做XOR运算；每个子密钥由密钥生成方案产生
        - SubBytes：透过一个非线性的替换函数，用查找表的方式把每个字节替换成对应的字节
        - ShiftRows：将矩阵中的每个横列进行循环式移位
        - MixColumns：为了充分混合矩阵中各个直行的操作。这个步骤使用线性转换来混合每内联的四个字节。最后一个加密循环中省略MixColumns步骤，而以另一个AddRoundKey取代

  - 非对称加密

  - - RSA 用于签名，上面已讲解，不再赘述

- 进程间通信

- - <https://blog.csdn.net/kuangdacaikuang/article/details/78891379>
  - URL Scheme
  - Keychain
  - UIPasteboard
  - UIDocumentInteractionController
  - Local socket
  - AirDrop
  - UIActivityViewController
  - App Groups

- 网络模型

- - 七层模型 （OSI）

  - - 应用层
    - 表示层
    - 会话层
    - 传输层
    - 网络层
    - 数据链路层
    - 物理层

  - 四层模型（TCP/IP）

  - - 应用层
    - 传输层
    - IP层
    - 网络接口层

- Charles抓取HTTPS原理

- - 客户端与Charles通信，Charles与服务器通信，数据均被Charles拦截

  - 过程

  - - 客户端向服务器请求证书
    - 拦截，代替客户端向服务器请求
    - 向“客户端”返回证书（这里是Charles）
    - 拦截，将自己的证书返回给客户端
    - 信任Charles证书，用Charles公钥加密对称密钥，发给“服务器”（Charles）
    - 拦截，用自己的私钥解密对称密钥，然后用服务器公钥加密后发给服务器
    - 解密对称密钥，发送响应
    - 解密报文，修改证书

- LLDB指令

- - 打印

  - - p
    - po
    - call
    - expression

  - 线程

  - - bt

  - image命令

  - - image lookup -address
    - image lookup -name
    - image lookup -type
