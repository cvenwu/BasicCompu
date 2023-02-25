<!--
 * @Author: yirufeng
 * @Date: 2020-12-25 10:52:04
 * @LastEditTime: 2021-11-26 16:27:33
 * @LastEditors: yirufeng
 * @Description: 
 * @FilePath: /BasicCompu/README.md
-->
# BasicComp
记录----面试计算机基础知识

面试的最终目的：你知道的东西是否达到了面试官的预期，所以不会一直问你不知道的东西，除非是想发散你的思维。


## 微软最后一面
1. [可能有]自我介绍(准备好中英文) -> 将自己简历上没有的亮点也要突出(下去好好想一下)
2. [猜测]项目(复盘) + 基础知识
3. 算法：
   1. 写一下开平方
   2. 
4. 反问(准备好反问的问题)
   1. 对于刚才的面试，作为一个技术经验丰富的leader，您对我的表现作何评价？
   2. 国内外对比来看，为什么谷歌微软这类公司在不加班的情况下仍然比国内加班强度特别大的公司在技术方面有领先呢？
   3. 对比进入微软的面试要求和国内大厂的面试要求，您觉得区别在哪里
   4. 作为一个有着十几年技术经验的leader，您对应届生有什么建议么？
   5. （因为十几年前自己还是个小孩子，所以发自好奇想问一下，可能自己认为就是现在学生普遍知识面比较广，但是深度不够）现在的技术发展更新迭代较快，对比来看您觉得现在的学生和您当时毕业那年的学生技术素养差别在哪里？

5个视频：
1. https://www.bilibili.com/video/BV1av411G7pB?spm_id_from=333.999.0.0
2. https://www.bilibili.com/video/BV1gT4y1o7H1?spm_id_from=333.999.0.0
3. https://www.bilibili.com/video/BV1qq4y1d7g6?spm_id_from=333.999.0.0
4. https://www.bilibili.com/video/BV1WZ4y1p7JT?spm_id_from=333.999.0.0
5. https://www.bilibili.com/video/BV1tZ4y1p7Rv?spm_id_from=333.999.0.0

## 算法题


### 平方根

#### 二分
时间复杂度：O(1)，由于内置的 exp 函数与 log 函数一般都很快，我们在这里将其复杂度视为 O(1)。
空间复杂度：O(1)。

```go
//不保留小数
func MySqrt(x int) int {
	if x < 0 { //非法输入
		return 0
	}

	if x == 0 {
		return 0
	}

	l, r := 1, x-1
	for l < r {
		mid := l + (r-l)>>1
		if mid*mid > x {  //golang中int可以存放32位，如果要避免溢出可以考虑加大位数或者使用float，或者直接牛顿迭代
			r = mid - 1
		} else if mid*mid < x {
			l = mid + 1
		} else {
			return mid
		}
	}

	return r
}

//假设保留4位小数
func MySqrtFloat(x int) float64 {
	if x < 0 { //非法输入
		return 0
	}

	if x == 0 {
		return 0
	}

	l, r := float64(1), float64(x-1)
	for l <= r {
		mid, _ := strconv.ParseFloat(fmt.Sprintf("%.4f", float64(l)+float64((r-l)/2)), 64)
		//保留4位小数，基准自己就需要设置为0.0001，也就是一个单位
		if mid*mid > float64(x) {
			r = mid - 0.0001
		} else if mid*mid < float64(x) {
			l = mid + 0.0001
		} else {
			return mid
		}
	}
	//最后返回
	return r
}
```

#### 牛顿迭代

时间复杂度：O(log x)，此方法是二次收敛的，相较于二分查找更快。

空间复杂度：O(1)O(1)。

```go
//todo: 牛顿迭代法求平方根
func MySqrtNiu(x int) int {
	//牛顿迭代法
	c, x0 := float64(x), float64(x)
	for {
		xi := 0.5 * (x0 + c/x0) //因为切线与x轴交点是xi，让xi不断逼近我们要求的值，所以越来越接近 ![cTKSvQ](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/cTKSvQ.png)
		//确保足够接近
		if math.Abs(xi-x0) < 1e-7 {
			break
		}

		x0 = xi
	}

	fmt.Println(x0)
	//todo: 如果是保留几位小数,将下面这几行代码加上，如果要更精确将上面的1e-7进行修改
	ret, _ := strconv.ParseFloat(fmt.Sprintf(".6f", x0), 64)
	fmt.Println(ret)

	return int(x0)
}
```








