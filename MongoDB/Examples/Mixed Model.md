

### [MongoDB ](../MongoDB.md) > [Examples](Examples.md) > Mixed Model
___


In this model, we will try to denormalise some of the objects and normalise the rest. We have embedded the lineitem documents into the order document, however the supplier, customer, and partsupp are separate documents. The nation document is embedded into the supplier and the customer documents and the nation itself embed the region document. The partsupp document embed the part document, but reference the supplier document as shown below:

![image](https://s3.amazonaws.com/b2cbucket/mixed+model.png)


The Order document:

```json
 {
	"_id": "ObjectID",
	"orderstatus": "String",
	"totalprice": "Double",
	"orderdate": "Date",
	"orderpriority": "String",
	"clerk": "String",
	"shippriority": "String",
	"comment": "String",
	"customer": "ObjectID<Customer>",
	"LineItems": "Array<Object<lineitem>>"
}
```

The customer document:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"address": "String",
	"phone": "Double",
	"acctbal": "Double",
	"mktsegment": "String",
	"comment": "String",
	"nation": "Object<nation>"
}
```

The lineitem document:

```json
{
	"_id": "ObjectID",
	"quantity": "Double",
	"extende dprice": "Double",
	"discount": "Double",
	"tax": "Double",
	"returnflag": "Boolean",
	"linestatus": "String",
	"shipdate": "Date",
	"commitdate": "Date",
	"receiptdate": "Date",
	"shipinstruct": "String",
	"shipmode": "String",
	"comment": "String",
	"partsupp": "ObjectID<partsupp>"
}
```

The partsupp document:

```json
{
	"_id": "ObjectID",
	"availqty": "Double",
	"supplycost": "Double",
	"comment": "String",
	"part": "Object<part>",
	"supplier": "ObjectID<supplier>"
}
```

The part document:

```json
{
	"_id": "String",
	"name": "String",
	"mfgr": "String",
	"brand": "String",
	"type": "String",
	"size": "Double",
	"container": "String",
	"retailprice": "Double",
	"comment": "String"
}
```

The supplier document:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"address": "String",
	"phone": "Double",
	"acctbal": "Double",
	"comment": "String",
	"nation": "Object<nation>"
}
```

The nation nation document :

```json
{
	"_id": "ObjectID",
	"name": "String",
	"comment": "String",
	"region": "Object<region>"
}
```

