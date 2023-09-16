**Reference** : https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana

<br/>

# Architecture

![](Images/Official%20Elastic%20Community%20-%20Beginner's%20Crash%20Course%20to%20Elastic%20Stack%20-%20Part%201%20Intro%20to%20Elasticsearch%20and%20Kibana%20[gS_nHTWZEJ8%20-%201536x864%20-%2017m39s].png)

![](Images/Official%20Elastic%20Community%20-%20Beginner's%20Crash%20Course%20to%20Elastic%20Stack%20-%20Part%201%20Intro%20to%20Elasticsearch%20and%20Kibana%20[gS_nHTWZEJ8%20-%201536x864%20-%2017m48s].png)

<br/>
<br/>
<br/>

---
---
---

<br/>
<br/>
<br/>

## Data is actually stored in shards. The search is run on shards
![](Images/Pasted%20image%2020230612223339.png)



> Shards can be scaled across nodes. shards and nodes can be increased as per requirement. This process of sharding can improve search performance. Eg: if the docuemnts are divided into 10 shards, then search can be run on all the shards in parallel effectivly reducing the time 





## Primary shards and replica shards
If primary shard goes down, data will be secured in replica shards
![](Images/Pasted%20image%2020230612224016.png)


![](Images/Pasted%20image%2020230612224109.png)



<br/>
<br/>
<br/>

---
---
---

<br/>
<br/>
<br/>

> Default port for elasticsearch 9200. and kibana 5601


<br/>
<br/>
<br/>

---
---
---

<br/>
<br/>
<br/>


## To debug cluster and nodes initially, can use kibana dev tools

### Get info about cluster health

```
GET _cluster/health
```

<br/>

### Get info about nodes in a cluster

```
GET _nodes/stats
```

<br/>
<br/>
<br/>

---

<br/>
<br/>
<br/>



## Create an index

Syntax:

```
PUT Name-of-the-Index
```

Example:

```
PUT favorite_candy
```

<br/>
<br/>
<br/>

---
<br/>
<br/>
<br/>

## Index a document

When indexing a document, both HTTP verbs `POST` or `PUT` can be used.

1. Use POST when you want Elasticsearch to autogenerate an id for your document.

Syntax:

```
POST Name-of-the-Index/_doc
{
  "field": "value"
}
```

Example:

```
POST favorite_candy/_doc
{
  "first_name": "Lisa",
  "candy": "Sour Skittles"
}
```

<br/>
<br/>
<br/>

---
<br/>
<br/>
<br/>

## Use PUT when you want to assign a specific id to your document(i.e. if your document has a natural identifier - purchase order number, patient id, & etc). For more detailed explanation, check out this [documentation](https://www.elastic.co/guide/en/elasticsearch/guide/current/index-doc.html) from Elastic!

Syntax:

```
PUT Name-of-the-Index/_doc/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```

Example:

```
PUT favorite_candy/_doc/1
{
  "first_name": "John",
  "candy": "Starburst"
}
```

<br/>
<br/>
<br/>

---
<br/>
<br/>
<br/>

**PUT with `_doc` will overwrite an existing document if we use the same id**
To prevent that, should use CREATE endpoint instead

### _create Endpoint

When you index a document using an id that already exists, the existing document is overwritten by the new document. If you do not want a existing document to be overwritten, you can use the _create endpoint!

With the _create Endpoint, no indexing will occur and you will get a 409 error message.

Syntax:

```
PUT Name-of-the-Index/_create/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```

Example:

```
PUT favorite_candy/_create/1
{
  "first_name": "Finn",
  "candy": "Jolly Ranchers"
}
```

<br/>
<br/>
<br/>

---
<br/>
<br/>
<br/>


## Update a document

If you want to update fields in a document, use the following syntax:

```
POST Name-of-the-Index/_update/id-of-the-document-you-want-to-update
{
  "doc": {
    "field1": "value",
    "field2": "value",
  }
} 
```

Example:

```
POST favorite_candy/_update/1
{
  "doc": {
    "candy": "M&M's"
  }
}
```


<br/>
<br/>
<br/>

---
<br/>
<br/>
<br/>


## Delete a document

Syntax:

```
DELETE Name-of-the-Index/_doc/id-of-the-document-you-want-to-delete
```

Example:

```
DELETE favorite_candy/_doc/1
```



---

##  READ

Syntax:

```
GET Name-of-the-Index/_doc/id-of-the-document-you-want-to-retrieve
```

Example:

```
GET favorite_candy/_doc/1
```