### 两链表相交

[查看自己的博客](http://www.sivan.tech/2021/11/25/%E5%BD%BB%E5%BA%95%E8%A7%A3%E5%86%B3%E8%A7%A3%E5%86%B3%E4%B8%A4%E4%B8%AA%E9%93%BE%E8%A1%A8%E7%9B%B8%E4%BA%A4%E7%9A%84%E9%97%AE%E9%A2%98/)

### Morris遍历的三种方式

#### Morris先序遍历：

```go
//✅别人写的morris先序遍历，参考：https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html
func MorrisPreOrderTraverse(root *TreeNode) []int {
	var prev *TreeNode
	cur := root
	var ret []int
	for cur != nil {
		if cur.Left == nil {
			ret = append(ret, cur.Val)
			cur = cur.Right
		} else {
			prev = cur.Left
			for (prev.Right != nil) && (prev.Right != cur) {
				prev = prev.Right
			}
			if prev.Right == nil {
				ret = append(ret, cur.Val)
                

				prev.Right = cur
				cur = cur.Left
			} else {
				prev.Right = nil
				cur = cur.Right
			}

		}
	}
	//直接返回我们的结果就可以
	return ret
}
```

#### Morris中序遍历：
```go
//✅Morris中序遍历
//参考别人的代码做改进：https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html
func MorrisInOrderTraverse(root *TreeNode) []int {
	var prev *TreeNode
	cur := root
	var ret []int
	for cur != nil {
		if cur.Left == nil {
			ret = append(ret, cur.Val)
			cur = cur.Right
		} else {
			//此时找到树的根节点
			prev = cur.Left
			for (prev.Right != nil) && (prev.Right != cur) {
				prev = prev.Right
			}
			if prev.Right == nil {
				prev.Right = cur
				cur = cur.Left
			} else {
				prev.Right = nil
				ret = append(ret, cur.Val)
				cur = cur.Right
			}
		}
	}
	//直接返回我们的结果就可以
	return ret
}
```

#### Morris后序遍历
```go

```

### 两棵树相交的根节点

根据出现的如下几种情况，列出思路如下：
1. 两个树不相交
2. 两个树正常相交
3. (特殊情况，需要先考虑一下)两个树相交但是其中一个树是另外一个树的子树

思路：
1. 首先判断一个树是否为另外一个树的子树：
   1. 如果是，则返回True，（这里看一下怎么找到一个树是另外一个子树时候，如何优雅的找到子树的根节点, 思路：可以序列化之后直接转变为子串匹配，返回子串匹配情况下的第一个根）
   2. 如果不是，又可以根据题意划分为如下两种情况：
      1. 验证两个树是否相交：时间复杂度O(N) 空间复杂度O(1)
      2. 找到两个树的交点：时间复杂度O(N) 空间复杂度O(N) 因为哈希表存储遍历到的节点，但是为了更进一步省空间，叶子节点可以不存，通过指针指向来节省空间


判断两个树是否相交以及找到第一个相交的节点：（有一个前提是一个树不是另外一个树的子树）
    第一步：Morris先序遍历树1，将树1的叶子节点的左节点指向树1的根，转变为LeetCode: https://leetcode-cn.com/problems/check-subtree-lcci/
    第二步：然后Morris先序遍历树2，如果发现某个节点的左指针指向树1，那么说明是相交的，
    第三步：将第1步改变的指针还原回去
    注意：第二步的过程中如果我们发现某一个遍历到的节点左指针指向树1的根节点，此时停止遍历，记录这个节点，这个就是相交树的根节点，然后执行第三步


能否考虑遍历其中一个树的过程中验证是否是另外一个树的子树，(感觉不能，因为如果遍历的树是另外一个树的子树，则无法验证。否则是可以的)


```go

```
