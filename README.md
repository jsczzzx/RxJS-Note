## Basics

```typescript
import { Observable } from "rxjs";

const observable$ = new Observable<string>(subscriber => {
  console.log('Observable executed');
  subscriber.next('Alice');
  setTimeout(() => subscriber.next('Ben'), 2000);
  setTimeout(() => subscriber.next('Charlie'), 4000);
  setTimeout(() => subscriber.complete(), 6000);
});

//const subscription = observable$.subscribe(value => console.log(value));

observable$.subscribe({
  next: value => console.log(value),
  error: err => console.log(err.message),
  complete: () => console.log('Completed')
});

/*setTimeout(() => {
  console.log('Unsubscribe');
  subscription.unsubscribe();
}, 3000);*/
```

### Teardown
```typescript
import { Observable } from 'rxjs';

// Create a custom observable
const customInterval$ = new Observable(subscriber => {
  // Set up the interval
  const intervalId = setInterval(() => {
    subscriber.next('Interval event');
  }, 1000);

  // Return a teardown function that clears the interval
  return () => {
    clearInterval(intervalId);
    console.log('Interval cleared');
  };
});

// Subscribe to the observable
const subscription = customInterval$.subscribe({
  next: value => console.log(value),
  complete: () => console.log('Complete'),
  error: err => console.error(err)
});

// Unsubscribe after 5 seconds to trigger teardown
setTimeout(() => {
  subscription.unsubscribe();
  console.log('Unsubscribed');
}, 5000);

```

## Creation Functions

