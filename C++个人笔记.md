  

# C++个人笔记

## 1. 基础性了解

-   ![image-20220314195406133](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220314195406133.png)

         ```c++
         #include<stdio.h>
         #include<string.h>
         namespace bit {
         	int scanf = 10;
         	int strlen = 20;
         }
         int main()
         {
         	printf("%d\n", bit::scanf);
         	printf("%d\n", bit::strlen);
         }
         ```

![image-20220314195419571](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220314195419571.png)

```c++
1.     stu::cout<< a <<stu::endl;                    2. using namespace std;
```

### 引用与隐形变量

```c++
int a=0; int& b=a; int& c=a; int& d=b;   b=10; 则abcd全为10
    
const int a=10; // int& b=a 报错  引用b属于权限放大，能读能写
const int& b=a; // 在a不可修改的情况下，必须承诺别名b也不可能修改

int c=10;  const int& d =c; //不报错， 引用d属于权限缩小，只读但不写

int* p=NULL; int*& q=p; //q是p的别名
```



```c++
int c=10; double d=1.1;  (产生临时变量double e=?) d=c;  (e=c; d=e;)
临时变量具有常性，引用时不可修改。所以引用时要加const：
const double &f=c;  (e=c; f=e)
```

![image-20220314195438108](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220314195438108.png)

以下介绍一个传引用返回，但注意，传回来的是一个已经销毁的变量的别名，实际上是不合法的

![image-20220314195451195](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220314195451195.png)

***但若把 int c 改为 static int c = a+b; c存储在静态区，则程序合法* **

### inline函数  

```c++
inline int ADD(int a, int b){     //在常用函数前加inline，没有栈帧开销
    return a+b;
}
```

### auto函数  简短遍历

```c++
int a=0; auto b=a;  //自动推理出b是int
int a[20]={1,2,3,4,5,6,7};
for (auto x : a)  cout << x << ' '; 

int t = 0;
int a[] = { 9,9,9,9,9,9,9 };
for (int i : a) t += i;
```

### vector

```c++
#include<vector>  //以下均为举例，只可意会不可言传
    vector<T> v1;
	vector<T> v2(v1);
	vector<T> v3(10); //开10个空间，默认全是0  vector<T>v3(10,0);
	vector<string> v8(3, "hi");
	v3.push_back(4);
    
    int a[]={1,2,3,4,5,6};
    vector<int> va(a,a+6);
    void Sort(vector<int> &v);

//sort排序
    sort(v.begin(), v.end());
//函数引用
  int a[]={1,2,3,4,5,6};
    vector<int> va(a,a+6);
    void Sort(vector<int> &v);
    vector<int>& Add(vector<int>& a, vector<int>& b){
        return a;
    }

	vector<string> v6{ ("my","name","is","fuck you") };
	for (vector<string>::iterator i = v6.begin(); i != v6.end(); i++) {
		cout << *i << endl;
	}   //还可以反转打印
	for (vector<string>::reverse_iterator i = v6.rbegin(); i != v6.rend(); i++) {
		cout << *i << endl;
	}

//二级向量
	vector< vector<int> >vv(3, vector<int> (4));  //三行四列
	vector< vector<int> >vv;
	for (int i = 0; i < 4; i++) {
		vv.push_back(vector<int>(i,5));  //每一行开辟的空间数量都不一样
	}
```

### const+指针  与 指针操作

```c++
const char* p1 = "wode";  //等同于 char const *p1; 不可修改指向的东西的值，但是可以修改指向啥东西
int c = 1;
int* const p2 = &c;   //不可修改指向啥东西，但是


void f(int* a, int* b, int(*p)(int));
int main()
{
    int** data;
    data = new int* [3];
    for (int i = 0; i < 3; i++) {
        data[i] = new int[4];
    }


    int fact(int);
    int i = 5, j = 6;
    int(*gp)(int) = fact;
    f(&i, &j, gp);
    f(&i, &j, fact);
  
}
```

### 动态内存分配

```c++
typedef int Type;
	Type* pointer = new Type;
	//...
	delete pointer;
    delete[] pointer;

	Type* pointer = new Type[10];
	Type** arr = new int* [10];
	for (int i = 0; i < 10; i++) {
		arr[i] = new int[10];
	}
	//...
	
```

### 输出格式控制

```c++
#include<iomanip>
    cout << (2 > 3) << boolalpha <<' '<<(2>3) << endl;
	cout << hex << 18 << ' ' << showbase << 18 << endl;
	cout << dec << 255 << ' ' << uppercase << 255 << endl;
	cout << showpos << 12 << endl;    //显示符号位+
	cout << scientific << 123456.78 << endl;


	cout << setw(8) << "C++" << setw(6) << 101 << endl;  //设定宽度
	double num = 1.23456;
	cout << setprecision(2) << num << endl;  //指定一个浮点数的精度，2是总位数
	cout << fixed << 123456.78 << endl;   //强制数值不以科学技术法显示，即只用小数形式显示
	cout << showpoint << 1.23 << endl;//t强制小数后面以0显示

	cout << left;
	cout << setw(8) << 1.23 <<'#'<< endl;
	cout << setw(8) << 1.23456 << endl;
```



