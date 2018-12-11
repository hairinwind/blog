
## semicolon to combine two lines code into one
```
samp_size = 100; print(samp_size)
```

## integer division
```
5//3
```
result is 1  
In Python 3, they made the / operator do a floating-point division, and added the // operator to do integer division

## get module from class
o is an instance of one class
```
o.__class__.__module__
```
If you know the Class, 
```
Class.__module__
```
## find the module file location
```
import fastai.column_data
fastai.column_data.__file__
```
output is '/home/yao/fastai/courses/dl1/fastai/column_data.py'

## python dict
when print a dict, it looks like this 
```
{'food': 'pudding', 'quantity': 4, 'color': 'pink'}
```
To instance a dict
```
D = {'food': 'pudding', 'quantity': 4, 'color': 'pink'}
```
To fetch value
```
D['food']
```
https://bdhacker.wordpress.com/2010/02/27/python-tutorial-dictionaries-key-value-pair-maps-basics/  

## numpy sub-array 
for example, a is a 2 dimension array.
```
a[x:y, c]
```
x is the start row index  
y is the end row index (exclusive) 
c is the column index
so a[1:3, 1]  
This takes column 1 (the second column) from row 1 (second row as the first row index is 0) and row 2, 1 <= rowInex < 3.

other examples. They are just view, not clone.
```
a_view1 = a[1:2, 3:6]    # 切片 slice
a_view2 = a[:100]        # 同上
a_view3 = a[::2]         # 跳步
a_view3 = a[:, ::2]      # columns 跳步
```
Below, they are "copy"
```
a_copy1 = a[[1,4,6], [2,4,6]]   # 用 index 选
a_copy2 = a[[True, True], [False, True]]  # 用 mask
a_copy3 = a[[1,2], :]        # 虽然 1,2 的确连在一起了, 但是他们确实是 copy
a_copy4 = a[a[1,:] != 0, :]  # fancy indexing
a_copy5 = a[np.isnan(a), :]  # fancy indexing
```

https://zhuanlan.zhihu.com/p/28626431  

## numpy determine if tow arrays share the memory
```
a = np.arange(50)
b = a.reshape((5, 10))
print (b.base is a)
```
https://blog.csdn.net/Jfuck/article/details/9464959  

## numpy ravel is faster tha flatten
ravel is just view change while flatten is doing a copy.

## numpy reshape, change the array dimension
```
np.reshape(a, (2, 5))
or 
aa = np.reshape(a, (-1, 2,5))  //-1 similar to pytorch tensor.view
```
https://docs.scipy.org/doc/numpy-1.15.1/reference/generated/numpy.reshape.html  

## numpy random number array numpy.random.permutation
Randomly permute a sequence, or return a permuted range.
```
>>> np.random.permutation(10)
array([1, 7, 4, 3, 0, 9, 2, 5, 8, 6])
```
产生一个0-9的随机顺序的数组

## isinstance
```
isinstance(output_size, (int, tuple))
```
This returns True when the type of output_size is either int or tuple.

## numpy array calculation
```
a = np.ones((2, 2))
a[0] = [1,2]
a[1] = [3,4]
b=a-[1,2]
print('a', a)
print('b', b)
```
a is [[1,2],[3,4]]  
b is [[0,0],[2,2]]  
it is a[0]-[1,2] = [0,0]  
and a[1]-[1,2] = [2,2]  

## numpy matrix transpose
```
a = np.array([[1, 2, 3], [4, 5, 6]], np.int32)
a.T 
```
the output is 
```
array([[1, 4],
       [2, 5],
       [3, 6]], dtype=int32)
```

## array concat
```
x=[512]
a = x + [1]
```
now, a is [512, 1]

## python callable class
A callable object can be a function or a class that implements __call__ method.
https://docs.python.org/2/reference/datamodel.html#object.__call__

```
class myClass():
    def __init__(self, n_users, n_movies):
        super().__init__()
        print('init...')
        
    def __call__(self, x):
        print('__call__', x)
        
u = myClass(1,2)
```
u is a instance of myClass. 
```
u('123')
```
If the `__call__` function is implemented, the instance is callable. the code above outputs
```
__call__ 123
``` 
If the class is not callable and you try to call it, you will get the error 'object is not callable'.

## dataframe pivot and stack, unstack
https://nikgrozev.com/2015/07/01/reshaping-in-pandas-pivot-pivot-table-stack-and-unstack-explained-with-pictures/

## dataframe loc 
```
googletrend.loc[googletrend.State=='NI', "State"] = 'HB,NI'
```
find all rows which googletrend.State=='NI', then assign the value 'HB,NI' to the column "State" on the rows matched

## dataframe T 
```
df.T
```
It is like Matrix transpose. 矩阵转置

## sub-dataframe by column name
```
df[['columnA', 'columnB']]
``` 

## reverse string 
```
'hello world'[::-1]
```
output is 'dlrow olleh'  
This is extended slice syntax. It works by doing [begin:end:step] - by leaving begin and end off and specifying a step of -1, it reverses a string.
https://docs.python.org/2/whatsnew/2.3.html#extended-slices



