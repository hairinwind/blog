
## npm package.json dependency version
```
- version Must match version exactly
- >version Must be greater than version
- >=version etc
- <version
- <=version
- ~version "Approximately equivalent to version" See semver
- ^version "Compatible with version" See semver
- 1.2.x 1.2.0, 1.2.1, etc., but not 1.3.0
```
https://docs.npmjs.com/files/package.json

## npm version
run npm version can update the version in package.json  
In one of my project, it is using bamboo to do the build. The version is a comibination of release branch name + build number. The release branch name and the build number can be got from the bamboo env variables.  
Then run 
```
npm version $combinationVersion
npm run build
npm publish
```
https://docs.npmjs.com/cli/version  

## npm python 2.7
```
npm config set python C:\\mySoft\\Python27\\python.exe
```

## npm list 
list global only and only the top package
```
npm list -g -depth 0
```
