# C++ 基础作业逐题讲解

本文档对应 `InfiniTensor/learning-cxx` 的 00 到 33 号练习，包含每一题的问题、答案思路、关键注释和语法点，以及本地编译运行命令。

## 环境与命令

### 推荐命令

仓库官方推荐使用 `xmake`：

```bash
xmake
xmake run learn 0
xmake run summary
```

其中：

- `xmake`：编译学习系统中的 `learn`、`summary` 等目标。
- `xmake run learn <编号>`：运行单个练习，例如 `xmake run learn 7`。
- `xmake run summary`：从 00 到 33 批量编译运行所有练习，并输出通过汇总。

### 本机安装 xmake 的方式

Windows PATH 中没有 xmake；WSL Ubuntu 可无 sudo 安装 xmake bundle：

```bash
mkdir -p ~/.local/bin ~/.cache/xmake-download
wget -O ~/.cache/xmake-download/xmake-bundle-v3.0.9.linux.x86_64 \
  https://github.com/xmake-io/xmake/releases/download/v3.0.9/xmake-bundle-v3.0.9.linux.x86_64
cp ~/.cache/xmake-download/xmake-bundle-v3.0.9.linux.x86_64 ~/.local/bin/xmake
chmod +x ~/.local/bin/xmake
```

该 bundle 在当前 WSL 缺少 `libncurses.so.6`，无 sudo 时可下载 Ubuntu 包并只解压到用户目录：

```bash
mkdir -p ~/.local/xmake-libs ~/.cache/xmake-download
cd ~/.cache/xmake-download
apt-get download libncurses6
dpkg-deb -x libncurses6_*.deb ~/.local/xmake-libs
```

之后运行时带上库路径：

```bash
export LD_LIBRARY_PATH="$HOME/.local/xmake-libs/usr/lib/x86_64-linux-gnu:${LD_LIBRARY_PATH:-}"
export PATH="$HOME/.local/bin:$PATH"
xmake --version
xmake
xmake run summary
```

### 验证结果

已在 WSL Ubuntu 下执行官方流程：

```bash
xmake
xmake run summary
```

结果为：

```text
34/34 [##################################]
```

## 00 Hello World

问题：修复输出语句，打印 `Hello, InfiniTensor!` 并换行。

答案：使用 `std::cout << "Hello, InfiniTensor!" << std::endl;`。

注释：原代码把流插入运算符写错了，字符串输出要用 `<<`，不能用 `:` 或 `+ std::endl`。

语法点：`std::cout` 是标准输出流，`std::endl` 输出换行并刷新缓冲区。

## 01 变量与加法

问题：定义变量 `x`，让后续打印 `x + x = x + x` 能编译运行。

答案：`auto x = 1;`。

注释：`auto` 根据初始化表达式推导类型，这里 `1` 推导为 `int`。

语法点：变量必须先声明后使用；`<<` 可以连续把多个值输出到同一个流。

## 02 函数声明和定义

问题：`main` 在 `add` 定义之前调用 `add`，需要补声明和函数体。

答案：在 `main` 前写 `int add(int a, int b);`，函数体返回 `a + b`。

注释：C++ 编译器从上到下看代码，函数在调用前至少要有声明。

语法点：函数声明只说明签名，函数定义提供实现；参数按值传递。

## 03 实参与形参

问题：填写 `arg` 和 `param` 的正确值。

答案：调用前 `arg == 99`，函数内初始 `param == 99`，自增后 `param == 100`，返回后 `arg == 99`。

注释：`func(int param)` 是按值传递，`param` 是 `arg` 的副本，函数内修改不会影响外部变量。

语法点：argument 是调用时传入的实参，parameter 是函数定义里的形参。

## 04 static 关键字

问题：判断局部静态变量多次调用时的值。

答案：五次返回值分别为 `5, 6, 7, 8, 9`。

注释：`static int static_ = param;` 只在第一次调用时初始化一次，后续调用不会再用新的 `param` 初始化。

语法点：函数内 `static` 变量具有静态存储期；函数前 `static` 表示内部链接。

## 05 constexpr

问题：原来计算 `constexpr fibonacci(90)` 编译期递归太深，需要修改一处使代码编译运行。

答案：把 `ANS_N` 改为 `20`。

注释：`constexpr` 要求编译期求值，递归过深可能超过编译器限制。改小输入能让编译期计算完成。

