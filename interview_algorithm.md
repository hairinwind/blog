
## reference
https://github.com/labuladong/fucking-algorithm

## pre order traverse (前序遍历)， middle order traverse(中序遍历) and post order traverse(后序遍历)
https://zhuanlan.zhihu.com/p/38603548
- 前序遍历：根节点，左节点，右节点 (从根节点开始)
- 中序遍历：左节点，根节点，右节点（从左叶节点开始）
- 后序遍历：左节点，右节点，根节点（从左叶节点开始）
- 层序遍历：根节点，第一层叶节点(从左到右), 第二层叶节点(从左到右)


##  BST: binary search tree
A binary search tree is a rooted binary tree, whose internal nodes each store a key (and optionally, an associated value) and each have two distinguished sub-trees, commonly denoted left and right.
- The left subtree of a node contains only nodes with keys lesser than the node’s key.
- The right subtree of a node contains only nodes with keys greater than the node’s key.
- The left and right subtree each must also be a binary search tree.

## Balanced tree: self-balancing or height balanced 
It is any node-based binary search tree that automatically keeps its height (maximal number of levels below the root) small


## tree recursive template
find max in the tree
```
func max(root):
    if not root: return None # root is null or empty
    maxLeft = max(root.left)
    maxRight = max(root.right)
    return max(root, maxLeft, maxRight)
```
find maxDepth in the tree
```
func maxDepth(root):
    if not root: return 0
    leftDepth = maxDepth(root.left)
    rightDepth = maxDepth(root.right)
    return max(leftDepth, rightDepth)+1
```
sum the tree
```
func sum(root):
    if not root: return 0
    leftSum = sum(root.left)
    rightSum = sum(root.right)
    return root + leftSum + rightSum
```

## two trees compare
Are p and q the equal tree
```
func equalTree(p, q):
    if not p and not q: return True # both are empty, consider they are equal
    if not p or not q: return False
    leftEqual = equalTree(p.left, q.left)
    rightEqual = equalTree(p.right, q.right)
    return p.val = q.val and leftEqual and rightEqual
```

## given the root and the target node , find the path from root to leaf in the tree
for example the tree is like this
```
root  
  |- c11
     | - c111
  |- c12
     | - c121
         | - c1211
     | - c122
```
Given you root and c1211, you need find the path root-c12-c121-c1211  
Let's say each node is a category
```
class Category {
	private String name;
	private List<Category> subCategories;
```
Here is the recursive function
```
private List<String> getMatchedSubCategory(Category root, String categoryName) {
    List<String> result = new ArrayList<String>();
    if (root.getSubCategories() == null) {
        return result;
    }
    for (Category subCategory : root.getSubCategories()) {
        if (subCategory.isEqual(categoryName)){
            result.add(subCategory.getName());
            return result;
        } else {
            List<String> hasSubCategoryMatched =  getMatchedSubCategory(subCategory, categoryName);
            if (hasSubCategoryMatched.size() == 0) {
                continue;
            } else {
                result.addAll(hasSubCategoryMatched);
                result.add(subCategory.getName());
            }
        }
    }
    return result;
}
```
The basic idea is like this
- if the node is matching the target node, return itself to the caller 
- if the node des not match the target node, check its sub-nodes recursively. If matched node found, return the sub-nodes path and it self to the caller. If no matched nnode found in sub-nodes, go to the next node. 

## 时间复杂度
递归算法的时间复杂度怎么计算？就是用子问题个数乘以解决一个子问题需要的时间。
https://github.com/labuladong/fucking-algorithm/blob/master/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E8%AF%A6%E8%A7%A3%E8%BF%9B%E9%98%B6.md
