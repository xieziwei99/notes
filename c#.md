[TOC]

## 基本特性

```c#
string f = @"Joe said ""Hello"" to me";   // Joe said "Hello" to me
```

- 如果没有指定访问修饰符，则使用类成员的默认访问修饰符，即为 **private**

- Internal 访问说明符允许一个类将其成员变量和成员函数暴露给当前程序中的其他函数和对象

- C# 可空类型（Nullable）

  ```c#
  int i; //默认值0
  int? ii; //默认值null
  ```

```c#
num3 = num1 ?? 5.34;      // num1 如果为空值则返回 5.34
```

```c#
int[,] a = new int[5, 2];	// 多维数组
int[][] scores = new int[2][]{new int[]{92,93,94},new int[]{85,66,87,88}};	// 交错数组
public int AddElements(params int[] arr)	// 可变参数
```

- C# 不支持多重继承
- c# 支持函数重载
- 通过在类定义前面放置关键字 **sealed**，可以将类声明为**密封类**，意味着不能被继承。
- C#中非常有限地使用指针。绝大多数情况下不用指针.

## c# 初步

### HelloWorld

```c#
using System;
namespace Hello {
    class Hello {
        static void Main(string[] args) {
            Console.WriteLine("Hello World");
            Console.ReadKey();
        }
    }
}
```

### 模版字符串

```c#
string firstFriend = "Maria";
string secondFriend = "Sage";
Console.WriteLine($"My friends are {firstFriend} and {secondFriend}");
```

### ref 关键字

```c#
using System;
namespace Hello {
    class SwapXY {
        // 交换x和y的值
        public static void Swap(ref int x, ref int y) {
            int temp = x;
            x = y;
            y = temp;
        }
        static void Main(string[] args) {
            int x = 2;
            int y = 3;
            Swap(ref x, ref y);
            Console.WriteLine("x = {0}, y = {1}", x, y);
            Console.ReadKey();
        }
    }
}

```

### out 关键字

```c#
using System;
namespace Hello {
    class OutputParam {
        // 从函数中输出多个值
        public static void GetInputs(out int x, out int y) {
            Console.Write("Please input x = ");
            x = Convert.ToInt32(Console.ReadLine());
            Console.Write("Please input y = ");
            y = Convert.ToInt32(Console.ReadLine());
        }
        static void Main(string[] args) {
            GetInputs(out int a, out int b);
            Console.WriteLine("a = {0}, b = {1}", a, b);
            Console.ReadKey();
        }
    }
}
```

### 委托

```c#
using System;
namespace Hello {
    // C#委托
    class DelegateDemo {
        public delegate void Say(string str);

        public static void SayHello(string str) {
            Console.WriteLine("---Hello---\n" + str);
        }

        public static void SayHi(string str) {
            Console.WriteLine("---Hi---\n" + str);
        }

        static void Main(string[] args) {
            Say say1 = new Say(SayHello);
            Say say2 = new Say(SayHi);
            // 委托的多播（Multicasting of a Delegate）
            Say say = say2 + say1;
            say1("Jack");
            say2("Jack");
            say("Jack");
            Console.ReadKey();
        }
    }
}
```

### 事件

```c#
using System;
namespace SimpleEvent {
    public class EventTest {
        private int value;

        public EventTest () {
            int n = 5;
            SetValue (n);
        }

        public void SetValue (int n) {
            if (value != n) {
                value = n;
                OnNumChange ();
            }
        }

        public delegate void NumManipulationHandler ();

        public event NumManipulationHandler ChangeNum;

        protected virtual void OnNumChange () {
            if (ChangeNum != null) {
                ChangeNum ();
            } else {
                Console.WriteLine ("event not fire");
            }
        }
    }

    public class SubscribeEvent {
        public void print () {
            Console.WriteLine ("event fire");
        }
    }

    public class MainClass {
        public static void Main () {
            EventTest eventTest = new EventTest ();
            SubscribeEvent subscribeEvent = new SubscribeEvent ();

            eventTest.ChangeNum += new EventTest.NumManipulationHandler (subscribeEvent.print);

            eventTest.SetValue (4);
            eventTest.SetValue (3);
        }
    }
}
```

