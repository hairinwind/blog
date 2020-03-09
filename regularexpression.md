
## not include string
I want to look up name=[something but not EMT]
```
name=\[((?!EMT)\w)+\]
``` 
The text below does not match 
serialVersionUID=[10020120822], name=[EMT], description=[eTransfer Payments]

The text below has the match, name=[ABC]
serialVersionUID=[10020120822], name=[ABC], description=[eTransfer Payments]

Here is the regex to match the xml section, which is self closed and does not have attribute na="true" 
```
regex=<((?!na=\"true\")[^>])+\/>  
```
Here is the text   
```
<a>a</a><10020120822/><aEMT na="true" /><abc efg/>  
```
The matched section is 
```
<10020120822/> 
and 
<abc efg/>
```  
