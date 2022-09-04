---
layout: post
title:  "How to issue POST requests with cURL"
date:   2022-09-04 09:40:47 +0200
categories: curl rails terminal 
---

Say we have a Rails application with an `Article` model that has `title:string` and `body:text`.


In order to create a new article, we visit the endpoint `/new`, where the action `articles#new` renders a simple form to fill.


Upon pressing the submit button, a `POST` reuqest is issued to the `/articles` endpoint with the form data passed in the `params` hash.
This request is handeled by the `articles#create` action, which creates a new article if data are given correctly.


### Using cURL

We can simulate this with `curl`: 

by running the following command from therminal:

```bash
curl -X POST http://127.0.0.1:3000/articles -d "article[title]=How to POST with curl" -d "article[body]=this article was created with curl"
```

* Note that the order of the flags does not matter.

this will pass the following params hash along with the request: (extracted from the server's log)

```
Parameters: {"article"=>{"title"=>"How to POST with curl", "body"=>"this article was created with curl"}}
```


### authenticity token


By default, Rails will block such requests because it adds authenticity token to each request.

We can disable that by adding the following line in `articles` controller, before all the actions:

```rb
skip_before_action :verify_authenticity_token
```

Now if we run the above `curl` command, a new post will be created!
