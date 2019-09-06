
## not include string
I want to look up name=[something but not EMT]
```
name=\[((?!EMT)\w)+\]
``` 
The text below does not match 
serialVersionUID=[10020120822], name=[EMT], description=[eTransfer Payments]

The text below has the match, name=[ABC]
serialVersionUID=[10020120822], name=[ABC], description=[eTransfer Payments]

