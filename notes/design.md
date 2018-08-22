
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

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 2.1 Decorator

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 2.2 Bridge


#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

# 3. 对象创建

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>

## 3.1 Factory Method

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 3.2 Abstract Factory

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 3.3 Prototype

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
</details>


## 3.4 Builder

#### 结构

#### 实现

<details><summary><b>具体实现</b></summary>
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

 