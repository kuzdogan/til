# Query and Update Operators in MongoDB

I've always confused the operators in MongoDB. Where do I put the operator? Which one shuld I use? What is this `$` sign before them? And many more confusions I can't put into words. 

## Query vs Update

Most of the times these are the two things we want to do in a db.

### Query

Basic find in mongo is as follows:
```
db.collection.find({ <field>: <value> })
```
At least for me, this has caused huge confusion about the syntax of queries. Because actually there is a standard and this is only a shorthand to:
```
db.collection.find({ <field>: { $eq: <value> }})
```

where `$eq` is a [*query operator*](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors) and `{ $eq: <value> }` as an _object_ (in javascript sense) is a [*query filter document*](https://docs.mongodb.com/manual/core/document/#document-query-filter). So when _querying_ we query a collection for documents with each field satisfying conditions.

A more clear and general representation would be:
```
db.collection.find({
  <field1>: {
    <queryOperator1>: <value>,
    ...
    <queryOperatorN>: <value>
  },
  ...
  <fieldN>: {
    <queryOperator1>: <value>,
    ...
    <queryOperatorN>: <value>
  }
})
```

And actually the syntax of _find_ and similar method is as follows:
```
db.collection.find(query, projection)
```

As in our cases we only had the query parameters, which were the _query filter documents_ we built. Here's an [example](https://docs.mongodb.com/manual/crud/#read-operations) with [_projection_](https://docs.mongodb.com/manual/reference/method/db.collection.find/#projections) and _modifier_:
```
db.users.find({
  { age: { $gt: 18 }},    <-- query
  { name: 1, address: 1}  <-- projection. i.e. show me these fields
}).limit(5)               <-- cursor modifier
```
Other useful modifiers are `.sort({name:1})` and `skip(5)`. We can combine modifiers: `.limit(20).sort({name:1}).skip(5)` = Only show 5 of the resulting documents, sort them by name but skip the first 5 items.

Here's the list of all [query operators](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors).

### Update
Update methods have the following syntax:
```
db.collection.updateOne(<filter>, <update>, <options>)
```

Here `<filter>` is the _query filter document_ we built above.

`<update>` is an _update document_. It has similar structure to a _query filter document_ as:
```
{
  <update operator>: { <field1>: <value1>, ... },
  <update operator>: { <field2>: <value2>, ... },
  ...
}
```

`<options>` is also an object with [parameters](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#syntax)

#### Update operators
Take this example
```
{ $set: { status: "D" }, $inc: { quantity: 2 } }
```

This update object sets the field `status` to `D` and increments the field `quantity` by `2`.

A complete update operation would be:
```
db.inventory.update(
  { 
    quantity: {$lt: 50},
    sold: true    // same as     sold: {$eq: true}
  },
  {
    $set: { status: "D" },
    $inc: { quantity: 2 }
  }
)
```

Notice now how the ordering of `<field>: {<operator>: <value>}` in queries became `<operator>:{<field>: <value>}` now. This is also another major confusion.