语法点：`constexpr` 表示可用于常量表达式；递归 `constexpr` 函数仍受编译器求值深度限制。

## 06 数组缓存

问题：补全数组缓存版 Fibonacci，并填写数组字节数。

答案：缓存命中返回 `arr[i]`，否则递归计算并写回；`sizeof(arr) == 90 * sizeof(unsigned long long)`。

注释：全局数组 `arr[90]{0, 1}` 已初始化前两项，其余为 0，可用非 0 判断是否已缓存。

语法点：数组名代表连续存储的一段元素；`sizeof(arr)` 得到整个数组字节数，不是指针大小。

## 07 循环缓存

问题：把递归 Fibonacci 改成循环缓存计算。

答案：初始化 `cache{0, 1}` 和 `cached = 1`，循环计算到目标下标。

注释：`cached` 表示当前已经算出的最大下标；循环中计算 `cache[cached + 1]`。

语法点：`static` 局部数组保留跨函数调用的缓存状态；循环避免指数级递归。

## 08 指针和步长

问题：判断从 `ptr` 开始、每隔 `stride` 取一个元素组成的序列是否满足 Fibonacci 递推。

答案：循环检查 `ptr[(i + 2) * stride] == ptr[i * stride] + ptr[(i + 1) * stride]`。

注释：`stride` 表示逻辑相邻元素在内存中的间隔。

语法点：指针可以用下标语法访问偏移元素；数组传参会退化为指针。

## 09 enum 和 union

问题：用 union 实现 `Color` 到 `ColorEnum` 的类型双关转换。

答案：先写入 `pun.c = c;`，再读取 `pun.e`。

注释：这展示了 union 共用内存的效果。题目也提醒，在 C++ 中这种无关类型读取通常不是良定义写法。

语法点：`enum class` 是有作用域枚举，访问枚举值要写 `Color::Red`；普通 `enum` 会把名字放进外层作用域。

## 10 trivial 类型

问题：初始化结构体缓存，并实现结构体版本 Fibonacci。

答案：`FibonacciCache fib{{0, 1}, 2};`，循环更新 `cache.cache[cache.cached]`。

注释：读取未初始化结构体成员是未定义行为，所以必须显式初始化数组前两项和计数器。

语法点：聚合初始化可一次初始化数组成员和普通成员。

## 11 方法

问题：把 Fibonacci 计算封装进结构体成员函数。

答案：结构体初始化为 `Fibonacci fib{{0, 1}, 2};`，成员函数里用 `this` 对象的 `cache` 和 `cached`。

注释：成员函数可以直接访问同一个对象的成员变量。

语法点：`struct` 默认 public；成员函数隐藏了一个 `this` 参数。

## 12 const 成员函数

问题：让 `constexpr const` 对象能调用 `get`。

答案：把方法签名改成 `int get(int i) const`，返回 `numbers[i]`。

注释：const 对象只能调用 const 成员函数。

语法点：成员函数末尾的 `const` 修饰隐藏的 `this` 指针，表示不会修改对象。

## 13 class 和构造函数

问题：私有成员不能在外部聚合初始化，需要构造函数。

答案：`Fibonacci(): cache{0, 1}, cached(2) {}`，`get` 中循环补缓存。

注释：`class` 默认 private，因此外部不能直接初始化字段。

语法点：构造函数初始化列表用于初始化成员；`class` 和 `struct` 主要区别是默认访问控制。

## 14 析构函数

问题：动态分配任意容量的缓存，并释放内存。

答案：构造时 `new size_t[capacity]{0, 1}`，析构时 `delete[] cache`。

注释：谁申请资源，谁负责释放。这里用 RAII 把释放绑定到对象生命周期。

语法点：数组 new 必须配数组 delete；析构函数名为 `~ClassName()`。

## 15 复制构造

问题：动态数组对象需要支持复制，不能共享同一块内存。

答案：新增 `capacity` 字段；复制构造分配新数组并逐项复制。

注释：浅拷贝会导致两个对象持有同一个指针，析构时重复释放。深拷贝让每个对象拥有自己的资源。

语法点：复制构造函数形如 `ClassName(ClassName const &other)`。

## 16 移动语义

问题：实现移动构造和移动赋值，并处理自移动。

答案：移动时转移 `cache` 指针、`cached`、`capacity`，再把源对象 `cache` 置为 `nullptr`；移动赋值先判断 `this != &other`。

注释：移动语义不是复制数据，而是转移资源所有权。