## 2. 基本操作（六大内置函数）

### 面试题

```c++
class A                              //问：此代码能否正确运行？
{
public:
    void printA(){                   //答：不能。p为空，this接收为空，this->_a出错。对象存储公共变量；
        cout<<_a<<endl;              //     但是p->show()等等函数位于公共代码段，无需解引用，show()不会出错
    }
    void show(){
        cout<<"show()"<<endl;
    }
private:
    int _a
};
int main()
{
    A* p=nullptr;
    p->printA();
    p->show();
}
```

### 六大默认成员函数



<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220327145450847.png" alt="image-20220327145450847" style="zoom:80%;" />

```c++
#include<iostream>
#include<assert.h>
#include<algorithm>
using namespace std;
class Date {
public:
	//构造函数，对内置类型完成赋值，不管自定义类型（拉得一批）,大部分情况下我们需要自己写
	//以下是声明加定义，但如果单独写定义，那么声明里有缺省，定义就不能
	Date(int year = 2022, int month = 4, int day = 1) {
		_year = year;
		_day = day;
		_month = month;
	}
	//拷贝构造函数，只完成浅拷贝
	Date(const Date& d) {
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}
	//赋值运算符重载，为了满足连等需求，不用void返回
	Date& operator=(const Date& d) {
		if (this != &d) {   //防止自己给自己赋值
			_year = d._year;
			_month = d._month;
			_day = d._day;
		}
		return *this;
	}
	//假设以下为日期加减，由于很麻烦，先简化，关键在于重载思想
	Date& operator+=(int day) {
		_day += day;
		return *this;
	}
	Date operator+(int day) const{   //为了让*this无法修改，可以在()后边+const
		Date ans(*this); //创建新日期,但临时变量不能返回引用
		ans += day;
		return ans;
	}
	Date& operator++() {  //++d -> d.operator++(&d)
		_day++;
		return *this;
	}
	Date operator++(int) { //d++ -> d.opetator(&d, 0), int仅仅为了构成重载
		_day++;
		return *this;
	}
	//了解一下，基本没必要的&， 可配合 cout<<&d1<<endl;
	Date* operator&() {
		return this;
		//return nullptr;   不想让别人取你对象的地址，就返回空
	}
	const Date* operator&() const {
		return this;
		//return nullptr;
	}
	void Print() const {  //非默认函数也是成员函数，也传this，别晕
		cout << _day << endl;
	}
	//void Print();  也是重载，没有问题

	friend ostream& operator<<(ostream& _cout, const Date& d);
	friend istream& operator>> (istream& _cin, Date& d); //输入别tm加const
private:
	int _year;
	int _month;
	int _day;
};

ostream& operator<<(ostream& _cout, const Date& d) {
	_cout << d._year << '/' << d._month << '/' << d._day << endl;
	return _cout;
}

istream& operator>>(istream& _cin, Date& d) {
	_cin >> d._year >> d._month >> d._day;
	return _cin;
}

class Stack {
public:
	Stack(int capacity = 4) {
		if (capacity == 0) {
			_a = nullptr;
			_size = _capacity = 0;
		}
		else {
			_a = (int*)malloc(sizeof(int) * capacity);
			_capacity = capacity;
			_size = 0;
		}
	}
	//析构函数，清理数据
	~Stack() {
		free(_a);
		_capacity = _size = 0;
		_a = nullptr;
	}
	//拷贝构造函数，需要自己写
	Stack(Stack& s) {
		_size = s._size;
		_capacity = s._capacity;
		int* t = (int*)malloc(sizeof(int) * _capacity);
		if (t == nullptr)  exit(-1); 
		else memcpy(t, s._a, sizeof(int) * _size);
	}

private:
	int* _a;
	int _size;
	int _capacity;
};
int main()
{
	Date d1(2020, 5, 31);
	const Date d2;   //依然调用了构造函数，利用了全缺省的特性，赋予默认值,并且不可变
	Date d3 = d1;  //此处是拷贝构造，不是运算符重载
	Date d4 = d1 + 1;  //大概因为d1+1是常量，不可修改，于是拷贝构造函数也得用const修饰
	d4.Print();

	cin >> d4;
	cout << d4;

	//小拓展
	int i = 1;
	(i += 10) += 3; // += 从右往左算，左值必须可修改(必须是变量)
}
```

### 总结 + const

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220327132226844.png" alt="image-20220327132226844" style="zoom:67%;" />

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220327145450847.png" alt="image-20220327145450847" style="zoom:80%;" />

**思考： 1.const对象能调用非const成员函数吗？  2.非const对象可以调用const成员函数吗？  **

