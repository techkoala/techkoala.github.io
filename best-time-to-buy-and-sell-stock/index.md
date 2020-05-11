# 121.Best Time to Buy and Sell Stock


此题知识点： `float('inf')`

It acts as an unbounded upper value for comparison. This is useful for finding lowest values for something. for example, calculating path route costs when traversing trees.

e.g. Finding the "cheapest" path in a list of options:

```python
>>> lowest_path_cost = float('inf')
>>> # pretend that these were calculated using some worthwhile algorithm
>>> path_costs = [1, 100, 2000000000000, 50]
>>> for path in path_costs:
...   if path < lowest_path_cost:
...     lowest_path_cost = path
...
>>> lowest_path_cost
1
```

if you didn't have `float('Inf') ` available to you, what value would you use for `the initial lowest_path_cost`? Would `9999999` be enough-`float('Inf')` removes this guesswork.

