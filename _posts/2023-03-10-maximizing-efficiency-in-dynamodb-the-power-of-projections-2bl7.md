DynamoDB is a fast and flexible NoSQL database service offered by Amazon Web Services (AWS). It is designed to provide high performance, scalability, and availability for modern applications. DynamoDB is a fully managed service, which means AWS takes care of the infrastructure, scaling, and maintenance of the database, allowing developers to focus on building their applications.

One important concept in DynamoDB is projections. A projection is a way to specify which attributes should be included in the result of a query or scan operation. By default, a query or scan operation returns all the attributes of an item. However, in many cases, you only need a subset of the attributes. Using projections, you can reduce the amount of data that needs to be read and returned, which can lead to faster query response times and lower data transfer costs.

In this article, we will explore the different types of projections available in DynamoDB, how to use them to optimize your queries, and best practices for using projections in your application. 

Here are some examples of when to use each projection type in different scenarios, as well as code examples for creating and querying a table with each projection type:


![Dynamo Projection Comparision](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/t318uq68g7srdzjjgftm.png)
**All attributes projection:**
You might use the All attributes projection when you need to retrieve all the attributes of an item in a table that stores financial transactions. For instance, imagine that you have a table called "Transactions" with the following attributes: TransactionId, Date, Amount, Description, and Status. If you need to retrieve all the details for a specific transaction, you could use the All attributes projection. 

```
// Create a table with the All attributes projection
const params = {
  TableName: 'Transactions',
  KeySchema: [
    { AttributeName: 'TransactionId', KeyType: 'HASH' }
  ],
  AttributeDefinitions: [
    { AttributeName: 'TransactionId', AttributeType: 'S' },
    { AttributeName: 'Date', AttributeType: 'S' },
    { AttributeName: 'Amount', AttributeType: 'N' },
    { AttributeName: 'Description', AttributeType: 'S' },
    { AttributeName: 'Status', AttributeType: 'S' }
  ],
  Projection: {
    ProjectionType: 'ALL'
  }
};

await dynamodb.createTable(params).promise();

// Query the table with the All attributes projection
const queryParams = {
  TableName: 'Transactions',
  KeyConditionExpression: 'TransactionId = :transactionId',
  ExpressionAttributeValues: {
    ':transactionId': 'abc123'
  }
};

const result = await dynamodb.query(queryParams).promise();
console.log(result.Items);

```
**Keys only projection:**
 You might use the Keys only projection when you need to retrieve a list of primary key values in a table that stores user information. For example, imagine that you have a table called "Users" with the following attributes: UserId, Name, Email, and PhoneNumber. If you need to retrieve a list of UserIds, you could use the Keys only projection. 

```
// Create a table with the Keys only projection
const params = {
  TableName: 'Users',
  KeySchema: [
    { AttributeName: 'UserId', KeyType: 'HASH' }
  ],
  AttributeDefinitions: [
    { AttributeName: 'UserId', AttributeType: 'S' },
    { AttributeName: 'Name', AttributeType: 'S' },
    { AttributeName: 'Email', AttributeType: 'S' },
    { AttributeName: 'PhoneNumber', AttributeType: 'S' }
  ],
  Projection: {
    ProjectionType: 'KEYS_ONLY'
  }
};

await dynamodb.createTable(params).promise();

// Query the table with the Keys only projection
const queryParams = {
  TableName: 'Users',
  KeyConditionExpression: 'Name = :name',
  ExpressionAttributeValues: {
    ':name': 'John Doe'
  },
  ProjectionExpression: 'UserId'
};

const result = await dynamodb.query(queryParams).promise();
console.log(result.Items);

```
**Include projection:** 
You might use the Include projection when you need to retrieve a subset of the attributes for a specific item in a table that stores product information. For instance, imagine that you have a table called "Products" with the following attributes: ProductId, Name, Description, Price, and Quantity. If you need to retrieve the Name and Price for a specific product, you could use the Include projection.

```
// Define the table schema
const tableSchema = {
  TableName: 'Products',
  KeySchema: [
    { AttributeName: 'ProductId', KeyType: 'HASH' }
  ],
  AttributeDefinitions: [
    { AttributeName: 'ProductId', AttributeType: 'S' },
    { AttributeName: 'Name', AttributeType: 'S' },
    { AttributeName: 'Description', AttributeType: 'S' },
    { AttributeName: 'Price', AttributeType: 'N' },
    { AttributeName: 'Quantity', AttributeType: 'N' }
  ],
  ProvisionedThroughput: {
    ReadCapacityUnits: 5,
    WriteCapacityUnits: 5
  },
  GlobalSecondaryIndexes: [
    {
      IndexName: 'NameIndex',
      KeySchema: [
        { AttributeName: 'Name', KeyType: 'HASH' }
      ],
      Projection: {
        ProjectionType: 'INCLUDE',
        NonKeyAttributes: [ 'Price' ]
      },
      ProvisionedThroughput: {
        ReadCapacityUnits: 5,
        WriteCapacityUnits: 5
      }
    }
  ]
};

// Create the table in DynamoDB
const createTableResult = await dynamodb.createTable(tableSchema).promise();
console.log('Table created:', createTableResult);

// Query the table using the NameIndex GSI and the Include projection
const queryParams = {
  TableName: 'Products',
  IndexName: 'NameIndex',
  KeyConditionExpression: 'Name = :name',
  ExpressionAttributeValues: {
    ':name': 'Product 1'
  },
  ProjectionExpression: 'Name, Price'
};

const queryResult = await dynamodb.query(queryParams).promise();
console.log('Query result:', queryResult.Items);

```
In summary, to use projections effectively, it is important to choose the most efficient projection type for your query, only request the attributes you need, avoid unnecessary scans and queries, use secondary indexes to improve query performance, minimize the size of your response data, and monitor your query performance.

Understanding and using projections can help you get the most out of DynamoDB and improve the performance of your queries, which is essential for building fast and efficient modern applications.
