## 堆栈（Stack）

#### 堆栈在实际生活中的例子
 <img src="https://photo69.macsc.com/180703/EPS-180703_60/Ud1yA6rShH_small.jpg" width = "300" alt="桶装薯片" align=center />
  
- 要吃到桶底的薯片，就必须把最上层的薯片逐个拿出来。
- 往桶里放入薯片总是在最顶端。

<br>

#### 为什么需要使用堆栈?
- 【为了保证秩序】即对数据的访问和修改方式进行限制，强制要求使用者按规则访问和修改数据。
<br>  
  
#### 堆栈的核心操作
- `push`：添加一个元素到栈顶;
- `pop`：移除栈顶的元素； 

将接口限制为这两个操作，意味着只能从数据结构的一侧添加或删除元素。
<br>
在计算机科学中，这种数据结构被称为 ***LIFI***（last-in-first-out 后进先出）。最后推入的元素是最先被弹出的。
  
<br>
 
#### 堆栈的在实际使用中的案例：
 1. iOS 的导航视图（navigation）使用堆栈结构，将视图控制器推入和弹出，实现了向屏幕推入一个新的页面，以及回退到上一个页面。
  <br>[`pushViewController(_:animated:)`](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621887-pushviewcontroller)<br>
  [`popViewController(animated:)`](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621886-popviewcontroller)
 2. 内存分配在架构级别使用堆栈。局部变量的内存也使用堆栈进行管理。
 3. 分治算法，例如在迷宫中寻找正确路径，使用堆栈进行回溯。

<br>
  
#### 实现一个堆栈（使用swift）
```swift
public struct Stack<Element> {
    // 声明一个storage变量，使用空数组来储存数据，并设置为私有，限制外部访问；
    private var storage: [Element] = [ ]
    
    // 添加构造方法
    public init() { }
    
    // 实现push方法。定义一个push函数，用于向栈顶（数组尾部）追加元素
    public mutating func push(_ element: Element) {
        storage.append(element)
    }
  
    // @discardableResult 用于消除因方法返回值未被使用而出现的警告。
    @discardableResult
    
    // 实现pop方法。定义一个pop函数，用于将栈顶（数组尾部）的元素移除，并返回被移除的元素（数组为空时返回nil）
    public mutating func pop() -> Element? {
        storage.popLast()
        /*ps, Swift中 removeLast() 和 popLast() 的区别：
         两者都是移除集合中的最后一个元素，并返回被移除的元素
         removeLast()：如果集合为空，使用removeLast()方法程序会崩溃
         popLast() 如果及格为空，使用popLast()方法返回nil
         */
    }
} 
```

为了便于调试，扩展`Stack`使其符合`CustomDebugStringConvertible`协议
```swift
  extension Stack: CustomDebugStringConvertible {
    public var debugDescription: String {
        """
        ---- top ----
        \(storage.map {"\($0)"}.reversed().joined(separator: "\n"))
        -------------
        """
    }
}
```
##### 实例化一个堆栈
```swift
var stack = Stack<Int>()
// 向堆栈推入5个数组
stack.push(1)
stack.push(2)
stack.push(3)
stack.push(4)
stack.push(5)

print(stack)
/* print:
---- top ----
5
4
3
2
1
-------------
*/

// 弹出栈顶的元素
stack.pop()

print(stack)
/* print:
---- top ----
4
3
2
1
-------------
*/
```


##### 为了便于使用，可以增加一些常用方法和属性
```swift
extension Stack {
    
    //获取栈顶的元素
    public func peek() -> Element? {
        storage.last
    }
    
    //堆栈是否为空，为空返回true，不为空返回false（判断方法为查看栈顶元素是否为空）
    public var isEmpty: Bool {
        peek() == nil
    }
}
```
```swift
print("栈顶元素是：\(stack.peek()!)")
print("Stack是否为空：\(stack.isEmpty)")
/* print:
栈顶元素是：5
Stack是否为空：false
*/
```


##### 为了便于初始化，可以添加一些构造方法
```swift
extension Stack {
    //将数组直接赋值给storage， 可以实现将数组类型转换为堆栈类型
    public init(_ elements: [Element]) {
        storage = elements
    }
}
```
```swift
let array = [1, 2, 3, 4, 5]
// 实现将数组转换为堆栈
var stack = Stack(array)
print(stack)

/* print
---- top ----
5
4
3
2
1
-------------
*/
```

扩展`Stack`，使其符合`ExpressibleByArrayLiteral`协议，即可以使用数组字面量进行初始化
```swift
extension Stack: ExpressibleByArrayLiteral {
    public init(arrayLiteral: Element...) {
        self.init()
        for element in arrayLiteral {
            self.storage.append(element)
        }
    }
}
```
```swift
// 使用数组字面量进行初始化
var stack: Stack = [1, 2, 3, 4, 5]

print(stack)
/* print
---- top ----
5
4
3
2
1
-------------
*/
```

#### 堆栈相关的算法问题

##### 1. 反转数组
创建一个函数，该函数使用堆栈按相反顺序打印数组的内容。

##### 解决方案
堆栈的主要用法之一是便于回溯。如果将一列值推入堆栈，再依次弹出堆栈则会按相反的顺序给出值；
```swift
func printInReverse<T>(_ array: [T]) {
  var stack = Stack<T>()

  for value in array {
    stack.push(value)
  }

  while let value = stack.pop() {
    print(value)
  }
}
```
将节点推入到堆栈中的时间复杂度为 _**O(n)**_ 。弹出堆栈打印值的时间复杂度也是 _**O(n)**_ 。总体而言，该算法的时间复杂度为 _**O(n)**_ 。

由于在函数内部分配容器（堆栈），因此也会产生 _**O(n)**_ 的空间复杂性成本。

注意：在生产代码中反转数组的方法是调用Swift标准库提供的`reversed()`方法。对于`Array`（数组），这种方法在时间和空间上都是 _**O(1)**_ 。这是因为它是`lazy`（惰性的），只会在原始集合中创建一个反向视图。如果遍历数组并打印出所有元素，那么可以预见的是，它在时间上为 _**O(n)**_ ，而在空间上为 _**O(1)**_。

<br>

##### 2. 平衡括号
检查圆括号是否平衡。给定一个字符串，检查是否有( 和 )符号，如果字符串中的括号是平衡的，则返回true。例如：
``` swift
// 1
h((e))llo(world)() // 平衡圆括号

// 2
(hello world // 不平衡圆括号
```
##### 解决方案
要检查字符串中是否有平衡括号，需要遍历字符串的每个字符。当你遇到一个左括号时，你将把它推到堆栈中。反之亦然，如果遇到右括号，则应弹出堆栈。
```swift
func checkParentheses(_ string: String) -> Bool {
  var stack = Stack<Character>()

  for character in string {
    if character == "(" {
      stack.push(character)
    } else if character == ")" {
      if stack.isEmpty {
        return false
      } else {
        stack.pop()
      }
    }
  }
  return stack.isEmpty
}
```
该算法的时间复杂度为 _**O(n)**_ ，其中 _**n**_ 是字符串中的字符数。由于使用堆栈数据结构，该算法还产生 _**O(n)**_ 的空间复杂度成本。





