## Map取value进数组
values方法返回的是一个`MapIterator<V>`,可以使用Array.from(map.values())装进一个数组里
## 声明定长数组
`const count: number[] = new Array(26).fill(0)`
## 获取字符ascii码
可以使用charCodeAt(index)来获取字符串中第index个字符的ascii码
## Map.groupBy
>`Map.groupBy(iterable, callbackFn)`

这个方法用于根据指定的分组函数将数组元素分组到一个 Map 对象中
参数说明
1. iterable : 可迭代对象（如数组），其元素将被分组
2. callbackFn : 分组函数，接收以下参数：
   - element : 当前处理的元素
   - index : 元素的索引（可选）
   - 返回一个值作为分组的键
工作原理
- 遍历可迭代对象中的每个元素
- 对每个元素调用分组函数，获取分组键
- 将具有相同键的元素收集到一个数组中
- 最终返回一个 Map，其中键是分组函数的返回值，值是包含相应元素的数组
## 数组排序去重
```ts
  nums.sort((a, b) => a - b)
  nums = nums.filter((item, index) => {
    return index === 0 || item !== nums[index - 1]
  })
  let ans = 1
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] <= 0) continue
    if (nums[i] === ans) {
      ans++
    } else {
      break
    }
  }
  return ans
```