语法点：右值引用使用 `&&`；`noexcept` 表示移动操作不会抛异常；`std::move` 把左值转换成右值引用。

## 17 派生类大小和构造顺序

问题：填写 `X`、`A`、`B` 的对象大小。

答案：`sizeof(X) == sizeof(int)`，`sizeof(A) == sizeof(int)`，`sizeof(B) == sizeof(A) + sizeof(X)`。

注释：`B` 继承自 `A`，并额外包含一个 `X` 成员，所以对象里有基类子对象和成员子对象。

语法点：派生类构造顺序是先基类，再成员，再派生类构造函数体；析构顺序相反。

## 18 虚函数

问题：填写虚函数调用和普通成员函数调用的返回字符。

答案：直接对象调用返回各自类型；经基类引用调用时，`virtual_name` 按动态类型分派，`direct_name` 按静态类型绑定。

注释：`D` 没有覆盖 `virtual_name`，继承 `C` 的 final 版本，因此虚调用返回 `C`。

语法点：`virtual` 开启动态绑定；`override` 检查覆盖；`final` 禁止继续覆盖。

## 19 虚析构和 dynamic_cast

问题：修复静态成员初始化、基类指针删除派生对象、基类引用转派生引用。

答案：静态计数字段用 `inline static`；`A` 的析构函数改为 `virtual`；转换用 `dynamic_cast<B &>(*ab)`。

注释：`new B` 会构造一个 `A` 子对象和一个 `B` 对象，所以计数分别变化。通过 `A *` 删除 `B` 时必须有虚析构。

语法点：多态类型可用 `dynamic_cast` 做运行时安全向下转换。

## 20 函数模板

问题：让 `plus` 支持多种数值类型，并修复浮点比较。

答案：写成 `template<class T> T plus(T a, T b)`；`0.1 + 0.2` 用误差范围判断。

注释：二进制浮点不能精确表示很多十进制小数，`0.1 + 0.2 == 0.3` 通常不成立。

语法点：函数模板由编译器根据实参类型实例化。

## 21 运行时类型分派

问题：把 `sigmoid` 模板化，并根据 tagged union 的 `type` 选择 float 或 double。

答案：`template<class T> T sigmoid(T x)`；`switch (x.type)` 后写入 `ans.f` 或 `ans.d`。

注释：`TaggedUnion` 用一个标签说明 union 里当前有效的是哪个成员。

语法点：union 多个成员共用内存；运行时类型分派常用 `switch`。

## 22 类模板和 4D 广播

问题：实现模板化 4D Tensor 的构造和单向广播加法。

答案：构造时复制 shape 并累乘 size；加法时逐元素计算当前下标和广播后的 `others` 下标。

注释：广播维度长度为 1 时，该维度永远取 0；否则取当前维度坐标。

语法点：类模板 `template<class T>` 让同一套 Tensor 逻辑支持 `int`、`float`、`double` 等类型。

## 23 非类型模板参数

问题：实现任意维 Tensor 的 shape 初始化和线性下标计算。

答案：构造时循环复制 `shape[i]` 并累乘总大小；下标公式为 `index = index * shape[i] + indices[i]`。

注释：这是 row-major 连续存储布局。

语法点：`template<unsigned int N, class T>` 中的 `N` 是非类型模板参数，编译期确定维度数。

## 24 std::array

问题：填写 `std::array` 的大小、字节数和数据指针访问。

答案：`arr.size()` 是元素个数；`sizeof(arr)` 是元素总字节数；底层指针用 `arr.data()`。

注释：`std::array<T, N>` 是固定长度容器，数据就在对象内部。

语法点：`std::memcmp` 比较原始字节；`std::strcmp` 比较 C 字符串。

## 25 std::vector

问题：填写 vector 的 size、capacity、元素访问、插入删除和构造。

答案：用 `vec.size()` 判断元素个数；`sizeof(std::vector<T>)` 判断对象本身大小；`vec.data()` 取连续数据指针；插入用 `vec.insert(vec.begin() + 1, 1.5)`；删除用 `vec.erase(vec.begin() + 3)`。

注释：vector 对象本身不是数组，它内部管理堆内存。`capacity` 可能大于 `size`，不同实现增长策略也可能不同，所以用范围判断避免硬编码。

语法点：`push_back` 追加，`pop_back` 删除末尾，`resize` 改元素个数，`reserve` 预留容量，`clear` 清空但通常不释放容量。