​             **3.const成员函数可以调用非const成员函数吗？  4.非const成员函数可以调用const成员函数吗？**

 1.3.都可以 ，权限缩小。 2.4.不行，权限放大了。

本质上，const成员函数限制的是this， 关键在于this的权限。

### 补充：内部类

```c++
class Outer //定义外部类Outer
{
public:
    class Inner { //定义内部类Inner
    private:
        int inner_n;
    public:
        //内部类成员函数set_outer_n()通过外部类引用参数访问外部类私有成员outer_n
        void set_outer_n(Outer& ref_outer) { ref_outer.outer_n = 10; }
        void set_inner_n() { inner_n = 100; }
        //内部类成员函数show()通过外部类引用参数访问外部类成员函数show()
        void show(Outer& ref_outer)
        {
            ref_outer.show();
            cout << "inner_n = " << inner_n << endl;
        }
    }; //内部类定义结束
    //Inner inner_obj; //定义内部类对象inner
   //外部类成员函数
    void show() { cout << "outer_n = " << outer_n << endl; }
private:
    int outer_n;
};
```



## 3. 进阶操作

### 对象转型

![image-20220412095412790](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220412095412790.png)

```c++
#include<iostream>
using namespace std;
class ppp {
public:
	int a;
	ppp(int i=1) {
		a = i;
	}
};
void fn(ppp t) {
	int i = t.a;
	cout << i;   //输出8
}
int main()
{
	ppp a;
	double z = 8.7;
	fn(z);
}
```



### 友元 + 初始化列表



<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220328202001552.png" alt="image-20220328202001552" style="zoom:67%;" />

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220328202045542.png" alt="image-20220328202045542" style="zoom:67%;" />

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220328203006268.png" alt="image-20220328203006268" style="zoom: 70%;" />



**坑题：解析同第一行**<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220328222436121.png" style="zoom:67%;" />

```c++
#define _CRT_SECURE_NO_WARNINGS 1
#include<iostream>
#include<cmath>
using namespace std;

class Time {
    friend class Date;
private:
    int hour;
    int minute;
    int second;
    Date d;
public:
    //初始化列表  格式如下：   (可以和平常的赋值混着用)
    Time(int h = 2, int m = 2, int s = 2) 
        : hour(h), minute(m), second(s) {}
    
    void set(int h=5, int m=5, int s=5) {
        hour = h, minute = m, second = s;

        //我想访问Date类，我就成为他的友元(但不可逆) friend class Time
        d.year = 2012; 
    }
};


class Date {
    friend class Time; 
private:
    int year;
    int month;
    int day;
    //Time t;
    
    //以下变量 初始化的方式和时期都是固定的，必须在初始化列表中
    int& a;
    const int b;
public:
    Date(int k, int z) : a(k), b(z) {}
};
```

**类中类：（初始化列表）：**

```c++
class circle {
	int radius;
public:
	circle(int r1) {   //如果你自己写了构造函数，往下有地方要注意
		radius = r1;
	}
};
class cylinder {
	circle bottom;
	int height;
public:
	cylinder(int ri, int t):bottom(ri) { //运用列表给变量bottom构造
		height = t;
	}
};
```





### 隐式类型转换 + 匿名对象

```c++

#include<iostream>
#include<vector>
#include<string>
using namespace std;
class A {
private:
    int a;
public:
    A(int a1=10086) {
        a = a1;
    }
};
void fff(A& d){}

int main() 
{
    A t1(1);
    A t2 = 2; //隐式类型转换，A类被赋值int
    //早期编译器：先 A temp(2) 再 A t2(temp), 现在直接调构造函数 

    int i = 0;
    double d = i; //隐式类型转换

    vector<string> v;
    string a = "hello";
    v.push_back(a);

    v.push_back("hello"); //支持单参数的隐式类型转换，方便快捷

    A(3);  //构造匿名对象，生命周期还在这一行，用完立马析构再销毁
    fff(A());

    vector<A> vv;
    A t3(3); vv.push_back(t3);

    vv.push_back(A(3)); //巴适得很
}

```

### 重载->强制类型转换

```c++
#include<bits/stdc++.h>
using namespace std;
class Complex {
	int x;
	int y;
public:
	Complex(int xx=1, int yy=1) {
		x = xx; y = yy;
	}
	operator int() {    //强制类型转换
		return x;
	}
	
};
int main()
{
	Complex a,b,c;
	cout << int(a) << endl;
	a = b + c; //一点问题没有，把类自动转换为int
	cout << int(a);
}
```



### 数组初始化（晕）

