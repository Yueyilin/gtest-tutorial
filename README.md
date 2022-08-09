# 简介
主要用于写单元测试，检查真自己的程序是否符合预期行为
googletest 是测试技术团队根据 Google 的特定要求和约束条件开发的测试框架。支持 Linux、Windows 还是 Mac 上工作，如果您编写 C++ 代码，googletest 都可以为您提供帮助。它支持任何类型的测试，而不仅仅是单元测试。
# 环境构建
参考：[https://google.github.io/googletest/quickstart-cmake.html](https://google.github.io/googletest/quickstart-cmake.html)
直接 `git clone https://github.com/google/googletest.git`也可以

# 用例
本文的源码：
代码结构
```cpp
.
├── build
├── CMakeLists.txt
├── include
│   ├── CMakeLists.txt
│   └── googletest # 上面 clone 的代码
└── src
    ├── gtest_demo1.cc
    └── gtest_demo2.cc

```
```cmake
cmake_minimum_required(VERSION 2.8.12)
project(gflags_test)
add_subdirectory(googletest)
```
```cmake
cmake_minimum_required(VERSION 2.8.12)

project(gflags_demo)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
## set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(EXECUTABLE_OUTPUT_PATH  ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

add_subdirectory(include)

add_executable(gtestexe1 src/gtest_demo1.cc)
target_link_libraries(gtestexe1 gtest)

add_executable(gtestexe2 src/gtest_demo2.cc)
target_link_libraries(gtestexe2 gtest)
```
```cpp
#include <gtest/gtest.h>

int add(int lhs, int rhs) { return lhs + rhs; }

int main(int argc, char const *argv[]) {

    EXPECT_EQ(add(1,1), 2); // PASS
    EXPECT_EQ(add(1,1), 1) << "FAILED: EXPECT: 2, but given 1";; // FAILDED
    
    return 0;
}
```
```cpp
#include <limits.h>
#include "gtest/gtest.h"

int Factorial(int n) {
  int result = 1;
  for (int i = 1; i <= n; i++) {
    result *= i;
  }
  return result;
}

bool IsPrime(int n) {
  if (n <= 1) return false;
  if (n % 2 == 0) return n == 2;
  for (int i = 3; ; i += 2) {
    if (i > n/i) break;
    if (n % i == 0) return false;
  }
  return true;
}

// Tests Factorial()
TEST(FactorialTest, Negative) {
  // test case.
  EXPECT_EQ(1, Factorial(-5));
  EXPECT_EQ(1, Factorial(-1));
  EXPECT_GT(Factorial(-10), 0);
}
// Tests factorial of 0.
TEST(FactorialTest, Zero) {
  EXPECT_EQ(1, Factorial(0));
}
// Tests factorial of positive numbers.
TEST(FactorialTest, Positive) {
  EXPECT_EQ(1, Factorial(1));
  EXPECT_EQ(2, Factorial(2));
  EXPECT_EQ(6, Factorial(3));
  EXPECT_EQ(40320, Factorial(8));
}

// Tests IsPrime()
// Tests negative input.
TEST(IsPrimeTest, Negative) {
  // This test belongs to the IsPrimeTest test case.
  EXPECT_FALSE(IsPrime(-1));
  EXPECT_FALSE(IsPrime(-2));
  EXPECT_FALSE(IsPrime(INT_MIN));
}
// Tests some trivial cases.
TEST(IsPrimeTest, Trivial) {
  EXPECT_FALSE(IsPrime(0));
  EXPECT_FALSE(IsPrime(1));
  EXPECT_TRUE(IsPrime(2));
  EXPECT_TRUE(IsPrime(3));
}
// Tests positive input.
TEST(IsPrimeTest, Positive) {
  EXPECT_FALSE(IsPrime(4));
  EXPECT_TRUE(IsPrime(5));
  EXPECT_FALSE(IsPrime(6));
  EXPECT_TRUE(IsPrime(23));
}


int main(int argc, char **argv) {
  printf("Running main() from %s\n", __FILE__);
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```
# 学习
## Assertion
官方文档：[https://google.github.io/googletest/reference/assertions.html](https://google.github.io/googletest/reference/assertions.html)

在gtest中，是通过断言（assertion）来判断代码实现的功能是否符合预期。断言的结果分为

- success
- non-fatal failture
- fatal failture

根据断言失败的种类，gtest提供了两种断言函数：

- success：即断言成功，程序的行为符合预期，程序继续向下允许。
- non-fatal failure：即断言失败，但是程序没有直接crash，而是继续向下运行。
   - gtest提供了宏函数`EXPECT_XXX(expected, actual)`：如果condition(expected, actual)返回false，则EXPECT_XXX产生的就是non-fatal failure错误，并显示相关错误。
- fatal failure：断言失败，程序直接crash，后续的测试案例不会被运行。
   - gtest提供了宏函数`ASSERT_XXX(expected, actual)`。
> 发生故障时，EXPECT_宏会产生非致命故障并允许当前函数继续运行，而ASSERT_ 宏会产生致命故障并中止当前函数。在写单元测试时，更加倾向于使用`EXPECT_XXX`，因为`ASSERT_XXX`是直接crash退出的，可能会导致一些内存、文件资源没有释放，因此可能会引入一些bug。

具体的`EXPECT_XXX`、`ASSERT_XXX`函数及其判断条件，部分如下。
ASSERT_系列：
bool值检查

- `ASSERT_TRUE(val)`，期待结果是true
- `ASSERT_FALSE(val)`，期待结果是false

数值型数据检查

- `ASSERT_EQ(val1, val2)`，传入的是需要比较的两个数 equal
- `ASSERT_NE(val1, val2)`，not equal，不等于才返回true
- `ASSERT_LT(val1, val2)`，less than，小于才返回true
- `ASSERT_GT(val1, val2)`，greater than，大于才返回true
- `ASSERT_LE(val1, val2)`，less equal，小于等于才返回true
- `ASSERT_GE(val1, val2)`，greater equal，大于等于才返回true

字符串检查

- `ASSERT_STREQ(expected_str, actual_str)`，两个C风格的字符串相等才正确返回
- `ASSERT_STRNE(str1, str2)`，两个C风格的字符串不相等时才正确返回
- `ASSERT_STRCASEEQ(expected_str, actual_str)`
- `ASSERT_STRCASENE(str1, str2)`

`EXPECT_`系列：
和上面的类似，只需要把`ASSERT`换成`EXPECT`。
## Qucikstart
```cpp
#include <gtest/gtest.h>

int add(int lhs, int rhs) { return lhs + rhs; }

int main(int argc, char const *argv[]) {

    EXPECT_EQ(add(1,1), 2); // PASS
    EXPECT_EQ(add(1,1), 1) << "FAILED: EXPECT: 2, but given 1";; // FAILDED
    
    return 0;
}
```
编译执行后输出如下：
```
$ ./gtestexe1
/home/aillian/gtest_tutorial/src/gtest_demo1.cc:8: Failure
Expected equality of these values:
  add(1,1)
    Which is: 2
  1
FAILED: EXPECT: 2, but given 1

```
`EXPECT_EQ(add(1,1), 1)`后有个`<<`，因为gtest允许添加自定义的描述信息，当这个语句测试未通过时就会显示，比如上面的`FAILED: EXPECT: 2, but given 1`。这个`<<`和`std::ostream`接受的类型一致。

## TEST
两个函数分别是

- 计算阶乘
- 判断质数

使用gtest对其测试
```cpp
#include <limits.h>
#include "gtest/gtest.h"

int Factorial(int n) {
  int result = 1;
  for (int i = 1; i <= n; i++) {
    result *= i;
  }
  return result;
}

bool IsPrime(int n) {
  if (n <= 1) return false;
  if (n % 2 == 0) return n == 2;
  for (int i = 3; ; i += 2) {
    if (i > n/i) break;
    if (n % i == 0) return false;
  }
  return true;
}

// Tests Factorial()
TEST(FactorialTest, Negative) {
  // test case.
  EXPECT_EQ(1, Factorial(-5));
  EXPECT_EQ(1, Factorial(-1));
  EXPECT_GT(Factorial(-10), 0);
}
// Tests factorial of 0.
TEST(FactorialTest, Zero) {
  EXPECT_EQ(1, Factorial(0));
}
// Tests factorial of positive numbers.
TEST(FactorialTest, Positive) {
  EXPECT_EQ(1, Factorial(1));
  EXPECT_EQ(2, Factorial(2));
  EXPECT_EQ(6, Factorial(3));
  EXPECT_EQ(40320, Factorial(8));
}

// Tests IsPrime()
// Tests negative input.
TEST(IsPrimeTest, Negative) {
  // This test belongs to the IsPrimeTest test case.
  EXPECT_FALSE(IsPrime(-1));
  EXPECT_FALSE(IsPrime(-2));
  EXPECT_FALSE(IsPrime(INT_MIN));
}
// Tests some trivial cases.
TEST(IsPrimeTest, Trivial) {
  EXPECT_FALSE(IsPrime(0));
  EXPECT_FALSE(IsPrime(1));
  EXPECT_TRUE(IsPrime(2));
  EXPECT_TRUE(IsPrime(3));
}
// Tests positive input.
TEST(IsPrimeTest, Positive) {
  EXPECT_FALSE(IsPrime(4));
  EXPECT_TRUE(IsPrime(5));
  EXPECT_FALSE(IsPrime(6));
  EXPECT_TRUE(IsPrime(23));
}


int main(int argc, char **argv) {
  printf("Running main() from %s\n", __FILE__);
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

怎么使用gtest来测试这个函数的行为？
可以使用`EXPECT_EQ()`宏来判断：
```cpp
EXPECT_EQ(1, Factorial(-5)); // 测试计算负数的阶乘
EXPECT_EQ(1, Factorial(0));   // 测试计算0的阶乘
EXPECT_EQ(6, Factorial(3));   // 测试计算正数的阶乘 
```
但是当测试案例规模变大，不好组织。
因此，gtest提供了一个宏TEST(TestSuiteName, TestName)，用于组织不同场景的cases，这个功能在gtest中称为`test suite`。比如针对`Factorial`函数，输入是负数的cases为一组，输入是0的case为一组，正数cases为一组。
```cpp
// 正数为一组
TEST(FactorialTest, Negative) {
  EXPECT_EQ(1, Factorial(-5));
  EXPECT_EQ(1, Factorial(-1));
  EXPECT_GT(Factorial(-10), 0);
}
// 0
TEST(FactorialTest, Zero) {
  EXPECT_EQ(1, Factorial(0));
}
// 负数为一组
TEST(FactorialTest, Positive) {
  EXPECT_EQ(1, Factorial(1));
  EXPECT_EQ(2, Factorial(2));
  EXPECT_EQ(6, Factorial(3));
  EXPECT_EQ(40320, Factorial(8));
}
```
参考链接：[https://google.github.io/googletest/primer.html#simple-tests](https://google.github.io/googletest/primer.html#simple-tests)
要创建`TEST`：

1. 使用`TEST()`宏定义和命名测试函数。这些是不返回值的普通 C++ 函数。
1. 在此函数中，连同您想要包含的任何有效 C++ 语句，使用各种 googletest 断言来检查值。
1. 测试的结果由断言决定；如果测试中的任何断言失败（致命或非致命），或者如果测试崩溃，则整个测试失败。否则，它会成功。
```cpp
TEST(TestSuiteName, TestName) {
  ... test body ...
}
```
`TEST()`第一个参数是TestSuite的名称，第二个参数是TestSuite中的Test名称。这两个名称都必须是有效的 C++ 标识符，并且它们不应包含任何下划线 ( _)。测试的全名由其包含的TestSuite及其Test名称组成。来自不同TestSuite的测试可以具有相同的Test名称。

googletest 按测试套件对测试结果进行分组，因此逻辑相关的测试应该在同一个测试套件中；换句话说，他们的第一个参数 TEST()应该是相同的。在上面的示例中，我们有三个测试， `Negative`、`Zero`和`Positive`，它们属于同一个测试套件`FactorialTest`。
运行这些TEST，需要在sample1_unittest.cc的main函数中，添加`RUN_ALL_TESTS()`函数即可
```cpp
int main(int argc, char **argv) {
  printf("Running main() from %s\n", __FILE__);
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```
编译并运行，结果如下：
```cpp
Running main() from /home/aillian/gtest_tutorial/src/gtest_demo2.cc
[==========] Running 6 tests from 2 test suites.
[----------] Global test environment set-up.
[----------] 3 tests from FactorialTest
[ RUN      ] FactorialTest.Negative
[       OK ] FactorialTest.Negative (0 ms)
[ RUN      ] FactorialTest.Zero
[       OK ] FactorialTest.Zero (0 ms)
[ RUN      ] FactorialTest.Positive
[       OK ] FactorialTest.Positive (0 ms)
[----------] 3 tests from FactorialTest (0 ms total)

[----------] 3 tests from IsPrimeTest
[ RUN      ] IsPrimeTest.Negative
[       OK ] IsPrimeTest.Negative (0 ms)
[ RUN      ] IsPrimeTest.Trivial
[       OK ] IsPrimeTest.Trivial (0 ms)
[ RUN      ] IsPrimeTest.Positive
[       OK ] IsPrimeTest.Positive (0 ms)
[----------] 3 tests from IsPrimeTest (0 ms total)

[----------] Global test environment tear-down
[==========] 6 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 6 tests.

```

## TEST_F
暂略

# 官方示例
如果您像我们一样，想查看 [googletest 示例。](https://github.com/google/googletest/blob/main/googletest/samples) 示例目录中有许多注释良好的示例，展示了如何使用各种 googletest 功能。

- 示例 #1 显示了使用 googletest 测试 C++ 函数的基本步骤。
- 示例 #2 显示了针对具有多个成员函数的类的更复杂的单元测试。
- 示例 #3 使用测试夹具。
- 示例 #4 教您如何使用 googletest 并googletest.h一起充分利用这两个库。
- 示例 #5 将共享测试逻辑放在基础测试夹具中，并在派生夹具中重用它。
- 示例 #6 演示了类型参数化测试。
- 示例 #7 教授值参数化测试的基础知识。
- 示例 #8 显示了Combine()在值参数化测试中的使用。
- 示例 #9 展示了使用侦听器 API 来修改 Google Test 的控制台输出以及使用反射 API 来检查测试结果。
- 示例 #10 展示了使用侦听器 API 来实现原始内存泄漏检查器。
# 参考文献
[官方文档](https://google.github.io/googletest/)
[gtest的介绍和使用  ](http://www.uml.org.cn/Test/201905061.asp)
[手把手教你使用gtest写单元测试（1/2）](https://zhuanlan.zhihu.com/p/369466622)
[cmake应用：集成gtest进行单元测试](https://zhuanlan.zhihu.com/p/374998755)
[官方示例](https://google.github.io/googletest/samples.html)
[
](https://zhuanlan.zhihu.com/p/374998755)
