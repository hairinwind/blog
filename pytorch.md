## tensor.view
https://pytorch.org/docs/stable/tensors.html  
for example, images.shape is torch.Size([100, 1, 28, 28])
```
images.view(-1, 28, 28)
```
after view, the shape is torch.Size([100, 28, 28])
```
images.view(-1, 2, 28, 28)
```
the shape is torch.Size([50, 2, 28, 28])
```
images.view(-1, 28)
```
the shape is torch.Size([2800, 28])

第一个参数-1 就是根据后面的参数，增加最外面一维，使新维度容纳原来所有元素。

