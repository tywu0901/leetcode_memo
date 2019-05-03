# leetcode memo
-----
### #5 Longest Palindromic Substring
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
### #11 Container With Most Water
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
### #23 Merge k Sorted Lists

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

