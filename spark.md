## RDD操作

###  转化操作

- 读取日志数据
```

lines=sc.textFile("path/to/log.txt")

##filter 操作
mvc=lines.filter(lambda x:"MVC" in x)
spring=lines.filter(lambda x:'Spring' in x)
## 两个数据集合并
data=mvc.union(spring)
```

### 行动操作
- count() 来返回计算的结果
```
spring.count()
mvcs.count()
```
- take() 获取数据
```
#打印出10个数据
for val in data.take(10):
     print val

```
- collect() 获取数据，不能用于数据量比较大的情况


#### 惰性操作
RDD的转化操作都是惰性求值，这意味着在被调用行动操作之前Spark  不会进行操作。可以把RDD当作我们通过转化操作构建出来的、记录如何计算数据的指令列表。

```
这样子可以把一些操作合并到一些来减少计算数据的步骤。
```

### 向spark传递函数

```
### 这样子操作是有问题的，但不会报错，这个正式前面所说的惰性槽
def filterWord(word,line):
    return word in line
    
mvc=lines.filter(filterWord)
#只有当执行mvc.take(10)的时候才会报错
##    正确模式
def filterWord(word):
     return 'mvc' in word
mvc=lines.filter(filterWord)

``` 

- flapmap对于每个输入的元素生成多个输出
- map 对于每个输入的元素生成单个输出
- distinct 转化操作生成一个只包含不同元素的新RDD，distinct操作开销很大，需要通过网络把所有数据进行shuffle，以确保每个元素都只有一份
- union 对数据集进行合并，会包含两个RDD同事包含的数据
- intersection() 返回两个RDD都有的元素。intersection需要通过网络来进行shffle
- subtract(other)跟intersection 功能差不多，也需要进行数据shuffle
- cartesian(other)求笛卡尔积，大规模数据开销大


## 行动操作
- reduce
```
sum=rdd.reduce(lambda x,y:x+y)
```
- fold() 跟reduce 类型
-collect 一般只在单元测试使用
- take(n) 返回rdd中的n个元素，并且尝试只访问尽量少的分区
- top() 