```c++
#include<bits/stdc++.h>
using namespace std;

class MyClass
{
	int value;
	int cap;
public:
	MyClass(int i = -1, int j = -2) { value = i; cap = j; }

	int getvalue() { return value; }
	void setvalue(int v) { value = v; }
	int get() { return value; }
	void print(){
		cout << this->cap;
	}
};

int main()
{
	//利用对象转型初始化数组
	MyClass a[10] = { {0,1},{1,2},{2,3},{3,4},{4,5},
		{5,6},{6,7},{7,8},{8,9},{9,10} }, b[10];
	MyClass c[3] = {
		MyClass(10,20), MyClass(20,30), MyClass(30,40),
	};
	
	MyClass* cl = new MyClass[3];
	cl[0] = MyClass(111, 222);
	cl[1] = MyClass(222, 333);
	cl[2] = MyClass(333, 444);

	vector<MyClass> vv;
	vv.push_back(MyClass(111, 222));
	vv.push_back(MyClass(222, 333));
	vv.push_back(MyClass(333, 444));

	vector<MyClass*>v2;
	v2.push_back(new MyClass(111, 222));
	v2.push_back(new MyClass(222, 333));
	v2.push_back(new MyClass(333, 444));

	for (int i = 1; i <= 3; i++) {
		cout << a[i].get() << ' ';
	}
	return 0;
}



//二维数组
int **a;
Matrix(int r = 2, int c = 3) {
		row = r; col = c;
		a = new int*[row];
		for (int i = 0; i < row; i++) {
			a[i] = new int[col];
		}
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				a[i][j] = 0;
			}
		}
	}


vector< vector<int> > a;
Matrix(int xx=0, int yy=0) {
		row = xx, col = yy;
/		a.resize(row, vector<int>(col));
	}
```

### 静态成员

```c++

//用静态成员来表示出有多少个类对象

class ill {
	static int st; //存在静态区，属于整个类，不属于某个具体对象
	int ji;
public:
	//静态函数：没有this指针，就只能访问静态成员
	static int get() {
		return st;
	}
	ill() {
		st++;
	}
};
int ill::st = 0; //特例，不受private限制
int main()
{
	ill t[5];
	//cout << ill::a;  public情况下直接突破类域就行
	cout << t[1].get();
	
}
```



![image-20220412094835947](https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220412094835947.png)

### 内存管理

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220412190852355.png" alt="image-20220412190852355" style="zoom: 50%;" />

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220412191258103.png" alt="image-20220412191258103" style="zoom:67%;" />

## 4.---------模板

```c++
#include<iostream>
using namespace std;
template <class T> //模板函数
T Add(const T& a, const T& b) {
	return a + b;
}
int main()
{
	int a1 = 1, a2 = 2;
	double b1 = 1.3, b2 = 3.1;
	cout << Add(a1, a2) << endl;//隐式实例化，靠推导
	//Add(a1,b1),类型不匹配报错,
	cout << Add(a1, (int)b1) << endl;//显示实例化，指定类型
	cout << Add<int>(a1, b2) << endl;
}
```

**补充：自定义与自带模板的小比较**

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220416143159406.png" alt="image-20220416143159406" style="zoom:67%;" />

其中，::是类域限定符，编译器没找到适配的自定义swap时，加上::即可调用全局swap

第一层swap(string& s)属于自定义，后三个swap()属于全局



**小改错：**

```c++
    const char& operator[](int i) const {
		assert(i < _size);
		return _str[i];
	}
	char fffff(const string& s) {
		return s[1];
	}
string& s加了const，只读不写，[]重载也要做出改变，前后都加const，保证this不可改变和返回值
```



## 4.5 容器模拟实现

