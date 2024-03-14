
## Observables

RXJS implements observables as a way of achieving "Reactive programming", Described on the angular website as *an object that can emit one or more values over time*. An EventEmitter is just a wrapper around a basic observable. 

Observables are explained quite well in detail here https://rxjs.dev/guide/observable
### Subjects
In RXJS a base observable object is unicast, ie it only allows you to emit to one subscriber. This is not very useful for reactive programming so RXJS also implements things called subjects. A subject is an object that behaves somewhat similarly to an observable but it that allows you to multicast values to multiple different observers.  
Thea 

### Building on observables
RXJS Observables use something called "Declarative programming". This is in contrast from the more mainstream "Imperative programming". Generally speaking declarative programming requires a bit of a change in mindset to wrap your head around, however I found that it is really similar to using the `.filter()`, `.map()`, `.sort()`, and `.concat()` methods to manipulate arrays, which are all declarative methods themselves.. 

The easiest example of declarative vs imperative programming are these two ways of incrementing every element in an array. 
##### Imperative approach

```ts
let arr = [1, 2, 3, 4, 5];
for(let i = 0; i < arr.length; i++) {
	arr[i] = arr[i] + 1;
}

console.log(arr); // [2, 3, 4, 5, 6]
```

This approach is considered imperative because you are using code to specifically describe how to increments in an array in basic terms. First looping through the array then adding one to each element. 

##### Declarative Approach
```ts
let arr = [1, 2, 3, 4, 5];

arr.map(num => num + 1);

console.log(arr); // [2, 3, 4, 5, 6]
```

This different approach achieves the same end result, however instead of specifying the individual steps of how to modify the array, it uses the `map()` method; 

#### In RXJS 
RXJS uses declarative style programming heavily in its observable operators. On any observable you can call the `.pipe()` method, and in here you can add any amount of comma separated operators to modify the value stream before it is consumed. Here values are passed in order from one operator to before finally being emitted by the observable. 

One example of this is the `.debounceTime()` operator that is used on subscriptions for inputs before an API call, this takes in a time value as a parameter and waits until there has been no events for that amount of time before emitting the most recent value.

There are also some other simple operators that are used frequently, `map()` works like `Array.map()` in that it allows you to mutate the value emitted before passing it along. `filter()` also works like it's array counterpart, except it filters observables and only passes on values that meet the criteria of it's predicate. 

```ts
const keyup$ = fromEvent<KeyboardEvent>(document, 'keyup'); // Create an event from the keyup listener

keyup$.pipe(
	filter(event => event.shiftKey) // Only take events when the shift key is held
	map(event => 'Key pressed: ' + event.key)
).subscribe(console.log);

```

This snippet will print to console every time a key is pressed with the shift key held in the format:
"Key pressed: a", "Key pressed: J". 



### Memory Safety


### Angular Interop
