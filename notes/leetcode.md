- 参考资料
- [leetcode](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Leetcode%20%E9%A2%98%E8%A7%A3.md)

---

分类： 
- 双指针
- 排序
- 贪心算法
- 二分查找
- 分治
- 搜索
- 动态规划

- 数学
- 数据结构相关

---

# 1 双指针

1.1 【有序数组的 Two Sum】167. Two Sum II - Input array is sorted

<details><summary>code</summary>

- [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)


```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        vector<int> res;
        if(numbers.empty())
            return res;
        // 双边搜索
        int left=0, right = numbers.size() -1;
        while(left <right){
            if(numbers[left] + numbers[right] < target)
                left++;
            else if(numbers[left] + numbers[right] > target)
                right --;
            else 
                break;
        }
        if(left >= right)
            return res;
        
        res.push_back(left+1);
        res.push_back(right+1);
        return res;
        
        
            
    }
};
```

</details>

1.2 【两数平方和】 633. Sum of Square Numbers[leetcode]


<details><summary>code</summary>

```c++
class Solution {
public:
    bool judgeSquareSum(int c) {
        if(c < 0)
            return false;
        int mid = static_cast<int>(sqrt(c));
        cout<<mid<<endl;
        int l=0, r = mid;
        while(l<=r){
            if(l*l + r*r < c)
                l++;
            else if(l*l + r*r > c)
                r--;
            else
                break;
        }
        if (l>r)
            return false;
        
        return true;
        
    }
};

```

</details>

1.3 【反转字符串中的元音字符】345. Reverse Vowels of a String (Easy)


<details><summary>code</summary>

- 逆转两个元音字母


```c++

class Solution {
public:
    string reverseVowels(string s) {
        if(s.empty())
            return "";

       // cout<<"start.."<<endl;
        int l=0,r=s.size()-1;
        while(l<r){
            while(!res.count(s[l]))
                l++;
            while(!res.count(s[r]))
                r--;
            if(l<r)
                swap(s[l++],s[r--]);
            
        }
       // cout<<"s:"<<s<<endl;
        return s;
    }
public:
    set<char> res;
    Solution(){
        res.insert('a');
        res.insert('A');
        res.insert('e');
        res.insert('E');
        res.insert('i');
        res.insert('I');
        res.insert('o');
        res.insert('O');
        res.insert('U');
        res.insert('u'); 
    }
    
    
    
};

```

</details>


1.4 【回文字符串】680. Valid Palindrome II

<details><summary>code</summary>

```c++
class Solution {
public:
    bool validPalindrome(string s) {
        if(s.empty() || s.size() ==1)
            return true;
        
        int i=0, j= s.size()-1;
        int cnt=0;
        bool t = ispalin(s,i,j,cnt);

        return t;

    }
    
    
    bool ispalin(string& s , int i, int j,int cnt){
        if (cnt > 1)
            return false;
            if(i>j)
            return true;

        if(s[i] != s[j]){
            cnt++;
            return  ispalin(s,i+1,j,cnt) || ispalin(s,i,j-1,cnt);
        }
        else 
            return ispalin(s,i+1,j-1,cnt);
    }
};
```

</details>

1.5 【归并两个有序数组】88. Merge Sorted Array (Easy)

<details><summary>code</summary>

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        if(m+n > nums1.size() || n==0)
            return ;
        int len = nums1.size()-1;
        
        if(m==0){
            for(int i=0;i<n;i++)
                nums1[i] = nums2[i];
        }


        int i=m-1,j=n-1;
        while((i>=0 || j>=0) && len>=0 ){
            if(i<0)
            {
                nums1[len] = nums2[j];
                j--;
            }else if(j<0)
                break;
            else if(nums1[i] >= nums2[j]){
                nums1[len]= nums1[i];
                i--;
            }else{
                nums1[len] = nums2[j];
                j--;
            }
            len--;
        }

    }
};

```

</details>

1.6 【判断链表是否存在环】141. Linked List Cycle (Easy)

<details><summary>code</summary>

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(!head || head->next == nullptr)
            return false;
        ListNode* low , *fast;
        low = head;
        fast = low->next;
        while(low && fast && fast->next){
            if(low == fast)
                return true;
            low = low->next;
            fast = fast->next->next;
        }
        return false;
        
    }
};
```

</details>

