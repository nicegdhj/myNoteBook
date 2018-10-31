
### codeDaily
--------------------------
#### 一些经验
- 边界条件:比如>= 还是> ，想一个最简单的case就可以了
- 递归debug: 有问题时，一样的还是想个最简单的case进行debug

---------------------------------



### 递归  
#### 必须有的要素  
- 终止条件(注意到最后边界结束时情况)  
- 递归逻辑主体
- 进入下一层递归

#### 总结的经验
- 使用递归时，要先想清楚问题是从小到大，还是自顶向下
- 所谓从从小到大，就是从低层开始，外层的状态依赖低层的状态（这种情况递归函数除了终止条件，还有返回值）
- 所谓自顶向下，就是从最外层开始，一步步往下(这种情况下递归函数一般只有终止条件，当到最后就完事了，不需要再返回上层,视具体情况也有返回值的)

##### 从小到大
*比如斐波那契数列，全排列, 检查后续遍历序列*

>[斐波那契数列](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=2&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking) 
> 不是最优解  

```python
def func(n):
    if n <= 0:
        return 0
    if n == 1:
        return 1
    res = func(n - 1) + func(n - 2)
    return res

```

> [剑指offer28题字符串的排列](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=2&rp=2&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking) 

```python
    # -*- coding:utf-8 -*-
import copy
class Solution:
    def Permutation(self, ss):
        # write code here
        if not ss:
            return []
        if len(ss) == 1:
            return [ss]
        res = self.perFunc(ss)
        # 去重
        return sorted(list(set(res)))

    def perFunc(self, ss):
        if len(ss) == 1:
            return ss
        res = self.perFunc(ss[1:])
        newRes = []
        for string in res:
            # 首字母与后面的交换
            string = ss[0] + string
            newRes.append(string)
            for index in range(len(string) - 1):
                temp = copy.deepcopy(string)
                # 从第二位开始交换, python的str是最小单位了
                temp = list(temp)
                temp[0], temp[index + 1] = temp[index + 1], temp[0]
                newRes.append(''.join(temp))
        return newRes

```


> [剑指offer24题:二叉搜索树的后序遍历序列](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=2&rp=2&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)   
>输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同.  

*先注意边界情况,使用递归解决时,一定要先从一个最简单的情况,如就3个数字9,22,20,然后思路就比较清晰了. 递归法一开始想的太复杂的列子,很容易脑子晕的,代码如下*
```python

# coding=utf-8
class Solution:
    def VerifySquenceOfBST(self, sequence):
        # 边界判断
        if len(sequence) == 0:
            return False
        # 终止条件
        if len(sequence) == 1:
            return True
        # 在二叉搜索树中找到分隔点
        index = 0
        while sequence[index] < sequence[-1]:
            index += 1
        for i in sequence[index:]:
            if i < sequence[-1]:
                return False
        # 进入下一层
        # 注意结束时的边界情况的判断写法
        left = True
        if (index > 0):
            # a[0:0]值为[]
            left = self.VerifySquenceOfBST(sequence[:index])
        right = True
        if (index < len(sequence)-1):
            right = self.VerifySquenceOfBST(sequence[index:-1])
        return left and right

```

##### 自顶向下
*比如是否是后续遍历序列，二叉树的遍历，快排 *

