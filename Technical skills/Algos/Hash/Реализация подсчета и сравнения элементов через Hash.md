
```python

class Solution:
    def isAnagram(self, s: str, t: str) :
        if len(s) != len(t) : 
            return False 
        
        hsh_table1 , hsh_table2 = {} , {}

        for i in range(len(s)) : 
            hsh_table1[s[i]] = hsh_table1.get(s[i] , 0) + 1
            hsh_table2[t[i]] = hsh_table2.get(t[i] , 0) + 1

        for i in s : 
            if hsh_table1[i] != hsh_table2.get(i, 0) : 
                return False 
        return True 

```


https://leetcode.com/problems/valid-anagram/





```python

class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hash_set = {}
        for i in range(len(nums)) : 
            hash_set[nums[i]] = i 

        for i in range(len(nums)) : 
            dif = target - nums[i] 
            if dif in hash_set and hash_set[dif] != i : 
                return (i , hash_set[dif])

```

https://leetcode.com/problems/two-sum/

