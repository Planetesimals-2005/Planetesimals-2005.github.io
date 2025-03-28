---
title: Junit断言和注解
date: 2025-03-28
---

# JUnit 断言（Assertions）

* 用途：通过比较实际结果与预期结果来判断测试是否通过，如果断言失败，测试会立即终止并抛出错误信息。

### JUnit 5 常用断言方法

| 方法                                         | 说明                                                                 |
|----------------------------------------------|----------------------------------------------------------------------|
| `assertEquals(expected, actual)`            | 验证两个值相等（对象需实现 `equals()` 方法）。                       |
| `assertTrue(condition)`                      | 验证条件为 `true`。                                                 |
| `assertFalse(condition)`                     | 验证条件为 `false`。                                                |
| `assertNull(object)`                         | 验证对象为 `null`。                                                 |
| `assertNotNull(object)`                      | 验证对象不为 `null`。                                                |
| `assertSame(expected, actual)`               | 验证两个对象引用同一实例。                                           |
| `assertNotSame(expected, actual)`            | 验证两个对象引用不同实例。                                           |
| `assertThrows(ExceptionType.class, executable)` | 验证代码块抛出指定异常（`executable` 是 Lambda 表达式或代码块）。    |
| `assertAll("description", executable1, ...)` | 批量验证多个断言（即使某个失败，仍会继续执行后续断言）。             |


    //1. ​验证非空返回值
    @Test
    void testReturnNotNull() {
    String result = someMethod(); // 调用被测试方法
    assertNotNull(result); // 验证返回值不为 null
    }


    //2. ​验证具体值
    @Test
    void testAdd() {
    int result = calculator.add(2, 3); // 假设被测试方法返回 5
    assertEquals(5, result); // 验证返回值等于 5
    }


    //3. ​验证布尔值
     @Test
    void testIsEven() {
    boolean result = numberUtil.isEven(4); // 假设返回 true
    assertTrue(result); // 验证为 true
    }


    //4. ​验证集合或数组
    @Test
    void testGetList() {
    List <String> result = service.getList();
    // 验证集合非空且包含特定元素
    assertNotNull(result);
    assertTrue(result.contains("apple"));
    }


    @Test
    void testArray() {
    int [] expected = {1, 2, 3};
    int [] result = someMethod(); // 返回数组
    assertArrayEquals(expected, result); // 验证数组内容一致
    }
    
    //5. ​验证对象属性
    @Test
    void testUserInfo() {
    User user = userService.getUserById(1);
    assertEquals("Alice", user.getName()); // 验证对象属性
    assertEquals(30, user.getAge());
    }

* 无返回值的方法（void）​

若方法返回 void，通常通过以下方式验证：

* 状态变更：检查对象状态是否被修改。
* 副作用：例如是否写入数据库、发送消息等
* 异常抛出：验证是否抛出预期异常。

示例如下：

    @Test
    void testUpdateUser() {
    User user = new User("Bob", 25);
    userService.updateUser(user); // void 方法
    // 假设通过其他方法验证状态变更
    User updatedUser = userService.getUserByName("Bob");
    assertEquals(25, updatedUser.getAge());
    }


* 值得注意的是 Assertion 的方法无法作为 if 的判断条件。
* 因为 if 的判断条件依赖 bool，而 assertion 通常没有返回值（void）

#Junit注解

* 用途：告诉 JUnit哪个方法是测试方法、哪些方法在测试前后运行、哪些测试需要忽略等等

# JUnit 核心注解详解（JUnit 4 vs JUnit 5）

以下是基于图片中断言方法的排版风格整理的 ​**JUnit 注解 Markdown 语法说明**​（已修正原图拼写错误）：

---

## JUnit 5 核心注解

| 注解                  | 说明                                                                 | 示例                                                                 |
|-----------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `@Test`               | 标记方法为**测试方法**                                               | `@Test void testMethod() { ... }`                                   |
| `@BeforeEach`         | 每个测试方法执行**前**运行（替代 JUnit4 的 `@Before`）               | `@BeforeEach void setup() { ... }`                                   |
| `@AfterEach`          | 每个测试方法执行**后**运行（替代 JUnit4 的 `@After`）                | `@AfterEach void cleanup() { ... }`                                  |
| `@BeforeAll`          | 所有测试方法执行**前**运行一次（需 `static`，替代 `@BeforeClass`）   | `@BeforeAll static void init() { ... }`                              |
| `@AfterAll`           | 所有测试方法执行**后**运行一次（需 `static`，替代 `@AfterClass`）    | `@AfterAll static void tearDown() { ... }`                           |
| `@DisplayName`        | 自定义测试类/方法的显示名称                                           | `@DisplayName("用户登录测试") @Test void testLogin() { ... }`       |
| `@Disabled`           | 禁用测试（替代 JUnit4 的 `@Ignore`）                                 | `@Disabled("功能待实现") @Test void testUnfinished() { ... }`       |

---

## 参数化测试注解（JUnit 5）

| 注解                  | 说明                                                                 | 示例                                                                 |
|-----------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `@ParameterizedTest`  | 标记方法为参数化测试                                                 | `@ParameterizedTest @ValueSource(ints = {1, 2}) void test(int num)` |
| `@ValueSource`        | 提供基础类型参数值                                                   | `@ValueSource(strings = {"apple", "banana"})`                       |
| `@CsvSource`          | 通过 CSV 格式传递多参数                                              | `@CsvSource({"1, Alice", "2, Bob"})`                                |
| `@MethodSource`       | 从指定方法获取参数流                                                 | `@MethodSource("dataProvider")`                                     |
