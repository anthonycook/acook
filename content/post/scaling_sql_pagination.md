---
title: Scaling SQL Pagnation To Millions Of Rows
date: 2020-07-21
hero: "/images/hero-2.jpg"
excerpt: Learn how to page your SQL data fast and efficiently
authors:
  - Anthony Cook

---
### The Problem

Pagination with SQL databases is a comma gotcha' for new developers. If you look online you'll be sure to find countless tutorials that tell you to use good old-fashioned OFFSET and LIMIT.

The issue with this method is that it doesn't scale, sure it's fine for tables with a few rows but as your data grows your quires will get slower...and slower until they eventually get to the point where they just timeout.

The reason being is that each time this type of query runs, it needs to cycle though every single row until it finds the desired result set.

Here's a quick example:

```sql
SELECT *
FROM customers
OFFSET 5900
LIMIT 100 
```

If we ran the query, it would have to go through 6000 rows just to return the last 100 rows! Now imagine doing this in a table with hundreds of thousands, or even millions of rows! Our database server would crumble.

### We Can Do Better
Enter cursor navigation, it sounds fancy but it's actually a really simple way of keeping our paginate quires fast and efficient.

All we need for this to work is a unique column in our table rows, a good use case would ID or a timestamp field, and possibly even user data such an email address (although sorting wouldn't be great)

Here's how it works:

```sql
SELECT *
FROM customers
WHERE id > 2353408315855209499
ORDER BY id ASC
LIMIT 100
```
What we are doing in this example is using a customer ID to say "ok, get me all the rows after this specific user ID". We can still use limit to keep the number of results down, but this time we are not scanning the entire table to find our results. We are telling the database engine to go to this exact row.

So how would you call the next page of results I hear you ask? Simple, we look at the results set from the SQL query we just ran and use the last row's user ID in our next query, this will give us the next 100 users and so on.

What's great about this is that if your unique column is seqential or based on time (like my ID's) then you'll be able to sort them by when they were inserted/created.

So that's it folks, just remember if you're still using OFSSET with LIMIT that it's probably time you ditch it and try cursor navigation.