> [快速排序](https://github.com/qiwsir/algorithm/blob/master/quick_sort.md)  
> 
```python
    def quickSort(arr):
	    less = []
	    pivotList = []
	    more = []
	    if len(arr) <= 1:
	        return arr
	    else:
	        pivot = arr[0]      #将第一个值做为基准
	        for i in arr:
	            if i < pivot:
	                less.append(i)
	            elif i > pivot:
	                more.append(i)
	            else:
	                pivotList.append(i)
	
	        less = quickSort(less)      #得到第一轮分组之后，继续将分组进行下去。
	        more = quickSort(more)
	
        return less + pivotList + more
```



### 动态规划
#### 基本的一维动态规划

- 首先分析好最终一步的问题，达到它这一步的小问题是什么，写好状态方程
- 走到array最后一步，仅仅是代表了走完array最后一步opt[array.length]的状态，还要依据题意分析究竟是返回哪个状态？


> [一个很好的教程](https://www.bilibili.com/video/av18512769)  
> [剑指offer31题:连续子数组最大和](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=2&rp=2&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking)


*注意到状态方程的定义，以及最后是要返回一个res，而不是返回opt[array.lenth-1]*

```Java
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        if (array.length <= 0) {
            return 0;
        }
        int res = array[0];
        int[] opt = new int[array.length];
        opt[0] = array[0];
        for (int i = 1; i < array.length; i++) {
            int state1 = opt[i-1] + array[i];
            int state0 = array[i];
            opt[i] = Math.max(state0, state1);
            res = Math.max(res, opt[i]);
        }
        return res;
    }
}
```

### 基本排序
#### 理解分治法和更加升入的理解递归
- 快排，归并，堆排是平均时间复杂度最低的, O(nlogn)
- 其他的比如冒泡和插入，平均是O(N^2)

*快排*
```java

 /**
     * 快速排序
     * 自顶向下进行递归
     * 需要注意的是partition部分的写法,不要额外的空间，只需要把烧饼元素记录下来
     * 相当于array数组有了一个空缺，然后有了空间可以左右挪动
     * 过程参考https://blog.csdn.net/IT_ZJYANG/article/details/53406764
     */
    public void quickSort (int[] array) {
        if (array == null) {
            return;
        }
        if (array.length == 1) {
            return;
        }
        quickSortHelper(array, 0, array.length - 1);
    }

    public void quickSortHelper (int[] array, int low, int high) {
        if (low >= high) {
            return;
        }
        int l = low, r = high;
        //排序
        int poviot = array[l];
        while (l < r) {
            while (array[r] >= poviot && l < r) { //注意=号，想个case就明白了
                r--;
            }
            array[l] = array[r];
            while (array[l] <= poviot && l < r) {
                l++;
            }
            array[r] = array[l];
        }
        //最后补上空缺，应该是根据poviot元素的选择有关系
        array[l] = poviot;
        //继续递归
        quickSortHelper(array, low, l - 1);
        quickSortHelper(array, l + 1, high);
    }

```



*归并排序*
```java
    /**
     * 主要是理解分治法
     * 先递归自顶而下的拆分
     * 合并的时候需要额外的空间，临时记录需要交互的局部数据，然后重新排序
     */

    public void mergeSort (int[] array) {
        if (array == null) {
            return;
        }
        if (array.length == 1) {
            return;
        }
        mergeSortHelper(array, 0, array.length - 1);
    }

    public void mergeSortHelper (int[] array, int l, int r) {
        if (r == l) {
            return;
        }
        //先分
        int mid = (r + l) / 2;
        mergeSortHelper(array, l, mid);
        mergeSortHelper(array, mid + 1, r); // 举个例子就清楚边界条件了

        //再并
        int[] left = new int[mid + 1 - l];
        int[] right = new int[r - mid];

        // 临时空间，先复制出来
        for (int i = l; i < mid + 1; i++) {
            left[i - l] = array[i];
        }
        for (int i = mid + 1; i < r + 1; i++) {
            right[i - mid - 1] = array[i];
        }

        // 局部排序
        int leftPos = 0, rightPos = 0, index = l;
        while (leftPos < left.length && rightPos < right.length) {
            if (left[leftPos] < right[rightPos]) {
                array[index] = left[leftPos];
                index++;
                leftPos++;
            } else {
                array[index] = right[rightPos];
                index++;
                rightPos++;
            }
        }
        // left和right总有一边会先到
        // 将左边剩余的归并
        while (leftPos < left.length) {
            array[index++] = left[leftPos++];
        }
        // 将右边剩余的归并
        while (rightPos < right.length) {
            array[index++] = right[rightPos++];
        }
    }


```
