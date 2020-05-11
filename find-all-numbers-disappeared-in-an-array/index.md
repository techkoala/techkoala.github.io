# 448. Find All Numbers Disappeared in an Array


```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        for n in nums:
            a = abs(n) - 1 #取得元素本应该出现的下标（位置）
            if nums[a] > 0: 
                nums[a] *= -1 #（对出现了的元素对应位置的元素做标记）
        return [i+1 for i in range(len(nums)) if nums[i] > 0]
```

对于这个问题需要注意的是条件给出了数列中的元素应该按照1<x<n出现

因此，解法中遍历每个元素，通过一个参数表示他们本应该出现的位置，当他们出现，那么对此位置的元素做标记（变为相反数），遍历完后，没有出现被标记的元素就是本应该出现但是没有出现元素的位置，因此，位置+1即为没出现的元素。

```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        N = set(nums)
        return [i for i in range(1, len(nums) + 1) if i not in N]
```

python中更简单的方法：直接使用set除去重复元素，然后遍历输出不存在的元素。但是消耗较多空间


