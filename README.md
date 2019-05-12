# LEETCODE MEMO
-----

[5. Longest Palindromic Substring](#5-longest-palindromic-substring)

[11. Container With Most Water](#11-Container-With-Most-Water)

[15&16. 3Sum & 3SumClosest](#15-3Sum)

[23. Merge k Sorted Lists](#23-Merge-k-Sorted-Lists)

[39&40. Combination Sum](#39-Combination-Sum)

[41. First Missing Positive](#41-First-Missing-Positive)

[42. Trapping Rain Water](#42-Trapping-Rain-Water)

[45. Jump Game II](#45-Jump-Game-II)

[48. Matrix Rotation](#48-Matrix-Rotation)

[49. Group Anagrams](#49-Group-Anagrams)

[53. Maximum Subarray](#53-Maximum-Subarray)

[56. Merge Intervals](#56-Merge-Intervals)

[67. Add Binary](#67-Add-Binary)

[69. Sqrt(x)](#69-Sqrtx)

[71. Simplify Path](#71-Simplify-Path)

[75. Sort Colors](#75-Sort-Colors)

[96. Unique Binary Search Trees](#96-Unique-Binary-Search-Trees)

[347. Top K Frequent Elements](#347-Top-K-Frequent-Elements)

## Permutation & Combination & Rotation
[31. Next Permutation](#31-Next-Permutation)

[46. Permutations](#46-Permutations)

[60. Permutation Sequence](#60-Permutation-Sequence)

[77. Combinations](#77-Combinations)

[78. Subsets](#78-Subsets)


-----

## #5 Longest Palindromic Substring
Given a string，find the longest Palindromic substring

#### Thoughts：
expand from the center: find the char cluster first (cluster constructed by the same chars)，then expand to the left and the right.

#### Answer：
``` 
string longestPalindrome(string s) {
    if (s.empty()) return "";
    if (s.size() == 1) return s;
    int min_start = 0, max_len = 1;
    for (int i = 0; i < s.size();) {
      if (s.size() - i <= max_len / 2) // max_len found
        break;
      int j = i, k = i;
      while (k < s.size()-1 && s[k+1] == s[k]) ++k; // Skip duplicate characters.
      i = k+1;
      while (k < s.size()-1 && j > 0 && s[k + 1] == s[j - 1]) { ++k; --j; } // Expand.
      int new_len = k - j + 1;
      if (new_len > max_len) { min_start = j; max_len = new_len; }
    }
    return s.substr(min_start, max_len);
}
```
[Back to the top](#readme)

-----

## #11 Container With Most Water
![image](https://github.com//tywu0901/leetcode_memo/raw/master/images/question_11.jpg)

Given a vector of the heights of walls, find the maximum water that could be trapped

#### Thoughts：
Start from the left-most and the right-most walls, and then travese to the center, finding higher walls/ more trapped volunm.
#### Answer：
``` 
int maxArea(vector<int>& height) {
    int water = 0;
    int i = 0, j = height.size() - 1;
    while (i < j) {
        int h = min(height[i], height[j]);
        water = max(water, (j - i) * h);
        while (height[i] <= h && i < j) i++;
        while (height[j] <= h && i < j) j--;
    }
    return water;
}
```
[Back to the top](#readme)

-----

## #15 3Sum
给一个vector，找出所有符合a+b+c = 0 的组合

#### Thought:
先sort。

traverse这个sorted array. 每到一个位置时，取当前数，下一个数，和数组最后一个数（最大数）求和。如果大于零，向后移动指针；如果小于零，则向前移动指针；等于零则为要找的组合。

#### Answer：
```
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        std::sort(nums.begin(), nums.end());
        if (nums.size() < 3) return res;
        
        for (int i = 0; i < nums.size()-2; ++i) {
            if ((i > 0) && (nums[i-1] == nums[i])) continue;
            
            int l = i+1;
            int r = nums.size()-1;
            while (l < r) {
                if (nums[i] + nums[l] + nums[r] < 0) l++;
                else if (nums[i] + nums[l] + nums[r] > 0 ) r--;
                else {
                    res.push_back(vector<int>{nums[i], nums[l], nums[r]});
                    r--;
                    l++;
                    while ((nums[l-1] == nums[l]) && (l < r)) l++; //get rid of duplicates
                    while ((nums[r+1] == nums[r]) && (l < r)) r--; //get rid of duplicates
                            
                }
            }
            
        }
        return res;
    }
```

## #16 3Sum Closest
给一个vector和一个target value，找出 a+b+c 最接近target 的组合

#### Thought:
一样的思路，记录最接近的和即可

#### Answer：
```
int threeSumClosest(vector<int>& nums, int target) {
        if (nums.size() == 0) return 0;
        else if (nums.size() == 1) return nums[0];
        else if (nums.size() == 2) return nums[0] + nums[1];
        int res = nums[0] + nums[1] + nums[2];
        std::sort(nums.begin(), nums.end());
        
        for (int i = 0; i < nums.size()-2; ++i) {
            if ((i > 0) && (nums[i-1] == nums[i])) continue;
            
            int l = i+1;
            int r = nums.size()-1;
            while (l < r) {
                int curSum = nums[i] + nums[l] + nums[r];
                if (curSum == target) return target;
                if (std::abs(curSum - target) < std::abs(res - target)) res = curSum;
                if (curSum > target) {
                    r--;
                    while (nums[r+1] == nums[r] && (l < r)) r--;
                }
                else {
                    l++;
                    while (nums[l-1] == nums[l] && (l < r)) l++;
                }
            }
            
        }
        return res;
    }
```

[Back to the top](#readme)

-----

## #23 Merge k Sorted Lists

#### Thoughts：
设一个container（我用的是vector，应该可以用priority queue），把每个lists当前的element装起来，找到这个container里面的min，连接到新的list上。

#### Answer：
``` 
ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.size() == 0) return nullptr;
        vector<int> v(lists.size());
        ListNode* res = new ListNode(0);
        ListNode* cur = res;
        short flag = lists.size();
        for (unsigned int i = 0; i < lists.size(); ++i ) {
            if (lists[i] == nullptr) v[i] = MAX_VAL;
            else v[i] = lists[i]->val;
        }
        for (unsigned int j = 0; j < lists.size(); ++j ) {
            if (v[j] == MAX_VAL) --flag;
        }
        while (flag) {
            auto it = min_element(v.begin(), v.end());
            cur->next = lists[it - v.begin()];
            cur = cur->next;
            lists[it - v.begin()] = lists[it - v.begin()]->next;
            if (lists[it - v.begin()] == nullptr) {
                v[it - v.begin()] = MAX_VAL;
                --flag;
            }
            else {
                v[it - v.begin()] = lists[it - v.begin()]->val;
                
            }
            

        }
        
        return res->next;
    }
```
#### 其他思路
两个两个合并（divid and conquer）

[Back to the top](#readme)

-----

## #31 Next Permutation
给一个排列，求下一个排列

#### Thoughts：
From wiki：

1. Find the largest index k such that nums[k] < nums[k + 1]. If no such index exists, just reverse nums and done.
2. Find the largest index l > k such that nums[k] < nums[l].
3. Swap nums[k] and nums[l].
4. Reverse the sub-array nums[k + 1:].

#### Answer：
```
void nextPermutation(vector<int>& nums) {
        if (nums.empty() || nums.size() == 1) return;
        auto iter = nums.end() - 2;
        
        while(iter >= nums.begin()) {
            if (*iter < *(iter + 1)) break;
            iter --;
        }
        
        if (iter < nums.begin()) {
            reverse(nums.begin(), nums.end());
            return;
        }
        
        auto iter2 = nums.end() - 1;
        while(iter2 > iter) {
            if (*iter2 > *iter) break;
            iter2--;
        }
        // int temp = *iter;
        // *iter = *iter2;
        // *iter2 = temp;
        iter_swap(iter, iter2);
        reverse(iter+1, nums.end());
        
        
        
        
    }
```


[Back to the top](#readme)

-----

## #39 Combination Sum
给一个candidate set 和一个target，给出所有和为target的组合。每个数字可用多次

Input: candidates = [2,3,6,7], target = 7,

A solution set is:
[
  [7],
  [2,2,3]
]

#### Thought：
Backtracking

#### Answer:
```
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
       //use backtracking
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> temp;
        bt(candidates, target, res, temp, 0);
        return res;
    }
    
    void bt(vector<int>& candidates, int target, vector<vector<int>>& res,vector<int>& temp, int pos) {
        if(target == 0) {
            //valid result
            res.push_back(temp);
            return;
        }
        
        if(pos >= candidates.size() || target < candidates[pos]) {
            //bounding: not valid result
            return;
        }
        
        for(int i = pos; i < candidates.size(); ++i) {
            temp.push_back(candidates[i]);
            bt(candidates, target - candidates[i], res, temp, i);
            temp.pop_back();
        }
    }
```

#### 其他思路：
Dynamic Programming

## #40 Combination Sum II
每个数字只能用一次，求unique combinations。

#### Thoughts
和39题一样，只要跳过duplicates就好了

#### Answer：
```
for(int i = pos; i < candidates.size();++i) {
            if(i&&candidates[i]==candidates[i-1] &&i>pos) continue;
            temp.push_back(candidates[i]);
            bt(candidates, target - candidates[i], res, temp, i + 1);
            temp.pop_back();
        }
```

注意： i > pos 是为了防止特殊情况：如果开头第一个数（位于pos）和前一个数（pos-1）是一样的时候，不视为dup

[Back to the top](#readme)

-----

## #41 First Missing Positive
给一个unsorted array，求第一个没出现的正整数

EX：[-1，1，2，-4，8，4]-> 3

要求：O(n) time, O(1) space

#### Thought
traverse这个array，如果一个数是正整数，并且在array的size范围内，把这个数放到对应的位置上。

traverse第二遍，第一个位置和所处的数不对应的，就是最小的没出现的正整数。

#### Answer:
```
int firstMissingPositive(vector<int>& nums) {
        if (nums.empty()) return 1;
        if (nums.size() == 1) return (nums[0] == 1)? 2 : 1;
        for (int i = 0; i < nums.size(); ++i ) {
            if (nums[i] > 0  && nums[i] <= nums.size() && nums[i] != i+1 && nums[nums[i]-1] != nums[i]) {
            //nums[nums[i]-1] != nums[i]为了防止无限循环
                swap(nums[nums[i]-1], nums[i]);
                i--;
            } 
                
        }
        int i = 0;
        while (i < nums.size()) {
            if (nums[i] != i+1) return i+1;
            i++;
        }
        return i+1;
        
    }
```


[Back to the top](#readme)

-----


## #42 Trapping Rain Water
给一个vector，记录的是墙的高度，求能困住的水的体积（可以对比#11）
![image](https://github.com//tywu0901/leetcode_memo/raw/master/images/question_42.png)

#### Thoughts
1. Brute Force: 每一格去找左边的最高和右边的最高，再取min（left_max, right_max），这一格水的高度就是min（left_max, right_max)- height[i].
2. Dynamic Programming:因为每次要找left_max和right_max是重复的，可以把找到的记下来。
3. 2-pointers：在一个iteration里解决问题：
    * Initialize left pointer to 0 and right pointer to size-1
    * While left<right, do:
        * If height[left] is smaller than height[right]
            * If height[left] ≥ left_max, update left_max
            * Else add left_max−height[left] to ans
            * Add 1 to left (check next left).
        * Else
            * If height[right]≥right_max, update right_max
            * Else add right_max−height[right] to ans
            * Subtract 1 from right (check next right).
            
   注： 因为update的都是较小的那一边，所以不用再比较min（left_max, right_max）
   
#### Answer:
```
int trap(vector<int>& height) {
        if (height.size() < 3) return 0;
        
        int l = 0, r = height.size() - 1, res = 0;
        int lm = height[l], rm = height[r];
        
        while(l < r) {
            int cl = height[l], cr = height[r];
            if (cl <  cr) {
                if (cl < lm) res += (lm-cl);
                else lm = cl;
                l++;
            }
            
            else {
                if (cr < rm) res += (rm-cr);
                else rm = cr;
                r--;
            }
        }
        return res;
    }
```

[Back to the top](#readme)

-----

## #45 Jump Game II
给一个正整数vector，每一个数代表能向前跳的距离，求从index 0 跳到最后一个index的最小步数

#### Thoughts：
BFS：把整个vector分成几个level（一步之类能到的index是level 1，两步之内是level 2），一旦最后一个index出现在level里，return这个level

### Answer：
```
int jump(vector<int>& nums) {
         int n = nums.size();
	     if(n<2)return 0;
         
	     int level=0,start = 0, end = 0;

	     while(true){
		     level++;
             int maxend = end + 1;
		     for(;start<=end;start++){	
			     maxend =max(maxend,nums[start]+start);
			     if(maxend>=n-1)return level;   
		     }
		     end=maxend;
	     }
	     return 0; //never reach here actually
     }
```

[Back to the top](#readme)

-----


## #46 Permutations
给一个不重复的数组，求排列

#### Thoughts：
Backtracking：每一个recursive call都排好一位，push_back之后再还原

#### Answer：
```
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        bt(nums, res, 0);
        
        return res;
    }
    
    void bt(vector<int>& nums, vector<vector<int>>& res, int pos) {
        if (pos >= nums.size()) {
            res.push_back(nums);
            return;
        }
        
        for (int i = pos; i < nums.size(); ++i) {
            swap(nums[pos], nums[i]);
            bt(nums, res, pos+1);
            swap(nums[pos], nums[i]);
        }
    }
```
[Back to the top](#readme)

-----


## #48 Matrix Rotation
把一个matrix顺时针转90度

#### Thoughts：
Credit to leetcode user @shichaotan:

![image](https://github.com//tywu0901/leetcode_memo/raw/master/images/question_48_1.png)

![image](https://github.com//tywu0901/leetcode_memo/raw/master/images/question_48_2.png)

先reverse，然后做transpose。

#### Answer：
```
    void rotate(vector<vector<int>>& matrix) {
        reverse(matrix.begin(), matrix.end());
        for (int i = 0; i < matrix.size(); ++i) {
            for (int j = i + 1; j < matrix[i].size(); ++j)
                swap(matrix[i][j], matrix[j][i]);
        }
    }
```


[Back to the top](#readme)

-----


## #49 Group Anagrams
给一个string的vector，输出里把相同字母出现相同字数的string归到一起。

#### Thought：
数字母，用字母个数作为key在一个map里存储string

#### Answer：
```
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> m;
        for (string s : strs) {
            string stamp = charCount(s);
            m[stamp].push_back(s);
        }
        
        vector<vector<string>> res;
        for (auto p : m) {
            res.push_back(p.second);
        }
        return res;
    }
    
    string charCount(string s) {
        int counter[26] = {0};
        string res = "";
        for (int i = 0; i < s.size(); ++i) {
            counter[s[i] - 'a'] += 1;
        }
        
        for (int j = 0; j < 26; ++j) {
            if (counter[j] != 0) res+= string(counter[j], j + 'a');
        }
        return res;
    }
```

[Back to the top](#readme)

-----

## #53 Maximum Subarray
给一个vector，找出和最大的subarray。

#### Thoughts:
traverse这个array，一旦前面的和小于零即舍去不要。

#### Answer：
```
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0, res = min_val;
        
        for (int i : nums) {
            sum += i;
            res = (res<sum)? sum : res;
            sum = (sum >=0)? sum : 0;
        }
        return res;
    }
    
private:
    int min_val = -2147483647;
    //这里把res设成最小值是为了整个vector都是负数的情况
```

#### Better Answer：
Credit to Leetcode user @sumedhds

```
int maxSubArray(vector& nums) {
	int reset=0;
	int maxsum=0;
	for(int i=0; i<nums.size(); i++){
		reset += nums[i];
		reset = max(reset, nums[i]); //这里先比较，如果全是负数，那么最小的负数就会被留下来
		maxsum = max(maxsum, reset);
	}
	return maxsum;
}
```



[Back to the top](#readme)

-----


## #56 Merge Intervals
给一个vecter里面都是interval，把能合并的合并了
> Input: [[1,3],[2,6],[8,10],[15,18]]
>
> Output: [[1,6],[8,10],[15,18]]
>
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].

#### Thoughts：
这题不难，但是回顾一下lambda expression：
> lambda 表达式
>
>[capture list] (params list) mutable exception-> return type { function body }

```
bool cmp(int a, int b)
{
    return  a < b;
}
int main{
    vector<int> myvec{ 3, 2, 5, 7, 3, 2 };
    vector<int> lbvec(myvec);

    sort(myvec.begin(), myvec.end(), cmp); // 旧式做法
    sort(lbvec.begin(), lbvec.end(), [](int a, int b) -> bool { return a < b; });   // Lambda表达式
}

```

#### Answer:
```
vector<vector<int>> merge(vector<vector<int>>& intervals) {
            if (ins.empty()) return vector<Interval>{};
    vector<Interval> res;
    sort(ins.begin(), ins.end(), [](Interval a, Interval b){return a.start < b.start;});
    res.push_back(ins[0]);
    for (int i = 1; i < ins.size(); i++) {
        if (res.back().end < ins[i].start) res.push_back(ins[i]);
        else
            res.back().end = max(res.back().end, ins[i].end);
    }
    return res;

    }
    
```

#### 其他思路：
BFS

[Back to the top](#readme)

-----

## #60 Permutation Sequence
给两个数，n是可用的正整数（n=3 即可用 1，2，3）  k是要给出的第几个组合。

>Input: [1,2,3]
>
>Output:
>
>[
>
>  [1,2,3],
>
>  [1,3,2],
>  
>  [2,1,3],
>  
>  [2,3,1],
>
>  [3,1,2],
>
>  [3,2,1]
>
>]
#### Thoughts:
一个一个排：第i个位置的数为 `i + k/(n-1)!`

#### Answer:
```
string getPermutation(int n, int k) {
        int i,j,f=1;
        string s(n,'0');
        
        //init
        for(i=1;i<=n;i++){
            f*=i; // f = n!
            s[i-1]+=i; // make s become 1234...n
        }
        
        for(i=0,k--;i<n;i++){
            f/=n-i; // how many perms with n-i-1 numbers
            j=i+k/f; // calculate index of char to put at s[i]
            char c=s[j];
            
            s.erase(s.begin() + j); //get rid of the char we want
            s.insert(s.begin() + i, c); // put it at the right place
            k%=f; //finish arraging one char, move to the next
        }
        return s;        
    }
```
[Back to the top](#readme)

-----

## #67 Add Binary
俩string代表俩binary number，输出他们的和（同样是string of binary number）

#### 单纯的欣赏环节：
Credit to Leetcode User @makuiyu :

```
string addBinary(string a, string b)
    {
        string s = "";
        
        int c = 0, i = a.size() - 1, j = b.size() - 1;
        while(i >= 0 || j >= 0 || c == 1)
        {
            c += i >= 0 ? a[i --] - '0' : 0;
            c += j >= 0 ? b[j --] - '0' : 0;
            s = char(c % 2 + '0') + s;
            c /= 2;
        }
        
        return s;
    }
```

[Back to the top](#readme)

-----


## #69 Sqrt(x)
求x的开方（最接近的整数）

#### 数学小课堂：
[Newton's Method](https://en.wikipedia.org/?title=Newton%27s_method)是一种求根的近似值的迭代方法。

要求x的开方，相当于是解`f(a) = a^2 - x `的根。

我们用x作为近似值，根据Newton's Law: `a_(n+1) = a_n - (f(a_n)/f'(a_n))`, 带入f得`a_(n+1) = a_n - (a_n^2 - x)/2a_n = (a_n^2 - x)/2a_n = (a_n - x/a_n)/2`

所以每一次迭代，`r = (r + x/r) / 2`

#### Answer:
```
int mySqrt(int x) {
        long r = x;
        while (r*r > x)
            r = (r + x/r) / 2;
        return r;
    }
```

[Back to the top](#readme)

-----

## #71 Simplify Path
给一个代表路径的string，输出最简路径。

>Input: "/home/"
>
>Output: "/home"

>Input: "/a/b/../"
>
>Output: "/a"
>
>..表示上一层

>Input: "/a/b/./"
>
>Output: "/a/b"
>
>.表示当前层

#### Thought：
把string变成stringstream，以‘/’断开，再逐个判断。

用vecotr装被split出来的string，如果要返回上一层，就pop_back

#### Answer：
```
    string simplifyPath(string path) {
        vector<string> s;
        string tmp;
        stringstream ss(path);
        while(getline(ss,tmp,'/')) {
            if (tmp == "" or tmp == "."/* case // and case /./ */) continue;
            if (tmp == ".." and !s.empty() /* case /../ */) s.pop_back();
            else if (tmp != "..") s.push_back(tmp);
        }
        string res;
        for(auto str : s) res += "/"+str;
        return res.empty() ? "/" : res;
    }
```

[Back to the top](#readme)

-----

## #75 Sort Colors
一个vector里只有0，1，2三种数。Sort这个array，把相同的数整理到一起。（输出的vector因该是[0,0,0...1,1,1.....2,2,2]）

#### Thought:
标记0和2的位置：pos0 = 0， pos2 = size() - 1

如果是0就和pos0 swap，并且++pos0；同理如果是2就和pos2 swap，--pos2.

最后1自然被留在了中间。

#### Answer：
```
    void sortColors(vector<int>& nums) {
        if (nums.empty() || nums.size() == 1) return;
        int zeroP = 0, twoP = nums.size() - 1;
	
	// I was considering about the dups, but it actually is not necessary
        //while (zeroP < nums.size() && nums[zeroP] == 0) zeroP++;
        //while (twoP >= 0 && nums[twoP] == 2) twoP--;
        //if (zeroP > twoP) return;
	
        for (int i = zeroP; i <= twoP; ++i) {
            if (nums[i] == 0 && i != zeroP) swap(nums[i--], nums[zeroP++]);
            else if (nums[i] == 2 && i!=twoP) swap(nums[i--], nums[twoP--]);
        }
    }
```

#### Another Solution:
Credit to LeetCode User @shichaotan:

标记0，1，2三个位置：pos0 = -1， pos1 = -1， pos2 = -1.

每check一个数，如果是0，三个pos都加；如果是1，加pos1和pos2；如果是2，只加pos2.

```
void sortColors(int A[], int n) {
    int n0 = -1, n1 = -1, n2 = -1;
    for (int i = 0; i < n; ++i) {
        if (A[i] == 0) 
        {
            A[++n2] = 2; A[++n1] = 1; A[++n0] = 0;
        }
        else if (A[i] == 1) 
        {
            A[++n2] = 2; A[++n1] = 1;
        }
        else if (A[i] == 2) 
        {
            A[++n2] = 2;
        }
    }
}
```

[Back to the top](#readme)

-----

## #77 Combinations
n:可用的数 （n=4 即 1，2，3，4）

k: 生成k个数的组合

#### Thoughs：
思路和perm一样，用backtracking

#### Answer：
```
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> res;
        vector<int> temp(k, 0);
        if(k == 0) {
            res.push_back(temp);
            return res;
        }
        
        
        bt(n, k, res, temp, 1, 0);
        
        return res;
    }
    
    void bt(int n, int k, vector<vector<int>>& res, vector<int>& temp, int start, int len) {
        if(len == k) {
            res.push_back(temp);
            return;
        }
        
        for (int i = start; i <= n; ++i) {
            temp[len++] = i;
            bt(n, k, res, temp, i+1, len);
            len--;
        }
    }
    
//     void bt(int n, int k, vector<vector<int>>& res, vector<int>& temp, int start) {
//         if(temp.size() == k) {
//             res.push_back(temp);
//             return;
//         }
        
//         for (int i = start; i <= n; ++i) {
//             temp.push_back(i);
//             bt(n, k, res, temp, i+1);
//             temp.pop_back();
//         }
//     }
```

[Back to the top](#readme)

-----

## #78 Subsets
给一个数组，输出所有的subset

#### First Try：
和perm的思路一样，只不过不管subset的size有多少都push_back到final res里

#### Code：
```
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        if (nums.empty()) return res;
        vector<int> temp;
        
        bt(nums, temp, res, 0);
        
        return res;

    }
    
    void bt(vector<int>& nums,  vector<int>& temp, vector<vector<int>>& res, int start) {
        res.push_back(temp);
        
        for (int i = start; i < nums.size(); ++i) {
            temp.push_back(nums[i]);
            bt(nums, temp, res, i+1);
            temp.pop_back();
        }
    }
```

### Better Solution:
Credit to LeetCode User @jianchao-li

首先，给的数组长度是n，那么就有2^n个subset

根据观察，1出现一次隔开一个，2连续出现两次然后空开两个，以此类推

>[], [ ], [ ], [    ], [ ], [    ], [    ], [       ]
>
>[], [1], [ ], [1   ], [ ], [1   ], [    ], [1      ]
>
>[], [1], [2], [1, 2], [ ], [1   ], [2   ], [1, 2   ]
>
>[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]

#### Code:
```
    vector<vector<int>> subsets(vector<int>& nums) {
        int n = nums.size(), p = 1 << n // size of subset;
        vector<vector<int>> subs(p, []) //result;
        for (int i = 0; i < p; i++) {
            for (int j = 0; j < n; j++) {
                if ((i >> j) & 1) {
                    subs[i].push_back(nums[j]);
                }
            }
        }
        return subs;
    }
```
注：这种方法在有dups的时候无效，因为无法确定subset有多少个

[Back to the top](#readme)

-----

## #96 Unique Binary Search Trees
给一个数n, 求用1，2.... n这n个数能组成多少个不同的binary search tree

#### Thoughts：
Credit to LeetCode User @leo_mao:
 >      n个数分别做root：
 >      1 as root: # of trees = F(0) * F(n-1)  // F(0) == 1
 >      2 as root: # of trees = F(1) * F(n-2) 
 >      3 as root: # of trees = F(2) * F(n-3)
 >      ...
 >      n-1 as root: # of trees = F(n-2) * F(1)
 >      n as root:   # of trees = F(n-1) * F(0) 
 
 所以循环的时候，memo[n-1]代表有n-1个数的时候有多少个bst；到n时，循环一遍memo，F(n) = F(0) * F(n-1) + F(1) * F(n-2) + F(2) * F(n-3) + ... + F(n-2) * F(1) + F(n-1) * F(0)
 
 #### Code:
 ```
     int numTrees(int n) {
        vector<int> dp(n+1, 0);
        
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; ++i) {
            for (int j = 1; j <=i; ++j) dp[i] += dp[j-1]*dp[i-j];
        }
        return dp[n];
    }
 ```
 
[Back to the top](#readme)

-----

## #347 Top K Frequent Elements

#### Thoughts:
次数就想到map，第几大就想到priority queue。

先用一个map统计每个字符串出现的次数，然后把字符串往pq（小顶堆）里放，同时控制pq的大小。

所以pq的top就是第k多出现的次数。那么只要字符串出现的字数大于top，就是我们要找的数。

#### Code:
```
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> counts;
        priority_queue<int, vector<int>, greater<int>> max_k;
        for(auto i : nums) ++counts[i];
        for(auto & i : counts) {
            max_k.push(i.second);
            while(max_k.size() > k) max_k.pop();
        }
        vector<int> res;
        for(auto & i : counts) {
            if(i.second >= max_k.top()) res.push_back(i.first);
        }
        return res;
    }
```

[Back to the top](#readme)

-----

