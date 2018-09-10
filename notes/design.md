
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
- [模板模式](http://www.runoob.com/design-pattern/template-pattern.html)

- **意图**：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
- **主要解决**：一些方法通用，却在每一个子类都重新写了这一方法。
- **何时使用**：有一些通用的方法。
- **如何解决**：将这些通用算法抽象出来。
- **关键代码**：在抽象类实现，其他步骤在子类实现。


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

- [观察者模式](http://www.runoob.com/design-pattern/observer-pattern.html)


- **意图**：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。
- **主要解决**：一个对象状态改变给其他对象通知的问题，而且要考虑到易用和低耦合，保证高度的协作。
- **何时使用**：一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。
- **如何解决**：使用面向对象技术，可以将这种依赖关系弱化。
- **关键代码**：在抽象类里有一个 ArrayList 存放观察者们。


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

- [策略模式](http://www.runoob.com/design-pattern/strategy-pattern.html)

- **意图**：定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。
- **主要解决**：在有多种算法相似的情况下，使用 if...else 所带来的复杂和难以维护。
- **何时使用**：一个系统有许多许多类，而区分它们的只是他们直接的行为。
- **如何解决**：将这些算法封装成一个一个的类，任意地替换。
- **关键代码**：实现同一个接口。

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

- [装饰器模式](http://www.runoob.com/design-pattern/decorator-pattern.html)

- **意图**：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
- **主要解决**：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。
- **何时使用**：在不想增加很多子类的情况下扩展类。
- **如何解决**：将具体功能职责划分，同时继承装饰者模式。


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

- [桥接模式](http://www.runoob.com/design-pattern/bridge-pattern.html)

- **意图**：将抽象部分与实现部分分离，使它们都可以独立的变化。
- **主要解决**：在有多种可能会变化的情况下，用继承会造成类爆炸问题，扩展起来不灵活。
- **何时使用**：实现系统可能有多个角度分类，每一种角度都可能变化。
- **如何解决**：把这种多角度分类分离出来，让它们独立变化，减少它们之间耦合。
- **关键代码**：抽象类依赖实现类。



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

- [工厂模式](http://www.runoob.com/design-pattern/factory-pattern.html)


- **意图**：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
- **主要解决**：主要解决接口选择的问题。
- **何时使用**：我们明确地计划不同条件下创建不同实例时。
- **如何解决**：让其子类实现工厂接口，返回的也是一个抽象的产品。
- **关键代码**：创建过程在其子类执行。

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

- [抽象工厂模式](http://www.runoob.com/design-pattern/abstract-factory-pattern.html)

- **意图**：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
- **主要解决**：主要解决接口选择的问题。
- **何时使用**：系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。
- **如何解决**：在一个产品族里面，定义多个产品。
- **关键代码**：在一个工厂里聚合多个同类产品。

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

- [原型模式](http://www.runoob.com/design-pattern/prototype-pattern.html)

- **意图**：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
- **主要解决**：在运行期建立和删除原型。
- **何时使用**： 1、当一个系统应该独立于它的产品创建，构成和表示时。 2、当要实例化的类是在运行时刻指定时，例如，通过动态装载。 3、为了避免创建一个与产品类层次平行的工厂类层次时。 4、当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。
- **如何解决**：利用已有的一个原型对象，快速地生成和原型对象一样的实例。

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

- [建造者模式](http://www.runoob.com/design-pattern/builder-pattern.html)

- **意图**：将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。
- **主要解决**：主要解决在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起的算法却相对稳定。
- **何时使用**：一些基本部件不会变，而其组合经常变化的时候。
- **如何解决**：将变与不变分离开。
- **关键代码**：建造者：创建和提供实例，导演：管理建造出来的实例的依赖关系。

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

- [单例模式](http://www.runoob.com/design-pattern/singleton-pattern.html)

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

- **意图**：运用共享技术有效地支持大量细粒度的对象。
- **主要解决**：在有大量对象时，有可能会造成内存溢出，我们把其中共同的部分抽象出来，如果有相同的业务请求，直接返回在内存中已有的对象，避免重新创建。
- **何时使用**： 1、系统中有大量对象。 2、这些对象消耗大量内存。 3、这些对象的状态大部分可以外部化。 4、这些对象可以按照内蕴状态分为很多组，当把外蕴对象从对象中剔除出来时，每一组对象都可以用一个对象来代替。 5、系统不依赖于这些对象身份，这些对象是不可分辨的。
- **如何解决**：用唯一标识码判断，如果在内存中有，则返回这个唯一标识码所标识的对象。
- **关键代码**：用 HashMap 存储这些对象。

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

- **意图**：为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。
- **主要解决**：降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口。
- **何时使用**： 1、客户端不需要知道系统内部的复杂联系，整个系统只需提供一个"接待员"即可。 2、定义系统的入口。
- **如何解决**：客户端不与系统耦合，外观类与系统耦合。

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

- **意图**：为其他对象提供一种代理以控制对这个对象的访问。
- **主要解决**：在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。
- **何时使用**：想在访问一个类时做一些控制。
- **如何解决**：增加中间层。
- **关键代码**：实现与被代理类组合。

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

- **意图**：用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。
- **主要解决**：对象与对象之间存在大量的关联关系，这样势必会导致系统的结构变得很复杂，同时若一个对象发生改变，我们也需要跟踪与之相关联的对象，同时做出相应的处理。
- **何时使用**：多个类相互耦合，形成了网状结构。
- **如何解决**：将上述网状结构分离为星型结构。
- **关键代码**：对象 Colleague 之间的通信封装到一个类中单独处理。


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
#include <iostream>
#include <string>

using namespace std;

enum class RequestType
{
    REQ_HANDLER1,
    REQ_HANDLER2,
    REQ_HANDLER3
};

class Reqest
{
    string description;
    RequestType reqType;
public:
    Reqest(const string & desc, RequestType type) : description(desc), reqType(type) {}
    RequestType getReqType() const { return reqType; }
    const string& getDescription() const { return description; }
};

class ChainHandler{
    
    ChainHandler *nextChain;
    void sendReqestToNextHandler(const Reqest & req)
    {
        if (nextChain != nullptr)
            nextChain->handle(req);
    }
protected:
    virtual bool canHandleRequest(const Reqest & req) = 0;
    virtual void processRequest(const Reqest & req) = 0;
public:
    ChainHandler() { nextChain = nullptr; }
    void setNextChain(ChainHandler *next) { nextChain = next; }
    
   
    void handle(const Reqest & req)
    {
        if (canHandleRequest(req))
            processRequest(req);
        else
            sendReqestToNextHandler(req);
    }
};


class Handler1 : public ChainHandler{
protected:
    bool canHandleRequest(const Reqest & req) override
    {
        return req.getReqType() == RequestType::REQ_HANDLER1;
    }
    void processRequest(const Reqest & req) override
    {
        cout << "Handler1 is handle reqest: " << req.getDescription() << endl;
    }
};
        
class Handler2 : public ChainHandler{
protected:
    bool canHandleRequest(const Reqest & req) override
    {
        return req.getReqType() == RequestType::REQ_HANDLER2;
    }
    void processRequest(const Reqest & req) override
    {
        cout << "Handler2 is handle reqest: " << req.getDescription() << endl;
    }
};

class Handler3 : public ChainHandler{
protected:
    bool canHandleRequest(const Reqest & req) override
    {
        return req.getReqType() == RequestType::REQ_HANDLER3;
    }
    void processRequest(const Reqest & req) override
    {
        cout << "Handler3 is handle reqest: " << req.getDescription() << endl;
    }
};

int main(){
    Handler1 h1;
    Handler2 h2;
    Handler3 h3;
    h1.setNextChain(&h2);
    h2.setNextChain(&h3);
    
    Reqest req("process task ... ", RequestType::REQ_HANDLER3);
    h1.handle(req);
    return 0;
}

```



</details>





# 8. 行为变化

- 在组件的构建过程中，组件行为的变化经常导致组件本身剧烈的变化。“行为变化”模式将组件的行为和组件本身进行解耦，从而支持组件行为的变化，实现两者之间的松耦合。

## 8.1 Command

- [命令模式](http://www.runoob.com/design-pattern/command-pattern.html)


- **意图**：将一个请求封装成一个对象，从而使您可以用不同的请求对客户进行参数化。
- **主要解决**：在软件系统中，行为请求者与行为实现者通常是一种紧耦合的关系，但某些场合，比如需要对行为进行记录、撤销或重做、事务等处理时，这种无法抵御变化的紧耦合的设计就不太合适。
- **何时使用**：在某些场合，比如要对行为进行"记录、撤销/重做、事务"等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，如何将"行为请求者"与"行为实现者"解耦？将一组行为抽象为对象，可以实现二者之间的松耦合。
- **如何解决**：通过调用者调用接受者执行命令，顺序：调用者→接受者→命令。
- **关键代码**：定义三个角色：1、received 真正的命令执行对象 2、Command 3、invoker 使用命令对象的入口

#### 结构

![pic_command]


Command：命令
Receiver：命令接收者，也就是命令真正的执行者
Invoker：通过它来调用命令
Client：可以设置命令与命令的接收者

#### 实现

<details><summary><b>具体实现</b></summary>

```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;


class Command
{
public:
    virtual void execute() = 0;
};

class ConcreteCommand1 : public Command
{
    string arg;
public:
    ConcreteCommand1(const string & a) : arg(a) {}
    void execute() override
    {
        cout<< "#1 process..."<<arg<<endl;
    }
};

class ConcreteCommand2 : public Command
{
    string arg;
public:
    ConcreteCommand2(const string & a) : arg(a) {}
    void execute() override
    {
        cout<< "#2 process..."<<arg<<endl;
    }
};
        
        
class MacroCommand : public Command
{
    vector<Command*> commands;
public:
    void addCommand(Command *c) { commands.push_back(c); }
    void execute() override
    {
        for (auto &c : commands)
        {
            c->execute();
        }
    }
};
        

        
int main()
{

    ConcreteCommand1 command1(receiver, "Arg ###");
    ConcreteCommand2 command2(receiver, "Arg $$$");
    
    MacroCommand macro;
    macro.addCommand(&command1);
    macro.addCommand(&command2);
    
    macro.execute();

}


```

</details>

## 8.2 Visitor

- [访问者模式](http://www.runoob.com/design-pattern/visitor-pattern.html)

- **意图**：主要将数据结构与数据操作分离。
- **主要解决**：稳定的数据结构和易变的操作耦合问题。
- **何时使用**：需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，使用访问者模式将这些封装到类中。
- **如何解决**：在被访问的类里面加一个对外提供接待访问者的接口。
- **关键代码**：在数据基础类里面有一个方法接受访问者，将自身引用传入访问者。

#### 结构

![pic](https://github.com/CyC2018/CS-Notes/raw/master/pics/ec923dc7-864c-47b0-a411-1f2c48d084de.png)

#### 实现

<details><summary><b>具体实现</b></summary>

```c++
#include <iostream>
using namespace std;

class Visitor;


class Element
{
public:
    virtual void accept(Visitor& visitor) = 0; //第一次多态辨析

    virtual ~Element(){}
};

class ElementA : public Element
{
public:
    void accept(Visitor &visitor) override {
        visitor.visitElementA(*this);
    }
    

};

class ElementB : public Element
{
public:
    void accept(Visitor &visitor) override {
        visitor.visitElementB(*this); //第二次多态辨析
    }

};


class Visitor{
public:
    virtual void visitElementA(ElementA& element) = 0;
    virtual void visitElementB(ElementB& element) = 0;
    
    virtual ~Visitor(){}
};

//==================================

//扩展1
class Visitor1 : public Visitor{
public:
    void visitElementA(ElementA& element) override{
        cout << "Visitor1 is processing ElementA" << endl;
    }
        
    void visitElementB(ElementB& element) override{
        cout << "Visitor1 is processing ElementB" << endl;
    }
};
     
//扩展2
class Visitor2 : public Visitor{
public:
    void visitElementA(ElementA& element) override{
        cout << "Visitor2 is processing ElementA" << endl;
    }
    
    void visitElementB(ElementB& element) override{
        cout << "Visitor2 is processing ElementB" << endl;
    }
};
        
    

        
int main()
{
    Visitor2 visitor;
    ElementB elementB;
    elementB.accept(visitor);// double dispatch
    
    ElementA elementA;
    elementA.accept(visitor);

    
    return 0;
}



```


</details>



# 9. 领域问题

- 在特定的领域中，某些变化虽然频繁，但可以抽象为某种规则，这时候，结合特定领域，将问题抽象为语法规则，从而给出在该领域的一般性解决方案。

## 9.1 Interpreter

- [解释器模式](http://www.runoob.com/design-pattern/interpreter-pattern.html)

- **意图**：给定一个语言，定义它的文法表示，并定义一个解释器，这个解释器使用该标识来解释语言中的句子。
- **主要解决**：对于一些固定文法构建一个解释句子的解释器。
- **何时使用**：如果一种特定类型的问题发生的频率足够高，那么可能就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。
- **如何解决**：构件语法树，定义终结符与非终结符。
- **关键代码**：构件环境类，包含解释器之外的一些全局信息，一般是 HashMap。

#### 结构

![](https://github.com/CyC2018/CS-Notes/raw/master/pics/794239e3-4baf-4aad-92df-f02f59b2a6fe.png)

TerminalExpression：终结符表达式，每个终结符都需要一个 TerminalExpression。
Context：上下文，包含解释器之外的一些全局信息。

#### 实现

<details><summary><b>具体实现</b></summary>

```c++


#include <iostream>
#include <map>
#include <stack>

using namespace std;

class Expression {
public:
    virtual int interpreter(map<char, int> var)=0;
    virtual ~Expression(){}
};

//变量表达式
class VarExpression: public Expression {
    
    char key;
    
public:
    VarExpression(const char& key)
    {
        this->key = key;
    }
    
    int interpreter(map<char, int> var) override {
        return var[key];
    }
    
};

//符号表达式
class SymbolExpression : public Expression {
    
    // 运算符左右两个参数
protected:
    Expression* left;
    Expression* right;
    
public:
    SymbolExpression( Expression* left,  Expression* right):
        left(left),right(right){
        
    }
    
};

//加法运算
class AddExpression : public SymbolExpression {
    
public:
    AddExpression(Expression* left, Expression* right):
        SymbolExpression(left,right){
        
    }
    int interpreter(map<char, int> var) override {
        return left->interpreter(var) + right->interpreter(var);
    }
    
};

//减法运算
class SubExpression : public SymbolExpression {
    
public:
    SubExpression(Expression* left, Expression* right):
        SymbolExpression(left,right){
        
    }
    int interpreter(map<char, int> var) override {
        return left->interpreter(var) - right->interpreter(var);
    }
    
};



Expression*  analyse(string expStr) {
    
    stack<Expression*> expStack;
    Expression* left = nullptr;
    Expression* right = nullptr;
    for(int i=0; i<expStr.size(); i++)
    {
        switch(expStr[i])
        {
            case '+':
                // 加法运算
                left = expStack.top();
                right = new VarExpression(expStr[++i]);
                expStack.push(new AddExpression(left, right));
                break;
            case '-':
                // 减法运算
                left = expStack.top();
                right = new VarExpression(expStr[++i]);
                expStack.push(new SubExpression(left, right));
                break;
            default:
                // 变量表达式
                expStack.push(new VarExpression(expStr[i]));
        }
    }
   
    Expression* expression = expStack.top();

    return expression;
}

void release(Expression* expression){
    
    //释放表达式树的节点内存...
}

int main(int argc, const char * argv[]) {
    
    
    string expStr = "a+b-c+d-e";
    map<char, int> var;
    var.insert(make_pair('a',5));
    var.insert(make_pair('b',2));
    var.insert(make_pair('c',1));
    var.insert(make_pair('d',6));
    var.insert(make_pair('e',10));

    
    Expression* expression= analyse(expStr);
    
    int result=expression->interpreter(var);
    
    cout<<result<<endl;
    
    release(expression);
    
    return 0;
}

```


</details>

 