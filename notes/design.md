
# 参考资料

- [菜鸟教程](http://www.runoob.com/design-pattern/mediator-pattern.html)
- [图说设计模式](https://design-patterns.readthedocs.io/zh_CN/latest/index.html#)
- [CS-Notes/notes/设计模式.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F.md)


---

# 分类

## 从目的分类
- 创建型
- 结构型
- 行为型



---

## 从封装变化角度对模式分类


#### [一、组件协作](#1-组件协作)
- [1.1 Template Method](#11-template-method)
- [1.2 Observer/Event](#12-observer)
- [1.3 Stratagy](#13-strategy)

#### [二、单一职责](#2-单一职责)
- [2.1 Decorator](#21-decorator)
- [2.2 Bridge](#22-bridge)


#### [三、对象创建](#3-对象创建)
- [3.1 Factory Method](#31-factory-method)
- [3.2 Abstract Factory](#32-abstract-facotory)
- [3.3 Prototype](#33-prototype)
- [3.4 Builder](#34-builder)

#### [四、对象性能](#4-对象性能)
- [4.1 Singleton](#41-singleton)
- [Flyweight](#42-flyweight)


#### [五、接口隔离](#5-接口隔离)
- [5.1 Facade](#51-facade)
- [5.2 Proxy](#52-proxy)
- [5.3 Mediator](#53-mediator)
- [5.4 Adapter](#54-adapter)

#### [六、状态变化](#6-状态变化)
- [6.1 Memento](#61-memento)
- [6.2 State](#62-state)


#### [七、数据结构](#7-数据结构)
- [7.1 Composite](#71-compositer)
- [7.2 Iterator](#72-iterator)
- [7.3 Chain of Responsibility](#73-chain-of-Responsibility)


#### [八、行为变化](#8-行为变化)
- [8.1 Command](#81-command)
- [8.2 Visitor](#82-visitor)


#### [九、领域问题](#9-领域问题)
- [9.1 Interpreter](#91-interpreter)



[^_^]:
	一、组件协作
	[pic_template_mothed]: /pics/pattern/template_method.png
	[pic_strategy]: /pics/pattern/strategy.png
	[pic_observer]: /pics/pattern/observer.png

	
[^_^]:
	二、单一职责
	[pic_decorator]: /pics/pattern/decorator.png
	[pic_bridge]: /pics/pattern/bridge.png
	
[^_^]:
	三、对象创建
	[pic_factory]: /pics/pattern/factory.png
	[pic_ab_factory]: /pics/pattern/abstract_factory.png
	[pic_builder]: /pics/pattern/builder.png
	[pic_prototype]: https://github.com/CyC2018/CS-Notes/raw/master/pics/a40661e4-1a71-46d2-a158-ff36f7fc3331.png
	
[^_^]:	
	四、对象性能
	[pic_singleton]: /pics/pattern/singleton.png
	[pic_flyweight]: /pics/pattern/flyweight.png

	
[^_^]:
	五、接口隔离
	[pic_facade]:	/pics/pattern/facade.png
	[pic_adapter]:	/pics/pattern/adapter.png
	[pic_proxy]:	/pics/pattern/proxy.png
	[pic_mediator]: /pics/pattern/mediator.png
	
	
[^_^]:
	六、状态变化
	[pic_state]: /pics/pattern/state.png
	[pic_memento]: /pics/pattern/memento.png
	
	
[^_^]:
	七、数据结构
	[pic_composite]: /pics/pattern/composite.png
	[pic_iterator]: /pics/pattern/iterator.png
	[pic_chain_of]: /pics/pattern/chain_of.png
	
	
[^_^]:
	八、行为变化
	[pic_command]: /pics/pattern/command.png
	[pic_visitor]: /pics/pattern/visitor.png
	
[^_^]:
	九、领域问题
	[pic_interpreter]: /pics/pattern/interpreter.png
	
	
---
# 1. 组件协作

- 现在软件专业分工之后的第一个结果：“框架与应用程序的划分”，“组件协作”模式通过晚期绑定，来实现框架与应用程序直接的松耦合，是二者之间的写作时常用的模式。

## 1.1 Template Method
- 定义： 定义一个操作中的算法的骨架（稳定），而将一些步骤延迟(变化)到子类中。`Template Mehtod` 使得子类可以不改变(复用)一个算法的结构即可重定义(Override 重写)该算法的某些特定步骤。

#### 结构

![pic_template_mothed]

#### 实现
- 可以采用继承的方式，将某些通用函数作为虚函数，在子类重写；

<details><summary><b>具体实现</b></summary>

```c++
class Library{
public:
	void Run(){
		step1();
		step2();
		step3();
		step4();
	}
	virtual ~Library(){}
	
protected:
	void step1(){// ... }
	
	void step2(){ // ... }

	virtual void step3() = 0;
	virtual void step4()  = 0;  // 将这两个函数设为纯虚函数

};


class Application: public Library{

protected:
	virtual void step3(){ // ... }
	virtual void step4(){ // ... }  // 子类的具体实现
};

```
在main中：
```c++
int main(){
	Library* lib = new Application();  // 晚绑定
	lib->run();
	
	delete lib;
	
	return 0;
}
```

</details>


## 1.2 Observer
定义
- 对象间的一种一对多(变化)的依赖关系， 以便当一个对象的状态发生改变的时候，所有依赖于它的对象都得到通知并自动更新。 

例如：
在MVC模式中，假设有两个视图，一个饼图和一个电子表格，使用同一个模型的数据，如果模型更改，都需要更新两个视图。


#### 结构

![pic_observer]

#### 实现
- 定义一个Subject，和多个Observer；
- Subject:具有注册和移除、通知观察者的功能；
- Observer： 每个具体的对象执行各自的具体行为

<details><summary><b>具体实现</b></summary>

```c++
// Subject
class Observer;

class Subject{
private:
	int value;
	std::list<Observer*> observers;
	
public:
	Subject(){}
	
	void doProcess(int val){
		this->value = val+5;
		notify(value);
	
	}
	
public:
	void add(Observer o){
		observers.push_back(o);
	}
	
	void remove(Observer o){
		observers.erase(o);
	}
	
	void notify(int val){
		std::list<Observer>::iterator it = observers.begin();
		for(it;it != observers.end(); it++){
			(*it)->update(val);  // 更新
		}
	}

};


```


多个Observer ：

```c++
class Observer{
public:
	virtual void update(int val)=0;
	virtual ~Observer(){}
};


class One : public Observer{
public:
	virtual void update(int val){
		// ...
	}
};

class Two: public Observer{
public:
	virtual void update(int val){
		// ...
	}
};

```
工作流程：

```c++
int main(){
	Subject sub;
	One one;
	Two two;  // 定义多个Observer
	
	sub.add(&one);
	sub.add(&two);  // 注册
	
	int val = 3;
	sub.doProcess(val);  // 通知多个
	
	return 0;
}

```

</details>



## 1.3 Stratagy
定义：
- 定义一系列算法，把它们一个个封装起来， 并且使他们可互相替换(变化). 该模式使得算法可独立于使用它的客户程序(稳定)而变化(扩展，子类化)。

#### 结构

![pic_strategy]

#### 实现
- 子类的具体行为不同，但是继承父类的接口相同；

<details><summary><b>具体实现</b></summary>

```c++
/*
*	交税问题
*	不同的税的税率不同，定义一个父类税，子类就是不同种类的税
*/
class Context;

class Tax{
public:
	virtual double Calc(const Context& context) = 0;
	virtual ~ Tax(){}
};

class CNTax: public Tax{
public:
	virtual double Calc(const Context& context){ // ... }
};


class USTax : public Tax{
public:
	virtual double Calc(const Context& context){ // ... }
};


class DETax : public Tax{
public:
	virtual double Calc(const Context& context){ // ... }
};

```

提供另一个类来调用`Tax`类


```c++
class TaxFactory;   // 工厂函数类
class Context;

class SalesOrder{
private:
	Tax * tax;
public:
	SalesOrder(TaxFactory * taxFactory){
		tax = taxFactory->NewTax();    // 这里假设有一个工厂模式类，利用他来new一个想要的tax类
	}
	~SalesOrder(){delete tax;}
	
	double CalcTax(){
		Context context;  // 信息
		
		double val = tax->Calc(context);   // 
	
	}

};

```
</details>


# 2. 单一职责

#### 结构
- 在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随着需求的变化，子类极具膨胀，同时充斥着重复代码，这是的关键是划清楚**责任**

- 典型模式
	- Decorator 装饰器模式
	- Bridge : 桥接模式

## 2.1 Decorator

- 动态(组合)地给一个对象增加一些额外的职责，就增加功能而言， Decorator模式比生成子类（继承）更为灵活(消除重复代码 ，减小子类个数)
#### 结构

![pic_decorator]

#### 实现

- 通过对象的组合达到这种效果
- 实现一个基本的NetWorkStream类
- 通过在另一个CryptoStream类中封装一个 NetWorkStream, 在添加额外的功能

<details><summary><b>具体实现</b></summary>

```
// 基类
class stream{
public:
	virtual int read(char* buffer, int num) =0;
	
	virtual ~stream(){ }
};


// 网络流

class NetWorkStream : public stream{

public:
	virtual int read(char* buffer, int num){
		// 具体操作
	}
};


// 装饰器
class CryptoStream: public stream{

protected:
	stream *stream;  // 组合一个对象
	
public:
	CryptoStream(stream *s){
	
	}
	
	virtual int read(char* buffer, int num){
		// 加密操作
		stream->read(buffer,num);  // 内部调用
	}	

};


```

```
void process(){

	NetWorkStream* ns = NetWorkStream();
	
	CryptoStream* cs = CryptoStream(ns);
}
```



</details>

## 2.2 Bridge
- 将抽象部分（业务功能） 与实现部分(平台实现)分离， 使它们都可以独立变化；

#### 结构

![pic_bridge]

#### 实现
- 例如： 手机和pc的消息功能实现
- 建立一个通用类Message类，内部包含一个具体的实现 MessageImp 类
- 具体的实现在 MessageImp 类中

<details><summary><b>具体实现</b></summary>

```
class MessageImp;

class Messager{
protected:
	MessageImp* messageImp;

public:
	virtual ~Messager(){}
	
	virtual void Login() = 0;
	virtual void sendmsg(string msg) = 0;
	virtual void recvmsg()=0;
	
	
};



// 实现基类
class MessageImp{
public:
	virtual ~MessageImp(){}

	virtual void PlaySound() = 0; 		// 声音
	virtual void Connect() = 0;		// 连接
	virtual void WriteText() = 0;		// 文本格式

};


// 具体实现
class PCMessageImp: public MessageImp{
public:

    virtual void PlaySound(){
        //**********
    }
    virtual void Connect(){
        //**********
    }
    virtual void WriteText(){
        //**********
    }
};

// 具体实现
class MobileMessagerImp : public MessagerImp{
public:

    virtual void PlaySound(){
        //==========
    }
    virtual void Connect(){
        //==========
    }
    virtual void WriteText(){
        //==========
    }

};


// 业务功能实现
class MessagerLite: public Messager{

public:
	MessagerLite(MessageImp* m):messageImp(m)
	{}
	
	~MessagerLite(){
	}
	

    virtual void Login(){
        messagerImp->Connect();	  // 具体的连接
        //........
    }
    virtual void sendmsg(string message){
        
        messagerImp->WriteText();		// 消息发送格式
        //........
    }
	
    virtual void recvmsg (){  
        messagerImp->PlaySound();		// 收到消息的声音
        //........
    }	

};

```

```
void process(){
	// 运行时装配
	MessageImp* mimp = new PCMessageImp();
	Messager * m = new Messager(mimp);
	
}
```




</details>

# 3. 对象创建

- 通过 “对象创建”模式来绕开new，来避免对象创建过程中所导致的紧耦合（依赖具体类）,从而支持对象创建的稳定。它是接口抽象后的第一步工作。

## 3.1 Factory Method

- 定义一个用于创建对象的接口，来让子类决定实例化哪一个类。Factory Method使得一个在等的实例化延迟（目的： 解耦，手段：虚函数）到子类

#### 结构

![pic_factory]

#### 实现

- 设计一个不同类型的Splitter类

<details><summary><b>具体实现</b></summary>

```c++
class ISplitter{

public:
	virtual void split() = 0;
	virtual ~ ISplitter(){}
};


//具体类
class BinarySplitter : public ISplitter{
		//...
};

class TxtSplitter: public ISplitter{
   		//... 
};

class PictureSplitter: public ISplitter{
  		//...  
};

class VideoSplitter: public ISplitter{
 		//...   
};

```

各自的工厂类：

```c++
class SplitterFactory{
public:
	virtual ISplitter* CreateSplitter() = 0;
	virtual ~SplitterFactory(){}

};


//具体工厂
class BinarySplitterFactory: public SplitterFactory{
public:
    virtual ISplitter* CreateSplitter(){
        return new BinarySplitter();
    }
};

class TxtSplitterFactory: public SplitterFactory{
public:
    virtual ISplitter* CreateSplitter(){
        return new TxtSplitter();
    }
};

class PictureSplitterFactory: public SplitterFactory{
public:
    virtual ISplitter* CreateSplitter(){
        return new PictureSplitter();
    }
};

class VideoSplitterFactory: public SplitterFactory{
public:
    virtual ISplitter* CreateSplitter(){
        return new VideoSplitter();
    }
};

```
同一接口：

```c++
void process(SplitterFactory*  factory){

	ISplitter* spliter = facotory->CreateSplitter();  // 多态

	spliter->split();	// 多态
}
```

</details>


## 3.2 Abstract Factory
- 提供一个接口，让该接口负责创建一系列“相关或者相互依赖的对象”， 无需指定他们具体的类
- 提供一个接口，用于创建 相关的对象家族 。

#### 结构

![pic_ab_factory]

#### 实现

- 对于数据库的访问，有多种不同的数据库类型，因此设计一个抽象的数据对象创建接口 

<details><summary><b>具体实现</b></summary>

抽象的工厂基类
```c++
//数据库访问有关的基类
class IDBConnection{
    
};

class IDBCommand{
    
};

class IDataReader{
    
};


class IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
    virtual IDBCommand* CreateDBCommand()=0;
    virtual IDataReader* CreateDataReader()=0;
    
};

```

具体的工厂类

```c++
//支持SQL Server
class SqlConnection: public IDBConnection{
    
};
class SqlCommand: public IDBCommand{
    
};
class SqlDataReader: public IDataReader{
    
};


class SqlDBFactory:public IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
    virtual IDBCommand* CreateDBCommand()=0;
    virtual IDataReader* CreateDataReader()=0;
 
};

//支持Oracle
class OracleConnection: public IDBConnection{
    
};

class OracleCommand: public IDBCommand{
    
};

class OracleDataReader: public IDataReader{
    
};


class OracleBFactory:public IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
    virtual IDBCommand* CreateDBCommand()=0;
    virtual IDataReader* CreateDataReader()=0;
 
};
```
工厂的使用：

```c++
void process (IDBFactory* dbFactory;){
        IDBConnection* connection =
            dbFactory->CreateDBConnection();
		
        IDBCommand* command =
            dbFactory->CreateDBCommand();	

        IDBDataReader* reader = dbFactory->CreateDataReader();		

}
```

</details>


## 3.3 Prototype

- 使用原型实例指定要创建对象的类型，通过复制这个原型来创建新对象。

#### 结构

![pic_prototype]

#### 实现

- 为对象创建每一个副本 

<details><summary><b>具体实现</b></summary>

```c++
class Prototype{

	int a;
public:
	Prototype(){}
	Prototype(Prototype& pro):a(pro.a){}
	Prototype* clone(){ return new Prototype(*this) };
	~Prototype(){}
};

```

创建副本：

```c++
void process(){

	Prototype* proto = new Prototype();
	
	Prototype* copy_proto = proto.clone();
}

```


- 在c++的多态中，clone常常声明为virtual函数
- 因为构造函数不能是虚函数，然而，程序有时候非常需要通过传递一个指向基类对象的指针，创建派生类对象的备份。
- 常见的解决方法是：在基类钟创建一个Clone（）成员函数，并将其设置为虚函数。Clone()函数创建当前对象的备份，并返回该对象。


```c++
class base{
public:
	base(){}
	base(base& b){ 
	//内部成员的复制，可以在初始化列表中完成 
	// ...
	}
	virtual ~base(){}
	virtual base *clone(){ return new base(*this);}
};


class derived : public base{
public:
	derived(){}
	derived(derived&) d{ // ... }
	
	virtual derived* clone() { return new derived(*this); }  // 多态中的clone函数，可以将返回类型设置为子类类型

};

...


base * obj1 = new base();
base * obj2 = obj1->clone();

derived* d1 = new derived();
derived* d2 = d1.clone();

base* d3 = d1.clone();

```


#### 原型模式的优势

1. 为什么不用new直接新建对象，而要用原型模式？
- 首先，用new新建对象不能获取当前对象运行时的状态
- 其次就算new了新对象，在将当前对象的值复制给新对象，效率也不如原型模式高。

2. 为什么不直接使用拷贝构造函数，而要使用原型模式？

- 原型模式与拷贝构造函数是不同的概念，拷贝构造函数涉及的类是已知的，原型模式涉及的类可以是未知的。
- 原型模式生成的新对象可能是一个派生类。拷贝构造函数生成的新对象只能是类本身。原型模式是描述了一个通用方法(或概念)，它不管是如何实现的，而拷贝构造则是描述了一个具体实现方法。

- 参考资料 ： [C++ clone()函数的用法](https://blog.csdn.net/xiangxianghehe/article/details/78793300)

</details>


## 3.4 Builder

- 将一个复杂对象的构建和其表示相分离，使得同样的构建过程(稳定)可以创建不同的表示方法；

- 封装一个对象的构造过程，并允许按步骤构造。

#### 结构

![pic_builder]

#### 实现

- 实现一栋房子的修建过程，过程是一样的，但是具体的修建方法不一样

<details><summary><b>具体实现</b></summary>

基类的实现：
```c++

class House{
    //....
};

class HouseBuilder {
public:
    House* GetResult(){
        return pHouse;
    }
    virtual ~HouseBuilder(){}
protected:
    
    House* pHouse;
	virtual void BuildPart1()=0;
    virtual void BuildPart2()=0;
    virtual void BuildPart3()=0;	
};


```

不同的子类的实现：

```c++
class StoneHouse: public House{
    
};

class StoneHouseBuilder: public HouseBuilder{
protected:
    
    virtual void BuildPart1(){
        //pHouse->Part1 = ...;
    }
    virtual void BuildPart2(){
        
    }
    virtual void BuildPart3(){
        
    }
    virtual void BuildPart4(){
        
    }
    virtual void BuildPart5(){
        
    }
    
};

```

完成过程为：

```c++

void process(HouseBuilder* housebuilder){

	housebuilder->BuildPart1();
	housebuilder->BuildPart2();
	housebuilder->BuildPart3();
	housebuilder->BuildPart4();
	housebuilder->BuildPart5();

}

```

</details>


# 4. 对象性能

## 4.1 Singleton

- 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

#### 注意
1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

#### 介绍

**意图**：保证一个类仅有一个实例，并提供一个访问它的全局访问点。
**主要解决**：一个全局使用的类频繁地创建与销毁。
**何时使用**：当您想控制实例数目，节省系统资源的时候。
**如何解决**：判断系统是否已经有这个单例，如果有则返回，如果没有则创建。
**关键代码**：构造函数是私有的。

**优点**： 1、在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。 2、避免对资源的多重占用（比如写文件操作）。
**缺点**：没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。


#### 结构
- 实现一个`singleObject`,有它的私有构造函数和本身的一个静态实例；

#### 实现

![pic_singleton]

<details><summary><b>具体实现</b></summary>

```c++
class SingleObject{
private:

	SingleObject();
	SingleObject(const SalesOrder& other);

	static SingleObject* instance ;

public:
	static SingleObject* getInstance();
	
	void showMessage(){
		cout<<"Hello,world"<<endl;
	}
};


SingleObject* SingleObject::getInstance(){
	// 非线程安全
	if(instance == nullptr)
	{
		instance = new SingleObject();
	}
	return instance;
}

```

单例模式的实现方式：
1. 懒汉式，线程不安全
- 如上面实现，不支持多线程

2. 懒汉式，线程安全
- 加锁,代价比较高


```C++

SingleObject* SingleObject::getInstance(){
	Lock lock;
	if(instance == nullptr)
	{
		instance = new SingleObject();
	}
	return instance;
}

```


3. 双检查锁
- //双检查锁，但由于内存读写reorder不安全


```c++

SingleObject* SingleObject::getInstance(){
    if(instance==nullptr){
        Lock lock;
        if (instance == nullptr) {
            instance = new Singleton();
        }
    }
}

```


</details>

## 4.2 Flyweight

- [享元模式](http://www.runoob.com/design-pattern/flyweight-pattern.html)

- 享元模式： 运用共享技术有效地支持大量细粒度的对象。

- 主要解决：在有大量对象时，有可能会造成内存溢出，我们把其中共同的部分抽象出来，如果有相同的业务请求，直接返回在内存中已有的对象，避免重新创建。

#### 结构

![pic_flyweight]


#### 实现

- 实现一个 Font 功能，系统中存在唯一地Font对象，使用键值存储Font,   使用factory类来返回对应关键字的Font对象

<details><summary><b>具体实现</b></summary>

```c++
class Font{
private:
	string key;
	
public:
	Font(string & key){
		// ...
	}
};


class FontFactory{
private:
	map<string, Font*> fontpool;

public:
Font* GetFont(string& key){

	map<string, Font*> :: iterator it = fontpool.find(key);
	if(it != fontpool.end())
	{
		return fontpool[key];
	}
	else{
		Font* font = new Font(key);
		fontpool[key] = font;
		return font;
	}

}

};


```


</details>


# 5. 接口隔离

- 在组件构建过程中，某些接口之间直接的依赖常常会带来很多问题，甚至根本无法实现，采用添加一层间接（稳定）的接口，来隔离本来互相紧密关联的接口是一种常用的解决方法。

## 5.1 Facade

- [外观模式](http://www.runoob.com/design-pattern/facade-pattern.html)

- 为子系统的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这个子系统更加容易使用(复用)；

- 主要解决：降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口。

#### 结构

![pic_facade]

Facade: 外观角色
SubSystem:子系统角色

- [图来自](https://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/facade.html)

#### 实现

- 实现 

<details><summary><b>具体实现</b></summary>

子系统：

```c++

class SystemA{
public:
	void operationA(){
		// ....
	}
};

class SystemB{
public:
	void operationB(){
		// ....
	}
};

class SystemA{
public:
	void operationC(){
		// ....
	}
};

```


```c++

class Facade{

public:
	Facade(){
		m_SystemA  = new SystemA();
		m_SystemB = new SystemB();
		m_SystemC = new SystemC();		
	}
	
	~Facade(){
		delete m_SystemA;
		delete m_SystemB;
		delete m_SystemC;	
	}
	
	void wrapOpration(){
		m_SystemA->operationA();
		m_SystemB->operationB();
		m_SystemC->opeartionC();
	}	
		
private:
	SystemC *m_SystemC;
	SystemA *m_SystemA;
	SystemB *m_SystemB;

};


```


</details>

## 5.2 Proxy

- [代理模式](http://www.runoob.com/design-pattern/proxy-pattern.html)

- 意图：为其他对象提供一种代理以控制对这个对象的访问。
- 主要解决：在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。

#### 结构

![pic_proxy]

#### 实现

- client访问proxy

<details><summary><b>具体实现</b></summary>

```c++
class ISubject{

public:
	virtual void process(){}
	virtual ~ISubject(){}
};

class SubProxy: public ISubject{

public:
	virtual void porocess() {
		// 对RealSubject 的间接访问
	}

	
public:
	ISubject* subject;
	
};

```

</details>


---

## 5.3 Mediator

- [中介者模式](http://www.runoob.com/design-pattern/mediator-pattern.html)

- 意图：用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。
- 主要解决：对象与对象之间存在大量的关联关系，这样势必会导致系统的结构变得很复杂，同时若一个对象发生改变，我们也需要跟踪与之相关联的对象，同时做出相应的处理。
- 何时使用：多个类相互耦合，形成了网状结构。
- 如何解决：将上述网状结构分离为星型结构。
- 关键代码：对象 Colleague 之间的通信封装到一个类中单独处理。


#### 结构

![pic_mediator]

Mediator: 抽象中介者
ConcreteMediator: 具体中介者
Colleague: 抽象同事类
ConcreteColleague: 具体同事类

#### 实现

- [2. 中介者模式](https://design-patterns.readthedocs.io/zh_CN/latest/behavioral_patterns/mediator.html)

- 多个同事通过中间人通话


<details><summary><b>具体实现</b></summary>

```c++

class Colleague{
private:
	Mediator* m;
public:
	virtual ~Colleague(){}
	virtual void sndMsg(int a){// ...}
	virtual void revMsg(string str){ // ... }
	
	virtual void setMediator(Mediator* med){ m = med; }
};


class Mediator{
private:
	map<int, Colleague*> colleaguepool;
public:
	virtual ~Mediator(){}
	virtual void operation(int num, string str){ // ... };
	virtual void register(int num, Colleague* col){// ... }
	
};



class concreteMediator : public Mediator{

public:
	virtual void register(int num, Colleague* col){
		map<int,Colleague*>::const_iterator itr = colleaguepool.find(num);
		if(itr == colleaguepool.end())
		{
			colleaguepool.insert(make_pair(num,col));
			//同时将中介类暴露给colleague 
			col->setMediator(this);
		}		
	
	}
	
	virtual void operation(int num, string str){
		map<int,Colleague*>::const_iterator itr = colleaguepool.find(num);
		if(itr == colleaguepool.end())
		{
			cout << "not found this colleague!" << endl;
			return;
		}
		
		Colleague* pc = itr->second;
		pc->revMsg(str);	
	
	}
	

};


class concreteColleague: public Colleague{

public:
	virtual void SndMsg(int num, string str){
		cout << "send msg from colleagueA,to:" << num << endl;
		m->operation(num,str);	
	}
	
	virtual void RevMsg(string str){
		cout << "ConcreteColleagueA reveivemsg:" << str <<endl;	
	}

};




```

过程：

```c++
void process () {

	ConcreteColleague * pa = new ConcreteColleague();
	ConcreteColleague * pb = new ConcreteColleague();
	ConcreteMediator * pm = new ConcreteMediator();
	pm->registered(1,pa);
	pm->registered(2,pb);
	
	// sendmsg from a to b
	pa->sendmsg(2,"hello,i am a");
	// sendmsg from b to a
	pb->sendmsg(1,"hello,i am b");
	
	delete pa,pb,pm;
}

```


</details>


---

## 5.4 Adapter

- [适配器模式](http://www.runoob.com/design-pattern/adapter-pattern.html)

- 意图：将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
- 主要解决：主要解决在软件系统中，常常要将一些"现存的对象"放到新的环境中，而新环境要求的接口是现对象不能满足的。

- 如何解决：继承或依赖（推荐）。
- 关键代码：适配器继承或依赖已有的对象，实现想要的目标接口。

#### 结构

![pic_adapter]

#### 实现

<details><summary><b>具体实现</b></summary>

```c++
// 新接口
class ITarget{

public:
	virtual void process ()= 0;
	virtual ~ITarget(){}
};


// 遗留接口

class IAdaptee{

public:
	virtual void foo(int data ){ //... }
	virtual int bar(){//...}
};




// 适配器

class Adapter: public ITarget{

protected:
	IAdaptee * adaptee;
	
public:
	Adapter(IAdaptee* ada){
		adaptee = ada;
	}
	
	virtual void process(){
		int data = adaptee->bar();
		adaptee->foo(data);
	}

};


```


</details>



---

# 6. 状态变化

- 在组建构建过程中， 某些对象的状态经常面临变化，如何对这些变化进行有效的管理？同时又维持高层模块的稳定？“状态变化”模式为这一问题提供了一种解决方案；


## 6.1 Memento

- [ 备忘录](http://www.runoob.com/design-pattern/memento-pattern.html)


- **意图**：在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。
- **主要解决**：所谓备忘录模式就是在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样可以在以后将对象恢复到原先保存的状态。

- **如何解决**：通过一个备忘录类专门存储对象状态。
- **关键代码**：客户不与备忘录类耦合，与备忘录管理类耦合。


#### 结构

![pic_memento]

备忘录模式使用三个类 Memento、Originator 和 CareTaker。
- Memento 包含了要被恢复的对象的状态。
- Originator 创建并在 Memento 对象中存储状态。
- Caretaker 对象负责从 Memento 中恢复对象的状态。


#### 实现


<details><summary><b>具体实现</b></summary>

```c++
class Memento {

private: 
	string state;
public:
	Memento(const string& s):state(s){}
	string getState(){ return state;}
	void setSate(const string& s){state = s;}
};


class Originator{

private:
	string state;

public:
	Originator(){}
	Memento createMemento(){
		Memento m(state);
		return m;
	}
	
	void setMemento(Memento m){
		state = m.getState();
	}

};


```

使用如下：

```c++
void process(){
	Originator origin;
	// 创建一个备忘录
	Memento m = origin.createMemento();
	
	// ... 一些操作
	
	// 恢复
	origin.setMemento(m);
}
```

</details>


## 6.2 State

- [状态模式](http://www.runoob.com/design-pattern/state-pattern.html)

- **意图**：允许对象在内部状态发生改变时改变它的行为，对象看起来好像修改了它的类。
- **主要解决**：对象的行为依赖于它的状态（属性），并且可以根据它的状态改变而改变它的相关行为。
- **何时使用**：代码中包含大量与对象状态有关的条件语句。
- **如何解决**：将各种具体的状态类抽象出来。

#### 结构

![pic_state]

#### 实现

创建一个 State 接口和实现了 State 接口的实体状态类。Context 是一个带有某个状态的类。

<details><summary><b>具体实现</b></summary>


```c++
class NetState{

public:
	virtual void handle(){ //.. }
	virtual ~NetState(){}
};

class openState: public NetState{

public:
	virtual void handle(){
		// ...
	}
};

class closeState: public NetState{
public:
	virtual void handle(){
	
	// ...
}

};

```
定义一个context类：

```c++
class Context{

private:
	State * state;
	
public:
	Context(State* s):state(s){
	
	}

	void request(){
		state.request();
	}

};

```


</details>



# 7. 数据结构
- 常常有一些组件在内部具有特定的数据结构，如果让客户程序依赖这些特定的数据结构，将极大的破坏组件的复用。这个时候，将这些特定的数据结构封装在内部，在外部提供统一的接口，来实现特定数据结构无关的访问，是一种有效的解决方案。


## 7.1 Composite

- [组合模式](http://www.runoob.com/design-pattern/composite-pattern.html)

- **意图**：将对象组合成树形结构以表示"部分-整体"的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。
- **主要解决**：它在我们树型结构的问题中，模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样来处理复杂元素，从而使得客户程序与复杂元素的内部结构解耦。


- **如何解决**：树枝和叶子实现统一接口，树枝内部组合该接口。
- **关键代码**：树枝内部组合该接口，并且含有内部属性 List，里面放 Component。


#### 结构

![pic_composite]


组件（Component）类是组合类（Composite）和叶子类（Leaf）的父类，可以把组合类看成是树的中间节点。

组合对象拥有一个或者多个组件对象，因此组合对象的操作可以委托给组件对象去处理，而组件对象可以是另一个组合对象或者叶子对象。

#### 实现

<details><summary><b>具体实现</b></summary>


```c++
class Component{

public:
	virtual ~Component(){}
	virtual void process() = 0;

};


class Composite : public Component{
private:
	string name;
	List<Component*> elements;
	
public:
    Composite(const string & s) : name(s) {}
    
    void add(Component* element) {
        elements.push_back(element);
    }
    void remove(Component* element){
        elements.remove(element);
    }
    
    void process(){
        
        //1. process current node
        
        
        //2. process leaf nodes
        for (auto &e : elements)
            e->process(); //多态调用      
    }	

};

//叶子节点
class Leaf : public Component{
    string name;
public:
    Leaf(string s) : name(s) {}
            
    void process(){
        //process current node
    }
};


```

使用：

```c++
void process(){
    Composite root("root");
    Composite treeNode1("treeNode1");
    Composite treeNode2("treeNode2");
    Composite treeNode3("treeNode3");
    Composite treeNode4("treeNode4");
    Leaf leat1("left1");
    Leaf leat2("left2");
    
    root.add(&treeNode1);
    treeNode1.add(&treeNode2);
    treeNode2.add(&leaf1);
    
    root.add(&treeNode3);
    treeNode3.add(&treeNode4);
    treeNode4.add(&leaf2);

}

```


</details>


## 7.2 Iterator

- [迭代器模式](http://www.runoob.com/design-pattern/iterator-pattern.html)


- **意图**：提供一种方法顺序访问一个聚合对象中各个元素, 而又无须暴露该对象的内部表示。
- **主要解决**：不同的方式来遍历整个整合对象。
- **何时使用**：遍历一个聚合对象。
- **如何解决**：把在元素之间游走的责任交给迭代器，而不是聚合对象。
- **关键代码**：定义接口：hasNext, next。


#### 结构

![pic_iterator]


Aggregate 是聚合类，其中 createIterator() 方法可以产生一个 Iterator；
Iterator 主要定义了一些方法；
Client 组合了 Aggregate，为了迭代遍历 Aggregate，也需要组合 Iterator。

#### 实现

<details><summary><b>具体实现</b></summary>


```c++
template<typename T>
class Iterator
{
public:
    virtual void first() = 0;
    virtual void next() = 0;
    virtual bool isDone() const = 0;
    virtual T& current() = 0;
};



template<typename T>
class MyCollection{
    
public:
    
    Iterator<T> GetIterator(){
        //...
    }
    
};

template<typename T>
class CollectionIterator : public Iterator<T>{
    MyCollection<T> mc;
public:
    
    CollectionIterator(const MyCollection<T> & c): mc(c){ }
    
    void first() override {
        
    }
    void next() override {
        
    }
    bool isDone() const override{
        
    }
    T& current() override{
        
    }
};


```


</details>


## 7.3 Chain of Responsibility

- [责任链模式](http://www.runoob.com/design-pattern/chain-of-responsibility-pattern.html)

- **意图**：避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。
- **主要解决**：职责链上的处理者负责处理请求，客户只需要将请求发送到职责链上即可，无须关心请求的处理细节和请求的传递，所以职责链将请求的发送者和请求的处理者解耦了。
- **何时使用**：在处理消息的时候以过滤很多道。
- **如何解决**：拦截的类都实现统一接口。


#### 结构

![pic_chain_of]

#### 实现

<details><summary><b>具体实现</b></summary>


```c++

class Request{
private:
	string name;
	
public:
	Request(string& str):name(str){}
	void doSomething(){ // ... }

};


class Handler{
private:
	Handler* chain;

public:
	virtual HandlerRequest(const Request& req) = 0;
	virtual ~Handler(){}

};


class ConcreteHandlerA: public Handler{

public:
	

};


```


</details>





# 8. 行为变化

## 8.1 Command

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 8.2 Visitor

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

# 9. 领域问题
## 9.1 Interpreter
#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

 