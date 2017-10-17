# 代理模式
### 什么是代理模式？
![](http://owvsetuqu.bkt.clouddn.com/image/designpattern/Proxy1.jpg)
代理模式是常用的结构型设计模式之一，当无法直接访问某个对象或访问某个对象存在困难时可以通过一个代理对象来间接访问，为了保证客户端使用的透明性，所访问的真实对象与代理对象需要实现相同的接口。
**概念：** 给某一个对象提供一个代理，并由代理对象控制对原对象的引用。
**角色：**
- Subject（抽象主题角色） --> 声明了真实主题和代理主题的公共接口，这样一来在使用真实主题的任何地方都可以使用代理主题。
- Proxy（代理主题角色） --> 代理主题角色内部含有对真实主题的引用，从而可以操作真实主题对象；代理主题角色负责在需要的时候创建真实主题对象；代理角色通常在将客户端调用传递到真实主题之前或之后，都要执行一些其他的操作，而不是单纯地将调用传递给真实主题对象。
- RealSubject（真实主题角色） --> 定义了代理角色所代表的真是对象。

**分类：**
根据代理模式的使用目的不同，代理模式又可以分为多种类型：
- 远程（Remote）代理：为一个位于不同的地址空间的对象提供一个局域代表对象。这个不同的地址空间可以是本电脑中，也可以在另一台电脑中。最典型的例子就是——客户端调用Web服务或WCF服务。
- 虚拟（Virtual）代理：根据需要创建一个资源消耗较大的对象，使得对象只在需要时才会被真正创建。
- Copy-on-Write代理：虚拟代理的一种，把复制（或者叫克隆）拖延到只有在客户端需要时，才真正采取行动。
- 保护（Protect or Access）代理：控制一个对象的访问，可以给不同的用户提供不同级别的使用权限。
- 防火墙（Firewall）代理：保护目标不让恶意用户接近。
- 智能引用（Smart Reference）代理：当一个对象被引用时，提供一些额外的操作，比如将对此对象调用的次数记录下来等。
- Cache代理：为某一个目标操作的结果提供临时的存储空间，以便多个客户端可以这些结果。

### 代码实现
以网上外卖商店为例，网上商店为线下实体店的代理：
![](http://owvsetuqu.bkt.clouddn.com/image/designpattern/Proxy2.png)

 	/// <summary>
    /// 抽象主题角色
    /// </summary>
    public abstract class Shop
    {
        /// <summary>
        /// 出售
        /// </summary>
        public abstract void Sell();
    }

	/// <summary>
    /// 线下实体商店
    /// </summary>
    public class RealShop : Shop
    {
        public override void Sell()
        {
            Console.WriteLine("卖了一份黄焖鸡米饭");
        }
    }

  	/// <summary>
    /// 网上商店
    /// </summary>
    public class InternetShop : Shop
    {
        // 引用真实主题实例
        RealShop realShop;

        /// <summary>
        /// 通过代理类访问真实实体对象的方法
        /// </summary>
        public override void Sell()
        {
            if (realShop == null)
            {
                realShop = new RealShop();
            }

            this.PreSell();

            // 调用真实主题方法
            realShop.Sell();

            this.PostSell();
        }

        // 代理角色执行的一些操作
        public void PreSell()
        {
            // 商品上架
            Console.WriteLine("线上创建商店，上传商品列表");
        }

        // 代理角色执行的一些操作
        public void PostSell()
        {
            Console.WriteLine("等待外卖骑手送货");
        }
    }

	/// <summary>
    /// 调用
    /// </summary>
    /// <param name="args"></param>
    static void Main(string[] args)
    {
        // 创建一个代理对象并发出请求
        InternetShop proxy = new InternetShop();
        proxy.Sell();

        Console.ReadLine();
    }

### 代理模式的优缺点
**优点：**
- 代理模式能够将调用用于真正被调用的对象隔离，在一定程度上降低了系统的耦合度；
- 代理对象在客户端和目标对象之间起到一个中介的作用，这样可以起到对目标对象的保护。代理对象可以在对目标对象发出请求之前进行一个额外的操作，例如权限检查等。

**缺点：**
- 由于在客户端和真实主题之间增加了一个代理对象，所以会造成请求的处理速度变慢
- 实现代理类也需要额外的工作，从而增加了系统的实现复杂度。

### 代理模式的应用场景
- 当客户端对象需要访问远程主机中的对象时可以使用远程代理。
- 当需要用一个消耗资源较少的对象来代表一个消耗资源较多的对象，从而降低系统开销、缩短运行时间时可以使用虚拟代理，例如一个对象需要很长时间才能完成加载时。
- 当需要为某一个被频繁访问的操作结果提供一个临时存储空间，以供多个客户端共享访问这些结果时可以使用缓冲代理。通过使用缓冲代理，系统无须在客户端每一次访问时都重新执行操作，只需直接从临时缓冲区获取操作结果即可。
- 当需要控制对一个对象的访问，为不同用户提供不同级别的访问权限时可以使用保护代理。
- 当需要为一个对象的访问（引用）提供一些额外的操作时可以使用智能引用代理。

在实际开发过程中，我们在客户端添加服务引用的时候，在客户程序中会添加一些额外的类，在客户端生成的类扮演着代理主题角色，我们客户端也是直接调用这些代理角色来访问远程服务提供的操作。这个是远程代理的一个典型例子。