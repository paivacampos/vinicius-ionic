# wSQL
wSQL - angularJS active record module for WebSQLite database - database in the browser. U can persist your data for your PhoneGap HTML5 applications build with ionic or angular. Good solution for mobile applications with offline mode.

### install from bower
bower install angular-wsql

### A sample of config file(u need to set your DB schema there) can be found in this repo at src/wSQL.db.config.js (just cope it in your project)

### methods:
* wSQL.select()
* wSQL.insert(table_name, data)
* wSQL.batch_insert(table_name, data)
* wSQL.update(table_name, data)
* wSQL.delete(table_name)

### optionally u can perform query directly with passing SQL
* wSQL.query(custom_sql)

### optionally u can perform queries upon tables
* wSQL.create_table(table_name, table_fields)
* wSQL.drop_table(table_name)

### methods to query request and generate results (all return promises):
* query - get all results
* row   - get one row
* col   - get one column
```javascript

    ...
    .query().then(function(result){
        // query returns promise
        console.log(result);
    }, function(error){
        // error result
        console.log(error);
    })

```

### list of query modifiers:
* where
* where_in
* and
* and_in
* or
* or_in
* join
* left_join
* having
* set
* group_by
* order_by
* limit //limit also does offset as a second argument


## no full DOC yet but samples below -- pretty simple
## also examples can be found in examples/example1

```javascript
angular.module('sampleApp', [
    "wSQL.db"
])
.controller('SampleController', function($scope, wSQL) {

    // SELECT id, category_id FROM table1 WHERE id=? LIMIT 1
    wSQL.select("id, category_id")
        .from("table1")
        .where("id", 3)
        .col()
        .then(function(d){
            console.log(d);
        });

    // SELECT * FROM table1 WHERE id>? AND category_id=? OR category_id=? LIMIT 1
    wSQL.select("*")
        .from("table1")
        .where("id", 3, ">")
        .and("category_id", 15)
        .or("category_id", 14)
        .row()
        .then(function(d){
            console.log(d);
        });

    // SELECT * FROM table1 WHERE id IN (?, ?, ?)
    wSQL.select()
        .from("table1")
        .where_in("id", [3,4,6])
        .query()
        .then(function(d){
            console.log(d);
        });

    // SELECT id, category_id, count(*) as amount FROM table1 GROUP BY category_id
    wSQL.select("id, category_id, count(*) as amount")
        .from("table1")
        .group_by("category_id")
        .query()
        .then(function(d){
            console.log(d);
        });

    // SELECT id, category_id, count(*) as amount FROM table1 GROUP BY category_id HAVING category_id=? OR category_id=?
    wSQL.select("id, category_id, count(*) as amount")
        .from("table1")
        .group_by("category_id")
        .having("category_id", 15)
        .or("category_id", 10)
        .query()
        .then(function(d){
            console.log(d);
        });


    // SELECT id, category_id, count(*) as amount FROM table1 WHERE id=57 GROUP BY category_id HAVING category_id=10
    wSQL.select("id, category_id, count(*) as amount")
        .from("table1")
        .where("id", 57)
        .group_by("category_id")
        .having("category_id", 10)
        .or("category_id", 15)
        .query()
        .then(function(d){
            console.log(d);
        });

    // INSERT INTO table1 (category_id) VALUES (?)
    wSQL.insert("table1", {
        category_id: 14
    }).then(function(insert){
        console.log("insert_id");
        console.log(insert.insertId);
    });

    // INSERT OR IGNORE INTO table1 (category_id,category_name,test_field) SELECT ? as category_id,? as category_name,? as test_field UNION SELECT ?,?,? UNION SELECT ?,?,? UNION SELECT ?,?,?
    wSQL.batch_insert_or_ignore("table1", [
        {
            category_id: rand(),
            category_name: "ttdasdas1",
            test_field: "test_field1"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas2",
            test_field: "test_field2"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas3",
            test_field: "test_field3"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas4",
            test_field: "test_field4"
        }
    ]).then(function(insert){
        console.log("insert_id");
        console.log(insert.insertId);
    });
    
    // INSERT INTO table1 (category_id,category_name,test_field) SELECT ? as category_id,? as category_name,? as test_field UNION SELECT ?,?,? UNION SELECT ?,?,? UNION SELECT ?,?,?
    wSQL.batch_insert("table1", [
        {
            category_id: 1,
            category_name: "ttdasdas1",
            test_field: "test_field1"
        },
        {
            category_id: 2,
            category_name: "ttdasdas2",
            test_field: "test_field2"
        },
        {
            category_id: 3,
            category_name: "ttdasdas3",
            test_field: "test_field3"
        },
        {
            category_id: 10,
            category_name: "ttdasdas4",
            test_field: "test_field4"
        }
    ]).then(function(insert){
        console.log("insert_id");
        console.log(insert.insertId);
    });

    // INSERT OR IGNORE INTO table1 (category_id,category_name,test_field) SELECT ? as category_id,? as category_name,? as test_field UNION SELECT ?,?,? UNION SELECT ?,?,? UNION SELECT ?,?,?
    wSQL.batch_insert("table1", [
        {
            category_id: rand(),
            category_name: "ttdasdas1",
            test_field: "test_field1"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas2",
            test_field: "test_field2"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas3",
            test_field: "test_field3"
        },
        {
            category_id: rand(),
            category_name: "ttdasdas4",
            test_field: "test_field4"
        }
    ],true).then(function(insert){
        console.log("insert_id");
        console.log(insert.insertId);
    });


    // UPDATE table1 SET category_id=? WHERE id=?
    wSQL.update("table1", {category_id: 6})
        .where("id", 19)
        .query()
        .then(function(result){
            console.log(result);
        });

    // DELETE FROM table1 WHERE id = ?
    wSQL.delete("table1")
        .where("id", 18)
        .query()
        .then(function(data){
            console.log(data);
        });

    /**
     * Direct Queries
     * (better to use ORM stuff)
      */
    // Simple direct query
    wSQL.query("SELECT * FROM table1").then(function(result){
        console.log(result);
    });

    // Direct Query with params in SQL
    wSQL.query("SELECT * FROM table1 WHERE id IN (1,5)").then(function(result){
        console.log(result);
    });

    // Pass query params in second argument to avoid escape errors
    wSQL.query("SELECT * FROM table1 WHERE category_name=?", ["'♥,♥,|'♥,♥,♥'"]).then(function(result){
        console.log(result);
    });

    // Or u can get error
    wSQL.query("SELECT * FROM table1 WHERE category_name= '♥,♥,|'♥,♥,♥'").then(function(result){
        console.log(result);
    }, function(error){
        console.log("___error");
        console.log(error);
    });

    /**
     * Like Queries
     */

    wSQL.select()
        .from("table1")
        .like("category_name", "my_query")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "query", "before")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "my_", "after")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "y_quer", "both")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "%query", false)
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "s")
        .and_like("test_field", "field4", "before")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "s")
        .like("test_field", "field4", "before")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .like("category_name", "s")
        .or_like("test_field", "field4", "before")
        .query()
        .then(function(d){
            console.log(d);
        });

    wSQL.select()
        .from("table1")
        .where("test_field", "test_field4")
        .like("category_name", "s")
        .or_like("test_field", "field4", "before")
        .query()
        .then(function(d){
            console.log(d);
        });

});

```