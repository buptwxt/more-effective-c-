# more effective c++读书笔记

## 基础议题
### 指针和引用的区别
1. 指针可以指向空，引用必须指向一个存在的对象，所以使用引用的代码效率比使用指针的高（不用判断空）。
2. 指针可以被重新赋值，指向其他对象；引用则总是指向初始化的对象，（修改引用就是修改引用的对象值，不是指向其他对象）。

使用引用情况：
>总是指向一个对象并且不改变指向
>重载操作符时（否则导致奇怪的语法 *ivec[5]）

使用指针情况：
>可能为空，可能改变指向

### 尽量使用C++风格的类型转换
C风格字符串不容易被识别出（grep），不能去除const属性

const_cast（const volatile属性）
static_cast （类似C规则转换）
dynamic_cast （父子类）
reinterpret_cast（重解释）
分工明确，可以定义宏，使用上更加方便

### 不要对数组使用多态
子类对象数组名赋值给父类对象数组参数，导致凌乱。
```
class BST{
    int a;
};
class SubBST: public BST{
    int b;
};
void func(BST array[]){
}  // 出错
```
多态和指针算法不能混合在一 起来用，所以数组与多态也不能用在一起。 

### 避免无用的缺省构造函数
1. 建立数组时

初始化列表解决构建数组
利用指针数组
placement new方法
```
void *rawMemory =
    operator new[](10*sizeof(EquipmentPiece));
EquipmentPiece *bestPieces =
static_cast<EquipmentPiece*>(rawMemory);
for (int i = 0; i < 10; ++i)
    new (&bestPieces[i]) EquipmentPiece( i );
for (int i = 9; i >= 0; --i)
   bestPieces[i].~EquipmentPiece();
operator delete[](rawMemory);
```
2. 作为模板的参数时
## 运算符
### 谨慎定义类型转换函数
用不使用语法关键字的等同的函数来替代转换运算符
explicit，编译器会拒绝为了隐式类型转换而调用构造函数
使用代理类
让编译器进行隐式类型转换所 造成的弊端要大于它所带来的好处，所以除非你确实需要，不要定义类型转换函数。

### 自增自减
前缀返回引用
后缀返回const常量，前缀与后缀靠int无名参数区别
前缀效率更高，如果没有特殊需求，使用前缀

### 不要重载&& ||
1. 破坏短路特性
2. 参数表达式顺序不确定
操作符重载的目的是使程序更容易阅读，书 写和理解，而不是用你的知识去迷惑其他人。如果你没有一个好理由重载操作符，就不要重 载。在遇到&&, ||, 和 ,时，绝对不要去重载。
&& || 短路特性弄没了
， 参数顺序弄没了

绝对不要重载这些符号
. .* :: ?:
new delete sizeof typeid
static_cast dynamic_cast const_cast reinterpret_cast

能重载
operator new operator delete
operator new[] operator delete[] +-*/%^&|~
! =<>+=-=*=/=%= ^=&=|=<<>> >>=<<=== != <=>=&&||++ -- , ->*-> () []
但是需要慎重啊


### 理解各种new和delete
new操作符号
operator new()函数
placement new
operator new[] 

delete 操作符
operator delete()函数
operator delete[]

### 在析构函数防止资源泄露
try catch也可以，但是代码冗余
auto_ptr 智能指针
通过句柄类包装对象

### 在构造函数中防止资源泄露
C++拒绝为没有完成构造操作的对象调用析构函数
C++ delete操作符可以操作NULL指针
对象在构造中抛出异常后 C++不负责清除对象，必须重新设计构造函数，常用方法是try catch，捕获异常后，执行清除代码。
const成员初始化呢？
private子函数一个一个try catch负责const 成员初始化
auto_ptr 理想方式
```
// 定义
const auto_ptr<AudioClip>theAudioClip; 
// 初始化
theAudioClip(audioClipFileName != ""
          ? new AudioClip(audioClipFileName)
: 0)
```


