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

### switchMap

### mergeMap


## Subjects
