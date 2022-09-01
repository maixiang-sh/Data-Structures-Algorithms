## Linked List 链表
#### 1:  Challenge 1: Reverse Printing 反向打印
Create a function that prints the nodes of a linked list in reverse order. For example:
创建一个函数，以相反的顺序打印链表的节点。例如：
```swift
1 -> 2 -> 3 -> nil

// should print out the following:
3
2
1
```

方法1：递归
将每个节点的打印函数压栈，实现反向打印
时间复杂度：_O(n)_，需要迭代一次链表。由于递归使用了函数调用栈（Call Stack），所以空间复杂度是_O(n)_
```swift
func printInReverse<T>(_ linkedList: LinkedList<T>) {

    // 利用递归。对于每一个存在值的 node 都执行打印函数。
    // 通过把打印函数压栈，实现倒序打印。
    func printNodeValue<T>(_ node: Node<T>?) {
        // 如果 node 存在，则继续检查 node 的下一个节点，并打印 node 的值
        if node != nil {
            printNodeValue(node!.next)
            print(node!.value)
        }
    }
    // 从头节点开始
    printNodeValue(linkedList.head)
}

// 运行函数
printInReverse(list)
/*
print:
3
2
1
*/
```


#### Challenge 2: find the intermediate node 找到中间节点
Create a function that finds the middle node of a linked list. For example:
创建一个查找链表中间节点的函数，例如：
```swift
1 -> 2 -> 3 -> 4 -> nil
// middle is 3

1 -> 2 -> 3 -> nil
// middle is 2

```

方法：快慢指针
快慢节点都从头开始迭代链表，快节点的速度是慢节点的2倍。
当快节点到达尾部时，慢节点刚好位于中部。
时间复杂度：_O(n)_，需要迭代一次链表。空间复杂度是_O(1)_。
```swift
func findIntermediateNodes<T>(_ linkedList: LinkedList<T>) -> Node<T>? {
	// 定义快节点、慢节点，都是从 head 开始
    var fasterNode = linkedList.head
    var slowerNode = linkedList.head
    
	// 如果快节点的下个节点不是nil，则一直循环
    while fasterNode?.next != nil {
		// 快节点 = 下下个节点
        fasterNode = fasterNode?.next?.next
		// 慢节点 = 下个节点
        slowerNode = slowerNode?.next
    }

	// 快节点迭代结束，返回慢节点
    return slowerNode
}


let IntermediateNode = findIntermediateNodes(list)
print("Intermediate Node is \(IntermediateNode?.value)")

/* print:
case1: 1 -> 2 -> 3 -> nil
Intermediate Node is Optional(2)

case2: 1 -> 2 -> 3 -> 4 -> nil
Intermediate Node is Optional(3)
*/
```

#### Challenge 3: reverse the list 反转链表
Create a function that reverses a linked list. You do this by manipulating the nodes so that they’re linked in the other direction. For example:
创建一个反转链表的函数。通过操纵节点使它们以相反的方向连接，例如：
```swift
// before
1 -> 2 -> 3 -> nil

// after
3 -> 2 -> 1 -> nil
```

