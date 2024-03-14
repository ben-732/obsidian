A tree that you get from binary search. Where there is a root with two branches. Everything on one branch is larger than the root. Everything in the other branch is smaller than the root. 


### Gymnastics
To find the biggest number in a binary tree, always go right. 
To find the minimum go left. 

#todo
Finding the next biggest node, two cases:
- node.right is not null: find the smallest key in the subtree of node.right
- node.right is null: go up till you find a bigger node, go right then find the smallest (go left). 

### Efficency
Trees with unnecessarily large height are really inefficient. They are just a linked list. 

The height of a tree is just the longest path between the root and a leaf. 

To improve the efficiency, we can manipulate the tree after each add or remove such that it looks like a more ideal tree after each operation. 

#### Local Manipulation: Rotation
![[Pasted image 20230816131914.png | center | 500]]
We need to check that the resulting tree from this operation is also a BST.

![[Pasted image 20230816132424.png | center | 500]]


#### Rotation Strategies
- AVL Trees
- Red-Black Trees
These have a worst case `add` `remove` `find` of $O(\log n)$

Red-black trees are slightly better as they use less rotations

- Splay Trees
These have an amortised `add` `remove` `find` of $O(\log n)$

##### AVL Trees
We want to keep the tree height balanced. 

The difference in height can only be at most 1 between each branch. 

>[!info] Claim
>If a BST with $n$ nodes is heigh-balanced, then the heigh of the root is less than $2\log n$


Instead of calculating the height of each node when necessary, we store that information in the node itself. 

Add a node; go up towards the root and fix out-of-balance along the way  
- Learn how rotation works!!!  
- 2 cases – Don’t try to memorise them!  
- Figure out these two cases on your own  
- If something is out of balance, how do I fix it with rotations?  
- Not easy to implement (not too difficult either).  

##### Splay Trees
**Idea** Whenever we add or find a node, rotate it all the way to the root

No keeping track of heights, no nothing. Just rotate!  

This can obviously cause some operations to perform poorly every now and then, but we hope that, on the long run, the tree brings itself to a good state ☺

##### Splay trees 2
**Idea** Whenever we add or find a node,  Zig-Zag and Zig-Zig it all the way to the root.

![[Pasted image 20230816134819.png | center | 500]]

![[Pasted image 20230816135007.png | center | 500]]

**Intuition:** When bringing 7 to the root, we also want to help every other node along the way

This is similar to path compression in union find. 

You use a **Zig-Zig** When the parent and the grandparent is going in the same direction
```
4
 \
   p
    \
     x
```

In this situation, you rotate the parent (p) first then the node (x)

If you do this with add then you bring commonly accessed keys up to near the root

#### Main takeaways
**Rotation is key** 
AVL trees (Invented by Adelson-Velsky and Landis, ‘62):  
- Idea: Maintain the tree height-balanced  
- Keep in mind: Need to update height along the way  
- Difficult to implement  

Splay trees (Invented by Sleator and Tarjan, ‘85):  
- Idea: Bring a node to the root, while helping every other node along the way  
- Zig-Zig & Zig-Zag  
- If a node does not have a grand-parent – just rotate once  
- Easier to implement  
- Frequently accessed keys close to the root!  
