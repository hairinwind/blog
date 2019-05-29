# course.fast.ai
## over-fitting or under-fitting
Here is a typical result after data training
```
[0.  0.03597  0.01879  0.99365]
[1.  0.02605  0.01836  0.99365]
[2.  0.02189  0.0196   0.99316]
```
The first column 0.1.2 is the epoch.  
The second column is the loss of the training.  
The third column is the loss of the validating.   
The fourth column is the accuracy.  

If the loss of training << loss of validating, it means it is over-fitting.   
If the loss of training >> loss of validating, it means it is under-fitting. For example, 
```
[2.  0.28683  0.21545  0.92905]
```
It is undefeeding. And we can increase the cycle_mult
```
learn.fit(1e-2, 3, cycle_len=1, cycle_mult=2)
```
Then we can increase epoch until it is over-fitting.

## learning rate

set earlier layers to 3x~10x lower learning rate than net higher layer

## precompute




