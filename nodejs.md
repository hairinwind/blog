
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