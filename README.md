# leetcode memo
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


