
# 98. 判断二叉树是否是二叉搜索树
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



