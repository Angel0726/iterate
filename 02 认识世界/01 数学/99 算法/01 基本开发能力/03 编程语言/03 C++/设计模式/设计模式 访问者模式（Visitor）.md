---
title: 设计模式 访问者模式（Visitor）
toc: true
date: 2018-07-27 20:05:49
---
# 设计模式 访问者模式（Visitor）


# 访问者模式（Visitor）

  1. 访问者模式：在不改变各元素的前提下定义作用于这些类的新的操作。


  2. 访问者模式使用双分派，将数据结构和作用于结构上的操作解耦，意味着执行的操作决定于请求的种类和接收者的状态。


  3. 如果系统具有较为稳定的数据结构，又有易于变化的算法操作，则适合使用访问者模式。






Visitor 模式
-问题
在面向对象系统的开发和设计过程，经常会遇到一种情况就是需求变更(Requirement Changing)，经常我们做好的一个设计、实现了一个系统原型，咱们的客户又会有了新的需 求。我们又因此不得不去修改已有的设计，最常见就是解决方案就是给已经设计、实现好的 类添加新的方法去实现客户新的需求，这样就陷入了设计变更的梦魇:不停地打补丁，其带 来的后果就是设计根本就不可能封闭、编译永远都是整个系统代码。

Visitor 模式则提供了一种解决方案:将更新(变更)封装到一个类中(访问操作)，并 由待更改类提供一个接收接口，则可达到效果。

■模式选择
我们通过 Visitor 模式解决上面的问题，其典型的结构图为:


![](http://images.iterate.site/blog/image/180727/Ifl7d1Cd8h.png?imageslim){ width=55% }

图 2-1: Visitor Pattern 结构图

Visitor模式在不破坏类的前提下，为类提供增加新的新操作。Visitor模式的关键是双分 派(Double-Dispatch)的技术【注释 1。C++语言支持的是单分派。

在 Visitor 模式中 Accept ()操作是一个双分派的操作。具体调用哪一个具体的 Accept ()操作，有两个决定因素：1) Element的类型。因为 Accept ()是多态的操作，需要具

体的 Element 类型的子类才可以决定到底调用哪一个 Accept ()实现；2) Visitor的类型。 Accept ()操作有一个参数(Visitor* vis)，要决定了实际传进来的 Visitor 的实际类别才可 以决定具体是调用哪个 VisitConcrete ()实现。

-实现
♦完整代码示例(code)

visitor.h


    #ifndef DESIGN_PATTERNS_VISITOR_H
    #define DESIGN_PATTERNS_VISITOR_H

    #include <vector>

    class Action;

    class Person {
    public:
      virtual ~Person() {}
      virtual void Accept(Action*) = 0;
    };

    class Man: public Person {
    public:
      void Accept(Action*);
    };

    class Woman: public Person {
    public:
      void Accept(Action*);
    };

    class ObjectStructure {
    public:
      void Attach(Person*);
      void Detach(Person*);
      void Display(Action*);

    private:
      std::vector <Person*> people;
    };

    class Action {
    public:
      virtual void GetManConclusion(Person*) = 0;
      virtual void GetWomanConclusion(Person*) = 0;
    };

    class Success: public Action {
      void GetManConclusion(Person*);
      void GetWomanConclusion(Person*);
    };

    class Failure: public Action {
      void GetManConclusion(Person*);
      void GetWomanConclusion(Person*);
    };

    #endif //DESIGN_PATTERNS_VISITOR_H



visitor.cpp


    #include "visitor.h"
    #include <iostream>

    void Man::Accept(Action *action) {
      action->GetManConclusion(this);
    }

    void Woman::Accept(Action *action) {
      action->GetWomanConclusion(this);
    }

    void ObjectStructure::Attach(Person *person) {
      people.push_back(person);
    }

    void ObjectStructure::Detach(Person *person) {
      for(std::vector <Person*>::iterator it = people.begin(); it != people.end(); ++it) {
        if(*it == person) {
          people.erase(it);
          return;
        }
      }
    }

    void ObjectStructure::Display(Action *action) {
      for (std::vector<Person *>::iterator it = people.begin(); it != people.end(); ++it) {
        (*it)->Accept(action);
      }
    }

    void Success::GetManConclusion(Person *person) {
      std::cout << "man gets success" << std::endl;
    }

    void Success::GetWomanConclusion(Person *person) {
      std::cout << "woman gets success" << std::endl;
    }

    void Failure::GetManConclusion(Person *person) {
      std::cout << "man gets failure" << std::endl;
    }

    void Failure::GetWomanConclusion(Person *person) {
      std::cout << "woman gets failure" << std::endl;
    }