## 26 std::vector<bool>

问题：补全 `std::vector<bool>` 的构造、代理引用和布尔判断。

答案：构造 `std::vector<bool> vec(100, true)`；设置元素后用 `!vec[index]` 判断 false；`auto ref = vec[30]` 得到代理对象，修改 ref 会影响 vector。

注释：`std::vector<bool>` 是特化版本，按 bit 压缩存储，`operator[]` 返回的不是普通 `bool &`。

语法点：模板特化可以让某个类型参数拥有不同实现。

## 27 strides

问题：根据连续张量 shape 计算每个维度的 stride。

答案：从最后一维向前累乘，最后一维 stride 为 1。

注释：形状 `[2, 3, 4]` 的 strides 是 `[12, 4, 1]`，表示第 0 维加 1 跨 12 个元素。

语法点：倒序循环可写成 `for (auto i = shape.size(); i-- > 0;)`。

## 28 std::string

问题：判断字符串字面量和 `std::string` 字面量的类型，并拼接字符串。

答案：`"Hello"s` 类型为 `std::string`，`"world"` 类型为 `const char *`，拼接结果为 `"Hello, world!"`。

注释：`using namespace std::string_literals;` 后，后缀 `s` 会生成 `std::string`。

语法点：`decltype(expr)` 取得表达式类型；`std::is_same_v<A, B>` 编译期判断类型是否相同。

## 29 std::map

问题：实现 key 是否存在和设置键值。

答案：`key_exists` 用 `map.find(key) != map.end()`；`set` 用 `map[key] = value`。

注释：`operator[]` 在 key 不存在时会插入默认值，因此只检查存在性时更适合 `find`。

语法点：`std::map` 是有序关联容器，按 key 查找 value。

## 30 std::unique_ptr

问题：分析三个表达式中资源生命周期的析构记录。

答案：Ubuntu/GCC 下结果为 `{"fd"}`、`{"d", "ffr"}`、`{"d", "d", "r"}`；MSVC 下临时对象析构顺序不同，代码中用 `_MSC_VER` 分支兼容。

注释：`unique_ptr` 独占资源，函数参数和返回值移动所有权。题目也说明结果依赖平台对象析构逻辑。

语法点：`std::unique_ptr` 不能复制，只能移动；临时对象销毁时会调用资源析构函数。

## 31 std::shared_ptr

问题：填写每一步 `weak_ptr::use_count()`。

答案：依次为 `4, 3, 2, 1, 3, 2, 3, 0, 0`。

注释：`weak_ptr` 不增加引用计数，但能观察控制块；`lock()` 成功时会创建一个新的 `shared_ptr` 并增加计数。

语法点：`shared_ptr` 共享所有权，最后一个 `shared_ptr` 销毁后对象释放，`weak_ptr::lock()` 在对象已释放时返回空指针。

## 32 std::transform

问题：把整数 vector 中每个元素乘 2 后转成字符串。

答案：创建 `std::vector<std::string> ans;`，调用 `std::transform(val.begin(), val.end(), std::back_inserter(ans), lambda)`。

注释：`back_inserter` 会让算法通过 `push_back` 写入结果容器。

语法点：lambda 表达式 `[](int v) { return std::to_string(v * 2); }` 可作为算法回调。

## 33 std::accumulate

问题：计算 `float` 类型、shape 为 `[1, 3, 224, 224]` 的连续张量字节数。

答案：初始值用 `sizeof(DataType)`，对每个维度累乘，结果为 `4 * 1 * 3 * 224 * 224 = 602112`。

注释：连续张量的元素数是各维度乘积，总字节数是元素数乘元素类型大小。

语法点：`std::accumulate(begin, end, init, op)` 用初值和二元函数归约一个区间。

## 每题核心修改代码片段

下面只列出每题最关键的修改代码，方便复习和对照题目。完整代码见各题的 `main.cpp`。

### 00

```cpp
std::cout << "Hello, InfiniTensor!" << std::endl;
```

### 01

```cpp
auto x = 1;
```

### 02

```cpp
int add(int a, int b);

int add(int a, int b) {
    return a + b;
}
```

### 03

```cpp
ASSERT(arg == 99, "arg should be 99");
func(arg);
ASSERT(arg == 99, "arg should be 99");

ASSERT(param == 99, "param should be 99");
param += 1;
ASSERT(param == 100, "param should be 100");
```

### 04

