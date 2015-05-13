---
title: Using shorthands
---

A *shorthand* is a simple way to write a route handler for common API scenarios. For example, a GET request to `/api/users` typically returns a list of users. Instead of writing out the mock by hand

```js
this.get('/api/users', function(db, request) {
  return {
    users: db.users
  };
});
```

we can use the shorthand form

```js
this.get('/api/users', 'users');
```

or more simply

```js
this.get('/api/users');
```

Find the rest of the shorthand descriptions below.

## GET shorthands

*Single collection*

{% capture shorthand %}
{% highlight js %}
this.get('/contacts');          // finds type by singularizing url
this.get('/contacts', 'users'); // optionally specify the collection as second param
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.get('/contacts', function(db, request) {
  return {
    contacts: db.contacts // db.users in the second case
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

*Multiple collections*

{% capture shorthand %}
{% highlight js %}
this.get('/', ['photos', 'articles']);
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.get('/', function(db, request) {
  return {
    photos: db.photos,
    articles: db.articles
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

*Single record*

{% capture shorthand %}
{% highlight js %}
this.get('/contacts/:id');         // finds type by singularizing url
this.get('/contacts/:id', 'user'); // optionally specify the type as second param
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.get('/contacts/:id', function(db, request) {
  var id = request.params.id;

  return {
    contact: db.contacts.find(id)
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

*Single record with related records*

{% capture shorthand %}
{% highlight js %}
this.get('/contacts/:id', ['contact', 'addresses']); // put the owning (singular) model first
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.get('/contacts/:id', function(db, request) {
  var id = request.params.id;

  return {
    contact: db.contacts.find(id),
    addresses: db.addresses.where({contact_id: id})
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

## POST shorthands

*Create a resource*

{% capture shorthand %}
{% highlight js %}
this.post('/contacts');          // finds type by singularizing url
this.post('/contacts', 'user');  // optionally specify the type as second param
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.post('/contacts', function(db, request) {
  var attrs = JSON.parse(request.requestBody);
  var record = db.contacts.insert(attrs);
  
  return {
    contact: record
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

## PUT shorthands

*Update a resource*

{% capture shorthand %}
{% highlight js %}
this.put('/contacts');          // finds type by singularizing url
this.put('/contacts', 'user');  // optionally specify the type as second param
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.put('/contacts/:id', function(db, request) {
  var id = db.params.id;
  var attrs = JSON.parse(request.requestBody)['contact'];
  var record = db.contacts.update(id, attrs);

  return {
    contact: record
  };
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

## DELETE shorthands

*Remove a resource*

{% capture shorthand %}
{% highlight js %}
this.del('/contacts/:id');          // finds type by singularizing url
this.del('/contacts/:id', 'user');  // optionally specify the type as second param
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.put('/contacts/:id', function(db, request) {
  var id = db.params.id;
  db.contacts.remove(id);

  return {};
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

*Remove a resource and related models*

{% capture shorthand %}
{% highlight js %}
this.del('/contacts/:id', ['contact', 'addresses']);
{% endhighlight %}
{% endcapture %}

{% capture expanded %}
{% highlight js %}
this.put('/contacts/:id', function(db, request) {
  var id = db.params.id;
  db.contacts.remove(id);

  var addresses = db.addresses.where({contact_id: id});
  addresses.forEach(function(address) {
    db.addresses.remove(address.id);
  });

  return {};
});
{% endhighlight %}
{% endcapture %}

{% include code-compare.html expanded=expanded shorthand=shorthand %}

---

Next, you'll learn how to seed your database with some starting data, both in development and testing.