main.cpp


    #include "visitor.h"
    #include <iostream>


    int main() {
        Person *man_;
        Person *woman_;
        ObjectStructure *object_structure_;
        Action *success_;
        Action *failure_;
        man_ = new Man();
        woman_ = new Woman();
        object_structure_ = new ObjectStructure();
        success_ = new Success();
        failure_ = new Failure();
        object_structure_->Attach(man_);
        object_structure_->Attach(woman_);
        object_structure_->Display(success_);
        object_structure_->Display(failure_);
        delete man_;
        delete woman_;
        delete object_structure_;
        delete success_;
        delete failure_;



        return 0;
    }




【注释 1 :双分派意味着执行的操作将取决于请求的种类和接收者的类型。更多资料请参 考资料。



♦代码说明

Visitor模式的实现过程中有以下的地方要注意：

1) Visitor类中的 Visit ()操作的实现。

♦这里我们可以向 Element 类仅仅提供一个接口 Visit ()，而在 Accept ()实现中具 体调用哪一个 Visit ()操作则通过函数重载(overload)的方式实现：我们提供 Visit

()的两个重载版本 a)Visit(ConcreteElementA* elmA)，b)Visit(ConcreteElementB* elmB)。

♦在 C++中我们还可以通过 RTTI (运行时类型识别：Runtime type identification)来 实现，即我们只提供一个 Visit ()函数体，传入的参数为 Element*型别参数，然 后用 RTTI 决定具体是哪一类的 ConcreteElement 参数，再决定具体要对哪个具体 类施加什么样的具体操作【注释 2】RTTI给接口带来了简单一致性，但是付出的 代价是时间(RTTI的实现)和代码的 Hard 编码(要进行强制转换)。

■讨论
有时候我们需要为 Element 提供更多的修改，这样我们就可以通过为 Element 提供一累

列的

Visitor模式可以使得 Element 在不修改自己的同时增加新的操作，但是这也带来了至少 以下的两个显著问题:

1) 破坏了封装性。Visitor模式要求 Visitor 可以从外部修改 Element 对象的状态，这一 般通过两个方式来实现：a) Element提供足够的 public 接口，使得 Visitor 可以通过 调用这些接口达到修改 Element 状态的目的；b) Element暴露更多的细节给 Visitor， 或者让 Element 提供 public 的实现给 Visitor (当然也给了累统中其他的对象)，或者 将 Visitor 声明为 Element 的 friend 类，仅将细节暴露给 Visitor。但是无论那种情况， 特别是后者都将是破坏了封装性原则(实际上就是 C++的 friend 机制得到了很多的 面向对象专家的诟病)。

2) ConcreteElement的扩展很困难：每增加一个 Element 的子类，就要修改 Visitor 的 接口，使得可以提供给这个新增加的子类的访问机制。从上面我们可以看到，或者 增加一个用于处理新增类的 Visit ()接口，或者重载一个处理新增类的 Visit ()操 作，或者要修改 RTTI 方式实现的 Visit ()实现。无论那种方式都给扩展新的 Element 子类带来了困难。



在《GoF 23种设计模式模式解析附 C++实现源码》和《设计模式解析之一 Visitor 模式 中，我给出了 Visitor 模式的诠释和示例实现源码。个人觉得例子和解析还是能够比较清晰 地为学习和掌握 Visitor 模式提供一些信息，但是对于其中的一个重要知识没有很好地解释， 这就是 multi-dispatch（多分派，multi-dispatch（多分派）是 Visitor 模式的关键，实际上 Visitor 模式就是提供了一种 multi-dispatch （多分派）中的 double dispatch （双分派）的实现方式。

