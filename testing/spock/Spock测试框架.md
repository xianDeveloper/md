# Spock测试框架

## 介绍

Spock是一个为groovy和java语言应用程序来测试和规范的框架。这个框架的突出点在于它美妙和高效表达规范的语言。得益于JUnit runner，Spock能够在大多数IDE、编译工具、持续集成服务下工作。Spock的灵感源于JUnit,jMock, RSpec, Groovy, Scala, Vulcans以及其他优秀的框架形态。
 PS：如果有使用dubbo的同学，这里推荐一个dubbo-postman工具:[dubbo-postman](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Feverythingbest%2Fdubbo-postman)

## 基本概念描述

Spock要求具备基本的groovy和单元测试知识。如果是java程序员，则不需要对groovy感到陌生，因为会java基本就会使用groovy了。

###### Spock测试类的一般结构

首先看一下测试模板方法的定义和JUnit的对比：

![img](https:////upload-images.jianshu.io/upload_images/8864801-53209c9eb846e486.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

和JUnit对比

Spock基于Groovy,所以像写java测试类一样，需要先创建一个groovy文件。
 在一个groovy文件里面的第一行：import spock.lang.*,同时这个类需要继承Specification。
 举例:



```dart
class MyFirstSpecification extends Specification {
  // 变量字段
  // 测试模板方法
  // 测试方法
  // 测试帮助方法
}
```

Spock的模板方法说明：



```cpp
def setupSpec() {}    // runs once -  before the first feature method
def setup() {}        // runs before every feature method
def cleanup() {}      // runs after every feature method
def cleanupSpec() {}  // runs once -  after the last feature method
```

定义变量：



```ruby
def obj = new ClassUnderSpecification()
def coll = new Collaborator()
```

定义测试方法：



```ruby
def "pushing an element on the stack"() {
  // 测试代码块，最重要的地方
}
```

代码块结构和测试各个概念阶段的映射：





![img](https:////upload-images.jianshu.io/upload_images/8864801-c03ea584989adb69.png?imageMogr2/auto-orient/strip|imageView2/2/w/329/format/webp)

测试代码块各阶段映射

代码块结构在def定义的方法内容里面：



```cpp
given:
def stack = new Stack()
def elem = "push me"

when:   // 执行需要测试的代码
then:   // 验证执行结果
```

条件判断：



```cpp
when:
stack.push(elem)

then:
!stack.empty
stack.size() == 1
stack.peek() == elem
```

条件不满足的情况输出结果举例:



```ruby
Condition not satisfied:

stack.size() == 2
|     |      |
|     1      false
[push me]
```

验证抛出异常举例：



```cpp
when:
stack.pop()

then:
thrown(EmptyStackException)
stack.empty
```

访问异常内容：



```ruby
when:
stack.pop()

then:
def e = thrown(EmptyStackException)
e.cause == null
```

验证不会抛出异常举例：



```cpp
//hashmap允许null的key
def "HashMap accepts null key"() {
  given:
  def map = new HashMap()

  when:
  map.put(null, "elem")

  then:
  notThrown(NullPointerException)
}
```

基于测试的交互，通过mock构造依赖的外部对象



```css
def "events are published to all subscribers"() {
  given:
  def subscriber1 = Mock(Subscriber)//通过Mock构造一个依赖的对象
  def subscriber2 = Mock(Subscriber)
  def publisher = new Publisher()
  publisher.add(subscriber1)
  publisher.add(subscriber2)

  when:
  publisher.fire("event")

  then:
  1 * subscriber1.receive("event")//验证方法名为receive，以及参数"event"为的方法执行一次
  1 * subscriber2.receive("event")
}
```

expect代码块：



```jsx
expect:
Math.max(1, 2) == 2
```

cleanup代码块



```cpp
given:
def file = new File("/some/path")
file.createNewFile()

// ...

cleanup:
file.delete()
```

where代码块，可能是Spock最厉害的地方了：



```css
def "computing the maximum of two numbers"() {
  expect:
  Math.max(a, b) == c

  where:
  a << [5, 3]//执行两次测试，值依次为5，3，下面类似
  b << [1, 9]
  c << [5, 9]
}
```

测试的时候帮助方法的处理：



```css
def "offered PC matches preferred configuration"() {
  when:
  def pc = shop.buyPc()

  then:
  pc.vendor == "Sunny"
  pc.clockRate >= 2333
  pc.ram >= 4096
  pc.os == "Linux"
}
```

额外定义一个帮助方法：



```ruby
def "offered PC matches preferred configuration"() {
  when:
  def pc = shop.buyPc()

  then:
  matchesPreferredConfiguration(pc)
}

def matchesPreferredConfiguration(pc) {
  pc.vendor == "Sunny"
  && pc.clockRate >= 2333
  && pc.ram >= 4096
  && pc.os == "Linux"
}
```

输出结果，结果没有显示具体的哪个判断失败了：



```ruby
Condition not satisfied:

matchesPreferredConfiguration(pc)
|                             |
false                         ...
```

使用assert可以达到这个目的：



```java
void matchesPreferredConfiguration(pc) {
  assert pc.vendor == "Sunny"
  assert pc.clockRate >= 2333
  assert pc.ram >= 4096
  assert pc.os == "Linux"
}
```

最终输出结果，可以看到具体的错误条件：



```ruby
Condition not satisfied:

assert pc.clockRate >= 2333
       |  |         |
       |  1666      false
       ...
```

在预期值断言的时候使用with：



```css
def "offered PC matches preferred configuration"() {
  when:
  def pc = shop.buyPc()

  then:
  with(pc) {
    vendor == "Sunny"
    clockRate >= 2333
    ram >= 406
    os == "Linux"
  }
}
```

使用mock对象的时候也可以用with：



```csharp
def service = Mock(Service) // has start(), stop(), and doWork() methods
def app = new Application(service) // controls the lifecycle of the service

when:
app.run()

then:
with(service) {
  1 * start()
  1 * doWork()
  1 * stop()
}
```

当多个预期值一起断言的时候使用verifyAll :



```css
def "offered PC matches preferred configuration"() {
  when:
  def pc = shop.buyPc()

  then:
  verifyAll(pc) {
    vendor == "Sunny"
    clockRate >= 2333
    ram >= 406
    os == "Linux"
  }
}
```

在没有对象参数的时候也可以这样：



```kotlin
expect:
  verifyAll {
    2 == 2
    4 == 4
  }
```

文档规范：



```cpp
given: "open a database connection"
// code goes here
```

and可以增加多个使代码根据可读性：



```cpp
given: "open a database connection"
// code goes here

and: "seed the customer table"
// code goes here

and: "seed the product table"
// code goes here
```

测试行为规范的一般模式：
 给定条件....
 当怎么样的时候...
 应该怎样



```dart
given: "an empty bank account"
// ...
when: "the account is credited $10"
// ...

then: "the account's balance is $10"
// ...
```

扩展注解：
 @Timeout 设置方法执行的时间
 @Ignore  跳过这个测试方法和JUnit类似
 @IgnoreRest  跳过其他所有的测试方法，只执行这个测试方法

### 数据驱动测试

在where里面可以通过数据表格，数据管道，指定变量三种情况对不同的测试case进行赋值，特别是在当需要测试很多种情况的的时候，这个模式具有非常高的可读性和简洁性：



```ruby
...
where:
a | _
3 | _
7 | _
0 | _

b << [5, 0, 0]

c = a > b ? a : b
```

### 基于测试的交互

场景如下，一个发送者向多个订阅者发送消息



```java
class PublisherSpec extends Specification {
  Publisher publisher = new Publisher()
  Subscriber subscriber = Mock()
  Subscriber subscriber2 = Mock()

  def setup() {
    publisher.subscribers << subscriber // << is a Groovy shorthand for List.add()
    publisher.subscribers << subscriber2
  }
```

期待执行一次接收方法：



```css
def "should send messages to all subscribers"() {
  when:
  publisher.send("hello")

  then:
  1 * subscriber.receive("hello")
  1 * subscriber2.receive("hello")
}
```

断言表达式解释：



```ruby
1 * subscriber.receive("hello")
|   |          |       |
|   |          |       关联参数
|   |          关联目标方法
|   关联目标对象
执行次数
```

在mock创建的时候可以定义多个交互行为：



```dart
class MySpec extends Specification {
    Subscriber subscriber = Mock {
        1 * receive("hello")
        1 * receive("goodbye")
    }
}
```

一个测试代码的多个交互通过when区分：



```csharp
when:
publisher.send("message1")

then:
1 * subscriber.receive("message1")

when:
publisher.send("message2")

then:
1 * subscriber.receive("message2")
```

结果输出：



```css
Too many invocations for:

2 * subscriber.receive(_) (3 invocations)
```

##### 打桩：

很多时候我们测试的时候不需要执行真正的方法，因为在测试代码里面构建一个真实的对象是比较麻烦的，特别是使用一些依赖注入框架的时候，因此有了打桩的功能：



```dart
interface Subscriber {
    String receive(String message)
}
```

模拟返回值：



```bash
subscriber.receive(_) >> "ok"
```

表达式解释：



```ruby
subscriber.receive(_) >> "ok"
|          |       |     |
|          |       |     任何参数的这个方法调用都会返回"ok"
|          |       argument constraint
|          method constraint
target constraint
```

需要调用多次返回不同结果的时候可以这样定义：



```bash
subscriber.receive(_) >>> ["ok", "error", "error", "ok"]
```

有时候我们测试的一个方法调用了类的其他方法，而其他的方法可能依赖了外部的对象，为了避免引入外部对象，可以使用部分mock功能减少构建外部对象的繁琐：



```csharp
// this is now the object under specification, not a collaborator
MessagePersister persister = Spy {
  // 这个方法的任意参数调用都返回true
  isPersistable(_) >> true
}

when:
persister.receive("msg")

then:
// when里面的receive会调用persist，前提是isPersistable是true，因为mock的值是true，这个方法一定会被调用
1 * persister.persist("msg")
```

### 和springboot集成

spring本身提供了spring-test测试模块，需要和Spock一起使用的话，只需要在Specification类上面加上SpringApplicationConfiguration这个注解就可以了，如果是mvc应用还需加上WebAppConfiguration这个，其他的使用和原来一样。

### maven配置

在pom.xml里面添加



```xml
<!-- Spock依赖 -->
    <dependency>
      <groupId>org.spockframework</groupId>
      <artifactId>spock-core</artifactId>
      <version>1.2-groovy-2.4</version>
      <scope>test</scope>
    </dependency>
    <!-- Spock需要的groovy依赖 -->
    <dependency> 
      <groupId>org.codehaus.groovy</groupId>
      <artifactId>groovy-all</artifactId>
      <version>2.4.15</version>
    </dependency>

<!--...... -->
<!--编译插件配置： -->
 <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.20.1</version>
        <configuration>
          <useFile>false</useFile>
          <includes>
            <include>**/*Test.java</include><!--指定JUnit的测试类名称后缀-->
            <include>**/*Spec.java</include><!--指定Spock的测试类名称后缀-->
          </includes>
        </configuration>
      </plugin>
```

### 总结

Spock整体上是一个规范描述型的测试框架，测试代码非常易读。对于java程序员，上手非常容易，在学习的时候可以先在一个项目上使用，当你发下它的优点的时候，你会尽可能的把其他的测试代码也改成用Spock来写，这个过程可能会花费你更多的时间，但是当你熟悉了以后，它带给你的收益一定是值得的。不要犹豫，学习新框架最快的方式就是马上动手。

### 参考链接

- [官方文档](https://links.jianshu.com/go?to=http%3A%2F%2Fspockframework.org%2Fspock%2Fdocs%2F1.2%2Fall_in_one.html%23_introduction)
- [官方例子](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fspockframework%2Fspock-example)
- [在线运行](https://links.jianshu.com/go?to=http%3A%2F%2Fwebconsole.spockframework.org%2F)