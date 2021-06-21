---
layout: post
title:  "Design Patterns: Observer - Part 1"
date:   2021-01-09 22:07:00 +02
categories: design-patterns
---

this pattern is a behavioral pattern, taken from the [classic book of the gang of four](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented-dp-0201633612/dp/0201633612/ref=mt_other?_encoding=UTF8&me=&qid=).
bellow is my implementation in typescript with a concrete example.


```ts
abstract class Observer {
    abstract update(): void;
}

abstract class Subject {
    abstract subscribe(o: Observer): void;
    abstract unsubscribe(o: Observer): void; 
    abstract notifyAll(): void;
}
```

Once we have these interfaces, we can go ahead and define the concrete classes:

```ts
class Singer extends Subject {

    private _state: Number;
    private _subscribers: Observer[];

    constructor(state = 0) {
        super();
        this._state = state;
        this._subscribers = [];
        console.log("a new singer created, with state: " + this.state);
    }

    subscribe(o: Observer): void {
        this._subscribers.push(o);

        console.log("current subscribers: " + this._subscribers.toString());
    }

    unsubscribe(o: Observer): void {
        const i = this._subscribers.indexOf(o);
        this._subscribers.splice(i, 1);

        console.log("current subscribers: " + this._subscribers.toString());
    }

    notifyAll(): void {
        for(let o of this._subscribers) {
            o.update();
        }
        console.log("All subscribers have been notified!");
    }

    get state() {
        return this._state;
    }

    set state(val: Number) {
        this._state = val;
        this.notifyAll();
    }
}


class Groopie extends Observer {
    private _state: Number = 0;
    private _singer: Singer = null as any;

    update(): void {
        this._state = this._singer.state;
        console.log("state updated: current value: " + this._state);
    }

    get state(): Number {
        return this._state;
    }

    constructor(singer: Singer) {
        super();
        this._singer = singer;
        this._singer.subscribe(this);
        this.update();
    }

}


const slash = new Singer(0);
const jane = new Groopie(slash);
console.log(jane.state);

slash.state = 2;
const john = new Groopie(slash);

console.log(jane.state);
console.log(john.state);

slash.unsubscribe(jane);

slash.state = 8;
console.log(jane.state);
console.log(john.state);

slash.subscribe(jane);

slash.state = 16;
console.log(jane.state);
console.log(john.state);
```


```ts
abstract class Observer {
    abstract update(): void;
}

abstract class Subject {
    abstract subscribe(o: Observer): void;
    abstract unsubscribe(o: Observer): void; 
    abstract notifyAll(): vo...
```

Play with this code in [TypeScript Playground](https://www.typescriptlang.org/play?#code/IYIwzgLgTsDGEAJYBthjAg8uAplAbnggN4BQCFCokM8CArgA4AmwEOAFAJQBcC+AewCWzANykAvqVLVocRCjQYAyvRAArHHTKUq4OXTBqwsKEJCcBfbGDyEovfsLHlKs2onoA7I+FPnLa1wCPEdBEVEEVwp3eQQvAQghADMATwBBZGRuPnCXKWlFdARlIS8AcyIcAA92L2YVNU1taV1GM3w2HAQAfUguvgA5egBbCyhxNo6u3t8TM3GwINsQqABtAF1xaKQBH2h6eAEoDn72BABeBAAGLhId3SNGPG5J3UoIAAshMAA6PogMyuZxwb3eCC+P3+c38i0uCE2YPesD2YAEyBwv2QAnKHAARMB4jgAO4IMBlSpQJBQHBdZgAGgQxKEXzJgPYfDxCAA1BDvn8QVwkQVHsZYYEsMF7GFnPdwRRIX8+mKFng-ox6GBPhwBELWvKUT50Zjsbi8bB6FAaV5EDDVVAlggubzFdCVQEHb8IAJlNAKdw9boRZRvHaAjrlnZQrlZToDajEEJ4a7lX57X8yswaphkjrA-KU2HFr8wIxkEJYJwhIyAIx6h6UQ1ojFYnH4i1WnA2snuxacnl8qGp+Yev7e31mCoB4X6igJJJpTLZGUiOXy5LHDgYxACBACZKDpVFtV3OPyygCX5MVjsV4NihSc9N42ts1Lntp0cIT7AQgICxdvEiQpEIODMAAhHi+YPrOCCVLa7KcKe94IDSECWl4h7QohM47LYCFdBwnTIEMozjMh56Foh8LEUiuiuvOKQZFkd5BpI0ikEUGAAOJQAIAiMKBCA1HUDSSis9hrhQ7RCJ05wAgMCDDGMRBXNcSIyXJ3R9BSeB8KUFSqfE9BZFQGDAF4qTbLo16ESuzBSe8VFAlhOmGVAJY4Shz4tqa+IggwLB0nwHbWogxH0Dg-Yuvy2FdNBCDBhQ8FsnZpEqVSZ7vGhGGuSCuG6E2BxHCc5LufpukOI5orPCcCX0bFbmUvCZWUnRHyNa1eAlr2nCKvVHVQrZt4JQUBScQmZKoFq8JeCSJSVRwtziEVCDqBZ3RXHNpK8fxgmcGA02fHqPkmm261zZ58XbIdaCfFd5xXAATCtk3qAInyYVt827QJoGnEd9ana+HAXZigqvUavnnR9XgPTg9a3VqV4+L1oMbYjR3w-CAAckPNmduJg-DJ2oi+fnvZ9JM3Vjx7o3NmN3djVw1gAbPj5PnRt1MTVDhOg7DPOkEAA)


### Variations:

In the next post we will discuss and implement the following scenario: 
What if the observers can subscribe to multiple subjects?

This arises several issues about redunant notifications, we will see two approaches to this problem.


stay tuned!