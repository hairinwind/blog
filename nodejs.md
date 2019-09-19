## Nodejs express typescript first project
In the project directory
```
npm init
npm install express body-parser request
npm install --save-dev typescript @types/node @types/express
npm install -g ts-node
```
add src directory, add index.js in the src dir.
```
import express from "express";

const app = express();
const port = 8080; // default port to listen

// define a route handler for the default home page
app.get( "/", ( req, res ) => {
    res.send( "Hello world!" );
} );

// start the Express server
app.listen( port, () => {
    console.log( `server started at http://localhost:${ port }` );
} );
``` 

add "start" in the package.json
```
"start": "ts-node src/index.ts"
```
To start the server, run "npm run start"

https://developer.okta.com/blog/2018/11/15/node-express-typescript
https://www.cnblogs.com/chyingp/p/nodejs-learning-express-body-parser.html

## enable enable CORS.
```
app.use(function(req, res, next) {
	res.header("Access-Control-Allow-Origin", "*");
	next();
});
```

## Express error handling
https://expressjs.com/en/guide/error-handling.html
golden retriever
Errors that occur in synchronous code inside route handlers and middleware require no extra work.
Errors returned from asynchronous functions that are invoked by route handlers and middleware must be passed to the next() function where they will be caught by Express and processed accordingly.

## await 
Making async function call looks like a synchronous function call. 

https://blog.risingstack.com/mastering-async-await-in-nodejs/


## avoid singleton 
https://medium.com/@iaincollins/how-not-to-create-a-singleton-in-node-js-bd7fde5361f5
```
var helloWorldA = require(‘./hello-world’)(),
    helloWorldB = require(‘./hello-world’)();
```

## let request-promise return full response
By default, the request-promise only return the json object in the body, it does not have the status code. In my case, I need propagate the status. I need set this option
```
const requestOptions = {
    ...
    resolveWithFullResponse: true, // to propagate the status, the full response shall be returned
    json: true, 
};

const results = await request.post(url, requestOptions);
const parsedResults = results.body;
```
the json:true option would return json obejct in results.body, so you don't need parse it. 

## debug nodejs in vscode
https://itnext.io/the-absolute-easiest-way-to-debug-node-js-with-vscode-2e02ef5b1bad  
for ts-node, just use 
```
ts-node --inspect src/index.ts
```




## script by nodejs 
This is like the main class in java, can be run standalone. 
```
function exit(message) {
    logger.error(message);
    process.exit();
}

program
    .option('-o, --orgId <orgId>', 'Organization ID')
    .option('-c, --capacityId <capacityId>', 'Capacity ID')
    .parse(process.argv);

if (!program.orgId) {
    exit('-o must have a value');
}

logger.info('release version', {
    orgId: program.orgId,
    capacityId: program.capacityId,
});


// the real logic.......
```

## req.params vs req.query
req.params contains route parameters (in the path portion of the URL)  
req.query contains the URL query parameters (after the ? in the URL)

## sequalize query specify return fields
```
mdsSequelize.datacatalog.findAll({
            raw: true,
            attributes: [
                'id', 'name', 'description', 'datasourceId',
                [Sequelize.col('datasource.name'), 'datasourceName'],
                [Sequelize.col('dataset.name'), 'datasetName'],
                [Sequelize.col('dataset->group.organizationId'), 'organizationId'],
            ],
            include: [
                {model: mdsSequelize.datasource, required: true, attributes: []},
                {model: mdsSequelize.dataset, required: true,
                    attributes: [],
                    include: [
                        {model: mdsSequelize.group, required: true, where,
                            attributes: []},
                    ]},
            ],
            order: [[Sequelize.col('datasource.name')], [Sequelize.col('dataset.name')]],
        });
```
- raw: true tells sequalize to return the result in flat json instead of a tree structure
- attributes in the top level specify the fields returned. 
- [Sequelize.col('datasource.name'), 'datasourceName'] is for the field in a joined table
- [Sequelize.col('dataset->group.organizationId'), 'organizationId'] is for the field in a joined joined table
- attributes with empty array [] in "include" tells sequalize don't return fields of that table
- required: true tells sequalize to do inner join instead of left join

## error [nodemon] app crashed - waiting for file changes before starting...
You may need to run 'npm install'. 

## allow fiddler to capture network traffic
http://blog.knor.net/2016/12/capture-network-traffic-in-nodejs-with-fiddler/
```
set https_proxy=http://127.0.0.1:8888
set http_proxy=http://127.0.0.1:8888
set NODE_TLS_REJECT_UNAUTHORIZED=0
```

## nodejs get environment variables
### issue
The business wants to display bamboo_buildNumber as part of angular project vesion on UI. For example, 1.0.0-build100.
### solution
bamboo_buildNumber is a env varaible on the build server. To get the env variable "bamboo_buildNumber". here is the nodejs code
```
process.env.bamboo_buildNumber
```
In my angular project, we have a prebuild.js to setup the project version. The prebuild.js is configured in package.json like this 
```
"prebuild": "node ./prebuild.js",
```
The prebuild.js is running in nodejs and I can get the bamboo_buildnumber and update the angular project version when the build is executed on the build server.  
Here is the code in prebuild.js to update the app.version
```
const buildNumber = process.env.bamboo_buildNumber;
pkg.version = pkg.version + "-build" + buildNumber;
const fs = require('fs');
fs.writeFile(
    './package.json',
    JSON.stringify(pkg, null, 2),
    { flat: 'w' },
    () => null
);
```

## send command line arguments to npm script
### issue 
pass in the arguments to npm script
### solution 
```
npm run [command] [-- <args>]
```
https://stackoverflow.com/questions/11580961/sending-command-line-arguments-to-npm-script