double dispatch （双分派）是 multi-dispatch （多分派）的特例，由于 Visitor 模式涉及的 是 double dispatch （双分派），因此这里仅仅讨论 double dispatch （双分派）的内容。实际上 double dispatch （双分派）是一种很经典的技术，但是当前的主流的面向对象程序设计语言

（例如 C++/Java/C#等）都并不支持多分派，仅仅支持单分派（single dispatch）。

单分派（single dispatch）的含义比较好理解，单分派（single dispatch）就是说我们在选

择一个方法的时候仅仅需要根据消息接收者（receiver）的运行时型别（Run time type。实 际上这也就是我们经常提到的多态的概念（当然 C++中的函数重载也是 Sigle dispatch的一 种实现方式。举一个简单的例子，我们有一个基类 B，B有一个虚方法（可被子类 override）， D1和 D2 是 B 的两个子类，在 D1 和 D2 中我们覆写（override） 了方法 f。这样我们对消息 f的调用，需要根据接收者 A 或者 A 的子类 D1/D2的具体型别才可以确定具体是调用 A 的 还是 D1/D2的 f 方法。

double dispatch（双分派）则在选择一个方法的时候，不仅仅要根据消息接收者（receiver） 的运行时型别（Run time type，还要根据参数的运行时型别（Run time type。当然如果所 有参数都考虑的话就是 multi-dispatch （多分派）。也举一个简单的例子，同于上面单分派中 例子，A的虚方法 f 带了一个 C 型别的参数，C也是一个基类，C有也有两个具体子类 E1 和 E2。这样，当我们在调用消息 f 的时候，我们不但要根据接收者的具体型别（A、D1、 D2），还要根据参数的具体型别（C、E1、E2），才可以最后确定调用的具体是哪一个方法 f。

遗憾的是，当前的主流面向对象程序设计语言（例如 C++Java/C#等）都并不支持双分 派（多分派），仅仅支持单分派。为了支持双分派（多分派），一个权宜的方法就是借助 RTTI 和 if 语言来人工确定一个对象的运行时型别，并使用向下类型转换（downcast）来实现。一 个常见的例子就是，我们取得对象的 RTTI 信息，然后 if 对象是某个具体类，则执行一部分 操作，else属于另外的类则执行另外的操作+ + +++。然而我们知道，RTTI —是占用较多的时间 和空间，并且不是很安全（经常可能在 downcast 中出现 exception）。

以上的分析主要是关注于单分派和双分派(多分派)，好像和 Visitor 模式没有什么关系。 其实不然，要真正理解 Visitor 模式就必须要理解单分派和双分派(多分派)的含义。再审 视一下 Visitor 模式的实现，Visitor模式的实现有两个关键的方法：1) Visitor的 visit 方法； 2) Element 的 Accept 方法。在给出的 Visitor 的实现中，我们会针对不同 Element

(ConcreteElementA/ ConcreteElementB )提供不同的接 口 ( VisitConcreteElementA/ VisitConcreteElementB)，当然我们可以对这个接口进行简化，简化的实现有两个选择：

1) 采用函数重载的方式进行。即 Visitor 及其子类只提供一个 Visit 的接口，但是有两 个函数体，Visit (ConcreteElementA* elm)和 Visit (ConcreteElementB* elm)，这样通过函 数重载的方式可以简化接口，但是不能改变实现。

2) 通过 RTTI 实现。我们不通过函数重载的方式实现，而使用 RTTI 的方式实现，在 《设

计模式解析之一 Visitor 模式》中我给出了这个思路，但是没有给出实现的代码，这里将给出

完整的实现。我们对 Visitor 极其子类仅提供 Visit 接口，该 Visit 接口的实现模式为： void Visitor::Visit(Element* elm)






# 相关

1. [design-patterns-cpp](https://github.com/yogykwan/design-patterns-cpp)  作者： [Jennica](http://jennica.space/)  厉害的
2. 《设计模式精解 - GoF 23种设计模式解析》
3. 《大话设计模式》作者 程杰