方法1：迭代链表，反转每一个节点的指向。
需要有三个变量从头至尾依次移动。
current：中间变量，从原头节点的下一个节点逐步向原尾节点移动。用于反转当前持有节点的指向，反转时因为原指向变更（ next 持有的节点发生变化），链表会被断开。完成反转后，会将自己持有的节点转移给 prev
prev：前一个变量，从原头节点逐步向原尾节点移动，尾随 current。用于持有被current 反转后的新头节点。完成所有节点反转后，prev 会持有反转后的头节点。
next：始终处于 current 的下一个节点，向原节点尾部移动。用于持有被 current 断开的链表，避免链表丢失。current 和 prev 完成反转和移动后，会将自己持有的节点转移给 current
![](#)(https://tva1.sinaimg.cn/large/e6c9d24egy1h5p6rhqqx9j20sq0a6t92.jpg)

时间复杂度：_O(n)_，需要迭代一次链表。由于使用“原地算法”，没有开辟额外的堆空间，所以空间复杂度是_O(1)_

```swift
func reverse<T>(_ linkedList: LinkedList<T>) -> LinkedList<T> {
	// 原链表
    var list = linkedList

    // prev：从原链表的头移动至尾（如图从左至右移动），每次移动时负责持有反转后的头节点
    var prev = linkedList.head
	// current：从原链表的头移动至尾（如图从左至右移动），每次移动时负责断开链表，反转指向
    var current = linkedList.head?.next
	// 断开原头节点的指向
    prev?.next = nil
    
    while current != nil {
        // next：负责持有被current断开的右侧节点，避免右侧节点丢失
        let next = current?.next
		// 反转 current 持有节点的指向
        current?.next = prev
		// prev 向右移动➡️
        prev = current
		// current 向右移动➡️
        current = next
    }
	// 反转完成后，反转后的头节点即 prev 持有的节点
    list.head = prev
	// 返回反转后的链表
    return list
}

print(reverse(list))

/* print:
case: 1 -> 2 -> 3 -> 4 -> nil
reverse: 4 -> 3 -> 2 -> 1 -> nil
*/
```


#### Challenge 4: Merge two lists 合并两个列表
Create a function that takes two sorted linked lists and merges them into a single sorted linked list. Your goal is to return a new linked list that contains the nodes from two lists in sorted order. You may assume the sort order is ascending. For example:
创建一个函数，该函数接受两个经过排序的链表，并将它们合并到一个经过排序的链表中。假设排序顺序是升序。例如：
```swift
// list1
1 -> 4 -> 10 -> 11

// list2
-1 -> 2 -> 3 -> 6

// merged list
-1 -> 1 -> 2 -> 3 -> 4 -> 6 -> 10 -> 11

```

方法1：同时迭代两个链表，比较节点大小
1. 比较两个链表头节点值的大小，将小的设置为新链表的头节点；
2. 同时迭代两个链表，并比较两个链表当前节点值的大小，将小的追加到新链表尾部。
3. 其中一个链表迭代完成后，将另一个链表的剩余部分追加到新链表尾部。

时间复杂度：_O(m + n)_，需要迭代两个链表。因为没有使用额外堆空间，所以空间复杂度是_O(1)_
```swift
// left 和 right 参数名分别代表两个链表
func mergeSorted<T: Comparable>(_ left: LinkedList<T>, _ right: LinkedList<T>) -> LinkedList<T> {
    // 检查两个链表是否为空，如果其中一个为空，则直接返回另一个链表
    guard !left.isEmpty else { return right }
    guard !right.isEmpty else { return left }
    
    // 创建一个新变量，用于持有合并链表的头节点
    var newHead: Node<T>?
    // 创建一个新变量，用于持有合并链表的尾节点
    var tail: Node<T>?
    
    // 创建两个新变量，用于分别迭代两个链表
    var currentLeft = left.head
    var currentRight = right.head
  
    if let leftNode = currentLeft, let rightNode = currentRight {
        // 比较两个链表头节点的值，将其中最小的节点设置为合并链表的头节点 newHead
        if leftNode.value < rightNode.value {
            newHead = leftNode
            // 最小头节点被 newHead 持有后，最小头节点链表的 current 向下一个节点移动，非最小头节点链表的 current 不动
            currentLeft = leftNode.next
        } else {
            newHead = rightNode
            currentRight = rightNode.next
        }
        // 让新链表的尾节点也持有新头节点（当链表只有一个头节点时，需要让 tail 也持有 头节点，避免之后的追加操作时节点丢失）
        tail = newHead
    }
    // 使用 current 同时开始迭代两个链表，直到其中一个链表迭代完成
    while let leftNode = currentLeft, let rightNode = currentRight {
        // 比较两个链表当前节点值的大小，并将最小的追加到新链表的尾部
        if leftNode.value < rightNode.value {
            tail?.next = leftNode
            // 最小节点链表 current 向后移动，非最小节点的 current 不动
            currentLeft = leftNode.next
        } else {
            tail?.next = rightNode
            currentRight = rightNode.next
        }
        // 将新链表的 tail 设置为新尾节点
        tail = tail?.next
    }
    
    // 其中一条链表链表结束后，对另一条未完成迭代的链表做判断。
    
    // 如果剩余的是左链表，将右链表的剩余部分节点追加到合并链表尾部
    if let leftNodes = currentLeft {
        tail?.next = leftNodes
    }
    // 如果剩余的是右链表，将右链表的剩余部分节点追加到合并链表尾部
    if let rightNodes = currentRight {
        tail?.next = rightNodes
    }
    
    // 迭代剩余链表节点，移动 tail 到剩余链表尾节点
    while let next = tail?.next {
        tail = next
    }
    
    // 创建一个新链表
    var list = LinkedList<T>()
    // 新链表的头节点 = newHead
    list.head = newHead
    // 新链表的尾节点 = tail
    list.tail = tail
    
    // 返回新链表
    return list
}

/*
list1: 1 -> 4 -> 10 -> 11 -> nil
list2: 1 -> 2 -> 3 -> 6 -> nil

合并list1、list2:
print(mergeSorted(list1, list2))
1 -> 1 -> 2 -> 3 -> 4 -> 6 -> 10 -> 11 -> nil
*/
```

方法2：递归
第一轮：比较两个节点值的大小，得到小节点的后，大节点进入下一轮。
第二轮：用大节点的值和上一轮中小节点的下个节点值做比较，得到小的后，将第一轮中的小节点指向第二轮的小节点。
一直重复这个过程，直到所有节点对比完。

时间复杂度：_O(m + n)_，需要遍历两个链表的节点。由于递归使用了函数调用栈（Call Stack），所以空间复杂度是_O(m + n)_
```swift
func mergeSortedByRecursive<T: Comparable>(_ left: LinkedList<T>, _ right: LinkedList<T>) -> LinkedList<T> {
    // 第一步仍然一样，判断两个链表是否为空
    guard !left.isEmpty else { return right }
    guard !right.isEmpty else { return left }
    
    // 定义递归函数，接收两个链表的节点，返回其中值最小的节点
    func mergeSorted(_ left: Node<T>?, _ right: Node<T>?) -> Node<T>? {
        // 第一步类似，判断两个节点是否存在，如果其中一个不存在，则返回另一个节点
        guard let left = left else { return right }
        guard let right = right else { return left }

        // 判断两个节点值的大小
        if left.value < right.value {
            // 如果left最小，则返回 left，返回前继续比较 left 后一个节点的值 和 right 的值，并将 left 指向值小的那个。
            left.next = mergeSorted(left.next, right)
            return left
        } else {
            right.next = mergeSorted(right.next, left)
            return right
        }
    }
    
    // 运行递归函数，传入两个链表的头节点，递归完成后返回两个链表中最小的头节点，并完成排序
    let newHead = mergeSorted(left.head, right.head)
    
    var tail = newHead
    
    // 迭代链表，找到尾节点
    while let next = tail?.next {
        tail = next
    }
    
    // 创建一个新链表
    var list = LinkedList<T>()
    // 新链表的头节点 = newHead
    list.head = newHead
    // 新链表的尾节点 = tail
    list.tail = tail
    
    // 返回新链表
    return list
}
    
/*
list1: 1 -> 4 -> 10 -> 11 -> nil
list2: 1 -> 2 -> 3 -> 6 -> nil

合并list1、list2:
print(mergeSortedByRecursive(list1, list2))
1 -> 1 -> 2 -> 3 -> 4 -> 6 -> 10 -> 11 -> nil
*/
```


#### Challenge 5: Remove all occurrences 删除所有匹配的节点
Create a function that removes all occurrences of a specific element from a linked list. The implementation is similar to the remove(at:) method you implemented for the linked list. For example: 
创建一个函数，该函数从链表中删除特定元素的所有匹配项。例如：
```swift
// original list
1 -> 3 -> 3 -> 3 -> 4

// list after removing all occurrences of 3
1 -> 4

```

方法：使用两个指针迭代链表，一个用于迭代，一个用于重新连接链表
有两种情况：
1. 要删除的值位于链表头部
2. 要删除的值位于链表中部和尾部
时间复杂度：_O(n)_，需要迭代链表。因为没有使用额外堆空间，所以空间复杂度是_O(1)_
```swift
func removeAll<T: Equatable>(_ value: T, in list: LinkedList<T>) -> LinkedList<T> {
	
	// 创建两个变量，分别持有头尾节点
    var head = list.head
    var tail = list.tail
    
	// 第一种情况：当“要被删除的值”出现在头节点时，将 head 向后移动指向下一个节点，直到节点值不等于“要被删除的值”
    while head != nil && head?.value == value {
        head = head?.next
    }
    
	// 第二种情况：“要删除的值”出现在链表中间和尾部，需要迭代链表。创建两个变量，用于在迭代中断开链表和重新连接链表
    var prev = head
    var current = head?.next

    // 迭代链表
    while current != nil {
		// 如果 current 是最后一个节点（最后一个节点指向nil）
        if current?.next == nil {
			// 将 tail 设置为 current
            tail = current
        }
        // 如果 current 的值 等于 ”要被删除的值“
        if current?.value == value {
			// 将前一个节点 指向 下下个节点
            prev?.next = current?.next
			// current 向后移动
            current = prev
        }
		// 	prev 向后移动
        prev = current
		// current 向后移动
        current = current?.next
    }
	// prev 最终持有尾节点，current 最终等于 nil
    tail = prev
    
	// 返回删除指定值后的链表
    var list = LinkedList<T>()
    list.head = head
    list.tail = tail
    return list
}

/*
case: 
3 -> 3 -> 3 -> 9 -> 3 -> 3 -> 6 -> 3 -> 2 -> 1 -> 3 -> nil

print:
9 -> 6 -> 2 -> 1 -> nil
*/
```