### of
Converts the arguments to an observable sequence, each argument becomes a next notification.
![image](https://github.com/user-attachments/assets/e9f0f6ff-4462-4c35-802f-cb7174cac273)

```typescript
import { of } from 'rxjs';
 
of(10, 20, 30)
  .subscribe({
    next: value => console.log('next:', value),
    error: err => console.log('error:', err),
    complete: () => console.log('the end'),
  });
```

### from
Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object.

![image](https://github.com/user-attachments/assets/4637d669-896e-44e7-9f27-2ae5859dbcb4)

```typescript
import { from } from 'rxjs';
 
from([10, 20, 30])
  .subscribe({
    next: value => console.log('next:', value),
    error: err => console.log('error:', err),
    complete: () => console.log('the end'),
  });
```

```typescript
import { from } from "rxjs";

const somePromise = new Promise((resolve, reject) => {
  resolve('Resolved!');
  //reject('Rejected!');
});

const observableFromPromise$ = from(somePromise);

observableFromPromise$.subscribe({
  next: value => console.log(value),
  error: err => console.log('Error:', err),
  complete: () => console.log('Completed')
});
```

![image](https://github.com/user-attachments/assets/09b4c940-79c7-4ba4-a517-b0ecb3c2b36e)
![image](https://github.com/user-attachments/assets/b222a9d4-1295-4d2a-808b-54e6219ffcd5)


### fromEvent
Creates an Observable that emits events of a specific type coming from the given event target.

```typescript
import { fromEvent, Observable } from 'rxjs';

const triggerButton = document.querySelector('button#trigger');

const subscription = fromEvent<MouseEvent>(triggerButton, 'click').subscribe(
  (event) => console.log(event.type, event.x, event.y)
);
```

![image](https://github.com/user-attachments/assets/878ed304-c983-489f-89e3-ef077e1dc818)


###

## Pipeable Operators

### filter
It only emits a value from the source if it passes a criterion function.

![image](https://github.com/user-attachments/assets/3bc8b045-ab81-40c2-a46c-4aa02ad9d847)

### map
It passes each source value through a transformation function to get corresponding output values.

![image](https://github.com/user-attachments/assets/7cba659b-a18c-42f4-be50-94562a09d177)

### tap
Used when you want to affect outside state with a notification without altering the notification.

![image](https://github.com/user-attachments/assets/e5a71a23-ffbf-4153-827e-60545010d1eb)


```typescript
import { of } from "rxjs";
import { filter, map, tap } from "rxjs/operators";


of(1, 7, 3, 6, 2).pipe(
  filter(value => value > 5),
  map(value => value * 2),
  tap({
    next: value => console.log('Spy:', value)
  }),
).subscribe(value => console.log('Output:', value));
```
### debounceTime
It passes only the most recent notification from each burst of emissions.

![image](https://github.com/user-attachments/assets/907a8173-8088-49d4-8471-a8dbefd216bc)

```typescript
import { fromEvent } from "rxjs";
import { debounceTime, map } from "rxjs/operators";

const sliderInput = document.querySelector('input#slider');

fromEvent(sliderInput, 'input').pipe(
  debounceTime(1000),
  map(event => event.target['value'])
).subscribe(value => console.log(value));

```

![ezgif-5-ed69109059](https://github.com/user-attachments/assets/bce5bd57-fd44-4b5c-8a12-c883c86661ff)


## Flattening Operators

### concatMap
Projects each source value to an Observable which is merged in the output Observable, in a serialized fashion waiting for each one to complete before merging the next.

![vpzm7ji2ttq5k558ynpp](https://github.com/user-attachments/assets/96d61d55-baa3-44aa-a955-ec503f0af3f4)

### switchMap
Projects each source value to an Observable which is merged in the output Observable, emitting values only from the most recently projected Observable.

![2xcccqp1v6cmk0kilgpa](https://github.com/user-attachments/assets/eda85eec-1e98-46a3-a84e-2677955ad4d4)

### mergeMap
Projects each source value to an Observable which is merged in the output Observable.

![2xcccqp1v6cmk0kilgpa](https://github.com/user-attachments/assets/99268797-7bc7-4dc7-9dc1-1a5f86dd0a54)


### exhasustMap
Projects each source value to an Observable which is merged in the output Observable only if the previous projected Observable has completed.

![larajwr99yeric3yrtbq](https://github.com/user-attachments/assets/e8042481-cf5a-4ca6-98aa-ba2239a8c049)


### Analogy

Let's compare all these operators with the handling of luggage at an airport. At this airport, there is only one conveyor belt to handle luggage. When an airplane arrives, all luggage is put on the conveyor belt so each traveler can retrieve their luggage.

#### With mergeMap
When an airplane arrives, the luggage is put on the conveyor belt, one after the other. When another airplane arrives as well, the luggage of that one is also put on the conveyor belt, merged with the luggage of the other airplane. So the conveyor belt will show a mix of luggage from the first and second airplane.

#### With switchMap
When an airplane arrives, the luggage is put on the conveyor belt, one after the other. But as soon as another airplane arrives, they stop putting any luggage from the previous airplane on the conveyor belt. They cancel any further luggage handling for that airplane and begin handling the luggage for the newly arrived airplane.

#### With concatMap
When an airplane arrives, the luggage is put on the conveyor belt, one after the other. When a new airplane arrives, all the luggage from the first airplane is put on the conveyor belt. Only when all that luggage is handled will the handling of the luggage on the second airplane start.

#### With exhaustMap
When an airplane arrives, the luggage is put on the conveyor belt, one after the other. When a new airplane arrives and the luggage of the first airplane is still handled, the luggage handling personnel simply ignores the second airplane. The luggage on the second airplane will not be handled.

#### TLDR
mergeMap: come in, merge, come out.

switchMap: come in, merge, come out, but cancel when new ones come in.

concatMap: come in, merge, come out, but maintain order.

exhaustMap: come in, merge, come out, but while ongoing, ignore new ones when they come in.

![image](https://github.com/user-attachments/assets/6cd8db15-1852-4d35-b7ea-f894e6e165cb)



## Hot vs Cold
### Cold Observables
- Cold observables are "cold" because they start emitting items only when a subscriber subscribes to them.
- Each subscriber gets its own independent execution of the observable sequence.
- Example: of(1, 2, 3) or from([1, 2, 3]).

### Hot Observables
- Hot observables are "hot" because they begin emitting items immediately, regardless of whether there are any subscribers.
- All subscribers share the same execution of the observable sequence.
- Example: fromEvent(document, 'click'), Subject.

## Subject
An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.

### Why Subject is Hot
A Subject in RxJS is a hot observable because:

#### Shared Execution:

When you subscribe to a Subject, you are subscribing to an already ongoing execution. All subscribers share the same instance of the subject and thus the same source of data.

#### Immediate Emission:

A Subject can emit values even if no one is subscribed yet. These values will not be buffered for future subscribers; they will miss the values emitted before they subscribed.

### Difference between Observables and Subjects
#### Observable

```typescript
import { Observable } from 'rxjs';

// Create a cold observable that emits a random value
const coldObservable$ = new Observable(subscriber => {
  const randomValue = Math.random();
  subscriber.next(randomValue);
  subscriber.complete();
});

// First subscriber
coldObservable$.subscribe({
  next: (v) => console.log(`Cold Observer A: ${v}`),
  complete: () => console.log('Cold Observer A Complete')
});

// Second subscriber
coldObservable$.subscribe({
  next: (v) => console.log(`Cold Observer B: ${v}`),
  complete: () => console.log('Cold Observer B Complete')
});
```

Output:
```less
Cold Observer A: 0.123456789  (random value)
Cold Observer A Complete
Cold Observer B: 0.987654321  (another random value)
Cold Observer B Complete
```

#### Subject
```typescript
import { Subject } from 'rxjs';

const subject = new Subject();

// Emit a random value
const randomValue = Math.random();
subject.next(randomValue);

// First subscriber
subject.subscribe({
  next: (v) => console.log(`Hot Observer A: ${v}`),
  complete: () => console.log('Hot Observer A Complete')
});

// Emit another random value
const anotherRandomValue = Math.random();
subject.next(anotherRandomValue);

// Second subscriber
subject.subscribe({
  next: (v) => console.log(`Hot Observer B: ${v}`),
  complete: () => console.log('Hot Observer B Complete')
});

// Emit another value
const yetAnotherRandomValue = Math.random();
subject.next(yetAnotherRandomValue);
```

Output:
```less
Hot Observer A: 0.123456789  (first random value)
Hot Observer A: 0.987654321  (second random value)
Hot Observer B: 0.987654321  (second random value)
Hot Observer A: 0.567890123  (third random value)
Hot Observer B: 0.567890123  (third random value)
```

### BehaviorSubject
Not finished yet.