1.7 【最长子序列】524. Longest Word in Dictionary through Deleting (Medium)

<details><summary>code</summary>

```c++
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        if(s.size() == 0 || d.size() == 0)
            return "";
        int maxlen=0;
        int len1 = s.size();
        int tmp=0;
        int idx=-1;
        sort(d.begin(),d.end());
        for(int i=0;i<d.size();i++){
            int len2 = d[i].size();
            if(len1 < len2)
                continue;
            tmp = isstr(s, d[i]);
            if(tmp == len2 && tmp > maxlen)
            {
                maxlen = tmp;
                idx = i;
             }
        }
        return idx== -1 ? "" : d[idx];
        
    }
    
    int  isstr(string s1, string s2){
        int i=0,j=0;
        while(i<s1.size() && j < s2.size()){
            if(s1[i] == s2[j])
                j++;
            i++;
        }
        return j;
    }
    
};
```

</details>


# 2. 排序

2.1 【Kth Element】 215. Kth Largest Element in an Array (Medium)

<details><summary>code</summary>

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        if(nums.size() == 0)
            return 0;
        
        priority_queue<int> pq;
        for(int n : nums){
            pq.push(n);
        }
        
        while( k>1)
        {
            pq.pop();
            k--;
        }
        
        return pq.top();
        
    }
};

```

</details>


2.2 【出现频率最多的 k 个数】 347. Top K Frequent Elements (Medium)

<details><summary>code</summary>

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int i = 0; i < nums.size(); i++)
            freq[nums[i]]++;
        
        int range_min = 0;
        int range_max = 0;
        for (auto it = freq.begin(); it != freq.end(); it++)
        {
            range_min = min(range_min, it->second);
            range_max = max(range_max, it->second);
        }
        
        vector<vector<int>> buckets(range_max - range_min + 1);
        for (auto it = freq.begin(); it != freq.end(); it++)
        {
            buckets[it->second - range_min].push_back(it->first);
        }
        
        vector<int> result;
        for (int i = int(buckets.size())-1; i >= 0; i--)
        {
            for (int j = 0; j < buckets[i].size(); j++)
            {
                result.push_back(buckets[i][j]);
                if (result.size() == k)
                    return result;
            }
        }
        
        return result;
    }  
        
};

```

</details>

2.3 【按照字符出现次数对字符串排序】451. Sort Characters By Frequency (Medium)

<details><summary>code</summary>

```c++
class Solution {
public:
    string frequencySort(string s) {
        int len = s.size();
        map<char, int> mapping;
        int maxn = 0;
        for(char c : s){
            mapping[c] ++;
            if(maxn < mapping[c])
                maxn = mapping[c];
        }

        vector<vector<char>> buckets(maxn+1);
        
        for(auto it = mapping.begin();it != mapping.end(); it++){  // 桶排序
            buckets[it->second].push_back(it->first);
        }
        
        string res;
        for(int i= maxn; i>=0; i-- ){
            for(char c : buckets[i]){
                for(int j = 0;j<i;j++){
                    res += c;
                }
            }        
        }
        
        return res;

    }
};

```

</details>


2.4 【荷兰国旗问题】75. Sort Colors (Medium)


<details><summary>code</summary>

> 荷兰国旗问题
> 荷兰国旗包含三种颜色：红、白、蓝。
> 有三种颜色的球，算法的目标是将这三种球按颜色顺序正确地排列。
> 它其实是三向切分快速排序的一种变种，在三向切分快速排序中，每次切分都将数组分成三个区间：小于切分> 元素、等于切分元素、大于切分元素，而该算法是将数组分成三个区间：等于红色、等于白色、等于蓝色。


```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if(nums.empty())
            return;
        int zero = 0, one = 0, two = nums.size()-1;
        while(one < two){
            if(nums[one] == 0){
                swap(nums[zero],nums[one]);

                zero++;
                one++;
            }
            else if(nums[one] == 2){
                swap(nums[one],nums[two]);

                two--;
            }
            else
                one++;
        }  

    }

};

```

</details>




# 3 贪心算法

- 保证每次操作都是局部最优的，并且最后得到的结果是全局最优的。

3.1 【分配饼干】455. Assign Cookies (Easy)


