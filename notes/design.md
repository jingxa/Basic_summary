
# 参考资料




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
- [7.3 Chain of](#73-chain-of)
- [7.4 Responsibility](#74-responsibility)

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
	二、对象创建
	三、对象性能
	四、接口隔离
	五、状态变化
	
	
	
	
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
#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 4.2 Flyweight

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


# 5. 接口隔离

## 5.1 Facade

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 5.2 Proxy

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 5.3 Mediator
#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 5.4 Adapter

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


# 6. 状态变化


## 6.1 Memento

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 6.2 State
#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>



# 7. 数据结构

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 7.1 Composite

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 7.2 Iterator
#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 7.3 Chain of

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 7.4 Responsibility

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
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

 