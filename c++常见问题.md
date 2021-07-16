## c++常见问题
- const的作用
    - 成员函数后加const修饰，是修饰this指针，this指针指向的内容不能更改
    - const就近修饰原则
        - `const char * p1`，修饰*p1，即指针指向的内容不可更改
        - `char const * p2`，修饰*p2，指针指向的内容不可更改
        - `char * const p3`, 修饰p3，指针本身不能更改
- static的作用
    -  隐藏，尽在作用域内有效
    -  持久化，局部变量只能初始化一次，变量放在数据区
- new和malloc区别
    - new运算符，可以被重载，分为三步，申请内存、基于内存进行构造、返回内存指针
    - malloc，只是用于申请内存
    - 另外placement new()，`auto pr = new(ptr) int`，基于已有内存的构造，即new的第二步；
- 单例模式的double check问题

- 只能在堆上创建，不在在栈上创建
    - 析构函数私有化
    - 在成员函数中，重载new和delete
- inline和宏的区别
    - 宏，是直接执行代码替换，没有参数校验
    - inline就是正常的函数，省去了参数的压栈、出栈，减少调用开销
    - inline
- 多态
    - 基于虚表的多态
      - 基类有虚函数，所有的实例都维护一个指向虚表的指针；
      - 虚表内部按照虚函数的定义顺序排序；
      - 派生类重写的函数地址，会将虚表中基类的函数地址给替换掉；这样，只用基类指针，就可以访问派生类的函数实现；
- 虚析构函数
    - 基类加虚析构函数，会自动触发，先析构派生，后析构当前，防止内存泄漏
    - 删除对象时，派生类的析构和基类的析构构成多态，所以先调用派生类的析构；析构函数
    - 参考：https://www.zhihu.com/question/268022905

- lambda实现原理
    - lambda结构
        - 捕获列表：捕获外部变量，捕获的变量可以在函数体中使用，可以省略，即不捕获外部变量。
        - 形参列表：和普通函数的形参列表一样。可省略，即无参数列表
        - mutable：mutable 关键字，如果有，则表示在函数体中可以修改捕获变量，根据具体需求决定是否需要省略。
        - 异常列表：noexcept / throw(...),和普通函数的异常列表一样，可省略，即代表可能抛出任何类型的异常。
        - 返回类型：和函数的返回类型一样。可省略，如省略，编译器将自动推导返回类型。
        - 函数体：代码实现。可省略，但是没意义。
    - lambda执行流程
        - 创建 lambda 类，实现构造函数，使用 lambda 表达式的函数体重载 operator()（所以 lambda 表达式 也叫匿名函数对象）
        - 创建 lambda 对象
        - 通过对象调用 operator()
        - lambda 表达式翻译后的代码，类名修改为lambda_XXX
            - 翻译前
            ```c++
                void LambdaDemo()
                {
                    int a = 1;
                    int b = 2;
                    auto lambda = [a, b](int x, int y)mutable throw() -> bool
                    {
                        return a + b > x + y;
                    };
                    bool ret = lambda(3, 4);
                }
            ```
            - 翻译后
            ```c++
                class lambda_xxxx
                {
                private:
                    int a;
                    int b;
                public:
                    lambda_xxxx(int _a, int _b) :a(_a), b(_b)
                    {
                    }
                    bool operator()(int x, int y) throw()
                    {
                        return a + b > x + y;
                    }
                };
                void LambdaDemo()
                {
                    int a = 1;
                    int b = 2;
                    lambda_xxxx lambda = lambda_xxxx(a, b);
                    bool ret = lambda.operator()(3, 4);
                }
            ```
    - lambda实现原理
        - lambda 表达式中的捕获列表，对应 lambda_xxxx 类的 private 成员
        - lambda 表达式中的形参列表，对应 lambda_xxxx 类成员函数 operator() 的形参列表
        - lambda 表达式中的 mutable，对应 lambda_xxxx 类成员函数 operator() 的常属性 const，即是否是 常成员函数
        - lambda 表达式中的返回类型，对应 lambda_xxxx 类成员函数 operator() 的返回类型
        - lambda 表达式中的函数体，对应 lambda_xxxx 类成员函数 operator() 的函数体
        - lambda 表达 捕获列表的捕获方式，也影响 对应 lambda_xxxx 类的 private 成员 的类型
            - 值捕获：private 成员 的类型与捕获变量的类型一致
            - 引用捕获：private 成员 的类型是捕获变量的引用类型
    - 参考文档
        - https://ld246.com/article/1545790136502

- C++11中的lambda，std::function以及std:bind
    - 参考文档：https://paul.pub/cpp-lambda-function-bind/
    