[learning: my learning - Gitee.com](https://gitee.com/Allorry/learning/tree/微项目/)

## 5. String

**与C风格字符串的小区别：**

```c++
int main()
{
    string s = "abc";
    s += '\0';
    s += "def";
    cout << s << endl;//输出abc def       但是vs有什么大病不输出空字符
    cout << s.c_str() << endl;//输出abc
    cout << (s.c_str() + 4) << endl;//输出def
}
```

### 基础库函数

**初始化()**

```c++

  std::string s0 ("Initial string");
  // constructors used in the same order as described above:
  std::string s1;
  std::string s2 (s0);                                               // Initial string
  std::string s3 (s0, 8, 3);                                         // str  从下标8往后3个
  std::string s4 ("A character sequence");                           // A character sequence
  std::string s5 ("Another character sequence", 12);                 // Another char 前12个，不是12个往后
  std::string ss (s0, 5);                                            // 从下标5往后，只能说str和char不一样，绝
  std::string s6a (10, 'x');                                         // xxxxxxxxxx
  std::string s6b (10, 42);      // 42 is the ASCII code for '*'     // **********
  std::string s7 (s0.begin(), s0.begin()+7);                         // Initial
```

**str::erase**

```c++

  std::string str ("This is an example sentence.");
                                           // "This is an example sentence."
  str.erase (10,8);                        // "This is an sentence."
  str.erase (str.begin()+9);               // "This is a sentence."
  str.erase (str.begin()+5, str.end()-9);  // "This sentence."
  str.erase (2)                            // 下标2往后的全部删除
```

**str::append**

```c++

  std::string str;
  std::string str2="Writing ";
  std::string str3="print 10 and then 5 more";

  str.append(str2);                       // "Writing "
  str.append(str3,6,3);                   // "10 "
  str.append("dots are cool",5);          // "dots "       前5个字符，不是从5往后
  str.append(str2, 5);                    // 从下标5往后，绝绝子
  str.append("here: ");                   // "here: "
  str.append(10u,'.');                    // ".........."
  str.append(str3.begin()+8,str3.end());  // " and then 5 more"
  str.append<int>(5,0x2E);                // "....."
  std::cout << str << '\n';               // Writing 10 dots here: .......... and then 5 more.....

```

**str::insert**

```c++

  std::string str2="the ";
  std::string str3="or not to be";
  std::string::iterator it;

  // used in the same order as described above:
  str.insert(6,str2);                 // to be (the )question
  str.insert(6,str3,3,4);             // to be (not )the question
  str.insert(10,"that is cool",8);    // to be not (that is )the question
  str.insert(10,"to be ");            // to be not (to be )that is the question
  str.insert(15,1,':');               // to be not to be(:) that is the question
  it = str.insert(str.begin()+5,','); // to be(,) not to be: that is the question
  str.insert (str.end(),3,'.');       // to be, not to be: that is the question(...)
  str.insert (it+2,str3.begin(),str3.begin()+3); // (or )

  std::cout << str << '\n';
```

**str::find(）**

```c++

  std::string str ("There are two needles in this haystack with needles.");
  std::string str2 ("needle");

  // different member versions of find in the same order as above:
  std::size_t found = str.find(str2);
  if (found!=std::string::npos)
    std::cout << "first 'needle' found at: " << found << '\n';

  found=str.find("needles are small",found+1,6);  //6此处是寻找对象的前6个字符
  if (found!=std::string::npos)
    std::cout << "second 'needle' found at: " << found << '\n';

  found=str.find("haystack");
  if (found!=std::string::npos)
    std::cout << "'haystack' also found at: " << found << '\n';

  found=str.find('.');
  if (found!=std::string::npos)
    std::cout << "Period found at: " << found << '\n';

  // let's replace the first needle:
  str.replace(str.find(str2),str2.length(),"preposition");
  std::cout << str << '\n';

  return 0;
}
```

```c++
first 'needle' found at: 14
second 'needle' found at: 44
'haystack' also found at: 30
Period found at: 51
There are two prepositions in this haystack with needles.
```

**str::assign**

```c++

  std::string str;
  std::string base="The quick brown fox jumps over a lazy dog.";

  // used in the same order as described above:

  str.assign(base);
  std::cout << str << '\n';

  str.assign(base,10,9);
  std::cout << str << '\n';         // "brown fox"

  str.assign("pangrams are cool",7);
  std::cout << str << '\n';         // "pangram"  前7个

  str.assign("c-string");
  std::cout << str << '\n';         // "c-string"

  str.assign(10,'*');
  std::cout << str << '\n';         // "**********"

  str.assign<int>(10,0x2D);
  std::cout << str << '\n';         // "----------"

  str.assign(base.begin()+16,base.end()-12);
  std::cout << str << '\n';         // "fox jumps over"

```

**str::replace**

```c++
  
  std::string base="this is a test string.";
  std::string str2="n example";
  std::string str3="sample phrase";
  std::string str4="useful.";

  // replace signatures used in the same order as described above:

  // Using positions:                 0123456789*123456789*12345
  std::string str=base;           // "this is a test string." 
  str.replace(9,5,str2);          // "this is an example string." (1)  下标9往后5个被str替代
  str.replace(19,6,str3,7,6);     // "this is an example phrase." (2)  下标19往后16，用str3的下标7往后6替代
  str.replace(8,10,"just a");     // "this is just a phrase."     (3)  
  str.replace(8,6,"a shorty",7);  // "this is a short phrase."    (4)
  str.replace(22,1,3,'!');        // "this is a short phrase!!!"  (5)  下标22往后1个被3个！替代

  // Using iterators:                                               0123456789*123456789*
  str.replace(str.begin(),str.end()-3,str3);                    // "sample phrase!!!"      (1)
  str.replace(str.begin(),str.begin()+6,"replace");             // "replace phrase!!!"     (3)
  str.replace(str.begin()+8,str.begin()+14,"is coolness",7);    // "replace is cool!!!"    (4)
  str.replace(str.begin()+12,str.end()-4,4,'o');                // "replace is cooool!!!"  (5)
  str.replace(str.begin()+11,str.end(),str4.begin(),str4.end());// "replace is useful."    (6)
```

**str::substr**

```c++
  
  std::string str="We think in generalities, but we live in details.";
                                           // (quoting Alfred N. Whitehead)

  std::string str2 = str.substr (3,5);     // "think"   下标3往后5个截取出来

  std::size_t pos = str.find("live");

  std::string str3 = str.substr (pos);     // 从下标pos一直往后截取到尾

  std::cout << str2 << ' ' << str3 << '\n';
```

## 6. Vector

### 基础库函数

**vector::construct**

```c++

  std::vector<int> first;                                // empty vector of ints
  std::vector<int> second (4,100);                       // 4个100
  std::vector<int> third (second.begin(),second.end());  // iterating through second
  std::vector<int> fourth (third);                       // a copy of third
```

**vector::resize**

```c++

  for (int i=1;i<10;i++) myvector.push_back(i);          //1 2 3 4 5
  myvector.resize(5);                                    //容量5  
  myvector.resize(8,100);                                //容量8，填补值为100
  myvector.resize(12);                                   //容量12，默认填补值为0

  myvector contains: 1 2 3 4 5 100 100 100 0 0 0 0
```

**vector::insert**

```c++
  // emplace 安置， 跟insert差不多   还有个emplace_back， 跟push_back一样

  td::vector<int> myvector (3,100);                                        //100,100,100
  std::vector<int>::iterator it;

  it = myvector.begin(); 
  it = myvector.insert ( it , 200 );                                       //200,100,100,100

  myvector.insert (it,2,300);                                              //300,300,200,100,100
     
  it = myvector.begin();                             

  std::vector<int> anothervector (2,400);                   
  myvector.insert (it+2,anothervector.begin(),anothervector.end());    //300,300,400,400,200,100,100

  int myarray [] = { 501,502,503 };                                  
  myvector.insert (myvector.begin(), myarray, myarray+3);        //501,502,503,300,300,400,400,200,100,100
```

**vector::erase**

```c++
// erase the 6th element
  myvector.erase (myvector.begin()+5);

  // erase the first 3 elements:
  myvector.erase (myvector.begin(),myvector.begin()+3);
```

**vector::swap**

```c++

  std::vector<int> foo (3,100);   // three ints with a value of 100
  std::vector<int> bar (5,200);   // five ints with a value of 200

  foo.swap(bar);  //参考string类实现，swap 由类外 + 模板组成

```

### 迭代器失效

1.  pos原本的位置，因为插入等操作，导致pos意义改变。
2.  pos原本所在的空间，因为增容操作被销毁，新空间压根没有pos的位置。 pos变成野指针。

```c++
#include<iostream>
#include<vector>
using namespace std;
int main()
{
	//删除偶数
	vector <int> v = { 2,14,21,54,33,75,976,23,5,754,12,53,0 };
	vector<int>::iterator i = v.begin();
	/*for (; i ！= v.end(); i++) {
		if (*i % 2 == 0) v.erase(i);
	  }
	  该代码报错，erase忽略了一些元素，还可能导致i 和 e
	*/
	//以上代码报错，以下内容为正确案例
	while (i != v.end()) {
		if (*i % 2 == 0)  i = v.erase(i);  //返回删除位置的下一个位置，不这么写会报错
		else i++;
	}
	for (i = v.begin(); i < v.end(); i++) {
		cout << *i << ' ';
	}
}
```

## 7. List

### 基础库函数

**list::assign**

```c++

  std::list<int> first;
  std::list<int> second;

  first.assign (7,100);                      // 7 ints with value 100

  second.assign (first.begin(),first.end()); // a copy of first

  int myints[]={1776,7,4};
  first.assign (myints,myints+3);            // assigning from array
```

**list::splice**

```c++

  std::list<int> mylist1, mylist2;
  std::list<int>::iterator it;

  // set some initial values:
  for (int i=1; i<=4; ++i)
     mylist1.push_back(i);      // mylist1: 1 2 3 4

  for (int i=1; i<=3; ++i)
     mylist2.push_back(i*10);   // mylist2: 10 20 30

  it = mylist1.begin();
  ++it;                         // points to 2

  mylist1.splice (it, mylist2); // mylist1: 1 10 20 30 2 3 4
                                // mylist2 (empty)
                                // "it" still points to 2 (the 5th element)
                                          
  mylist2.splice (mylist2.begin(),mylist1, it);  //插入在it位置的字符
                                // mylist1: 1 10 20 30 3 4
                                // mylist2: 2
                                // "it" is now invalid.
  it = mylist1.begin();
  std::advance(it,3);           // "it" points now to 30

  mylist1.splice ( mylist1.begin(), mylist1, it, mylist1.end());
                                // mylist1: 30 3 4 1 10 20
```

**list::unique**

```c++
1 2 2 2 3 2 2   ->    1 2 3 2        适合搭配sort使用
```



## 8. 继承

**微思考：如何设计出一个无法被继承的类？    ----把父类所有成员全部设为private，或者基类后边加final关键字**

**补充：如果一个子类有多个父类，按照声明顺序构造父类切片，与初始化列表无关**

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220505213249854.png" alt="image-20220505213249854" style="zoom: 67%;" />   

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220505213419522.png" alt="image-20220505213419522" style="zoom:67%;" />



```c++
#include<iostream>
using namespace std;
class person {
protected:
	string _name ;
	int _num ;
	static int _count;
public:
	person(string name="", int num=111) {
		_name = name; _num = num;
		cout << "person 构造" << endl;
	}
	person(person& x) {
		_name = x._name;
		_num = x._num;
		cout << "person 拷贝" << endl;
	}
	~person() {
		_name = ""; _num = 0;
		cout << "person ~析构" << endl;
	}
	void print() {
		cout << _name << ' ' << _num << endl;
	}
	person& operator=(person& x) {
		_name = x._name;
		_num = x._num;
		cout << "person operator=" << endl;
		return *this;
	}
};

class student :public person {
public:
	//  隐藏  or  重定义
	//当子类父类有同名变量，子类会屏蔽父类变量，即隐藏
	//子类父类，只要函数重名，就构成隐藏关系，非重载关系，也需要加作用域
	//重载需要在同一个作用域
	void print(int x) {
		cout << _name << ' ' << _num << endl;
		cout <<person:: _name << ' ' << person::_num << endl;
	}
	//先调用person把父类的成员初始化，再初始化自己特有的
	student(string name="", int num=111, int score=1) :
		person(name, num), _score(score) {
		cout << "student 构造" << endl;
	}
	student(student& x) :
		person(x), _score(x._score) {
		cout << "student 拷贝" << endl;
	}
	~student() { //因为多态的原因，析构统一被编译器处理为 destructor()，导致隐藏
		person::~person();
		_score = 0;
		cout << "student ~析构" << endl;
		//为了保证析构时先进后出的顺序，析构完子类后，自动去调用父类析构，
		//导致父类析构多调用了一次
	} 
	student& operator=(student& x) {
		if (this == &x) exit(1);
		person::operator=(x); //必须加类域突破隐藏，不然无限递归自己
		_score = x._score;
		cout << "student operator=" << endl;
		return *this;
	}
	
protected:
	int _num ;
	string _name ;

	int _score ;
};

// friend友元关系不能被继承，老老实实多敲一行代码就好

int person::_count = 0;   //静态成员属于父子所有类

int main()
{
	/*student s;
	s.print(10);
	s.person::print();*/

	student s1("haoye", 112, 12);
	student s2(s1);
	student s3;
	s1 = s3;
}
```



### 多继承

```c++
#include<iostream>
using namespace std;

class student {
protected:
	string _name;
	int _num;
};

class teacher :virtual public student{

};

//虚继承可以解决多继承的二义性和数据冗杂
class person :virtual public student, virtual public teacher {
	void nothing() {
		/*student::_name = "haoye";
	    teacher::_name = "woqu";*/
		_name = "e";
	}
};
int main()
{

}
```

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220507152214575.png" alt="image-20220507152214575" style="zoom: 50%;" />

**普通多继承**

```c++
class A {
public:
	int _a = 1;
};
class B :public A{
public:
	int _b = 1;
};
class C :public A {
public:
	int _c = 1;
};
class D :public B, public C {
public:
	int _d = 1;
};

D d;
	d.B::_a = 11;
	d.C::_a = 2;
	d._b = 3;
	d._c = 4;
	d._d = 5;
	cout << d.C::_a << ' ' << d._b << ' ' << d._c << ' ' << d._d << endl;
	// d.D::B::_b;  这么写都没问题，但是单独写d._a会报错
```

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220507194910290.png" alt="image-20220507194910290" style="zoom: 50%;" />



**虚继承**

```c++
cout << person::name << ' ' << student::name << ' ' << teacher::name << endl;class A {
public:
	int _a = 1;
11
    1116165
class B :virtual public A{
public:
	int _b = 1;
};
class C :virtual public A {
public:
	int _c = 1;
};
class D :virtual public B, virtual public C {
public:
	int _d = 1;
};

D d;
	d.B::_a = 11;
	d.C::_a = 2;
	d._b = 3;
	d._c = 4;
	d._d = 5;
	cout << d._a << ' ' << d._b << ' ' << d._c << ' ' << d._d << endl;
```

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220507200218433.png" alt="image-20220507200218433" style="zoom: 50%;" />

## 9. 多态

**多态即多种形态。    静态的多态如函数重载， 动态的多态如虚函数**

**多态条件： 必须通过基类的指针/引用调用虚函数； 必须调用“虚函数”，且子类必须对基类的虚函数进行重写 **

**正常虚函数要求--函数名， 返回值，传参都一样，如下：**

```c++
#include <iostream>
using namespace std;

class person {
public:
	virtual void ticket() { cout << "all" << endl; }
};

class student :public person{
public:
	virtual void ticket() { cout << "half" << endl; }
};

class adult :public person{
public:
	virtual void ticket() { cout << "all" << endl; }
};

class soldier :public person{
public:
	virtual void ticket() { cout << "priority" << endl; }
};

void f(person& x) { //如果去掉&，则不构成多态，清一色调用person::f()，和传入的对象类型无关
	x.ticket();
}
//构成多态，指向谁就调用谁的虚函数，跟对象（把adult传入person&）有关，不构成多态，则跟对象类型（如person）有关
int main()
{
	adult a; student b; soldier c;
	f(a), f(b), f(c);
}



//区分重载和多态
#include<iostream>
using namespace std;
class Base {
public:
	virtual void fn(int x) { cout << "fu" << endl; }
};
class Sub : public Base {
public:
	virtual void fn(double x) { cout << "zi" << endl; }
};
void f(Base& x) {
	x.fn(3.5);
	x.fn(2);
}
int main()
{
	Base b;Sub s;
	f(b);f(s);
}
```

### 瑕疵品--协变

**允许返回值不同，但必须返回  基类/子类的  引用或指针**

```c++
class A {};   

class B :public A {};

class person {
public:
	virtual A* f() { cout << "A-person" << endl;  return new A; }
};

class student :public person{
public:
	virtual B* f() { cout << "B-student" << endl; return new B; }
};
```

### 争议品--析构函数的特殊性

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220507230253339.png" alt="image-20220507230253339" style="zoom: 50%;" />

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220507230442453.png" alt="image-20220507230442453" style="zoom: 50%;" />

### 纯虚函数

```c++
#include<iostream>
using namespace std;
//抽象类，不能实例化对象，但能用指针/引用， 他的意义：
//1. 对应现实世界中没有实例的事物，比如 人 植物
//2. 体现接口继承，强制子类重写虚函数
class Car {
public:
	//纯虚函数
	virtual void drive() = 0;
};
class Benz :public Car {
	virtual void drive() {
		cout << "benz" << endl;
	}
};
class Wo :public Car {
	virtual void drive() {
		cout << "haoye" << endl;
	}
};
int main()
{
	Benz b; Wo w;
	Car* c;
	c = &b;
	c->drive();
	c = &w;
	c->drive();
}
```

**虚函数面试题： 求sizeof(Base)：      答案为16字节，并不是普通内存对齐用到的8字节，因为每个虚函数都有 虚表指针**  

```c++
class Base {
public:
	virtual void func() {
		cout << "func 1" << endl;
	}
    virtual void haoye() {
		cout << "haoye" << endl;
	}
private:
	int _b = 1;
	char _ch = '1';
};  
```

<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220508194615249.png" alt="image-20220508194615249" style="zoom:50%;" />

**思考题：为什么多态（写虚函数时）必须用到基类的指针/引用？**

**答： 因为子类的切片中，成员可以传给父类，但虚表指针vfptr不行（父类有子类的vfptr不奇怪吗？会导致混乱）**

​       **因此，必须引用或指针，才能收到正确的vfptr，进而调用虚函数**



<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220509192323991.png" alt="image-20220509192323991" style="zoom: 67%;" />

**虚表指针有自己的地址，他的值是虚函数的地址，以空指针结尾。    **



<img src="https://gitee.com/Allorry/cloud-notes/raw/master/img/image-20220509202011425.png" alt="image-20220509202011425" style="zoom:67%;" />

**虚表在构造函数中产生。所以构造不能为虚函数， 而虚函数一定是动态的成员函数，不能是内联函数**

## ?. 其他

### easyx

```c++
#include<stdio.h>
#include<easyx.h>
int main()
{
	//initgraph(400, 400, SHOWCONSOLE | NOMINIMIZE | NOCLOSE);  //三个功能一应俱全
	initgraph(640, 480, EW_SHOWCONSOLE);   //长 宽 显示操作台(专门给用户输入数据)
	//设置背景颜色
	setbkcolor(GREEN);
	cleardevice();    //覆盖，可以用白色去覆盖初始的黑色
	//设置背景模式
	setbkmode(TRANSPARENT);



	// 画圆
	setfillcolor(YELLOW); //填充颜色
	setlinecolor(BLUE);
	setlinestyle(PS_SOLID, 5);  //实线+宽度
	circle(50, 50, 50);
	fillcircle(50, 150, 50);   //有边框填充
	solidcircle(50, 250, 50);  //无边框填充

	// 文字
	settextcolor(RED);
	settextcolor(RGB(190, 183, 229));
	outtextxy(50, 50, L"Hhhhhaoye");  //这里会出现一些错误，原因是字符集
	// 解决方案1. L""      2. char str[]      3. 改为多字节字符集   

	fillrectangle(300, 300, 400, 400);  //左上坐标到右下坐标
	settextcolor(RGB(0, 0, 13));
	outtextxy(350, 350, L"wolegedacao");


	system("pause");
	closegraph();
	return 0;
}


//播放音乐
#include<stdio.h>
#include<graphics.h>   
#include<mmsystem.h>       //包含多媒体设备接口的头文件    
#pragma comment(lib,"winmm.lib")  //加载静态库
void BGM() {
	//alias 别名 
	mciSendString("open  ./One B-Boy.mp3   alias BGM",0,0,0);
	mciSendString("play  BGM    repeat", 0, 0, 0);
}
```



