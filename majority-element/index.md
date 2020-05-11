# 169.Majority Element


# 暴力解法：

利用字典存储来遍历字典的方法，可以做，但是时间效率太低

## 遍历字典

```python
d = {'x': 1, 'y': 2, 'z': 3}
```

### 遍历keys

```python
for key in d: 
	print key

y x z

for key in d.iterkeys(): # d.iterkeys(): an iterator over the keys of d
	print key

`y x z`

for key in d.keys(): # d.keys() -> ['y', 'x', 'z'] 
	print key

`y x z`
```

### 遍历values

```python
for value in d.itervalues(): # d.itervalues: an iterator over the values of d 
	print value

`2 1 3`

for value in d.values(): # d.values() -> [2, 1, 3]
	print value

`2 1 3`
```

### 遍历keys和values

```python
for key, value in d.iteritems(): # d.iteritems: an iterator over the (key, value) items 
	print key,'corresponds to',d[key]

y corresponds to 2
x corresponds to 1
z corresponds to 3

for key, value in d.items(): # d.items(): list of d's (key, value) pairs, as 2-tuples # [('y', 2), ('x', 1), ('z', 3)] 
	print key,'corresponds to',value

y corresponds to 2
x corresponds to 1
z corresponds to 3
```

# 投票法：（前提是大于一半的要求）

[MisterBooo/LeetCodeAnimation](https://github.com/MisterBooo/LeetCodeAnimation/blob/master/notes/LeetCode第169号问题：求众数.md)
