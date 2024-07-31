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

const subscription = observable$.subscribe(value => console.log(value));

setTimeout(() => {
  console.log('Unsubscribe');
  subscription.unsubscribe();
}, 3000);
```

## Hot vs Cold


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

### switchMap
Projects each source value to an Observable which is merged in the output Observable, emitting values only from the most recently projected Observable.



### mergeMap
Projects each source value to an Observable which is merged in the output Observable.

## Subjects
