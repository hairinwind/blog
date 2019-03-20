
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

## merge two dict
```
>>> x = {'a': 1, 'b': 2}
>>> y = {'b': 3, 'c': 4}

>>> z = {**x, **y}

>>> z
{'c': 4, 'a': 1, 'b': 3}
```

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

## substring is similar to sub-array
https://guide.freecodecamp.org/python/is-there-a-way-to-substring-a-string-in-python/

string[start:end]: Get all characters from index start to end-1

string[:end]: Get all characters from the beginning of the string to end-1

string[start:]: Get all characters from index start to the end of the string

string[start:end:step]: Get all characters from start to end-1 discounting every step character


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

## which directory are the third party packages installed
There are a few different locations where third party packages can be installed on your system. 
```
>>> import sys
>>> sys.prefix
```
The third party packages installed using easy_install or pip are typically placed in one of the directories pointed to by site.getsitepackages:
```
>>> import site
>>> site.getsitepackages()
```
https://realpython.com/python-virtual-environments-a-primer/

## create virtual environment
```
python -m venv TARGET_FOLDER_NAME
```
Or 
```
py -3 -m venv TARGET_FOLDER_NAME
```
https://realpython.com/python-virtual-environments-a-primer/

run this command to activate the virtualenv
```
bin/activate
```
Here is the article talking about the steps of recover the virtual env on another developer's machine 
https://developer.akamai.com/blog/2017/06/21/how-building-virtual-python-environment 

## pipenv to manage virtual environment and dependencies
pipenv can manage virtual environment and dependencies, so you don't need create virtual env by yourself. 
https://packaging.python.org/tutorials/managing-dependencies/
Here are the steps to make the project 
```
python -m pip install --user pipenv
cd myproject
python -m pipenv install lxml
```
pipfile and pipfile.lock are created  
Now, create main.py to import lxml
```
from lxml import etree

root = etree.Element("root")
print(root.tag)

root.append( etree.Element("child1") )
child2 = etree.SubElement(root, "child2")
child3 = etree.SubElement(root, "child3")
print(etree.tostring(root, pretty_print=True))
```
Here is the command to run the main.py
```
python -m pipenv run python main.py
```
Check in the files to git, when you check it out in another place, run 
```
python -m pipenv install
```
It creates a new virtual env and install all dependencies. Then run 
```
python -m pipenv run python main.py
```
Here is the command options of pipenv. https://pipenv.readthedocs.io/en/latest/#cmdoption-pipenv-rm  
The virtual env will be saved in C:\Users\yao.dong\.virtualenvs. To remove one virtual environment created by pipenv 
```
cd myproject
python -m pipenv --rm
```

## return first item or None
https://stackoverflow.com/questions/363944/python-idiom-to-return-first-item-or-none
```
next(iter(your_list), None)
```

## python function arguments
https://stackoverflow.com/questions/817087/call-a-function-with-argument-list-in-python  
```
def func(*args, **kvargs):
	for arg in args:
		print(arg)
	for k,v in kvargs.items():
		print(k, v)
```
\*args means take the rest non-key/value arguments and put them in a list called args
\*\*kvargs means take the rest key/value arguments and put them in a dict called kvargs

## python scheduler
APScheduler https://lz5z.com/Python%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1%E7%9A%84%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F/

## python requirements.txt
to create requirements.txt
```
pip freeze > requirements.txt
```
install the dependencies from requirements.txt
```
pip install -r requirements.txt
```

## Applying Functions to List Items
http://damianmingle.com/applying-functions-to-list-items/  
List Comprehension Example
```
result = [x.upper() for x in dataList]
```

## list comprehension with if condition expression
https://docs.python.org/3/tutorial/datastructures.html?highlight=list%20comprehension#list-comprehensions  
https://docs.python.org/3/reference/expressions.html#conditional-expressions  
https://www.python.org/dev/peps/pep-0308/  
https://stackoverflow.com/questions/4260280/if-else-in-pythons-list-comprehension
```
appMainScript = [len(x.string) if x.string is not None else 0 for x in scriptsFound]
```
without else, the if expression has to be put at the end
```
appMainScript = [len(x.string) for x in scriptsFound if x.string is not None]
```