```cpp
ASSERT(func(5) == 5, "static variable value incorrect");
ASSERT(func(4) == 6, "static variable value incorrect");
ASSERT(func(3) == 7, "static variable value incorrect");
ASSERT(func(2) == 8, "static variable value incorrect");
ASSERT(func(1) == 9, "static variable value incorrect");
```

### 05

```cpp
constexpr auto ANS_N = 20;
constexpr auto ANS = fibonacci(ANS_N);
```

### 06

```cpp
return arr[i] ? arr[i] : (arr[i] = fibonacci(i - 1) + fibonacci(i - 2));

ASSERT(sizeof(arr) == 90 * sizeof(unsigned long long), "sizeof array is size of all its elements");
```

### 07

```cpp
static unsigned long long cache[96]{0, 1};
static int cached = 1;

for (; cached < i; ++cached) {
    cache[cached + 1] = cache[cached] + cache[cached - 1];
}
return cache[i];
```

### 08

```cpp
for (int i = 0; i + 2 < len; ++i) {
    if (ptr[(i + 2) * stride] != ptr[i * stride] + ptr[(i + 1) * stride]) {
        return false;
    }
}
return true;
```

### 09

```cpp
TypePun pun;
pun.c = c;
return pun.e;
```

### 10

```cpp
for (; cache.cached <= i; ++cache.cached) {
    cache.cache[cache.cached] = cache.cache[cache.cached - 1] + cache.cache[cache.cached - 2];
}

FibonacciCache fib{{0, 1}, 2};
```

### 11

```cpp
for (; cached <= i; ++cached) {
    cache[cached] = cache[cached - 1] + cache[cached - 2];
}

Fibonacci fib{{0, 1}, 2};
```

### 12

```cpp
int get(int i) const {
    return numbers[i];
}
```

### 13

```cpp
Fibonacci(): cache{0, 1}, cached(2) {}

for (; cached <= i; ++cached) {
    cache[cached] = cache[cached - 1] + cache[cached - 2];
}
```

### 14

```cpp
DynFibonacci(int capacity): cache(new size_t[capacity]{0, 1}), cached(2) {}

~DynFibonacci() {
    delete[] cache;
}
```

### 15

```cpp
DynFibonacci(int capacity_): cache(new size_t[capacity_]{0, 1}), cached(2), capacity(capacity_) {}

DynFibonacci(DynFibonacci const &other)
    : cache(new size_t[other.capacity]), cached(other.cached), capacity(other.capacity) {
    for (int i = 0; i < capacity; ++i) {
        cache[i] = other.cache[i];
    }
}
```

### 16

```cpp
DynFibonacci(DynFibonacci &&other) noexcept
    : cache(other.cache), cached(other.cached), capacity(other.capacity) {
    other.cache = nullptr;
    other.cached = 0;
    other.capacity = 0;
}

DynFibonacci &operator=(DynFibonacci &&other) noexcept {
    if (this != &other) {
        delete[] cache;
        cache = other.cache;
        cached = other.cached;
        capacity = other.capacity;
        other.cache = nullptr;
        other.cached = 0;
        other.capacity = 0;
    }
    return *this;
}
```

### 17

```cpp
static_assert(sizeof(X) == sizeof(int), "There is an int in X");
static_assert(sizeof(A) == sizeof(int), "There is an int in A");
static_assert(sizeof(B) == sizeof(A) + sizeof(X), "B is an A with an X");
```

### 18

```cpp
ASSERT(a.virtual_name() == 'A', MSG);
ASSERT(b.virtual_name() == 'B', MSG);
ASSERT(c.virtual_name() == 'C', MSG);
ASSERT(d.virtual_name() == 'C', MSG);

ASSERT(rab.virtual_name() == 'B', MSG);
ASSERT(rbc.virtual_name() == 'C', MSG);
ASSERT(rcd.virtual_name() == 'C', MSG);

ASSERT(rab.direct_name() == 'A', MSG);
ASSERT(rbc.direct_name() == 'B', MSG);
ASSERT(rcd.direct_name() == 'C', MSG);
```

### 19

```cpp
inline static int num_a = 0;
virtual ~A() {
    --num_a;
}

inline static int num_b = 0;
~B() override {
    --num_b;
}

B &bb = dynamic_cast<B &>(*ab);
```

### 20

```cpp
template<class T>
T plus(T a, T b) {
    return a + b;
}

ASSERT(plus(0.1, 0.2) > 0.299 && plus(0.1, 0.2) < 0.301, "How to make this pass?");
```