<details><summary>code</summary>

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        
        int i=0,j=0;
        while(i< g.size() && j < s.size()){
            if(g[i] <= s[j])
                i++;  // 满足一个孩子，下一个孩子
            j++;    // 下一块饼干
        }
        return i;
    }
};
```

</details>


3.2【不重叠的区间个数】435. Non-overlapping Intervals (Medium)


<details><summary>code</summary>

- 删除重复区间，保留最小的区间，即留下最多区间数

- 利用区间的结尾，排序
- 然后比较后一个区间的开头和当前区间的结尾，只要后面区间区间的开头在当前区间内，寻找到不在区间的内部的开头，
- 计算最多能组成的不重叠区间个数，然后用区间总个数减去不重叠区间的个数。



```c++
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    int eraseOverlapIntervals(vector<Interval>& num) {
        if(num.empty())
            return 0;
        
        sort(num.begin(), num.end() , comp);
        
        int len = num.size();
        int cnt = 1;
        int end = num[0].end;
        
        for(int i=1;i<len;i++)
        {
            if(num[i].start < end)
            {
                continue;
            }
            cnt++;
            end = num[i].end;
        }
        return len - cnt;
        
    }
    
static    bool comp(Interval a, Interval b){
        if(a.end < b.end)
            return true;
        
        return false;
    }
    
};
```

</details>

3.3 【投飞镖刺破气球】452. Minimum Number of Arrows to Burst Balloons (Medium)


<details><summary>code</summary>

- [452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

- 二维坐标，表示x轴上的位置起点和终点，但是，箭头只能垂直射入，最小能够射破气球的箭头数
- 计算不重叠的区域个数


```c++
class Solution {
public:
    int findMinArrowShots(vector<pair<int, int>>& points) {
        if(points.empty())
            return 0;
        
        sort(points.begin(), points.end(), comp);
        int len = points.size();
        int cnt =1;
        int end = points[0].second;
        
        for(int i=1;i<len;i++){
            if(points[i].first <= end){
                continue;
            }
            cnt++;
            end = points[i].second;
          //  cout<<"i:"<<i<<" "<<points[i].first<<endl;
        }
        return cnt;
        
        
        
    }
    
    
 static   bool comp(pair<int,int>& a,pair<int,int>&b ){
        if (a.second < b.second)
            return true;
        return false;
    }
    
};

```

</details>

3.4 【根据身高和序号重组队列】406. Queue Reconstruction by Height(Medium)


<details><summary>code</summary>


- [406. Queue Reconstruction by Height(Medium)](https://leetcode.com/problems/queue-reconstruction-by-height/description/)


> 为了在每次插入操作时不影响后续的操作，身高较高的学生应该先做插入操作，否则身高较小的学生原先正确插入第 k 个位置可能会变成第 k+1 个位置。

> 身高降序、k 值升序，然后按排好序的顺序插入队列的第 k 个位置中。


```c++
class Solution {
public:
    vector<pair<int, int>> reconstructQueue(vector<pair<int, int>>& people) {
        
        vector<pair<int,int>> res;
        if(people.empty())
            return res;
        
        sort(people.begin(),people.end(),comp);
          
        
        
        for(auto &p : people){
            res.insert(res.begin()+p.second, p);     // 首先插入高的人，在按排名插入

        }
        return res;
        
        
    }
    
    static bool comp (pair<int,int>&a, pair<int,int>&b){            // 先排列身高，然后排名
        if(a.first == b.first)
            return  a.second < b.second;
        else
            return a.first > b.first;
    }
};


```

</details>


3.5 【分隔字符串使同种字符出现在一起】763. Partition Labels (Medium)

<details><summary>code</summary>

- [763. Partition Labels (Medium)](https://leetcode.com/problems/partition-labels/description/)

- 贪婪：始终寻找最后一个下标

```c++

class Solution {
public:
    vector<int> partitionLabels(string S) {
        vector<int> res;
        int len  = S.size();
        if(len <=1)
        {
            res.push_back(len);
            return res;
        }
        
        res.resize(26);
        for(int i=0;i<len;i++){    // 寻找每个字母出现的最后下标
            res[S[i] - 'a'] = i;
        }
        
        int start = 0;
        int end = res[S[0] -'a'];
        
        vector<int> output;
        for(int i=0;i<len;i++){
            if(i == end){// 最后一次出现
                output.push_back(end - start + 1);  // 统计分割总数
                if(i+1 <=len-1)
                    end = res[S[i+1] - 'a'];   // 下一个字母的最后一个下标
                start = i+1;
            }
            else if(i == len -1){  // 最后一个字母
                output.push_back(end -start + 1);
            
            }else{   // 继续寻找
                if(end < res[S[i] - 'a'])  // 当前字母的下标更在后面
                    end = res[S[i] - 'a'];   // 更新尾节点
            }
        
        }
        return output;
     
    }
};