## convert string to int if it is not None
```
n = int(s) if s else None
```

## Packaging Python and install it 
Follow this tutorial to package your module. https://packaging.python.org/tutorials/packaging-projects/  

If you upload to pypi, you can pip install it directly.  
An alternative way is to install from git repository. For example https://github.com/hairinwind/pemail.git is the repository of my gmail module. 
I can install it 
```
pip install git+https://github.com/hairinwind/pemail.git#egg=pemail
```
after install, I can import it
```
from pemail install gmail
gmail.send_email_text(subject,text, password='gmail_password')
```

## How to access environment variable values?
```
import os
print(os.environ['PATH'])
```

## get absolute path by relative path
```
import os
os.path.abspath("mydir/myfile.txt")
```

## mkdir if it already exists
```
import os
os.makedirs(path, exist_ok=True)
```

## copy multiple files 
```
import os
import shutil
src_files = os.listdir(src)
for file_name in src_files:
    full_file_name = os.path.join(src, file_name)
    if (os.path.isfile(full_file_name)):
        shutil.copy(full_file_name, dest)
```


## iterate for loop with index
```
for idx, val in enumerate(ints):
    print(idx, val)
```

## print in the same line
```
print('.', end='', flush=True)
```

## bytes to string
need decode
```
>>> b"abcde".decode("utf-8") 
'abcde'
```

## run system command 
```
import os
import subprocess
path = os.path.abspath(dir + "/" + file_name)
output= subprocess.Popen(('certutil.exe', '-hashfile', path), stdout=subprocess.PIPE).stdout
result = []
for line in output:
    result.append(line.decode("utf-8").replace('\r\n', ''))
output.close()
```

## dump yaml in its preserved order
https://stackoverflow.com/questions/31605131/dumping-a-dictionary-to-a-yaml-file-while-preserving-order  
Run the below setup code at the start.
```
def setup_yaml():
  represent_dict_order = lambda self, data:  self.represent_mapping('tag:yaml.org,2002:map', data.items())
  yaml.add_representer(OrderedDict, represent_dict_order)    
setup_yaml()
```
Use an OrderedDict instead of dict.
```
with open(dir+'/'+yamlFileName, 'w') as outfile:
	yaml.dump(OrderedDict(data), outfile, default_flow_style=False)
```
data type is dict.

## string format
```
>>> 'abc {0} aaa {1} float {2:9.3f}'.format('1st', '2nd', 234.456789)
'abc 1st aaa 2nd float   234.457'
```
or C-style
```
>>> "%s is %d years old." % ('john', 17)
'john is 17 years old.'
```

## getopt to get command options
https://docs.python.org/3.1/library/getopt.html  
```
getopt.getopt(args, shortopts, longopts=[])
```
with options that require an argument followed by a colon  
for example
```
opts, args = getopt.getopt(sys.argv[1:], "ho:v", ["help", "output="])
```
a possible command is like this:  
python main.py -h -oabc -v --help --output=abc

## timezone
```
import time
time.tzname[time.localtime().tm_isdst]
```
time.localtime().tm_isdst indicates that dst is active.

## regular expression search vs findall vs match 
- search is to scan through string looking for the first location where the regular expression pattern produces a match, and return a corresponding match object.
- findall returns all matches.
- match returns match object when the beginning of string matches the regular expression

## python except 
From this example we can know the syntax of python exception
```
import sys

try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except OSError as err:
    print("OS error: {0}".format(err))
except ValueError:
    print("Could not convert data to an integer.")
except:
    print("Unexpected error:", sys.exc_info()[0])
    raise
```
raising exceptions
```
raise NameError('the name is wrong')
```

## multiple threads 
https://www.novixys.com/blog/updating-file-multiple-threads-python/  