### 21

```cpp
template<class T>
T sigmoid(T x) {
    return 1 / (1 + std::exp(-x));
}

switch (x.type) {
    case DataType::Float:
        ans.f = sigmoid(x.f);
        break;
    case DataType::Double:
        ans.d = sigmoid(x.d);
        break;
}
```

### 22

```cpp
for (auto i = 0u; i < 4; ++i) {
    shape[i] = shape_[i];
    size *= shape[i];
}

auto index = ((i0 * shape[1] + i1) * shape[2] + i2) * shape[3] + i3;
auto o0 = others.shape[0] == 1 ? 0 : i0;
auto o1 = others.shape[1] == 1 ? 0 : i1;
auto o2 = others.shape[2] == 1 ? 0 : i2;
auto o3 = others.shape[3] == 1 ? 0 : i3;
auto other_index = ((o0 * others.shape[1] + o1) * others.shape[2] + o2) * others.shape[3] + o3;
data[index] += others.data[other_index];
```

### 23

```cpp
for (unsigned int i = 0; i < N; ++i) {
    shape[i] = shape_[i];
    size *= shape[i];
}

index = index * shape[i] + indices[i];
```

### 24

```cpp
ASSERT(arr.size() == 5, "Fill in the correct value.");
ASSERT(sizeof(arr) == sizeof(int) * 5, "Fill in the correct value.");
ASSERT(std::memcmp(arr.data(), ans, sizeof(ans)) == 0, "Fill in the correct values.");
```

### 25

```cpp
ASSERT(sizeof(vec) == sizeof(std::vector<double>), "Fill in the correct value.");
ASSERT(std::memcmp(vec.data(), ans, sizeof(ans)) == 0, "Fill in the correct values.");

vec.insert(vec.begin() + 1, 1.5);
vec.erase(vec.begin() + 3);

std::vector<char> vec(48, 'z');
vec.reserve(256);
```

### 26

```cpp
std::vector<bool> vec(100, true);
vec[20] = false;
ASSERT(!vec[20], "Fill in `vec[20]` or `!vec[20]`.");

auto ref = vec[30];
ref = false;
ASSERT(!vec[30], "Fill in `vec[30]` or `!vec[30]`.");
```

### 27

```cpp
udim stride = 1;
for (auto i = shape.size(); i-- > 0;) {
    strides[i] = stride;
    stride *= shape[i];
}
```

### 28

```cpp
ASSERT((std::is_same_v<decltype(hello), std::string>), "Fill in the missing type.");
ASSERT((std::is_same_v<decltype(world), const char *>), "Fill in the missing type.");
ASSERT(hello + ", " + world + '!' == "Hello, world!", "Fill in the missing string.");
```

### 29

```cpp
return map.find(key) != map.end();

map[key] = value;
```

### 30

```cpp
#if defined(_MSC_VER)
        {"ffr", "d"},
        {"r", "d", "d"},
#else
        {"d", "ffr"},
        {"d", "d", "r"},
#endif
```

### 31

```cpp
ASSERT(observer.use_count() == 4, "");
ASSERT(observer.use_count() == 3, "");
ASSERT(observer.use_count() == 2, "");
ASSERT(observer.use_count() == 1, "");
ASSERT(observer.use_count() == 3, "");
ASSERT(observer.use_count() == 2, "");
ASSERT(observer.use_count() == 3, "");
ASSERT(observer.use_count() == 0, "");
ASSERT(observer.use_count() == 0, "");
```

### 32

```cpp
std::vector<std::string> ans;
std::transform(val.begin(), val.end(), std::back_inserter(ans), [](int v) {
    return std::to_string(v * 2);
});
```

### 33

```cpp
int size = std::accumulate(std::begin(shape), std::end(shape), static_cast<int>(sizeof(DataType)),
                           [](int bytes, int dim) {
                               return bytes * dim;
                           });
```

## 提交信息

作业页面填写：

- GitHub 仓库：`https://github.com/Risingsea0923/learning-cxx`
- PR 链接：留空
- Commit 链接：提交并推送后填写 `https://github.com/Risingsea0923/learning-cxx/commit/<commit_hash>`
- 附件：上传 `submission-learning-cxx.zip`

建议提交命令：

```bash
git add exercises HOMEWORK.md
git commit -m "Complete C++ learning exercises"
git push -u origin complete-cxx-homework
```
