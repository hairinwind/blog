
## dict to dataframe and groupby
https://www.testdome.com/d/python-interview-questions/9
Implement a group_by_owners function that:

- Accepts a dictionary containing the file owner name for each file name.
- Returns a dictionary containing a list of file names for each owner name, in any order.  
For example, for dictionary {'Input.txt': 'Randy', 'Code.py': 'Stan', 'Output.txt': 'Randy'} the group_by_owners function should return {'Randy': ['Input.txt', 'Output.txt'], 'Stan': ['Code.py']}.
```
import pandas as pd
class FileOwners:

    @staticmethod
    def group_by_owners(files):
        files_dataframe = pd.DataFrame(list(files.items()), columns=['fileName', 'owner'])
        return files_dataframe.groupby(['owner'])['fileName'].apply(list).to_dict()

files = {
    'Input.txt': 'Randy',
    'Code.py': 'Stan',
    'Output.txt': 'Randy'
}

```
files type is dict. 
pd.DataFrame(list(files.items()),...) converts the dict to a dataframe
then groupby and convert it back to dict

## ice cream machine
Implement the IceCreamMachine's scoops method so that it returns all combinations of one ingredient and one topping. If there are no ingredients or toppings, the method should return an empty list.

For example, IceCreamMachine(["vanilla", "chocolate"], ["chocolate sauce"]).scoops() should return [['vanilla', 'chocolate sauce'], ['chocolate', 'chocolate sauce']].
```
import pandas as pd
class IceCreamMachine:
    
    def __init__(self, ingredients, toppings):
        self.ingredients = ingredients
        self.toppings = toppings
        
    def scoops(self):
        # key is for outer join
        ingredients_key = self.initialKeys('c', self.ingredients)
        toppings_key = self.initialKeys('c', self.toppings)
        ingredients_df= pd.DataFrame({'key': ingredients_key,'ingredients':self.ingredients})
        toppings_df = pd.DataFrame({'key': toppings_key,'toppings':self.toppings})
        merged_df = pd.merge(ingredients_df, toppings_df, how="outer")
        merged_df.drop(['key'], axis=1, inplace=True)
        return merged_df.values
    
    def initialKeys(self, key, target):
        return [key] * len(target)

machine = IceCreamMachine(["vanilla", "chocolate"], ["chocolate sauce"])
scoops = machine.scoops()
print(scoops)
```

