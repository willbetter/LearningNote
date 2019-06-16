
# 92. [M]反转单链表2
```
Reverse a linked list from position m to n. Do it in one-pass.

Note: 1 ≤ m ≤ n ≤ length of list.

Example:
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL
```

这个题目看似和反转链表一样的简单, 但是实现起来并没有那么容易, 边界条件比反转链表多一点. 解决方案:1.先顺序遍历找到需要反转的结点的前一个结点标记为pre, 找到需要插入的第一个结点的前一个结点标记start, 需要反转的第二个结点标记then. 解决思路就是, **将them插入到pre的后面, 将then从start的后面删除.**

```
//反转链表的m~n
func reverseBetween(_ head: ListNode?, _ m: Int, _ n: Int) -> ListNode? {
    guard let head = head else { return nil }
    let dummy = ListNode(0)
    dummy.next = head
    var pre: ListNode? = dummy
    for _ in 1..<m {
        pre = pre?.next
    }
    
    let start = pre?.next
    var then = start?.next
    for _ in m..<n {
        start?.next = then?.next
        then?.next = pre?.next
        pre?.next = then
        then = start?.next
    }
    
    return dummy.next
}
```

# 98. [M]判断二叉树是否是二叉搜索树
二叉搜索树: 在树的任意一个结点, 其左子树每个结点的值都小于这个结点的值, 其右子树每个结点的值都小于这个结点的值.  

乍一看, 只需比较结点左子树小于结点, 右子树大于结点即可, 实际上这种解法是不正确的, 比如`[10,5,15,null,null,6,20]`. 要解决这个问题, 需要转换一下思维, 解决思路还是递归, 我们还是从根节点开始, **根节点的值, 在其左子树中一定是最大的, 在其右子树中一定是最小的.** 基于这个事实, 我们可以编写出如下的解决方案: 

```
class Problem98Solution1 {
    func isValidBST(_ root: TreeNode?) -> Bool {
        return judge(node: root, lowwer: nil, upper: nil)
    }
    
    private func judge(node: TreeNode?, lowwer: Int?, upper: Int?) -> Bool {
        guard let node = node else { return true }
        
        let val = node.val
        if let lowwer = lowwer, lowwer >= val { return false }
        if let upper = upper, upper <= val { return false }
        
        let leftValid = judge(node: node.left, lowwer: lowwer, upper: val)
        let rightValid = judge(node: node.right, lowwer: val, upper: upper)
        
        return leftValid && rightValid
    }
}
```

# 102. [M]按层遍历二叉树
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example:
Given binary tree [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
return its level order traversal as:
[
  [3],
  [9,20],
  [15,7]
]

**解决1:**遍历, 将每一层的数据存储起来存储每层的结点和每层结点的结果, 最终返回.

```
class Problem102Solution1 {
    func levelOrder(_ root: TreeNode?) -> [[Int]] {
        var results = [[Int]]()
        guard let root = root else { return results }
        travelLayer(result: &results, nodes: [root])
        return results
    }
    
    func travelLayer(result: inout [[Int]], nodes: [TreeNode]) {
        guard !nodes.isEmpty else { return }
        var layerResult = [Int]()
        var nextLayerNodes = [TreeNode]()
        for node in nodes {
            layerResult.append(node.val)
            if let leftNode = node.left {
                nextLayerNodes.append(leftNode)
            }
            if let rightNode = node.right {
                nextLayerNodes.append(rightNode)
            }
        }
        
        if layerResult.isEmpty == false {
            result.append(layerResult)
        }
        if nextLayerNodes.isEmpty == false {
            travelLayer(result: &result, nodes: nextLayerNodes)
        }
    }
}
```

**解决2**: 解法2的时间复杂度优于解法1,但是事实上并没有快多少. 优雅的递归解决方案: 深度优先遍历, 遍历时候判断如果存放结果的结点存在则将结果放在对应的结果集中, 如果不存在则创建对应的集合并放入结果集中, 最后递归遍历左子树和右子树. 代码如下所示.

```
class Solution {
    func levelOrder(_ root: TreeNode?) -> [[Int]] {
        var results = [[Int]]()
        guard let root = root else { return results }
        travelLayer(result: &results, node: root, height: 0)
        return results
    }
    
    func travelLayer(result: inout [[Int]], node: TreeNode?, height: Int) {
        guard let node = node else { return }
        if height >= result.count {
            result.append([Int]())
        }
        result[height].append(node.val)
        travelLayer(result: &result, node: node.left, height: height + 1)
        travelLayer(result: &result, node: node.right, height: height + 1)
    }
}
```

参考了leetcode上其它的解法, 发现耗时短的算法基本上是基于第一种思路,使用循环实现的代码.**进一步证明了循环比递归更快**, 递归比较慢的原因是递归需要开辟新的堆栈,创建临时变量,销毁堆栈返回. 代码指令条数要多于循环,所占用栈空间也要比循环多.


# 104. 二叉树最大深度
 Given a binary tree, find its maximum depth.
 
 The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
 
 Note: A leaf is a node with no children.
 
 Example:
 
 Given binary tree [3,9,20,null,null,15,7],
 
 3
 / \
 9  20
 /  \
 15   7
 return its depth = 3.
 
 
 **递归解决方案:**
 
 **分解问题:** 第一层深度等一第二层的深度+1, 第二层深度等于第三层深度+1, 如果结点没有子结点, 则是最大深度.
 
 代码如下所示: 先判断左子树深度, 再判断右子树深度, 比较两个深度最大的就是这个结点的最大深度, 依次递归查找.

```
class Problem104Solution1 {
    func maxDepth(_ root: TreeNode?) -> Int {
        guard let root = root else { return 0 }
        
        return maxDepth(root, depth: 1)
    }
    
    func maxDepth(_ root: TreeNode?, depth: Int) -> Int {
        guard let root = root else { return depth - 1 }
        
        let leftDepth = maxDepth(root.left, depth: depth + 1)
        let rightDepth = maxDepth(root.right, depth: depth + 1)
        return max(leftDepth, rightDepth)
    }
}
```


# 75.[[M]颜色排序](https://leetcode.com/tag/two-pointers/)
乍一看是一个挺简单的题目, 思路也比较简单. 两个指针指向数组, 一个在前面,一个在后面,依次遍历数组,如果遍历到0则与前面元素交换,如果遍历到2这与后面的交换.这里有很多的边界条件需要考虑. swift代码如下所示:

```
/// 3.两个指针,一前一后,遍历指针从头开始遍历,如果数字是0这和第一个交换,如果是2这和最后一个交换,原地排序.
class Problem75Solution2 {
    func sortColors(_ nums: inout [Int]) {
        guard nums.count > 1 else { return}
        var left = 0, right = nums.count - 1, i = 0
        while i <= right {
            if nums[i] == 0 {
                nums.swapAt(i, left)
                left += 1
            }else if nums[i] == 2{
                nums.swapAt(i, right)
                right -= 1
                i -= 1
            }
            i += 1
            print(nums)
        }
    }
}
```
需要特别注意的是:

1. 如果i与后面的元素进行交换,则交换过来的数字是多少是不清楚的,因此这时候i不能加1; 
2. 循环终止的条件:`i<=right`. 当遍历到右边界时候,循环需要终止;


