

### 3. 数组中重复的数字

<details><summary>code</summary>

- [数组中重复的数字](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 思路：O(N) + O(1)
- 将 `j=nums[i]` ，将j放到nums的下标为j中，如果存在`nums[k] == num[j] == j `，说明重复；


```c++
bool duplicate(vector<int>nums, int len, int* dup){

	if(len <=0) return false;
	
	for(int i= 0;i< len;i++)
	{
		while(nums[i] != i){
			int j = nums[i];
			if(nums[j] == nums[i])
			{
				dup[0] = nums[i];
				return true;
				
			}else{
				swap(nums[i],nums[j]);
			}
		}
	}
	return false;
}

```

</details>



### 4. 二维数组中的查找

<details><summary>code</summary>

- [二维数组中的查找](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 复杂度：O(M + N) + O(1)
- 从右上角开始查找。矩阵中的一个数，它左边的数都比它小，下边的数都比它大。
	因此，从右上角开始查找，就可以根据 target 和当前元素的大小关系来缩小查找区间。 


```c++
bool FindTarget(int target, vector<vector<int>> &nums){
	if(nums.empty())
	{
		return false;
	}

	int m = nums.size();
	int n = nums[0].size();
	
	int i=0, j = n-1;
	while(i< m && j >=0){
		if(nums[i][j] == target)
			return true;
		else if (nums[i][j] > target)
			j--;
		else 
			i++;

	}
	return false;
}

```

</details>



### 5. 替换空格

<details><summary>code</summary>

- [替换空格](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 将一个字符串中的空格替换成 "%20"。

```c++

class Solution {
public:
void replaceSpace(char *str,int length) {
        //遍历一边字符串找出空格的数量
        if(str==NULL||length<0)
            return ;
        int i=0;
        int oldSzie=0;//记录以前的长度
        int space=0;//记录空格的数量
        while(str[i]!='\0')
            {
               oldSzie++;
               if(str[i]==' ')
                   {
                     space++;
                   }
                  i++; 
            }
    
      
        int newSize=oldSzie+space*2;//插入后的长度
        if(newSize > length)//如果计算后的长度大于总长度就无法插入
            return ;

        while(oldSzie>=0&&newSize>oldSzie)//放字符
            {
              if(str[oldSzie]==' ') //碰到空格就替换
                  {
                     str[newSize--]='0';
                     str[newSize--]='2';
                     str[newSize--]='%';
                     
                  }
               else //不是空格就把pOldlength指向的字符装入pNewlength指向的位置
               {
                    str[newSize--]=str[oldSzie];
                   
               }
             oldSzie--; //不管是if还是elsr都要把pOldlength前移
             
           }
        
}
};

```

</details>



### 6. 从尾到头打印链表

<details><summary>code</summary>

- [从尾到头打印链表](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用栈
  

```

class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> res;
        if(!head)
            return res;
        
        stack<int> s;
        while(head){
            s.push(head->val);
            head = head->next;
        }
        
        while(!s.empty()){
            res.push_back(s.top());
            s.pop();
        }
        return res;
    }
};

```

</details>







### 7. 重建二叉树

<details><summary>code</summary>


- [重建二叉树](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)



```c++

class Solution {
public:
    unordered_map<int,int> map;        // 中序的值和下标的映射
    
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        for(int i=0;i<pre.size();i++)
            map[vin[i]] = i;
        
        return createRoot(pre,vin,0,0,vin.size()-1);
    }
    
    // 参数： 前序， 中序， 前序root下标，中序左子树开始下标， 中序右子树开始下标
    TreeNode* createRoot( vector<int>& pre,vector<int>& vin,int pre_root, int vin_l, int vin_r){
        if(vin_l > vin_r)    // 结束
            return NULL;
        int root_val = pre[pre_root];    // root节点
        int index = map[root_val];        // root下标
        
        
        
        int left_size = index - vin_l;    // 左子树大小
        int right_size = vin_r - index;   // 右子树大小
        
        TreeNode* root = new TreeNode(root_val);
        root->left = createRoot(pre,vin, pre_root+1,vin_l,index-1);
        root->right = createRoot(pre,vin,pre_root+left_size+1, index+1,vin_r);
        return root;
        
    }
};

```

</details>



### 8. 二叉树的下一个结点

<details><summary>code</summary>

- [二叉树的下一个结点](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)



```c++

class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        TreeLinkNode* ne;
        if(pNode->right){     // 存在右子树
            ne =  pNode->right;
            while(ne->left)   // 右子树的最左边
                ne = ne->left;
            return ne;
        }
        else{// 左节点
        while(pNode->next != NULL){
            ne = pNode->next;   // 父节点
            if(ne->left == pNode)
                return ne;
            //右节点, 寻找祖先节点为某节点的左子树
             pNode = pNode->next; 
        }
       }
        return NULL;
        
    }
};

```

</details>



### 9. 用两个栈实现队列

<details><summary>code</summary>

- [用两个栈实现队列](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++

class Solution
{
    
private:
    stack<int> stack_push;
    stack<int> stack_pop;
    
public:
    void push(int node) {
        stack_push.push(node);
        
    }

    int pop() {
        if(stack_pop.empty()){
            while(!stack_push.empty())
            {
                stack_pop.push(stack_push.top());
                stack_push.pop();
            }   
        }
        try{
            int res = stack_pop.top();
            stack_pop.pop();
            return res;
        }catch(exception& e){
            return 0;
        }
    }
};

```

</details>



### 10.1 斐波那契数列

<details><summary>code</summary>

- [斐波那契数列](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++
class Solution {
public:
    int Fibonacci(int n) {
        if(n==0)
            return n;
        int pre1=0, pre2 = 1;
        int res=0;
        for(int i=2;i<=n;i++){
            res = pre1 + pre2;
            pre1= pre2;
            pre2 = res;
        }
        return res;
    }
};

```


</details>




### 10.2 跳台阶

<details><summary>code</summary>

- [跳台阶](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=11161&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    int jumpFloor(int n) {
        if(n<=2)
            return n;
        int pre1=1;
        int pre2=2;
        int res=0;
        for(int i=3;i<=n;i++){
            res = pre1 + pre2;
            pre1 = pre2;
            pre2= res;
        }
        return res;
        
    }
};

```

</details>



### 10.3 变态跳台阶

<details><summary>code</summary>

- [变态跳台阶](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=11162&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    int jumpFloorII(int n) {
        if(n<=2)
            return n;
        vector<int> res(n,1);
        res[0] = 1;
        res[1] = 2;
        for(int i=2;i < n;i++)
        {   
            for( int j=0;j <i;j++){
                res[i]+=res[j];
            }
        }
        
        return res[n-1];
    }
};

```

</details>



### 10.4 矩形覆盖

<details><summary>code</summary>

- [矩形覆盖](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E5%89%91%E6%8C%87%20offer%20%E9%A2%98%E8%A7%A3.md#56-%E6%95%B0%E7%BB%84%E4%B8%AD%E5%8F%AA%E5%87%BA%E7%8E%B0%E4%B8%80%E6%AC%A1%E7%9A%84%E6%95%B0%E5%AD%97)

- 还是斐波那契问题


```c++

int rectCover(int n){

	if(n <=2)
		return n;
	int pre1 = 1;
	int pre2 = 2;
	
	int res=0;
	for(int i=3;i<=n;i++){
		res =pre1 + pre2;
		pre1 = pre2;
		pre2 = res;
	}
	
	return res;
}

```

</details>




### 11. 旋转数组的最小数字

<details><summary>code</summary>

- [旋转数组的最小数字](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- o(N): 遍历一遍
- O(logN): 二分查找


情况：
- 数字不重复的情况下，直接使用二分查找
	- `nums[m] <= nums[h]`,区间是`[l,m]`
	- 否则，解在`[m+1,h], 令l = m+1`
- 数字重复的情况下：
	- `nums[l] == nums[m] == nums[h]，那么此时无法确定解在哪个区间，需要切换到顺序查找`

```c++
int minNumberInRotateArray(vector<int> nums) 
{
	if(nums.empty())
		return 0;

	int l=0, h = nums.size() -1;
	
	while(l<h){
		int m = (l+h)/2;
		if(nums[m] == nums[l] && nums[m] == nums[h])
			return directFind(nums,l,h);
		else if(nums[m] <= nums[h])
			h = m;
		else 
			l = m+1;
	}
	return nums[l];
}


int directFind(vector<int>& nums, int l , int h){
	int min_num = INT_MAX;
	for(int i=l;i<h;i++){
		if(min_num > nums[i])
			min_num = nums[i];
	}
	return min_num;
}

```

</details>



### 12. 矩阵中的路径

<details><summary>code</summary>

- [矩阵中的路径](https://www.nowcoder.com/practice/c61c6999eecb4b8f88a98f66b273a3cc?tpId=13&tqId=11218&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++

class Solution {
public:
    bool hasPath(char* matrix, int rows, int cols, char* str)
    {
        if(!matrix || !str || rows==0 || cols == 0)
            return false;
        
        vector<vector<bool>> marked(rows, vector<bool>(cols,false));
        int len= rows*cols;
        bool res = false;
        for(int m=0;m<rows;m++){
            for(int n=0;n<cols;n++){
                res = res || equalStr(matrix,m,n,rows,cols,str,0,marked);
       
           // 重置marked
          //  marked.swap(vector<vector<bool>>(rows, vector<bool>(cols,false)));         
        }
        }
        return res;
    
   }
    
    bool equalStr(char* matrix,int i,int j,int r, int c , char* str, int k,vector<vector<bool>>& marked){
        if(i<0 || i>=r || j<0 || j>=c || marked[i][j])
            return false;
        
        bool res=false;
        if(matrix[i*c + j ] == str[k] && marked[i][j] == false){
            k++;
            marked[i][j] = true;
            
            if(str[k] == '\0')
                return true;
            
            res = (equalStr(matrix,i,j+1,r,c,str,k,marked) || 
                   equalStr(matrix,i,j-1,r,c,str,k,marked) ||
                   equalStr(matrix,i+1,j,r,c,str,k,marked) ||
                   equalStr(matrix,i-1,j,r,c,str,k,marked) );
            
         if(res != true)
             marked[i][j] = false; 
        }
        
        return res;
    }
    


};

```

</details>




### 13. 机器人的运动范围

<details><summary>code</summary>

-  [机器人的运动范围](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


>地上有一个 m 行和 n 列的方格。一个机器人从坐标 (0, 0) 的格子开始移动，
>每一次只能向左右上下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于 k 的格子。
>例如，当 k 为 18 时，机器人能够进入方格（35, 37），因为 3+5+3+7=18。
>但是，它不能进入方格（35, 38），因为 3+5+3+8=19。请问该机器人能够达到多少个格子？

- 思路：
1. 坐标和的计算： 使用数组保存
2. DFS遍历，保存最大的移动数


```c++
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        if(threshold < 0 || rows <=0 || cols <=0 )
            return 0;
        vector<vector<int>> sums = calcSum(rows, cols);
        vector<vector<bool>> marked (rows, vector<bool> (cols, false));
        
        int cnt = 0;
        dfs(threshold, 0,0, rows,cols, cnt, marked, sums);
        return cnt;
        
    }
    
    // 坐标和的计算
    vector<vector<int>> calcSum(int rows, int cols){
        int len = rows>cols ? rows : cols;
        vector<int> maxLen(len,0);
        for(int i=0;i<len; i++){
            int j=i;
            while(j>0){
                maxLen[i] += j % 10;
                j = j/10;
            }
        }
        
        vector<vector<int>> res(rows, vector<int>(cols,0));
        for(int i=0;i<rows;i++){
            for(int j=0;j<cols;j++)
                res[i][j] = maxLen[i] + maxLen[j];
        }
        return res;     
    }
    
    
    // dfs
    void dfs(int threshold, int i, int j, int r, int c, int& cnt,
             vector<vector<bool>>& marked, vector<vector<int>>& sums){
        if(i<0 || i>=r || j<0 || j>= c || marked[i][j])
            return;
        marked[i][j] = true;
        if(sums[i][j]  > threshold)
            return;
        cnt ++;

        dfs(threshold, i,j+1, r,c, cnt, marked, sums);
        dfs(threshold, i,j-1, r,c, cnt, marked, sums);
        dfs(threshold, i+1,j, r,c, cnt, marked, sums);
        dfs(threshold, i-1,j, r,c, cnt, marked, sums);
   
    }
 
};
```

</details>




### 14. 剪绳子

<details><summary>code</summary>

- [ 剪绳子](https://leetcode.com/problems/integer-break/description/)

> 把一根绳子剪成多段，并且使得每段的长度乘积最大

```c++
    int integerBreak(int n) {
        if(n<2)
            return 0;
        if(n==2)
            return 1;
        if(n==3)
            return 2;
// 方法1 ： 贪心 ，至少支取2的长度
//         int timeof3 = n/3;
//         if((n - timeof3*3) == 1) 
//             timeof3 --;
//         int timeof2 = (n-timeof3*3)/2;
//        // cout<<"timeof3:"<<timeof3<<" timeof2："<<timeof2<<endl;
        
//         return pow(3,timeof3)*pow(2,timeof2);
        
// 方法2 ： dp[i] = max(dp[i], j*(i-j), j* dp[i-j]) // 分成两部分，或者取一部分，对剩下的再分
        vector<int> dp(n+1, 0);
        dp[1] = 1;
        for(int i=2;i<=n;i++){
            for(int j=1;j<i;j++){
                dp[i] = max(dp[i],max(j*(i-j),j * dp[i - j] ));
            }
        }
        return dp[n];
        
        
    }

```

</details>





### 15. 二进制中 1 的个数

<details><summary>code</summary>

- 关键点： **把一个整数减去1之后在和原来的整数做与运算， 得到的结果相当于把整数的二进制中的最右边一个1变成0**

>输入一个整数，输出该数二进制表示中 1 的个数。
>n       : 10110100
>n-1     : 10110011
>n&(n-1) : 10110000

```
class Solution {
public:
     int  NumberOf1(int n) {
         int cnt =0;
         while(n!=0){
             cnt++;
             n = n&(n-1);
         }
         return cnt;
     }
};



// 如果整数位负数，使用右边移动，这趟最多32次
    int  NumberOf1(int n) {
         int count = 0;
		 unsigned int flag = 1;
		 while(flag){
			if(n & flag)
				count ++;
			flag =  flag << 1;
		 }
		return count;
     }


// 整数为： 1100 ， 减去1100 - 1 = 1011
// 然后两个数相与： 1100 & 1011 = 1000 ，刚好只消去以为1
int NumberOf1(int n){

	int count = 0;
	while(n){
		++count;
		n = (n-1) & n;
	}
	return count;

}
	 
	 
```




### 15.1 用一条语句判断一个整数是不是2的整数次方

- 一个整数如果是2的整数次方，那么他的二进制表中有且只有一位为1，其他所有位为0

- 把这个整数减去1之后在和它自己做与运算，这个整数中唯一的1 为0
```c++

((n-1) & n) == 0

```

### 15.2 输入两个整数m和n，计算需要改变m的二进制表示中的多少位才能得到n。

- 例如 10的二进制位1010， 13为1101，需要改变1010中的3位才能得到1101

分为两步：
1. 求两个数的异或
2. 统计异或结果中 1 的位数


```c++

int CountSum(int m, int n){
	int count = 0;
	int sum = m ^ n;  // 例如10和13 异或为 0111
	while(sum){
		sum = (sum - 1) & sum;
		++count;
	}
	return count;
}

```

</details>


### 16. 数值的整数次方

<details><summary>code</summary>

>给定一个 double 类型的浮点数 base 和 int 类型的整数 exponent。
>求 base 的 exponent 次方。

![](https://camo.githubusercontent.com/a9c60dcae8e57f42cb7f60473d695dddb3dd6221/68747470733a2f2f6c617465782e636f6465636f67732e636f6d2f6769662e6c617465783f785e6e3d5c6c6566745c7b5c626567696e7b61727261797d7b72636c7d28782a78295e7b6e2f327d26267b6e5c25323d307d5c5c782a28782a78295e7b6e2f327d26267b6e5c25323d317d5c656e647b61727261797d5c72696768742e)


```c++
class Solution {
public:
    double Power(double b, int e) {
        if(e == 0)
            return 1;
        if(e==1)
            return b;
        int isN = false;
        if(e<0)   // 负指数
        {
            e = -e;
            isN = true;
        }
        double p = Power(b*b, e>>1);
        if(e & 0x1 == 1)
            p = b*p;
        
        return isN ?1/ p : p; 
        
    }
};

```

普通方法：
- 计算机表示小数小数(float 和double)都有误差，不能用等号直接判断两个小数是否相等，比较两个数的差

```c++

bool equal(double num1, double num2){
	if((num1 - num2 > -0.00000001) && (num1 - num2 < 0.00000001))
		return true;
	else
		return false;
}


bool PowerWithUnsigned(double base, unsigned int exponent){
	double result = 0.0;
	for(int i=0;i<=exponent;++i)
		result *= base;
		
	return result;
}


double  Power(double base, int exponent){

	bool invalid = false;
	if(equal(base,0.0) && exponent < 0){
		invalid = true;
		return 0.0;
	}
	
	unsigned int absEx = (unsigned int)(exponent);
	if(exponent < 0)
		absEx = (unsigned int)(-exponent);		// 负值重新赋值
		
	double result = PowerWithUnsigned (base, exponent);
	
	if(exponent < 0)
		result = 1.0 / result;
		
	return result;

}


```

</details>




### 17. 打印从 1 到最大的 n 位数

<details><summary>code</summary>

- 注意字符的长度

```c++

// 进位溢出计算
bool Increment(char* number){

	bool isOverflow = false;
	int nTakeover = 0;
	int nLength = strlen(number);  // 总长度
	
	for(int i = nLength -1;i>=0;i--){
		int nSum = number[i] - '0' + nTakeover; // 计算当前位的值
		if(i == nLength - 1)  // 最后一位，加1
			nSum ++;
		if(nSum >=10)  // 产生了进位	
		{
			if(i==0)
				isOverflow = true; // 第 0 位，达到最大数字
			else{
				nSum -= 10;
				nTakeover = 1;
				number[i] = '0' + nSum;  // 更新 第 i 位
			}
		}else{
			number[i] = '0' + nSum;
			break;
		}
		
		return isOverflow;
	}
}



// 打印输出
void printNumber(char* number){

	bool isBeginning0 = true;
	
	int nLength = strlen(number);
	
	for(int i=0;i<nLength; ++i){
	
		if(isBeginning0 && number[i] != '0')
			isBeginning0 = false;
			
		if(!isBeginning0){
			printf("%c", number[i]);
		}
	}
	printf("\n");
}




void print1ton( int n){

	if(n <=0)
		return;
		
	char number[] = new char[n + 1];
	memset(number, '0', n);
	number[n] = '\0';
	
	while(!Increment(number)){
		printNumber(number);
	}
	
	delete [] number;
}

```

方法2：

- 使用全排列的方式

```c++



void print1toNRecursive(char* number, int len , int idx){

	if(idx == len -1)  // 最后一位
	{
		printNumber(number);
		return ;
	}
	
	for(int i=0;i<10;i++){
	
		number[idx+1] = i+'0';
		print1toNRecursive(number,len,idx + 1);
	}

}





void print1ton(int n){

	if(n <=0)
		return;
	
	char number[] = new char[n+1];
	number[n] = '\0';
	
	
	for(int i=0;i<10;++i){
		number[0] = i+ '0';
		print1toNRecursive(number,n,0);
	}
	
	delete[] number;

}




```

</details>





### 18.1 在 O(1) 时间内删除链表节点

<details><summary>code</summary>

- 中间节点：将下一个节点的值赋值给当前节点，删除下一节点
- 尾巴节点：遍历到尾部

```c++
ListNode* delnode(ListNode* head, ListNode* node){
	if(head == nullptr || head->next == nullptr || node == nullptr)
		return nullptr;
	if(node->next != nullptr){
		ListNode* next = node->next;
		node->val = next->val;
		node->next = next->next;
	}else{
		ListNode* cur = head;
		while(cur->next != node)
			cur = cur->next;
		cur->next = nullptr;
	}
	return head;

}

```

</details>



### 18.2 删除链表中重复的结点

<details><summary>code</summary>

- [删除链表中重复的结点](https://www.nowcoder.com/practice/fc533c45b73a41b0b44ccba763f866ef?tpId=13&tqId=11209&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 递归删除


```c++

ListNode* deleteDuplicate(ListNode* head){
	if(head == nullptr || head->next == nullptr)
		return head;
		
	ListNode* cur, *next;
	next = head->next;
	
	if(next->val == head->val){
		while(next != nullptr && next->val == head->val){
			next= next->next;
		}
		return deleteDuplicate(next);
	}
	else{
		head->next = deleteDuplicate(next);
		return head;
	}
}
```


非递归：

```c++
class Solution {
public:
    ListNode* deleteDuplication(ListNode* head)
    {
        if(head == nullptr || head->next == nullptr)
            return head;
        ListNode * root = new ListNode(0);
        ListNode* pre,*next;
        pre = root;
        pre->next= head;
        next = head->next;
        
        while(next !=nullptr){
            if( head->val == next->val){   
                while(next !=nullptr && next->val == head->val)
                    next = next->next; 
                
                head = next;
                pre->next = head;   // 如果重复，一直指向下一个
            }else{
                pre = head;
                head = head->next;             
            }
             
            if(head != nullptr)   // 当前节点不为空节点
                next =head->next;
        }
        
        return root->next;

    }
};



```

</details>




### 19. 正则表达式匹配

<details><summary>code</summary>

> 两个字符串都为空，返回true; 如果p为空，返回false
>(1)pattern 第二个字符不为‘*’
>  两种组合： 
> 1. aabc 和 aabc  : match(s+1, p+1) 
> 2. aabc 和a.bc   : match(s+1,p+1)
>  匹配成功都前进一步

>(2) pattern 第二个字符等于'*'
> 三种组合：
> 1. bc 和 a*bc  : a* 无用， s不动，p动，  match(s,p+2);
> 2. aabc 和 a*bc : a* 多个； p不动，s动， match(s+1, p);
> 3. aabc 和 .*abc   :  .* 可以匹配0到多个， match(s+1,p);



```c++
bool match(char* str, char* pattern)
{
	if (*str == '\0' && *pattern == '\0')
		return true;
	if (*str != '\0' && *pattern == '\0')
		return false;
	//if the next character in pattern is not '*'
	if (*(pattern+1) != '*')
	{
		if (*str == *pattern || (*str != '\0' && *pattern == '.'))
			return match(str+1, pattern+1);
		else
			return false;
	}
	//if the next character is '*'
	else
	{
		if (*str == *pattern || (*str != '\0' && *pattern == '.'))
			return match(str, pattern+2) || match(str+1, pattern);
		else
			return match(str, pattern+2);
	}
}

```

</details>



### 20. 表示数值的字符串

<details><summary>code</summary>

```c++
    bool isNumeric(char* s)
    {
        bool pre_ = false;   // 前面的正负号
        bool last_ = false;  // e后面正负号
        bool e_    = false;  // e
        
        bool dot1_ = false;
        int size = strlen(s);
        int i=0;
        
        for( i=0;i< size;i++){
            switch(s[i]){
                case '+': case '-':{
                            if( e_ && last_ ) // e后面符号位
                                return false;
                            else if(e_ && i+1 < size)
                                last_ = true;
                            else if(pre_ )
                                return false;
                            else if( i == 0 )
                                pre_ = true;
                            else
                                return false;
                            break;
                         }
                case '.':{
                            if( dot1_  )  // 前面多个小数点
                                return false;
                            else if( e_ )        // e_后出现 ‘.’.直接false
                                return false;  
                            else if(i < size -1 && i > 0) // 小数点出现在e前面
                                dot1_ = true;
                            else
                                return false;
                            break;
                         }
                    
                case 'E': case 'e' : {
                            if(e_)
                                return false;
                            else if(i< size -1)
                                e_ = true;
                            else
                                return false;
                            break;
                        }
                default:{
                           if( s[i] >= '0' && s[i] <= '9')
                               continue;
                            else
                                return false;
                            break;
                }
                    
            }
        }
        
   
        return true;

    }

};
```

</details>





### 21. 调整数组顺序使奇数位于偶数前面

<details><summary>code</summary>

- [调整数组顺序使奇数位于偶数前面](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
        if(array.empty())
            return ;
        
        int cnt =0;
        for(int i=0;i<array.size();i++)
            if(array[i] % 2== 1)
                cnt++;
        
        vector<int> num(array);
        int j=0;
        for(int i=0;i< num.size();i++){
            if(num[i] % 2 == 1)  // 奇数
                array[j++] = num[i];
            else
                array[cnt++] = num[i];
        }
        
    }
};


// 交换

void reOrderArray(vector<int>& array){
	int len = array.size();
	if(len < 1)
		return;
	int i = 0, j = len - 1;
	
	while(i<j){
		// 向后移动，知道遇见偶数
		while(i<j && (array[i] & 0x1) != 0)
			i++;
		// 向前移动，直到遇到奇数
		while(i<j && (array[j] & 0x1) == 0)
			--j;
		
		if(i <j)
		{
			swap(array[i], array[j]);
		}
	
	}

}

```

</details>





### 22. 链表中倒数第 K 个结点

<details><summary>code</summary>

- [链表中倒数第 K 个结点](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 遍历两次： 第一次获取长度，第二次得到结果
- 遍历一次： 采用双指针，两个指针相差k步,最后一个指针走到末尾，另一个指针指向倒数第 k个节点；


```c++
    ListNode* FindKthToTail(ListNode* head, unsigned int k) {
    
        if(!head)
            return nullptr;
        ListNode* p = head;
        
        int cnt = 0;
        while(p){
            p = p->next;
            cnt++;
        }
        int rm = cnt -k;
        if(rm < 0)
            return nullptr;
        
        p = head;
        while(rm-- >0)
            p = p->next;
        
        return p;
 
    }
	
	
// 方法2

ListNode* FindKthToTail(ListNode* head, unsigned int k) {
	if(head == nullptr || k == 0)
		return nullptr;
		
	ListNode* pre = head;
	ListNode* last = head;
	
	for(int i=0;i<k-1;++i){  // 向前走k-1步
		if(pre->next != nullptr)  // 长度不够
			pre = pre->next;
		else
			return nullptr;
	}
	
	while(pre->next != nullptr){
		pre = pre->next;
		last = last->next;
	}

	return last;
}

	
	
```

</details>




### 23. 链表中环的入口结点

<details><summary>code</summary>


> 假设x为环前面的路程
> a为环入口到相遇点的路程, c为环的长度
> 当快慢指针相遇的时候：
> 此时慢指针走的路程为Sslow = x + m * c + a
> 快指针走的路程为Sfast = x + n * c + a
> 2 Sslow = Sfast
> 2 * ( x + m*c + a ) = (x + n *c + a)
> 从而可以推导出：
> x = (n - 2 * m )*c - a
> = (n - 2 *m -1 )*c + c - a
> 即环前面的路程 = 数个环的长度（为可能为0） + c - a


```c++
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* head)
    {
        if( ! head || head->next == nullptr)
            return nullptr;
        
        ListNode* fast, *slow;
        
        slow = head->next;
        fast = head->next->next;
        
        while(fast && fast->next  && fast != slow){
            slow = slow->next;
            fast = fast->next->next;
        }
        
        if(fast == nullptr)  // 无环
            return nullptr;
        
        fast = head;
        while(fast != slow)
        {
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
};
```

</details>


### 24. 反转链表

<details><summary>code</summary>

- [反转链表](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用头插法


```c++
class Solution {
public:
    ListNode* ReverseList(ListNode* head) {
        if( head == nullptr || head->next == nullptr)
            return head;
        
        ListNode* new_head = new ListNode(0);
        ListNode* cur ;
        while(head){
            cur = head->next;
            head->next = new_head->next;
            new_head->next = head;
            head = cur;
        }
        head = new_head->next;
        
        delete new_head;
        return head;
        
    }
};

// 递归

ListNode* ReverseList(ListNode* head){
	if( head == nullptr || head->next == nullptr)
		return head;
		
	ListNode* next = head->next;
	
	head->next = nullptr;
	
	ListNode* newhead = ReverseList(next);
	
	next->next = head;
	
	return newhead;
	
}



```

</details>




### 25. 合并两个排序的链表


<details><summary>code</summary>

- [合并两个排序的链表](https://www.nowcoder.com/practice/d8b6b4358f774294a89de2a6ac4d9337?tpId=13&tqId=11169&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)




递归：

```c++
class Solution {
public:
    ListNode* Merge(ListNode* head1, ListNode* head2)
    {
        if(head1 == nullptr)
            return  head2;
        else if(head2 == nullptr)
            return head1;
        
        if(head1->val < head2->val){
            head1->next = Merge(head1->next, head2);
            return head1;
        }
        else{
            head2->next = Merge(head1, head2->next);
            return head2;
        }

    }
};
```


迭代：

```c++


class Solution {
public:
    ListNode* Merge(ListNode* head1, ListNode* head2)
    {
        if(head1 == nullptr)
            return  head2;
        else if(head2 == nullptr)
            return head1;
        ListNode* new_head = new ListNode(0);
        
        ListNode* cur = new_head;
        while(head1 != nullptr && head2 != nullptr){
            if(head1->val <= head2->val){
                cur->next = head1;
                head1= head1->next;
            }else{
                cur->next = head2;
                head2 = head2->next;
                
            }
            cur = cur->next;    
        }
        
        if(head1 != nullptr)
            cur->next = head1;
        
        if(head2 != nullptr)
            cur->next = head2;
        
        cur = new_head->next;
        delete new_head;
        
        return cur;
    }
};

```




</details>





### 26. 树的子结构

<details><summary>code</summary>

- [树的子结构](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    bool HasSubtree(TreeNode* root1, TreeNode* root2)
    {
        if(! root1  || !root2 )
            return false;
        return isequal(root1,root2)|| isequal(root1->left,root2) || isequal(root1->right, root2);

    }
    
    bool isequal(TreeNode* root1, TreeNode* root2){
        if (root2 == nullptr)
            return true;
        if (root1 == nullptr)
            return false;
        if(root1->val != root2->val)
            return false;
        
        return isequal(root1->left, root2->left) && isequal(root1->right, root2->right);

    }
};
```

</details>




### 27. 二叉树的镜像

<details><summary>code</summary>

- [二叉树的镜像](https://www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=13&tqId=11171&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 先序遍历或者后序遍历



```c++
class Solution {
public:
    void Mirror(TreeNode *root) {
        if(!root)
            return;
        if(root->left)
            Mirror(root->left);
        if(root->right)
            Mirror(root->right);
        
        TreeNode* tmp = root->left;
        root->left = root->right;
        root->right = tmp;
    }
};
```

</details>




### 28 对称的二叉树

<details><summary>code</summary>

- [对称的二叉树](https://www.nowcoder.com/practice/ff05d44dfdb04e1d83bdbdab320efbcb?tpId=13&tqId=11211&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    bool isSymmetrical(TreeNode* root)
    {
        if(!root)
            return true;
        
        return issymmetric(root->left, root->right);
        
        
    
    }
    
   bool issymmetric(TreeNode* left, TreeNode* right){
       if(!left && ! right)
           return true;
       if(!left || !right)
           return false;
       if(left->val != right->val)
           return false;
        
       return issymmetric(left->left, right->right) && issymmetric(left->right, right->left);
   }

};
```

</details>





### 29. 顺时针打印矩阵

<details><summary>code</summary>

- [ 顺时针打印矩阵](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=11172&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 考虑最后两个循环的单行单列问题



```c++
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        vector<int> res;
        if(matrix.empty())
            return res;
        int r2 = matrix.size()-1;  
        int c2 = matrix[0].size()-1;
        int r1=0;
        int c1=0;
        int i;
        
       while(r1<=r2 && c1<= c2){
           for(i=c1;i<=c2;i++){  // 右行
               res.push_back(matrix[r1][i]);
           }
     
           for(i = r1+1; i<= r2;i++){  // 下行
               res.push_back(matrix[i][c2]);
           }
            
           // 后两种防止单列情况
           if(r1!=r2)  // 单行
               for(i = c2-1; i>=c1; i--){  // 左行
                   res.push_back(matrix[r2][i]);
               }
            if(c1!=c2)  // 单列
               for(i=r2-1; i>r1;i--){ // 上行
                   res.push_back(matrix[i][c1]);
               }
           r1++;c1++;r2--;c2--;

       }
        return res;
        
    }
};
```

</details>





### 30. 包含 min 函数的栈

<details><summary>code</summary>

- [包含 min 函数的栈](https://www.nowcoder.com/practice/4c776177d2c04c2494f2555c9fcc1e49?tpId=13&tqId=11173&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)<details><summary>code</summary>

```c++
class Solution {
public:
    void push(int value) {
           if(minstack.empty())
               minstack.push(value);
            else if(value < minstack.top()){
                minstack.push(value);
            }
        datastack.push(value);
        
        
    }
    void pop() {
		
		assert(! datastack.empty() && ! minstack.empty() );
        if(datastack.top() == minstack.top())
            minstack.pop();
        datastack.pop();
        
    }
    int top() {
		assert(! datastack.empty() && ! minstack.empty() );		
        return datastack.top();
        
    }
    int min() {
		assert(! datastack.empty() && ! minstack.empty() );
        return minstack.top();
    }
private:
    stack<int> datastack;
    stack<int> minstack;
};
```

</details>




### 31. 栈的压入、弹出序列

<details><summary>code</summary>

- [栈的压入、弹出序列](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用一个栈模拟


```c++
class Solution {
public:
    bool IsPopOrder(vector<int> in,vector<int> out) {
        if(in.empty() && !out.empty() || ( !in.empty() && out.empty() ))
            return false;
        stack<int> sin;
        int index = 0;
        int len = out.size();
        for(int i : in){
            sin.push(i);
            while(!sin.empty() && index < len && sin.top() == out[index])
            {
                sin.pop();
                index++;
            }
        }
        return sin.empty();
        
    }
};
```

</details>




### 32.1 从上往下打印二叉树

<details><summary>code</summary>

- [从上往下打印二叉树](https://www.nowcoder.com/practice/7fe2212963db4790b57431d9ed259701?tpId=13&tqId=11175&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用队列,逐渐遍历
>

```c++
class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> res;
        if(!root)
            return res;
        deque<TreeNode*> d;
        d.push_back(root);
        TreeNode* cur;
        while(!d.empty()){
            cur = d.front();
            res.push_back(cur->val);
            if(cur->left)
                d.push_back(cur->left);
            if(cur->right)
                d.push_back(cur->right);
            d.pop_front();
        }
        return res;
        
    }
};
```

</details>






### 32.2 把二叉树打印成多行

<details><summary>code</summary>

- [把二叉树打印成多行](https://www.nowcoder.com/practice/445c44d982d04483b04a54f298796288?tpId=13&tqId=11213&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 多行，需要分割，及时读取队列长度来做分割

```c++
class Solution {
public:
        vector<vector<int> > Print(TreeNode* root) {
            vector<vector<int>> res;
            if(!root)
                return res;
           deque<TreeNode*> d;
            d.push_back(root);		// 入队列
           TreeNode* cur;
           int len =0;
           while(! d.empty()){
               vector<int> tmp;
               len = d.size();				// 重要： 获取每层的长度
               for(int i=0;i<len;i++){    // 迭代，一层
                    cur = d.front();
                    tmp.push_back(cur->val);
                    if(cur->left)
                        d.push_back(cur->left);
                    if(cur->right)
                        d.push_back(cur->right);
                    d.pop_front();  
               }
               res.push_back(tmp);
           }
            return res;
        }
    
};
```

</details>



### 32.3 按之字形顺序打印二叉树

<details><summary>code</summary>

- [按之字形顺序打印二叉树](https://www.nowcoder.com/practice/91b69814117f4e8097390d107d2efbe0?tpId=13&tqId=11212&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用两个栈模拟左右顺序

```c++
class Solution {
public:
    vector<vector<int> > Print(TreeNode* root) {
        vector<vector<int>> res;
        if(!root)
            return res;
        stack<TreeNode*> left;
        stack<TreeNode*> right;
        bool flag = false;
        TreeNode* cur;
        
        left.push(root);
        
        while(!left.empty() || !right.empty()){ // 有一个不为空
            vector<int> tmp;
            while(! left.empty()){		// 从左往右
                cur = left.top();
                tmp.push_back(cur->val);
                if(cur->left)
                    right.push(cur->left);
                if(cur->right)
                    right.push(cur->right);  // 后进先出
                left.pop();
            }
            res.push_back(tmp);
            
            vector<int> tmp2;
            while(!right.empty()){			// 从右往左
                 cur = right.top();
                 tmp2.push_back(cur->val);
                if(cur->right)
                    left.push(cur->right);
                if(cur->left)
                    left.push(cur->left);  // 后进先出
                right.pop();
            }
            
            if(!tmp2.empty())
                res.push_back(tmp2);

        }
        
        return res;
  
        
    }
    
};
```

</details>




### 33. 二叉搜索树的后序遍历序列

<details><summary>code</summary>

- [二叉搜索树的后序遍历序列](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 二叉搜索树：left < root < right



```c++
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.empty())
            return false;
        
        return isBST(sequence,0,sequence.size()-1);
    }
    
    
    bool isBST(vector<int> s, int start, int end){
        if(end - start <=1)  // 一个节点或者含一个子节点
            return true;
        int root = s[end];  //后序
        int cur = start;
        
        // 查找左子树
        while(cur < end && s[cur] < root)
            cur++;
        for(int i= cur;i<end;i++){  // 右子树存在小于root的值
            if(s[i]< root)
                return false;
        }
        return isBST(s,start,cur-1) && isBST(s,cur,end-1);
    }
};
```

</details>






### 34. 二叉树中和为某一值的路径

<details><summary>code</summary>

- [二叉树中和为某一值的路径](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
       vector<int> tmp;
        tracking(root, expectNumber,tmp);
        return res;
        
    }
    vector<vector<int>> res;
    
    void tracking(TreeNode* root, int sum , vector<int> tmp){    // 使用赋值传值,就不用回退
        if(! root || sum<0) return;
        sum -= root->val;
        tmp.push_back(root->val);
        if(sum == 0 && root->left == NULL && root->right == NULL)
            res.push_back(tmp);
        else{
            tracking(root->left, sum,tmp);
            tracking(root->right, sum, tmp);
        }
    }
};
```

</details>





### 35. 复杂链表的复制

<details><summary>code</summary>

- [复杂链表的复制](https://www.nowcoder.com/practice/f836b2c43afc4b35ad6adc41ec941dba?tpId=13&tqId=11178&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 在每一个节点后面复制一个节点，指针同样指向复制节点


```c++
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if(!pHead)
            return NULL;
        // 对每个节点进行复制
        RandomListNode* cur = pHead;
        while(cur){
            RandomListNode* tmp = new RandomListNode(cur->label);    // 新建节点
            tmp->next = cur->next;
            cur->next = tmp;
            cur = tmp->next;
        }
        
        // random节点
        cur = pHead;
        while(cur){
            RandomListNode* tmp = cur->next;
            if(cur->random)
                tmp->random = cur->random->next;  // 指向复制节点
            cur = tmp->next;
        }
        
        // 分开
        cur = pHead;
        RandomListNode* head = cur->next; //复制节点的头结点
        while (cur->next != NULL) {
        RandomListNode* next = cur->next;
        cur->next = next->next;
        cur = next;
        }
        
        return head;
    }
};
```

</details>






### 36. 二叉搜索树与双向链表

<details><summary>code</summary>

- [二叉搜索树与双向链表](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=11179&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用中序遍历来进行


```c++
class Solution {
public:
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
            if (pRootOfTree == NULL)
            return NULL;
            inorder(pRootOfTree);
            return head;
        
    }
    TreeNode* head= NULL,*pre =NULL;	// 保留前置指针
    
    void inorder(TreeNode* root){
        if(!root)
            return;
        inorder(root->left);
        root->left = pre;    //前置
        if(pre)
            pre->right = root;
        pre = root;
        if(!head)
            head = root;
        inorder(root->right);
        
        
    }
};
```


</details>






### 37. 序列化二叉树

<details><summary>code</summary>

- [序列化二叉树](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=11214&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- 随意选择一种遍历方式，然后主要考虑替换空节点


```c++
    char* Serialize(TreeNode *root) {
       if(root == NULL)
           return NULL;
        string str;
        Serialize(root, str);
        char *ret = new char[str.length() + 1];
        int i;
        for(i = 0; i < str.length(); i++){
            ret[i] = str[i];
        }
        ret[i] = '\0';
        return ret;
    }
    void Serialize(TreeNode *root, string& str){
        if(root == NULL){
            str += '#';
            return ;
        }
        string r = to_string(root->val);
        str += r;
        str += ',';
        Serialize(root->left, str);
        Serialize(root->right, str);
    }
     
    TreeNode* Deserialize(char *str) {
        if(str == NULL)
            return NULL;
        TreeNode *ret = Deserialize(&str);
 
        return ret;
    }
    TreeNode* Deserialize(char **str){//由于递归时，会不断的向后读取字符串
        if(**str == '#'){  //所以一定要用**str,
            ++(*str);         //以保证得到递归后指针str指向未被读取的字符
            return NULL;
        }
        int num = 0;
        while(**str != '\0' && **str != ','){
            num = num*10 + ((**str) - '0');
            ++(*str);
        }
        TreeNode *root = new TreeNode(num);
        if(**str == '\0')
            return root;
        else
            (*str)++;
        root->left = Deserialize(str);
        root->right = Deserialize(str);
        return root;
    }

```

</details>





### 38. 字符串的排列

<details><summary>code</summary>

- [字符串的排列](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
 
    
    vector<string> Permutation(string str) {
        vector<string> result;
        if(str.length()==0){
            return result;
        }
        sort(str.begin(),str.end());
        set<string> res;
        PermutationHelp(res, 0, str);
        
        
        for(string i: res){
            result.push_back(i);
        }
        return result;
    }

    
void PermutationHelp(set<string>& res, int k, string str) //遍历第k位的所有可能
    {
        if(k == str.size() - 1)			// 到达尾端，成为一个新的排列
            res.insert(str);
        for(int i = k; i < str.size(); i++)
        {
            if(i != k && str[k] == str[i])  // 重复跳过
                continue;
            swap(str[i], str[k]);
            PermutationHelp(res, k + 1, str);
        }
    }

};
```

</details>






### 39. 数组中出现次数超过一半的数字

<details><summary>code</summary>

- [数组中出现次数超过一半的数字](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- unordered_map
- 统计


```c++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        unordered_map<int,int> res;
        for(int val:numbers)
        {
            res[val]++;
        }

        int c =0;
        int max=0;
        for(auto& val: res){
            if(val.second >c ){
                c = val.second;
                max = val.first;
            }
        }


        if(c < (numbers.size()/2 + 1) )
            return 0;
        else
            return max;

    }
};
```


- 多数投票问题，可以利用 Boyer-Moore Majority Vote Algorithm 来解决这个问题，使得时间复杂度为 O(N)。

使用 cnt 来统计一个元素出现的次数，当遍历到的元素和统计元素相等时，令 cnt++，否则令 cnt--。如果前面查找了 i 个元素，且 cnt == 0，说明前 i 个元素没有 majority，或者有 majority，但是出现的次数少于 i / 2 ，因为如果多于 i / 2 的话 cnt 就一定不会为 0 。此时剩下的 n - i 个元素中，majority 的数目依然多于 (n - i) / 2，因此继续查找就能找出 majority。



```c++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if(numbers.empty())
            return 0;
        int c = numbers[0];
        int cnt = 1;
        for(int i=1;i<numbers.size();i++)   // 寻找最多的元素
        {
            cnt = (c == numbers[i]) ? cnt+1 : cnt -1;
            if(cnt == 0)
            {
                c = numbers[i];
                cnt =1;
            }   
        }
       
    cnt = 0;
    for (int val : numbers)	// 检查是否多数
        if (val == c)
            cnt++;
    return cnt > (numbers.size() / 2) ? c : 0;
    }
};
```

</details>




### 40. 最小的 K 个数

<details><summary>code</summary>


- [最小的 K 个数](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 第一种方式： 使用快速排序
- 第二种方式： 使用大顶堆


```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(input.empty() || k > input.size() || k < 0)
            return res;
            
        findK(input, k-1);
        
        for(int i=0;i<k;i++)
            res.push_back(input[i]);
        
        return res;
 
    }
    void findK(vector<int>& nums, int k){
        int l = 0, h = nums.size()-1;
        int mid = 0;
        while( l < h){
            mid = partition(nums,l,h);
            if(mid == k)
                break;
            else if( k > mid)
                l = mid +1;
            else 
                h =  mid -1;
        }
    }
    
    int partition(vector<int>& nums, int l , int h){
  
         int p = nums[l];     /* 切分元素 */
        int i = l, j = h + 1;
        while (true) {
            while (i != h && nums[++i] < p) ;
            while (j != l && nums[--j] > p) ;
            if (i >= j)
                break;
            swap(nums[i], nums[j]);
        }
        swap(nums[l], nums[j]);
        return j;
    }
    
    
};
```


```c++

class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(input.empty() || k > input.size() || k < 0)
            return res;
     
        priority_queue<int> pq;  // 大顶堆
        
        for(auto in : input){            // 维持最小的k个元素
            pq.push(in);
            if(pq.size() > k){
                pq.pop();
            }
        }
        
        while(!pq.empty()){
            res.push_back(pq.top());
            pq.pop();
        }
        
        return res;
    }
    
};

```

</details>





### 41.1 数据流中的中位数

<details><summary>code</summary>

- [数据流中的中位数](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- 插入一次，排序一次 

```c++
class Solution {
public:
    vector<int> data;


    void Insert(int num) {
        data.push_back(num);
        std::sort(data.begin(), data.end());
    }
 
    double GetMedian() {
        unsigned int size = data.size();
        if (size & 1) {   // 奇数 个
            return data[size >> 1];
        } else {   // 偶数
            int left = data[(size >> 1) - 1];
            int right = data[size >> 1];
            return (static_cast<double>(left) + right)/2 ;
        }
    }

};
```


- 使用两个堆来存储

```c++
class Solution {
public:
    priority_queue<int, vector<int>, greater<int>> right;  // 右边放大元素，堆顶为最小元素
    priority_queue<int> left; // 左边放小元素，堆顶为最大元素


    void Insert(int num) {
        // 奇数个在小根堆取
        if(left.empty() || num < left.top() )
            left.push(num);
        else
            right.push(num);
        
        if(left.size()== right.size() +2){  // 小元素堆多
            right.push(left.top());
            left.pop();
        }
        if(left.size() == right.size() -1){ // 大元素堆多
            left.push(right.top());
            right.pop();
        }
    }
 
    double GetMedian() {
        int l_len = left.size();
        int b_len = right.size();
        return (l_len == b_len)? (left.top() + right.top()) / 2.0 : left.top();
    }

};
```

</details>




### 41.2 字符流中第一个不重复的字符

<details><summary>code</summary>

- [字符流中第一个不重复的字符](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- unordered_map 统计

```c++
class Solution
{
public:
  //Insert one char from stringstream
    vector<char> str;
    int  cnt[256];
    void Insert(char ch)
    {
        str.push_back(ch);
        cnt[ch-'\0']++;
    }
  //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        int size=str.size();
        for(int i=0;i<size;++i)
        {
            if(cnt[str[i]-'\0']==1)
                return str[i];
        }
        return '#';
    }

};
```

</details>




### 42. 连续子数组的最大和

<details><summary>code</summary>

- [连续子数组的最大和](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- dp  `dp[i] = max(array[i], dp[i-1]+array[i])`


```c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.empty())return 0;
        int temp = 0;
        int sum = INT_MIN;
        for(int i=0;i<array.size();i++){
            temp = max(array[i], temp + array[i]);
            sum = max(sum, temp);
        }
        return sum;
    }
};
```

</details>






### 43. 从 1 到 n 整数中 1 出现的次数

<details><summary>code</summary>

- [从 1 到 n 整数中 1 出现的次数](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 对每一个数字统计元素1个数



- 暴力破解

```c++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        if(n<=0)
            return 0;
        int count =0;
        for(int i=1;i<=n;i++){
            int temp = i;
            while(temp){
                if(temp%10 == 1)
                    count ++;
                temp  = temp /10;
            }
        }
        return count;
    }
};
```



- 每十个数之间的有的总数

```c++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
    int cnt = 0;
    for (int m = 1; m <= n; m *= 10) {
        int a = n / m, b = n % m;
        cnt += (a + 8) / 10 * m + (a % 10 == 1 ? b + 1 : 0);
    }
    return cnt;
    }
};

```


</details>






### 44. 数字序列中的某一位数字

<details><summary>code</summary>

- 数字以 0123456789101112131415... 的格式序列化到一个字符串中，求这个字符串的第 index 位。

1. 计算是个、十、百、千中的那个区间
2. 然后计算具体的数字

```c++

// 个，十、 百、千
int getPlace(int place){
	if(place == 1)
		return 10;
	return (int)pow(10,place -1) * 9;
}


// 区间的起始数字

int getBeginNumber(int place){

	if(place == 1)
		return 0;
		
	return (int)pow(10,place -1);
}


// 计算第index位

int getIndex(int index, int place){
	int beginnumber = getBeginNumber(place);
	int shift = index / place;
	string num = to_string(beginnumber + shift);  // 获取数字
	int count = index % place; // 数字中的某位
	
	return num[count] - '0';

}

// 主函数

int getDigitIndex(int index){
	if(index < 0)
		return -1;
		
	int place = 1;  // 个、十、百。。。
	while(true){
	
		int count = getPlace(place);    // 区间数量
		int totalCount = count * place;
		if(index < totalCount)
			return getIndex(place,index);
				
		index -= totalCount;
		place++;
	}
}

```



</details>






### 45. 把数组排成最小的数

<details><summary>code</summary>

- [把数组排成最小的数](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 自定义一个字符串比较函数


```c++
class Solution {
public:
   string PrintMinNumber(vector<int> numbers) {
        if(numbers.empty())
            return "";
        string left,right;
       vector<string> res;
       for(auto i:numbers){
           res.push_back(to_string(i));
       }
       sort(res.begin(),res.end(),comp);
       string result;
       for(auto i: res)
           result.append(i);
      
        return result;
    }
   static bool comp(string a, string b){
        string A = a+b;
        string B = b+a;
        return A < B;
    }
    
};
```

</details>




### 46. 把数字翻译成字符串

<details><summary>code</summary>

- [把数字翻译成字符串](https://leetcode.com/problems/decode-ways/description/)


```c++
class Solution {
public:
    int numDecodings(string s) {
        if (s.empty())
            return 0;
        
        int len = s.size();
        vector<int> nums(len+1,0);
        nums[0] = 1;
        nums[1] = (s[0] == '0')?0:1;  // 第一个数字为0
        for(int i=2;i <= len; i++){  // i代表数字个数
            int one = s[i-1]- '0';  // 单个字
            if( 0 != one)
                nums[i] += nums[i-1];
            if(s[i-2] == '0')  // 前一个字0
                continue;
            int two = stoi(s.substr(i-2,2));
            if(two <= 26)
                nums[i] += nums[i-2];
        }
        return nums[len];


    }
};
```

</details>






### 47. 礼物的最大价值

<details><summary>code</summary>

- [礼物的最大价值](https://www.nowcoder.com/questionTerminal/72a99e28381a407991f2c96d8cb238ab)

- `dp[i][j] = max(dp[i-1][j] ,dp[i][j-1]) + d[i][j]`


```c++
class Bonus {
public:
    int getMost(vector<vector<int> > board) {
        // write code here
         if(board.empty())
            return 0;
        int m = board.size();
        int n = board[0].size();
 
        vector<vector<int>> res;
        for(int i=0;i<m+1;i++){  // 给board添加一个padding
            vector<int> tmp(n+1,0);
            res.push_back(tmp);
        }  // 生成M*N；
 
        res[0][0] = board[0][0];
        for(int i=0;i<m;i++)
        {
            for(int j=0;j<n;j++){
                if(i == 0 && j == 0)      // 初始化
                    res[i][j] = board[0][0];
                else if( i== 0)           // 单一情况
                    res[i][j] = res[i][j-1] + board[i][j];
                else if(j == 0)           // 单一情况
                    res[i][j] = res[i-1][j] + board[i][j];
                else
                    res[i][j] = max(res[i-1][j],res[i][j-1]) + board[i][j];
            }
        }
 
        return res[m-1][n-1];
 
 
     
    }
};
```

</details>






### 48. 最长不含重复字符的子字符串

<details><summary>code</summary>


- [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)


> 输入一个字符串（只包含 a~z 的字符），求其最长不含重复字符的子字符串的长度。例如对于 arabcacfr，最长不含重复字符的子字符串为 acfr，长度为 4。


- 保留对应字符的起始位置，如果再次出现，计算当前子字符串的长度，更新最大长度，然后更新当前字符的最新位置


使用一个变量保存当前重复变量的之前位置，算法复杂度为O(N^2)

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        bitset<256> set;
        
        int len = s.size();
        int cur =0;
        int begin=0;
        int max=0;
        while(cur< len){
            if(set[s[cur] - '\0'] == false)
            {
                set[s[cur] - '\0'] = 1;
                cur++;
            }
            else{
              //  cout<<"cur:"<<cur<<endl;
                if(max < set.count())max = set.count();
                begin++;
                set.reset();
                cur = begin;
            }
              
        }
        
        return set.count()>max ? set.count() : max;
        
    }
};
```

使用一个字符表来保存256个字符，记录每个字符前一次出现的位置

```c++

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> sets(256,-1);
        
        if(s.size() <=1)
            return s.size();
        int be = 0;
        int len = s.size();
        int cur = 0;
        
        int maxLen = 0;
        int pre = 0;
        int curLen = 0; 
        
        for(cur =0; cur < len ; cur ++){
            pre = sets[s[cur] - '\0'];    // 前一次出现的位置
             
            if(pre == -1 || cur - pre > curLen)   // 当前的长度
                ++curLen;
            else {                // 已经出现过
                maxLen = max(maxLen, curLen);    // 本次计算的最长长度
                curLen = cur - pre;              // 记录当前的长度
            }
            
            sets[s[cur] - '\0'] = cur;             // 记录当前字符出现的位置
            
        }
        maxLen = max(maxLen, curLen);    // 最后比较一次
        return maxLen;
    }
};



```




</details>







### 49. 丑数

<details><summary>code</summary>

- [丑数](https://www.nowcoder.com/practice/6aa9e04fc3794f68acf8778237ba065b?tpId=13&tqId=11186&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++
class Solution {
public:
    int GetUglyNumber_Solution(int index) {
        if (index < 6)
            return index;
        int i2= 0, i3= 0, i5 = 0;
        vector<int> dp(index,0);
        dp[0] =1;
        for(int i=1; i< index;i++){
            int next2 = dp[i2] * 2, next3 = dp[i3] * 3, next5 = dp[i5] * 5;
            dp[i] = min(min(next3,next5),next2);
            if(dp[i] == next2)
                i2++;
            if(dp[i] == next3)
                i3++;
            if(dp[i] == next5)
                i5++;
        }
        
        return dp[index -1];
        
        
        
    }
};
```

</details>




### 50. 第一个只出现一次的字符位置

<details><summary>code</summary>

- [第一个只出现一次的字符位置](https://www.nowcoder.com/practice/1c82e8cf713b4bbeb2a5b31cf5b0417c?tpId=13&tqId=11187&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++

- 使用hash来记录当前字符的个数
- 还可以使用两个位图来记录字符的出现次数是否两次，第一次出现放到位图1，出现第二次放到位图2，再遍历一次查看是否两个位图都置位


class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.empty())
            return -1;
        unordered_map<char, int> map;
        
        for(int i=0;i<str.size();i++){
            map[str[i]]++;
            
        }
        int cnt=-1;
        for(int i=0;i<str.size();i++)
        {
            if(1 == map[str[i]]){
                cnt = i;
                break;
            }
        }
        
        return cnt;
    }
};
```


```c++

class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.empty())
            return -1;
        std::bitset<256> bt1;
        std::bitset<256> bt2;
        
        size_t d;
        for(int i=0;i<str.size();i++){
            d = str[i] - '\0';
            if(bt1.test(d)== false)
                bt1.set(d);
            else
                bt2.set(d);
            
        }
        int cnt=-1;
        for(int i=0;i<str.size();i++)
        {
            d = str[i] - '\0';
            if(bt1.test(d) == true && bt2.test(d) == false){
                cnt = i;
                break;
            }
        }
        
        return cnt;
    }
};


```


</details>




### 51. 数组中的逆序对

<details><summary>code</summary>

- [数组中的逆序对](https://www.nowcoder.com/practice/96bd6684e04a44eb80e6a68efc0ec6c5?tpId=13&tqId=11188&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 使用归并排序，每一次交换，统计两个下标差


```c++
class Solution {
    vector<int> tmp;
    int cnt=0;

    
public:
    int InversePairs(vector<int> data) {
        int len = data.size();
        tmp.resize(len);
        mergesort(data,0,len-1);
       return (int) (cnt % 1000000007); 


    }
    

void mergesort(vector<int>& nums,int l, int h){
    if (h - l < 1)
        return;
    int m = l + (h - l) / 2;
    mergesort(nums, l, m);
    mergesort(nums, m + 1, h);
    twomerge(nums, l, m, h);

}


void twomerge(vector<int>& nums, int l, int m, int h){
    int i = l, j = m + 1, k = l;
    while (i <= m || j <= h) {
        if (i > m)
            tmp[k] = nums[j++];
        else if (j > h)
            tmp[k] = nums[i++];
        else if (nums[i] < nums[j])
            tmp[k] = nums[i++];
        else {
            tmp[k] = nums[j++];
            cnt += m - i + 1;  // nums[i] >= nums[j]，说明 nums[i...mid] 都大于 nums[j]
        }
        k++;
    }
    for (k = l; k <= h; k++)
        nums[k] = tmp[k];

}

};
```

</details>




### 52. 两个链表的第一个公共结点

<details><summary>code</summary>

- [两个链表的第一个公共结点](https://www.nowcoder.com/practice/6ab1d9a29e88450685099d45c9e31e46?tpId=13&tqId=11189&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- 设 A 的长度为 a + c，B 的长度为 b + c，
- 其中 c 为尾部公共部分长度，可知 a + c + b = b + c + a。
- A访问完访问B
- B访问完访问A


```c++
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        ListNode* l1= pHead1, *l2 = pHead2;
        while(l1 != l2){
            l1 = (l1==nullptr)? pHead2 : l1->next;
            l2 = (l2==nullptr)?pHead1 : l2->next;
        }
        return l1;
        
    }
};
```

</details>





### 53 数字在排序数组中出现的次数

<details><summary>code</summary>

- [数字在排序数组中出现的次数](https://www.nowcoder.com/practice/70610bf967994b22bb1c26f9ae901fa2?tpId=13&tqId=11190&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 简单方法就是统计

- 排序： 二分查找


```c++
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        int first = binfind(data,k);
        int last = binfind(data,k+1);
        if(! data.empty() && data[first] == k)
            return last - first;
        return 0;
        
    }
    
    int binfind(vector<int> data, int k){  // 寻找到数字开始的位置
        int len = data.size();
        int l = 0 , h = len;
        while(l < h){
            int mid = (l+h-1)/2;
            if(data[mid] >= k)
                h = mid;
            else 
                l = mid+1;
        }
        return l;
    }
};
```

</details>


### 54. 二叉搜索树的第 K 个结点

<details><summary>code</summary>

- [二叉搜索树的第 K 个结点](https://www.nowcoder.com/practice/ef068f602dde4d28aab2b210e859150a?tpId=13&tqId=11215&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 中序遍历


```c++
class Solution {
    TreeNode* res=nullptr;
    int cnt=0;
public:
    TreeNode* KthNode(TreeNode* pRoot, int k)
    {
        inorder(pRoot, k);
        return res;
    }
    
    void inorder(TreeNode* root, int k){
        if(!root || cnt >=k)
            return;
        inorder(root->left,k);
        cnt ++;
        if(cnt == k){
            res = root;
        }
        inorder(root->right,k);
    }

    
};
```

</details>




### 55.1 二叉树的深度

<details><summary>code</summary>

- [二叉树的深度](https://www.nowcoder.com/practice/435fb86331474282a3499955f0a41e8b?tpId=13&tqId=11191&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if (! pRoot)
            return 0;
        return 1+ max(TreeDepth(pRoot->left), TreeDepth(pRoot->right));
    
    }
};
```
</details>




### 55.2 平衡二叉树

<details><summary>code</summary>

- [平衡二叉树](https://www.nowcoder.com/practice/8b3b95850edb4115918ecebdf1b4d222?tpId=13&tqId=11192&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 左右子树相差为最多1


```c++
class Solution {
public:
    bool isBalanced = true;
    bool IsBalanced_Solution(TreeNode* pRoot) {
    height(pRoot);
    return isBalanced;
    }
    
    int height(TreeNode* root){
        if (root == nullptr || !isBalanced)
            return 0;
        int left = height(root->left);
        int right = height(root->right);
        if (abs(left - right) > 1)
            isBalanced = false;
        return 1 + max(left, right);
    }
};

```

</details>





### 56. 数组中只出现一次的数字

<details><summary>code</summary>

- [数组中只出现一次的数字](https://www.nowcoder.com/practice/e02fdb54d7524710a7d664d082bb7811?tpId=13&tqId=11193&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)
- [136. Single Number](https://leetcode.com/problems/single-number/description/)


- 两个相同的数异或为0 ，
- 两个不用的数异或中至少有一位为0,找到这一位
- 然后 与数相与


```c++
class Solution {
public:
    void FindNumsAppearOnce(vector<int> nums,int* num1,int *num2) {
        if(nums.empty())
            return ;
    int diff = 0;
    for (int num : nums)
        diff ^= num;
    
    int index = findBitOne(diff);  // 寻找当前数的中为1的二进制位数下标

    for (int num : nums) {
        if (isBitOne(num, index))
            num1[0] ^= num;
        else
            num2[0] ^= num;
    }
        
    }
    
    
    int findBitOne(int diff){   // 两个不同的数异或中的一位为1
        int index =0;
        while(index < 32 && (diff & 1) == 0 ){
            diff =diff >> 1;
            index++;
        }
        return index;
    }
    
    bool isBitOne(int num, int index){  // 一个数 和两个数异或的值的某一位 相与
        num >>= index;
        if((num & 1) == 1) 
            return true;
        return false;
    }
};

```

</details>







### 56.1 数组中只出现一次的数字（2）

<details><summary>code</summary>

- [137. Single Number II](https://leetcode.com/problems/single-number-ii/description/)

- 除了一个元素外，其他元素都出现3次

```c++
	
    那么，如果是三次的话，香浓定理，需要用 2bits 进行记录

    当5第一次出现的时候，b = 5, a=0,  b记录这个数字
    当5第二次出现的时候，b = 0, a=5， a记录了这个数字
    当5第三次出现的时候，b = 0, a=0， 都清空了，可以去处理其他数字了
    所以，如果有某个数字出现了1次，就存在b中，出现了两次，就存在a中，所以返回 a|b
	
	
	公式方面 ，上面两次的时候，b清空的公式是 b = b xor i
            而第三次时，b要等于零，而这时a是True，所以再 & 一个a的非就可以，b = b xor & ~a
			
   a = b = 0
    for num in nums:
        b = b ^ num & ~a   
        a = a ^ num & ~b   
    return a|b	

```



```c++

class Solution {
public:
    int singleNumber(vector<int>& nums) {
    
        int a =0,  b = 0;
        
        for(auto i: nums){
            b = b ^ i & ~ a;
            a = a ^ i & ~ b;
            
        }
        return a | b;
    
     
    }
};

```

</details>



### 57.1 和为 S 的两个数字

<details><summary>code</summary>

- [和为 S 的两个数字](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


- 采用双指针，左右向中间查找，并且保存一个乘积最小的两个数

```c++
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        int l= 0, h = array.size() -1;
        int cur=0;
        vector<int> res;
        while(l<h){
            cur = array[l] + array[h];
            if(cur < sum)
                l++;
            else if(cur > sum)
                h --;
            else{
                res.push_back(array[l]);
                res.push_back(array[h]);
                break;
            }
        }
        return res;
    }
};
```
</details>





### 57.2 和为 S 的连续正数序列


<details><summary>code</summary>

- [和为 S 的连续正数序列](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 从最小值开始，保存开始 l 和结束 r 两个变量，
- 如果和小于sum,r 向后增加，如果大于sum , 减去 l的值 ,提高 l

- 最多序列 r 不超过 和的一半 加 1；


```c++
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> res;
       // 从小开始增加
        int l =1,r=1,smax = 1;
        while(r < (sum/2 +2)){
            r++;
            smax +=r;
            while(smax > sum){ // 总和大于sum
                smax -= l;
                l++;  // 提高小值 试试
            }
            if(smax == sum  && l!=r){
                vector<int> tmp;
                for(int i=l; i<=r;i++)  // 保存当前序列
                    tmp.push_back(i);
                res.push_back(tmp);
            }
        }
        return res;
        
    }
   
};

```

</details>





### 58.1 翻转单词顺序列

<details><summary>code</summary>

- [翻转单词顺序列](https://www.nowcoder.com/practice/3194a4f4cf814f63919d0790578d51f3?tpId=13&tqId=11197&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)
- 使用额外空间

```c++
class Solution {
public:
    string ReverseSentence(string str) {
        stack<string> res;
        int cur=0;

          for(int i=0;i<str.size()+1;i++)
          {
              if(' ' == str[i] || '\t' == str[i] || i==str.size())
              {
                  res.push(string(str,cur,i-cur));
                  cur = i+1;
              }


          }
        string s;
        while(!res.empty()){
            s+=res.top();
            s+=' ';
            res.pop();
        }
        return s.substr(0,str.size());
    }
};

// 方法2 


class Solution {
public:
    string ReverseSentence(string str) {
        // 对每个单词进行旋转
        int len = str.size();
        int begin = 0;
        int end = 0;
        for(int i=0;i<=len; i++){
            if( i== len || str[i] == ' ' || str[i] ==  '\t'){
                end = i;
                reverseOne(str, begin, end-1);  
                begin = i+1;
            }  
        }
        
        reverseOne(str, 0, len-1);  // 对整个字符串旋转一次
        
        return str;
    }
    
    // 旋转每个单词
    void reverseOne(string& str, int i, int j){
        while(i<j)
            swap(str[i++],str[j--]);
    }
    
};
```

</details>





### 58.2 左旋转字符串


<details><summary>code</summary>

- [左旋转字符串](https://www.nowcoder.com/practice/12d959b108cb42b1ab72cef4d36af5ec?tpId=13&tqId=11196&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

```c++
class Solution {
public:
    string LeftRotateString(string str, int k) {
        if(str.empty())return "";
        string s=str;
        myreverse(s,0,k-1);  // 前半部分旋转
        myreverse(s,k,str.size()-1);  // 后半部分旋转
        myreverse(s,0,str.size()-1);  // 整体旋转
        return s;
    }
    
    void myreverse(string& s,int l, int h){  // [l,h]
         while(l<h)
             swap(s[l++],s[h--]);
        }              
};
```

</details>




### 59. 滑动窗口的最大值

<details><summary>code</summary>

- [滑动窗口的最大值](https://www.nowcoder.com/practice/1624bc35a45c42c0bc17d17fa0cba788?tpId=13&tqId=11217&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

1. 最简单的方法，就是每次移动一个窗口，然后寻找窗口的最大值
2. 维护一个最大堆，保存size个数字，每次插入，然后取堆顶数字就可以，删除失效的数字

```c++
class Solution {
public:
    vector<int> maxInWindows(const vector<int>& num, unsigned int size)
    {    
        vector<int> res;
         int len = num.size();
         if(num.empty() || size <1 || len < size)
             return res;
        
        int max=0,cur=0;
        int i=0;
        for(int i=0;i<size;i++){   // 第一个窗口
            if(max< num[i]){
                max = num[i];
                cur = i;
            }
        }
        res.push_back(max);
        for(int i=size;i<len;i++){
            if(cur < i -size +1)   // 前一个窗口的最大值
                findMax(num,i-size+1,i,max,cur);
            else if(max < num[i])
            {
                max = num[i];
                cur = i;
            }
            
            res.push_back(max);
        }
        
        
        return res;
        
    }
    
    // 遍历一个窗口寻找最大值
    void findMax(const vector<int>& num, int l, int r,int& max,int& cur){  
        max =0, cur = 0;
        for(int i=l;i<=r;i++){
            if(max< num[i]){
                max = num[i];
                cur = i;
            }
        }
    }
    
};


// 方法2 


vector<int> maxInWindows(const vector<int>& nums, unsigned int size){
	vector<int> result;
	priority_queue<pair<int,int>> pq;
	if(nums.size() < size || size < 1)
		return result;
		
	// 入堆 第一个窗口的元素
	for(int i=0;i<size -1 ; ++i){
		pq.push(pair<int,int>(nums[i], i));  // 存储元素和位置
	}
	
	// 存储每个窗口的最大值
	for(int i=size-1; i< nums.size(); ++i){
	
		pq.push(pair<int,int>(nums[i], i));
		pair<int,int> p = pq.top();
		
		while(p.second < i-(size -1 )){  // 前一个窗口的元素，出堆
			pq.pop();
			p = pq.top();
		}
		
		result.push_back(p.first);   // 保存最大元素
	}
	
	return result;

}

```

</details>


### 60. n 个骰子的点数

<details><summary>code</summary>

- [n 个骰子的点数](https://www.lintcode.com/problem/dices-sum/description)

```c++
class Solution {
public:
    /**
     * @param n an integer
     * @return a list of pair<sum, probability>
     */
    vector<pair<int, double>> dicesSum(int n) {
	 constexpr int face = 6;
	 int num = face * n;
	vector<vector<long>> dp(n+1, vector<long>(num+1));

	for(int i=1;i<=face;i++)  // 一个骰子的点数数量
		dp[1][i] = 1;
	
	for(int i=2;i<=n;i++){   // 骰子数量
		for(int j=i;j<=num;j++){   // 骰子点数和
			for(int k=1;k<=face && k<=j;k++)  // 当前和 == i-1个点的数量
				dp[i][j] += dp[i-1][j-k];
		}
	}
	
	double totalnum = pow(6,n);
	vector<pair<int, double>> res;
	
	for(int i=n;i<=num;i++){
		res.push_back(pair<int,double>(i,dp[n][i]/totalnum));
	}
		
		
	return res;	
    }
};
```

</details>




### 61. 扑克牌顺子

<details><summary>code</summary>

- [扑克牌顺子](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 癞子为0， 只需要统计非癞子的数之间的差值，对于癞子的个数就返回false

```c++
class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        	if(numbers.size() != 5)
		return false;
	
	sort(numbers.begin(),numbers.end());
	int cnt= 0;  // 统计癞子
	for(auto i: numbers){
		if(i ==0)
			cnt ++;
	}
	
	for(int i=cnt+1;i<numbers.size();i++){ // 从癞子第二个后面开始
		if(numbers[i-1] == numbers[i])  // 同样一张牌
			return false;
		cnt -= (numbers[i] - numbers[i-1] -1);  // 癞子补全
	}
	
	return cnt>=0;
    }
};
```

</details>



### 62. 圆圈中最后剩下的数

<details><summary>code</summary>

- [圆圈中最后剩下的数](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 笨方法： 使用一个数组模拟所有人，出队的下标就标记为-1，然后循环数数，遇到-1的就跳过
- 第二种方法： 使用递归

```c++
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n<=0 || m<=0)
            return -1;
        int i=-1;
        int cnt=0;
        int N=n;
        vector<int> res(n,1);
        while(n >0){
            i++;          //指向上一个被删除对象的下一个元素。
            if(i>=N) i=0;  //模拟环。
            if(res[i] == -1) continue; //跳过被删除的对象。
            cnt++;                     //记录已走过的。
            if(cnt==m) {               //找到待删除的对象。
                res[i]=-1;
                cnt = 0;
                n--;
            }        
        }
        return i;//返回跳出循环时的i,即最后一个被设置为-1的元素

    }
};



// 方法2
int LastRemaining_Solution(int n, int m){
	if(n == 0)
		return -1;
	if(n==1)
		return 0;
	
	return (LastRemaining_Solution(n-1, m) + m)%n;

}


```



</details>







### 63. 股票的最大利润

<details><summary>code</summary>

- [股票的最大利润](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)
- 寻找最小的一天，和股票价格最高的一天

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;
        int pmin=prices[0],pmax = 0;
        
        for(int i=1;i<prices.size();i++){
            pmin = min(pmin, prices[i]);
            pmax = max(pmax, prices[i] - pmin);
        }
        return pmax;
    }
};
```

</details>





### 64. 求 1+2+3+...+n

<details><summary>code</summary>

- [求 1+2+3+...+n](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

- 不能使用乘除法，使用 && 操作 作为递归判断退出条件

```c++
class Solution {
public:
    int Sum_Solution(int n) {
        int sum=n;
        bool b= (n>0)&&((sum +=Sum_Solution(n-1)) >0 );
        return sum;
    }
};
```

</details>







### 65. 不用加减乘除做加法

<details><summary>code</summary>

- [不用加减乘除做加法](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)<details><summary>code</summary>

思路：

> a ^ b 表示没有考虑进位的情况下两数的和，(a & b) << 1 就是进位。


```c++
class Solution {
public:
    int Add(int num1, int num2)
    {
        int n = num1 ^ num2;
        int m = (num1 & num2)<<1;
        while(m){
            num1 = n ^ m;
            num2= (n& m)<<1;
            n = num1;
            m = num2;
        }
        return n|m ;
    }
};
```

</details>






### 66. 构建乘积数组

<details><summary>code</summary>

- [构建乘积数组](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)
- 除了本身坐标，其他都要乘

```c++
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        vector<int> res;
        if(A.size() == 0)
            return res;
        res.resize(A.size(),1);
        int product =1;
        for(int i=0;i<A.size();i++)
        {
            res[i] = product;
            product *= A[i];
        }
        
        product =1;
        for(int i=A.size()-1;i>=0;i--){
            res[i] *= product;
            product *=A[i]; 
        }
       return res;     
    }
};
```

</details>


#### 67. 把字符串转换成整数

<details><summary>code</summary>

- [把字符串转换成整数](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)


```c++
class Solution {
public:
    int StrToInt(string str) {
        if(str.empty())
            return 0;
        int res =0;
        int flag = true;
        for(int i=0;i<str.size();i++){
            if(i==0 && (str[i] == '+' ||str[i] == '-' )){
                if(str[i] == '-')
                    flag = false;
                continue;
            }
            if('0'> str[i] || '9' < str[i])
                return 0;
            res = res*10 + (str[i] - '0');
        }
        return flag ? res : -res;
    }
};
```

</details>






### 68. 树中两个节点的最低公共祖先

<details><summary>code</summary>

- [树中两个节点的最低公共祖先](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/)

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q)
            return root;
        TreeNode* left = lowestCommonAncestor(root->left, p,q);
        TreeNode* right = lowestCommonAncestor(root->right,p,q);
        
        if(left != nullptr && right != nullptr)
            return root;
        else if(left == nullptr)
            return right;
        else 
            return left;
        
    }
};
```

</details>









