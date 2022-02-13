
# Backend Mongo-Aggregation-pipelines

**Aggregation pipelines create a mock movies table in sequel and mongodb which has 500 documents which has following columns/fields**

- id
- movie_name
- movie_genre
- production_year (between 1990 to 2020)
- budget (9000 to 20000)
- rating (0-10)
- rated (PG or G)
- language
- genres

# create an aggregation query for the following

# using aggregate dbs:
```js
  use aggregate
```

# 1.  

-rating is atleast 8
```js
   db.movie.aggregate([{$match : {'rating ' : {$gte : 8  }  }} ]) 
```
**Picture for above:**
![Screenshot (265)](https://user-images.githubusercontent.com/80479635/153769332-ab27e887-7cda-4df7-be6e-adb50b1f80d9.png)



- genres do not contain "Thriller" or "Romance".
```js
    db.movie.aggregate([{$match : {genre : {$ne : {$in : [ "Thriller", "Romance" ]}  }  }]) 
```
**Picture for above**
![Screenshot (266)](https://user-images.githubusercontent.com/80479635/153769342-cbb5dab7-afad-4835-829e-1da5fc5b4e8f.png)


- rated "PG"
```js
    db.movie.aggregate([{$match : {rated : {$eq : "PG"  }  }} ])
```


- languages contain English or French.
```js
    db.movie.aggregate([{$match : {language : {$in : [ "French", "English" ]}}}])
```
**Picture for above**
![Screenshot (267)](https://user-images.githubusercontent.com/80479635/153769355-f0c4fd62-737f-4894-b6ed-97f102fa1fc3.png)


- production_year after 2012

```js
   db.movie.aggregate([{$match : { production_year : {$gt : 2012 } } }])
```
**Picture for above**
![Screenshot (268)](https://user-images.githubusercontent.com/80479635/153769357-c70b7ad6-8640-436d-9398-73fd3b6ccbc8.png)



# 2. you have been given a task by your manager.

- you have a huge collection of data
- you need to find the total number of duplicates that are found on against a key
- here the duplicates are formed from the name

```js
     { name: "aman", id: 1 } , { name: "albert", id : 2 } { name: "aman", id: 3 } , { name: "albert", id : 4 }  { name :"nrupul", id: 5 } 
```

- in this case required output is
```js
   { name: "aman", duplicates: 1 }, { name: "albert", duplicates: 1 } ,{ name: "nrupul", duplicates: 0 } 
```

- use aggregations and try to solve this

**-insertMany in collection task**
```js
   db.task.insertMany([  { name: "aman", id: 1 } , { name: "albert", id : 2 }, { name: "aman", id: 3 } , { name: "albert", id : 4 },{ name :"nrupul", id: 5 } ])
```

**-Required Query and Output**
```js
  db.task.aggregate([ {$group : { _id : "$name", count : {$sum :1}}},{$match : { _id : { $ne : null},"count" : { $gt : 1}  }},{$project : { name : "$_id", "_id" : 0, duplicates : "$count" } }  ])
```
**Output->**
[ { name: 'aman', duplicates: 2 }, { name: 'albert', duplicates: 2 } ]

**-OR**
```js
  db.task.aggregate( [{$match : {}}, {$group : { _id : "$name", duplicates: {$sum :1}}} ])
```
**Output->**
[
  { _id: 'aman', duplicates: 2 },
  { _id: 'nrupul', duplicates: 1 },
  { _id: 'albert', duplicates: 2 }
]

**Picture for above**
![Screenshot (269)](https://user-images.githubusercontent.com/80479635/153769365-1ef564ac-084e-4146-8a1b-2242baa88563.png)



# 3. 

- you have a set of data in a collection in the following manner ```
- city
- email
- order_id```
- there can be duplicate emails
- you need to filter out the total no of unique emails on each city
- you can use aggregation for this

**-Query for above problem**

- isertMany in collection cities 
```js
   db.cities.insertMany([ { "city": "Agra", "id": 1, "email": "abc@mail.com" },{ "city": "Agra", "id": 2, "email": "abd@mail.com" },{ "city": "Mumbai", "id": 3, "email": "mh@mail.com" },{ "city": "Bangalore", "id": 4, "email": "ban@mail.com" }, { "city": "Mumbai", "id": 5, "email": "mht@mail.com" }])  
```

- Solutions for above query
```js
  db.cities.aggregate([ {$group : { _id : "$email", count : {$sum :1}}},{$match : { _id : { $ne : null},"count" : 1}},{$project : { Unique_email_ID : "$_id", "_id" : 0, emailAppearedCount : "$count" }}])
```

**Output->**
[
  { Unique_email_ID: 'mh@mail.com', emailAppearedCount: 1 },
  { Unique_email_ID: 'abd@mail.com', emailAppearedCount: 1 },
  { Unique_email_ID: 'ban@mail.com', emailAppearedCount: 1 },
  { Unique_email_ID: 'mht@mail.com', emailAppearedCount: 1 }
]

**Picture for above query**
![Screenshot (270)](https://user-images.githubusercontent.com/80479635/153769375-248aaa65-b723-4618-acd1-0ca6c87739d5.png)



# 4.

- create a database with multiple collections
- lets assume you are building an ecommerce application and you need to create a system for that
-users collection with all relevant users information: name, email, country, address ( nested object etc )
-all categories
-products: sku, id, item, qty, price with category information
- orders collection with: containing information of user, and purchases made, and all required values, transaction details

**Querries for above problem**

**users collection for user details**
```js
 db.users.insertMany([{"name": "Satya","email": "thesatyamall123@gmail.com","country": "India", "address": {"village": "Parsauna Buzurg","post": "Kurmauta Manjharia" ,"district": "Kushinagar","state": "Uttar Pradesh","pin": 274402}}])
```

**products collections for products details**
```js
 db.products.insertMany([{"id": 1,"item": "Shirt","qty": 2,"price": {"low": 200,"midium": 350,"high": 500}}])
```

**orders collections for order**
```js
db.orders.insertMany([
    {
        "user":{"name": "Satya","email": "thesatyamall123@gmail.com","country": "India", "address": {"village": "Parsauna Buzurg","post": "Kurmauta Manjharia" ,"district": "Kushinagar","state": "Uttar Pradesh","pin": 274402}},
        "purchases":{"id": 1,"item": "Shirt","qty": 2,"price": {"low": 200,"midium": 350,"high": 500}},
        "transaction":{
            "card": "Rupay",
            "card_number": "4050 6070 8090 4354",
            "card_holder": "Satya",
            "amount": 1000
        }
    }
])
```

**picture for above**
![Screenshot (271)](https://user-images.githubusercontent.com/80479635/153769388-b8f19ecc-e84d-47f1-8e80-9a1afe6f96ac.png)
