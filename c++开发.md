# 迭代器

在vector容器里面，迭代器是一个指针类型，相当于int *之类的

begin，end返回的是迭代器类型

```c++
template<class T>
class MyVector {
    T* start;
    T* finish;
    T* end_of_storage;
public:
    using iterator = T*;

    iterator begin() { return start; }
    iterator end() { return finish; }
    
    void push_back(const T& v) {
        if (finish == end_of_storage) {
            // 重新分配、更大容量，并搬移元素
        }
        new (finish) T(v);
        ++finish;
    }

};
```



在list里面，它是一个成员类

```
#include <iostream>

template <typename T>
class List {
private:
    struct Node {
        T data;
        Node* next;
        Node(const T& val) : data(val), next(nullptr) {}
    };

    Node* head;
    Node* tail;

public:
    List() : head(nullptr), tail(nullptr) {}
    ~List() {
        Node* current = head;
        while (current) {
            Node* tmp = current;
            current = current->next;
            delete tmp;
        }
    }

    void push_back(const T& value) {
        Node* newNode = new Node(value);
        if (!head)
            head = tail = newNode;
        else {
            tail->next = newNode;
            tail = newNode;
        }
    }
    
    // ✅ 嵌套的迭代器类
    class iterator {
        Node* current;
    
    public:
        iterator(Node* ptr = nullptr) : current(ptr) {}
    
        T& operator*() const { return current->data; }
    
        iterator& operator++() {
            if (current) current = current->next;
            return *this;
        }
    
        bool operator!=(const iterator& other) const {
            return current != other.current;
        }
    };
    
    iterator begin() { return iterator(head); }
    iterator end()   { return iterator(nullptr); }

};
```

总结：迭代器就是容器里面单位元素的指针类型，换句话，它是一个指向单位元素的指针类型

它只是一个类型，相当于int*

# 为什么有些函数可以不创建对象或者指针直接调用？->static

普通函数

```c++
class A {
public:
    void say() {
        std::cout << "I am A" << std::endl;
    }
};

int main() {
    A a;
    a.say();  // 表面上是这样
}





//实际
void A__say(A* this) {
    std::cout << "I am A" << std::endl;
}

int main() {
    A a;
    A__say(&a);  // 隐式 this 指针传入
}


```

a.say() 本质是 say(&a)，也就是把 this = &a 

static声明的函数没有this指针，不需要创建对象|指针，像全局函数一样调用

# 堆和栈

| 特性         | 栈（Stack）        | 堆（Heap）               |
| ------------ | ------------------ | ------------------------ |
| 分配方式     | 自动（编译器完成） | 手动（程序员用 `new`）   |
| 生命周期     | 函数结束自动销毁   | 程序员控制（或智能指针） |
| 分配速度     | 非常快（指针加减） | 相对较慢（系统分配器）   |
| 内存大小     | 小（几 MB）        | 大（可达 GB）            |
| 易错风险     | 小                 | 易内存泄漏、悬空指针     |
| 是否可返回值 | ❌ 不可返回引用     | ✅ 可返回指针或引用       |
| 创建对象方式 | `T obj;`           | `T* obj = new T;`        |

	#include <iostream>

```c++
using namespace std;

void showStackAndHeap() {
    int stackVar = 10;
    int* heapVar = new int(20);

    cout << "Stack variable address: " << &stackVar << endl;
    cout << "Heap variable address:  " << heapVar << endl;
    
    delete heapVar;  // 别忘了释放内存

}

int main() {
    while(1){
    showStackAndHeap();
    }
    return 0;
}
```

每次调用都会导致重新申请堆空间，不释放内存，导致前面申请的没有释放，会导致运行占用增大，直到程序关闭，回收全部资源





在Qt里面

只要你用的是 `QObject` 派生类，并在 `new` 时传入了 `parent`，**就不用手动释放内存**，Qt 会帮你自动回收

**你用的是非 QObject 的普通类（比如 STL 容器、QString、QDateTime 等）：**

- 这些类是值类型，不属于 Qt 的对象树；
- 但一般用栈对象（直接创建对象）就行，不用管释放（栈自动销毁）；