The region document:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"comment": "String"
}
```


An example is shown below:


Order

```json
 {
	"_id": "7821ef4d-8e8c-46e0-950d-83c245b9bee8",
	"orderstatus": "Open",
	"totalprice": "233",
	"orderdate": "2015-12-21 10:51:25",
	"orderpriority": "High",
	"clerk": "John",
	"shippriority": "High",
	"comment": "This is an order",
	"customer": "f4005128-a7d0-11e5-bf7f-feff819cdc9f",
	"LineItems": [{
		"_id": "2ddbd282-a7d1-11e5-bf7f-feff819cdc9f",
		"quantity": "1",
		"extende dprice": "200",
		"discount": "20",
		"tax": "2",
		"returnflag": "false",
		"linestatus": "available",
		"shipdate": "2015-12-21 10:51:25",
		"commitdate": "2015-12-21 10:51:25",
		"receiptdate": "2015-12-21 10:51:25",
		"shipinstruct": "some text",
		"shipmode": "DHL",
		"comment": "some text",
		"partsupp": "62353e7e-a7d1-11e5-bf7f-feff819cdc9f"
	}]
}
```

customer:

```json
{
	"_id": "f4005128-a7d0-11e5-bf7f-feff819cdc9f",
	"name": "Bilal",
	"address": "Street 1",
	"phone": "1223456",
	"acctbal": "212",
	"mktsegment": "some text",
	"comment": "some text",
	"nation": {
		"_id": "0aa770e6-a7d1-11e5-bf7f-feff819cdc9f",
		"name": "US",
		"comment": "some text",
		"region": {
			"_id": "18f228bc-a7d1-11e5-bf7f-feff819cdc9f",
			"name": "Texas",
			"comment": "some text"
		}
	}
}
```

partsupp :

```json
{
	"_id": "62353e7e-a7d1-11e5-bf7f-feff819cdc9f",
	"availqty": "20",
	"supplycost": "220",
	"comment": "some text",
	"part": {
		"_id": "75f28eda-a7d1-11e5-bf7f-feff819cdc9f",
		"name": "Tshirt",
		"mfgr": "Boss",
		"brand": "Boss",
		"type": "sport",
		"size": "40",
		"container": "some text",
		"retailprice": "230",
		"comment": "some text"
	},
	"supplier": "968a0d3a-a7d1-11e5-bf7f-feff819cdc9f"
}
```

supplier:

```json
{
	"_id": "968a0d3a-a7d1-11e5-bf7f-feff819cdc9f",
	"name": "Boss Supplier",
	"address": "street 2",
	"phone": "212323",
	"acctbal": "2933",
	"comment": "some text",
	"nation": {
		"_id": "0aa770e6-a7d1-11e5-bf7f-feff819cdc9f",
		"name": "US",
		"comment": "some text",
		"region": {
			"_id": "18f228bc-a7d1-11e5-bf7f-feff819cdc9f",
			"name": "Texas",
			"comment": "some text"
		}
	}
}
```

 
In the following sections, we will show how to represent three complex TPC-H sql queries in MongoDB using this data model.
 
##### Pricing Summary Report Query (Q1)

This query is used to report the amount of billed, shipped and returned items. The SQL query is shown below:

````
select   l_returnflag,   l_linestatus,   sum(l_quantity) as sum_qty,   sum(l_extendedprice) as sum_base_price,   sum(l_extendedprice*(1-l_discount)) as sum_disc_price,   sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge,   avg(l_quantity) as avg_qty,   avg(l_extendedprice) as avg_price,   avg(l_discount) as avg_disc,   count(*) as count_orderfrom   lineitemwhere   l_shipdate <= date '1998-12-01' - interval '[DELTA]' day (3)group by   l_returnflag,   l_linestatusorder by   l_returnflag,   l_linestatus;
````

Since the above query needs to be run only against the lineitem table, then we can easily run the corresponding query in MongoDB agains the lineitem collection.


To execute the above query in MongoDB, we will use the pipeline aggregation framework. We will divide the above query to different stages then execute them all at once. The stages that we will need are a "match" stage, a "project" stage, a "group" stage, and a "sort" stage. The "match" stage is used to get all the documents that are having the lineitem shipdate less than or equal some date value or the sql query part shown below:

````
where   l_shipdate <= date '1998-12-01' - interval '[DELTA]' day (3)
````

The equivalent match stage is shown below:

````
{  
   "$match":{  
      "Items":{  
         "$elemMatch":{  
            "SHIPDATE":{  
               "$lte": ISODate("2016-01-01T00:00:00.000Z")
            }
         }
      }
   }
}
````

Then since the returned documents will contain an array of lineitem documents, we need to unwind these document so that they can be processed by the other stages. The unwind stage query is shown below:

````
{  
   "$unwind":"$Items"
}
````

After unwinding the lineitem documents, we can run the project stage which will return only the attributes that we need as per the original sql query. The project stage query is shown below:



````
{  
   "$project":{  
      "Items.RETURNFLAG":1,
      "Items.LINESTATUS":1,
      "Items.QUANTITY":1,
      "Items.EXTENDEDPRICE":1,
      "Items.DISCOUNT":1,
      "l_dis_min_1":{  
         "$subtract":[  
            1,
            "$Items.DISCOUNT"
         ]
      },
      "l_tax_plus_1":{  
         "$add":[  
            "$Items.TAX",
            1
         ]
      }
   }
}
````

The above stage will return the attributes that we need to use in the following stages. We can also perform any required mathematical operations on any of the attributes.  Then we can perform the group stage which will group the results by certain attributes as shown below:

````
{  
   "$group":{  
      "_id":{  
         "RETURNFLAG":"$Items.RETURNFLAG",
         "LINESTATUS":"$Items.LINESTATUS"
      },
      "sum_qty":{  
         "$sum":"$Items.QUANTITY"
      },
      "sum_base_price":{  
         "$sum":"$Items.EXTENDEDPRICE"
      },
      "sum_disc_price":{  
         "$sum":{  
            "$multiply":[  
               "$Items.EXTENDEDPRICE",
               "$l_dis_min_1"
            ]
         }
      },
      "sum_charge":{  
         "$sum":{  
            "$multiply":[  
               "$Items.EXTENDEDPRICE",
               {  
                  "$multiply":[  
                     "$l_tax_plus_1",
                     "$l_dis_min_1"
                  ]
               }
            ]
         }
      },
      "avg_price":{  
         "$avg":"$Items.EXTENDEDPRICE"
      },
      "avg_disc":{  
         "$avg":"$Items.DISCOUNT"
      },
      "count_order":{  
         "$sum":1
      }
   }
}
````

Finally we sort the results using the sort stage as shown below:

````
{  
   "$sort":{  
      "RETURNFLAG":1,
      "LINESTATUS":1
   }
}
````


The complete query in mongoDB is shown below:


````
[  
   {  
      "$match":{  
         "Items":{  
            "$elemMatch":{  
               "SHIPDATE":{  
                  "$lte": ISODate("2016-01-01T00:00:00.000Z")
               }
            }
         }
      }
   },
   {  
      "$project":{  
         "Items.RETURNFLAG":1,
         "Items.LINESTATUS":1,
         "Items.QUANTITY":1,
         "Items.EXTENDEDPRICE":1,
         "Items.DISCOUNT":1,
         "l_dis_min_1":{  
            "$subtract":[  
               1,
               "$Items.DISCOUNT"
            ]
         },
         "l_tax_plus_1":{  
            "$add":[  
               "$Items.TAX",
               1
            ]
         }
      }
   },
   {  
      "$group":{  
         "_id":{  
            "RETURNFLAG":"$Items.RETURNFLAG",
            "LINESTATUS":"$Items.LINESTATUS"
         },
         "sum_qty":{  
            "$sum":"$Items.QUANTITY"
         },
         "sum_base_price":{  
            "$sum":"$Items.EXTENDEDPRICE"
         },
         "sum_disc_price":{  
            "$sum":{  
               "$multiply":[  
                  "$Items.EXTENDEDPRICE",
                  "$l_dis_min_1"
               ]
            }
         },
         "sum_charge":{  
            "$sum":{  
               "$multiply":[  
                  "$Items.EXTENDEDPRICE",
                  {  
                     "$multiply":[  
                        "$l_tax_plus_1",
                        "$l_dis_min_1"
                     ]
                  }
               ]
            }
         },
         "avg_price":{  
            "$avg":"$Items.EXTENDEDPRICE"
         },
         "avg_disc":{  
            "$avg":"$Items.DISCOUNT"
         },
         "count_order":{  
            "$sum":1
         }
      }
   },
   {  
      "$sort":{  
         "RETURNFLAG":1,
         "LINESTATUS":1
      }
   }
]
````

The complete java code for the api that will return the results of TPCH Q1 is shown below:

````
    @GET
    @Path("/q1")
    @Timed
    @ApiOperation(value = "get result of TPCH Q1 using this model", notes = "Returns mongoDB document(s)", response = Document.class, responseContainer = "list")
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public ArrayList<Document> getQ1Results() {

        AggregateIterable<Document> result;

        try {

            String matchStringQuery = "{\"$match\":{\"Items\":{\"$elemMatch\":{\"SHIPDATE\":{\"$lte\":ISODate(\"2016-01-01T00:00:00.000Z\")}}}}}";

            String unWindStringQuery = "{$unwind: \"$Items\"}";

            String projectStringQuery = "{\"$project\":{\"Items.RETURNFLAG\":1,\"Items.LINESTATUS\":1,\"Items.QUANTITY\":1,\"Items.EXTENDEDPRICE\":1,\"Items.DISCOUNT\":1,\"l_dis_min_1\":{\"$subtract\":[1,\"$Items.DISCOUNT\"]},\"l_tax_plus_1\":{\"$add\":[\"$Items.TAX\",1]}}}";

            String groupStringQuery = "{\"$group\":{\"_id\":{\"RETURNFLAG\":\"$Items.RETURNFLAG\",\"LINESTATUS\":\"$Items.LINESTATUS\"},\"sum_qty\":{\"$sum\":\"$Items.QUANTITY\"},\"sum_base_price\":{\"$sum\":\"$Items.EXTENDEDPRICE\"},\"sum_disc_price\":{\"$sum\":{\"$multiply\":[\"$Items.EXTENDEDPRICE\",\"$l_dis_min_1\"]}},\"sum_charge\":{\"$sum\":{\"$multiply\":[\"$Items.EXTENDEDPRICE\",{\"$multiply\":[\"$l_tax_plus_1\",\"$l_dis_min_1\"]}]}},\"avg_price\":{\"$avg\":\"$Items.EXTENDEDPRICE\"},\"avg_disc\":{\"$avg\":\"$Items.DISCOUNT\"},\"count_order\":{\"$sum\":1}}}";

            String sortStringQuery = "{\"$sort\":{\"Items.RETURNFLAG\":1,\"Items.LINESTATUS\":1}}";

            BsonDocument matchBsonQuery = BsonDocument.parse(matchStringQuery);

            BsonDocument unWindBsonQuery = BsonDocument
                    .parse(unWindStringQuery);

            BsonDocument projectBsonQuery = BsonDocument
                    .parse(projectStringQuery);

            BsonDocument groupBsonQuery = BsonDocument.parse(groupStringQuery);

            BsonDocument sortBsonQuery = BsonDocument.parse(sortStringQuery);

            ArrayList<Bson> aggregateQuery = new ArrayList<Bson>();

            aggregateQuery.add(matchBsonQuery);
            aggregateQuery.add(unWindBsonQuery);
            aggregateQuery.add(projectBsonQuery);
            aggregateQuery.add(groupBsonQuery);
            aggregateQuery.add(sortBsonQuery);

            result = this.mixed_order.aggregate(aggregateQuery);

            MongoCursor<Document> iterator = result.iterator();

            ArrayList<Document> results = new ArrayList<Document>();
            while (iterator.hasNext()) {
                Document resultDoc = iterator.next();
                results.add(resultDoc);
            }

            return results;
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }
````

If you call the above query, you will get the results below:


![image](https://s3.amazonaws.com/b2cbucket/mixed+q1.png)



##### Shipping Priority Query (Q3)

This query is used to get the 10 unshipped orders with the highest value. In order to do that, the query joins three tables (customer, order and lineitem) as shown below:


````
select l_orderkey, sum(l_extendedprice*(1-l_discount)) as revenue, o_orderdate, o_shippriorityfrom customer, orders, lineitemwhere c_mktsegment = '[SEGMENT]' and c_custkey = o_custkey and l_orderkey = o_orderkey and o_orderdate < date '[DATE]' and l_shipdate > date '[DATE]'group by l_orderkey, o_orderdate, o_shippriorityorder by revenue desc, o_orderdate;
````

Since mongoDB doesn't support joins and since the aggregation in mongoDB happens only in the collection level, then to run an aggregate query against related documents we should first fetch all the related documents and put them in a temporary collection to be able to run later aggregate query on this newly created collection. We do that by querying first the order collection and fetch all the documents that meet the condition "o_orderdate < date '[DATE]'". Then for each customer objectID, we fetch the corresponding customer document and embed it inside the order document as shown below:

````
  BsonDocument bsonQuery = BsonDocument
                    .parse("{\"ORDERDATE\":{\"$lte\":ISODate(\"2016-01-01T00:00:00.000Z\") }}");

            this.database.createCollection("mixed_q3_new_joined_orders");

            final MongoCollection<Document> mixed_q3_new_joined_orders = this.database
                    .getCollection("mixed_q3_new_joined_orders");



            this.mixed_order.find(bsonQuery).forEach(new Block<Document>() {
                @Override
                public void apply(final Document order) {
                    BsonDocument customerBsonQuery = BsonDocument
                            .parse("{\"CUSTKEY\":\"" + order.get("CUSTKEY")
                                    + "\"}");

                    order.put("customer", mixed_customer
                            .find(customerBsonQuery).first());

                    mixed_q3_new_joined_orders.insertOne(order);
                }
            });
````

Now we have a new collection called "mixed_q3_new_joined_orders" that will have all the documents needed to run the original sql aggregate query. After we run the aggregate query, we can drop the temporary collection.


Similar to Q1, we will divide the above query to different stages then execute them all at once using the mongoDB aggregation pipeline framework. The stages that we will need are a "match" stage, an "unwind" stage, a "project" stage, a "group" stage, and a "sort" stage. The "match" stage is used to get all the documents that corresponds to the sql query part shown below:

````
where c_mktsegment = '[SEGMENT]' and c_custkey = o_custkey and l_orderkey = o_orderkey and o_orderdate < date '[DATE]' and l_shipdate > date '[DATE]'
````
Except that no need to query the join conditions since we have already represented the relationship between the different objects by embedding documents. The match stage query is shown below:

````
{  
   "$match":{  
      "customer.MKTSEGMENT":"AUTOMOBILE",
      "Items.SHIPDATE":{  
         "$gte": ISODate("1990-01-01T00:00:00.000Z")
      }
   }
}
````

Similarly the unwind stage:

````
{  
   "$unwind":"$Items"
}
````

The project stage:

````
{  
   "$project":{  
      "ORDERDATE":1,
      "SHIPPRIORITY":1,
      "Items.EXTENDEDPRICE":1,
      "l_dis_min_1":{  
         "$subtract":[  
            1,
            "$Items.DISCOUNT"
         ]
      }
   }
}
````

The group stage:

````
{  
   "$group":{  
      "_id":{  
         "ORDERKEY":"$ORDERKEY",
         "ORDERDATE":"$ORDERDATE",
         "SHIPPRIORITY":"$SHIPPRIORITY"
      },
      "revenue":{  
         "$sum":{  
            "$multiply":[  
               "$Items.EXTENDEDPRICE",
               "$l_dis_min_1"
            ]
         }
      }
   }
}
````

Finally the sort stage:

````
{  
   "$sort":{  
      "revenue":1,
      "ORDERDATE":1
   }
}
````


The complete query in mongoDB is shown below:

````
[  
   {  
      "$match":{  
         "customer.MKTSEGMENT":"AUTOMOBILE",
         "Items.SHIPDATE":{  
            "$gte":ISODate("1990-01-01T00:00:00.000Z")
         }
      }
   },
   {  
      "$unwind":"$Items"
   },
   {  
      "$project":{  
         "ORDERDATE":1,
         "SHIPPRIORITY":1,
         "Items.EXTENDEDPRICE":1,
         "l_dis_min_1":{  
            "$subtract":[  
               1,
               "$Items.DISCOUNT"
            ]
         }
      }
   },
   {  
      "$group":{  
         "_id":{  
            "ORDERKEY":"$ORDERKEY",
            "ORDERDATE":"$ORDERDATE",
            "SHIPPRIORITY":"$SHIPPRIORITY"
         },
         "revenue":{  
            "$sum":{  
               "$multiply":[  
                  "$Items.EXTENDEDPRICE",
                  "$l_dis_min_1"
               ]
            }
         }
      }
   },
   {  
      "$sort":{  
         "revenue":1,
         "ORDERDATE":1
      }
   }
]
````

The complete java code for the api that will return the results of TPCH Q3 is shown below:


````
     @GET
    @Path("/q3")
    @Timed
    @ApiOperation(value = "get result of TPCH Q3 using this model", notes = "Returns mongoDB document(s)", response = Document.class, responseContainer = "list")
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public ArrayList<Document> getQ3Results() {

        AggregateIterable<Document> result;

        try {

            BsonDocument bsonQuery = BsonDocument
                    .parse("{\"ORDERDATE\":{\"$lte\":ISODate(\"2016-01-01T00:00:00.000Z\") }}");

            this.database.createCollection("mixed_q3_new_joined_orders");

            final MongoCollection<Document> mixed_q3_new_joined_orders = this.database
                    .getCollection("mixed_q3_new_joined_orders");



            this.mixed_order.find(bsonQuery).forEach(new Block<Document>() {
                @Override
                public void apply(final Document order) {
                    BsonDocument customerBsonQuery = BsonDocument
                            .parse("{\"CUSTKEY\":\"" + order.get("CUSTKEY")
                                    + "\"}");

                    order.put("customer", mixed_customer
                            .find(customerBsonQuery).first());

                    mixed_q3_new_joined_orders.insertOne(order);
                }
            });

            String matchStringQuery = "{\"$match\":{\"customer.MKTSEGMENT\":\"AUTOMOBILE\",\"Items.SHIPDATE\":{\"$gte\": ISODate(\"1990-01-01T00:00:00.000Z\") }}}";

            String unWindStringQuery = "{$unwind: \"$Items\"}";

            String projectStringQuery = "{\"$project\":{\"ORDERDATE\":1,\"SHIPPRIORITY\":1,\"Items.EXTENDEDPRICE\":1,\"l_dis_min_1\":{\"$subtract\":[1,\"$Items.DISCOUNT\"]}}}";

            String groupStringQuery = "{\"$group\":{\"_id\":{\"ORDERKEY\":\"$ORDERKEY\",\"ORDERDATE\":\"$ORDERDATE\",\"SHIPPRIORITY\":\"$SHIPPRIORITY\"},\"revenue\":{\"$sum\":{\"$multiply\":[\"$Items.EXTENDEDPRICE\",\"$l_dis_min_1\"]}}}}";

            String sortStringQuery = "{\"$sort\":{\"revenue\":1,\"ORDERDATE\":1}}";


            BsonDocument matchBsonQuery = BsonDocument.parse(matchStringQuery);

            BsonDocument unWindBsonQuery = BsonDocument
                    .parse(unWindStringQuery);

            BsonDocument projectBsonQuery = BsonDocument
                    .parse(projectStringQuery);

            BsonDocument groupBsonQuery = BsonDocument.parse(groupStringQuery);

            BsonDocument sortBsonQuery = BsonDocument.parse(sortStringQuery);

            ArrayList<Bson> aggregateQuery = new ArrayList<Bson>();

            aggregateQuery.add(matchBsonQuery);
            aggregateQuery.add(unWindBsonQuery);
            aggregateQuery.add(projectBsonQuery);
            aggregateQuery.add(groupBsonQuery);
            aggregateQuery.add(sortBsonQuery);

            result = mixed_q3_new_joined_orders.aggregate(aggregateQuery);

            MongoCursor<Document> iterator = result.iterator();

            ArrayList<Document> results = new ArrayList<Document>();
            while (iterator.hasNext()) {
                Document resultDoc = iterator.next();
                results.add(resultDoc);
            }

            mixed_q3_new_joined_orders.drop();

            return results;
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }
````

If you call the above query, you will get the results below:

![image](https://s3.amazonaws.com/b2cbucket/mixed+q2.png)


##### Order Priority Checking Query (Q4)

This query is used to see how well the order priority system is working and gives indication of the customer satisfaction. The SQL query is shown below:

````
select o_orderpriority, count(*) as order_countfrom
 orderswhere o_orderdate >= date '[DATE]' and o_orderdate < date '[DATE]' + interval '3' month and exists (select *from lineitemwhere l_orderkey = o_orderkey and l_commitdate < l_receiptdate)group by o_orderpriorityorder by o_orderpriority;
````


Similar to Q1 and Q3, we will divide the above query to different stages then execute them all at once using the mongoDB aggregation pipeline framework. The stages we will need are  a "project" stage, a "match" stage, a "group" stage, and a "sort" stage. The "project" is used at the beginning to compare the lineitem COMMITDATE with the RECEIPTDATE and returned a 0 or 1 based on the result of the comparison. Then this value can be used in the following match stage which is shown below:

The first project stage:

````
{  
   "$project":{  
      "ORDERDATE":1,
      "ORDERPRIORITY":1,
      "eq":{  
         "$cond":[  
            {  
               "$lt":[  
                  "$Items.COMMITDATE",
                  "$Items.RECEIPTDATE"
               ]
            },
            0,
            1
         ]
      }
   }
}
````

The following match stage:


````
{  
   "$match":{  
      "ORDERDATE":{  
         "$gte": ISODate("1990-01-01T00:00:00.000Z")
      },
      "ORDERDATE":{  
         "$lt": ISODate("2000-01-01T00:00:00.000Z")
      },
      "eq":{  
         "$eq":1
      }
   }
}
````


The group stage:

````
{  
   "$group":{  
      "_id":{  
         "ORDERPRIORITY":"$ORDERPRIORITY"
      },
      "order_count":{  
         "$sum":1
      }
   }
}
````

Finally the sort stage:

````
{  
   "$sort":{  
      "ORDERPRIORITY":1
   }
}
````


The complete query in mongoDB is shown below:

````
[  
   {  
      "$project":{  
         "ORDERDATE":1,
         "ORDERPRIORITY":1,
         "eq":{  
            "$cond":[  
               {  
                  "$lt":[  
                     "$Items.COMMITDATE",
                     "$Items.RECEIPTDATE"
                  ]
               },
               0,
               1
            ]
         }
      }
   },
   {  
      "$match":{  
         "ORDERDATE":{  
            "$gte": ISODate("1990-01-01T00:00:00.000Z")
         },
         "ORDERDATE":{  
            "$lt": ISODate("2000-01-01T00:00:00.000Z")
         },
         "eq":{  
            "$eq":1
         }
      }
   },
   {  
      "$group":{  
         "_id":{  
            "ORDERPRIORITY":"$ORDERPRIORITY"
         },
         "order_count":{  
            "$sum":1
         }
      }
   },
   {  
      "$sort":{  
         "ORDERPRIORITY":1
      }
   }
]
````

The complete java code for the api that will return the results of TPCH Q4 is shown below:


````
    @GET
    @Path("/q4")
    @Timed
    @ApiOperation(value = "get result of TPCH Q4 using this model", notes = "Returns mongoDB document(s)", response = Document.class, responseContainer = "list")
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public ArrayList<Document> getQ4Results() {

        AggregateIterable<Document> result;

        try {

            String projectStringQuery = "{\"$project\":{\"ORDERDATE\":1,\"ORDERPRIORITY\":1,\"eq\":{\"$cond\":[{\"$lt\":[\"$Items.COMMITDATE\",\"$Items.RECEIPTDATE\"]},0,1]}}}";

            String matchStringQuery = "{\"$match\": {\"ORDERDATE\": {\"$gte\": ISODate(\"1990-01-01T00:00:00.000Z\")},\"ORDERDATE\": {\"$lt\": ISODate(\"2000-01-01T00:00:00.000Z\")},\"eq\":{\"$eq\":1}}}";

            String groupStringQuery = "{\"$group\":{\"_id\":{\"ORDERPRIORITY\":\"$ORDERPRIORITY\"},\"order_count\":{\"$sum\":1}}}";

            String sortStringQuery = "{\"$sort\":{\"ORDERPRIORITY\":1}}";


            BsonDocument projectBsonQuery = BsonDocument
                    .parse(projectStringQuery);

            BsonDocument matchBsonQuery = BsonDocument.parse(matchStringQuery);

            BsonDocument groupBsonQuery = BsonDocument.parse(groupStringQuery);

            BsonDocument sortBsonQuery = BsonDocument.parse(sortStringQuery);

            ArrayList<Bson> aggregateQuery = new ArrayList<Bson>();

            aggregateQuery.add(projectBsonQuery);
            aggregateQuery.add(matchBsonQuery);
            aggregateQuery.add(groupBsonQuery);
            aggregateQuery.add(sortBsonQuery);

            result = mixed_order.aggregate(aggregateQuery);


            MongoCursor<Document> iterator = result.iterator();

            ArrayList<Document> results = new ArrayList<Document>();
            while (iterator.hasNext()) {
                Document resultDoc = iterator.next();
                results.add(resultDoc);
            }

            return results;
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }
````

If you call the above query, you will get the results below:

![image](https://s3.amazonaws.com/b2cbucket/mixed+q4.png)

 
 
 
 
 
 
 


