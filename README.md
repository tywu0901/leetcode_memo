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