```

</details>


3.6 【种植花朵】605. Can Place Flowers (Easy)


<details><summary>code</summary>


- [605. Can Place Flowers (Easy)](https://leetcode.com/problems/can-place-flowers/description/)

- 花必须间隔种的问题，寻找能够种植最多的解

- 贪心思想：


```c++
class Solution {
public:
    bool canPlaceFlowers(vector<int>& bed, int n) {
        // 两朵花之间有空格
        int len = bed.size();
        int cnt = 0;
        // 记录前一个位置和后一个位置的情况
        int pre = 0, next = 0;
        
        for(int i=0;i<len&& cnt < len; ++i){
            // 当前未被占用
            if(bed[i] == 1)
                continue;
            
            if(i == 0){  // 第一个位置
                pre = 0;
            }else{
                pre = bed[i - 1];  // 前一个位置
            }
            
            if(i == len -1){  // 最后一个
                next = 0;
            }else{
                next = bed[i+1];
            }
            
            // 前后都为空
            if(pre == 0 && next == 0){
                ++ cnt;
                bed[i] = 1;
            }
        }
        
        return cnt >= n;   // 是否能够容下
    }
};



```

</details>

3.7 【判断是否为子序列】392. Is Subsequence (Medium)

<details><summary>code</summary>

- [392. Is Subsequence (Medium)](https://leetcode.com/problems/is-subsequence/description/)

- 不要求连续子串，每次比较一个字符即可

```c++

class Solution {
public:
    bool isSubsequence(string s, string t) {
        if(!s.empty() && t.empty())     /
            return false;       
        int lenA = s.size();
        int lenB= t.size();
        int i=0,j=0;
        
        while(i<lenA && j < lenB){
            if(s[i] == t[j])
            {
                ++i;
            }
            
            ++j;
        }
        
        if(i != lenA)
            return false;
        else 
            return true;
    }
};


```

</details>


3.8 【修改一个数成为非递减数组】665. Non-decreasing Array (Easy)


<details><summary>code</summary>

- [665. Non-decreasing Array (Easy)](https://leetcode.com/problems/non-decreasing-array/description/)

- 最多修改一次数字，那么比较当前数字和后两个数字


```c++

class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        int cnt = 0;
        int len = nums.size();
        for(int i=1;i<len && cnt < 2; ++i){
            if(nums[i] >= nums[i-1]){   // 升序
                continue;
            }
            ++cnt;
            if(i - 2 >= 0 && nums[i-2] > nums[i]){  // 前两个数大于当前数
                nums[i] = nums[i -1];       // 将当前数和前一个数改为相当
            }else{
                nums[i-1] = nums[i];       // 前一个数和当前数相等
            }
        }
        return cnt <=1;
    }
};

```

</details>

3.9 【股票的最大收益】122. Best Time to Buy and Sell Stock II (Easy)

<details><summary>code</summary>

- [122. Best Time to Buy and Sell Stock II (Easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

- 买入在卖出，在买入，卖出，同一天可以卖出买入？

```c++

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0 ;
        
        int p = 0;
        for(int i=1;i<prices.size(); ++i){
            if(prices[i] > prices[i -1])
                p += prices[i] - prices[i-1];
        }
        return p;
        
    }
};

```

</details>

3.10 【子数组最大的和】53. Maximum Subarray (Easy)

<details><summary>code</summary>

- [53. Maximum Subarray (Easy)](https://leetcode.com/problems/maximum-subarray/description/)

- 使用动态规划求解连续的最大和

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.empty())
            return 0;
        
        int pre = nums[0];
        int maxnum = pre;
        
        for(int i=1;i<nums.size(); ++i){
            pre = pre > 0 ? pre + nums[i] : nums[i];
            if(pre > maxnum)
                maxnum = pre;

        }
        return maxnum;        
    }
};


```

</details>



<details><summary>code</summary>

</details>


