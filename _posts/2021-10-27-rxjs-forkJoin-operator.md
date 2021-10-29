---
layout: post
title:  "rxjs ForkJoin operator - Use case with Angular"
date:   2021-10-27 20:40:47 +0200
categories: rxjs Angular TypeScript
---

## Scenario

Say we have a backend, which exposes a RESTful API to the Posts resource. In particular, these routes are avaialable:

```
GET	/posts
GET	/posts/:id
POST	/posts
PUT	/posts/:id
PATCH	/posts/:id
DELETE	/posts/:id
```

The frontend is an Angular application, with a dedicated service called `ApiService` to communicate with the backend Posts API:

We want to implement all CRUD operations on posts, using the `rxjs` library.


## The frontend Api Service

The frontend `ApiService` looks something like this:

```typescript
@Injectable({ providedIn: 'root' })
export class ApiService {
  BASE_URL = 'https://jsonplaceholder.typicode.com';
  httpOptions = {};
  constructor(private http: HttpClient) {}

  getPost(id: number): Observable<Post> {
    return this.http.get<Post>(`${this.BASE_URL}/posts/${id}`);
  }

  getPosts(ids: number[]): Observable<Post[]> {
   // return ??? 
  }
}

```

Given that we have implemented the `getPost(id)` method to get a single post, how do we implement `getPosts(ids)`?

One approach is to implement a `getAllPosts()` method, and then use it inside `getPosts(ids)` to filter the desired posts:



But for the sake of this blogpost, say we don't want to use this option, but rather use `getPost` to do it.

One approach would be to subscribe to each post, and then push it to an array of posts:

```typescript
getPosts(ids: number[]): Observable<Post[]> {
	const posts = [];
	ids.forEach(id => this.getPost(id).subscribe(post => posts.push(post)));
	return of(posts); 
}
```

Here, we iterated the post ids array, and for each post, subscribed to `getPost(id)` and when the post arrived, we pused it to the posts array.

Notice that we used the `rxjs` [of function](https://rxjs.dev/api/index/function/of) to return the posts array as an observable.

However, this approach has a major problem:

Since each `getPost()` is asynchronous, posts may arrive at diffent order, from the order of given ids. Furthermore, this order might change
every time `getPosts` is called.

So if in our template we want to display posts in the given order, this would not work.

Besides, I don't think it is a good practice to subscribe to inner observable in this manner.


## The solution

We can use the [forkJoin](https://rxjs.dev/api/index/function/forkJoin) operator to combine all post observables to a single one:
```
getPosts(ids: number[]): Observable<Post[]> {
    return forkJoin(ids.map(id => this.getPost(id));
}
```

The benefit of this operator is that it emits an array of values in the exact same order as the passed array. Thus, our posts will be displayed in 
the correct order in the template. And also, we now don't need to subscribe to each post explicitely.



## The full service

For reference, the full service implementation is given here. 


```typescript
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { forkJoin, Observable, of } from 'rxjs';
import { Post } from './interfaces/post.interface';

const httpOptions = {
  headers: new HttpHeaders({
    'Content-Type': 'application/json',
  }),
};

@Injectable({ providedIn: 'root' })
export class ApiService {
  BASE_URL = 'https://jsonplaceholder.typicode.com';
  httpOptions = {};
  constructor(private http: HttpClient) {}

  getPost(id: number): Observable<Post> {
    return this.http.get<Post>(`${this.BASE_URL}/posts/${id}`);
  }

  getPosts(ids: number[]): Observable<Post[]> {
    return forkJoin(ids.map(id => this.getPost(id)));
  }

  addPost(post: Post): Observable<Post> {
    const url = `${this.BASE_URL}/posts`;
    return this.http.post<Post>(url, post, httpOptions);
  }

  updatePost(post: Post): Observable<Post> {
    return this.http.put<Post>(
      `${this.BASE_URL}/posts/${post.id}`,
      post,
      httpOptions
    );
  }

  deletePost(id: number): Observable<any> {
    const url = `${this.BASE_URL}/posts/${id}`;
    return this.http.delete(url, httpOptions);
  }
}
```

## Stackblitz demo

I have careated a [demo Angular app in Stackbitz](https://stackblitz.com/edit/angular-ivy-e7ttsq?file=src%2Fapp%2Fapi.service.ts), with the given sevice, using fake API calls to [jsonplaceholder site](https://jsonplaceholder.typicode.com/) (an excellent resource for that), instead of a real backend. You can fiddle with the code there.

