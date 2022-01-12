# Types


## Tuples
```typescript
const person: {
  role: [number, string]; // <-------------
} = {
  role: [2, 'author'] // <-------------
};
```


## Enums
```typescript
enum Role { ADMIN, READ_ONLY, AUTHOR };
enum Role { ADMIN = 5, READ_ONLY, AUTHOR = 500 };
enum Role { ADMIN = 'ADMIN', READ_ONLY = 100, AUTHOR = 'AUTHOR' };
 ```
 
 
## Union
```typescript
type Combinable = number | string;
```


## Literal
```typescript
type ConversionDescriptor = 'as-number' | 'as-text';
```


## unknown
- better than `any` - you have to explicit check a type
```typescript
let userInput: unknown;
let userName: string;

userInput = 5;
userInput = 'Max';
if (typeof userInput === 'string') { // unknown needs explicit type check
  userName = userInput;
}
```


## never
- no return
```typescript
function generateError(message: string, code: number): never {
  throw { message: message, errorCode: code };
  // while (true) {}
}
```


## Index properties
- it fits for general specification
```typescript
interface ErrorContainer { // { email: 'Not a valid email', username: 'Must start with a character!' }
  [prop: string]: string; // <-----------------------
}

const errorBag: ErrorContainer = {
  email: 'Not a valid email!',
  username: 'Must start with a capital character!'
};
```


## Function Overloads
- define return type depend on params
```typescript
type Combinable = string | number;
type Numeric = number | boolean;

type Universal = Combinable & Numeric;

function add(a: number, b: number): number;
function add(a: string, b: string): string;
function add(a: string, b: number): string;
function add(a: number, b: string): string;
function add(a: Combinable, b: Combinable) {
  if (typeof a === 'string' || typeof b === 'string') {
    return a.toString() + b.toString();
  }
  return a + b;
}
```


## Nullish coalescing
```typescript
const userInput = undefined;

const storedData = userInput ?? 'DEFAULT'; // <--------------

console.log(storedData);
```


# Class


## Shorthand
```typescript
class Department {
  // private id: string;
  // private name: string;
  private employees: string[] = [];

  constructor(private id: string, public name: string) {
    // this.id = id;
    // this.name = n;
  }
}
```


## readonly
- value can be init only
```typescript
class Department {
  // private readonly id: string;
  // private name: string;
  private employees: string[] = [];

  constructor(private readonly id: string, public name: string) {
    // this.id = id;
    // this.name = n;
  }
}
```


## Singleton
```typescript
class Department{
  private static instance: Department;

  private constructor(id: string, private reports: string[]) {
  }

  static getInstance() {
    if (Department.instance) {
      return this.instance;
    }
    this.instance = new Department('d2', []);
    return this.instance;
  }
}
```


## TypeGuard (class only, not interface)
```typescript
type Vehicle = Car | Truck;

const v1 = new Car();
const v2 = new Truck();

function useVehicle(vehicle: Vehicle) {
  vehicle.drive();
  if (vehicle instanceof Truck) { //<------------------------
    vehicle.loadCargo(1000);
  }
}
```


## Discriminated Unions (TypeGuard for interface)
- you can not check interface in TS
- this is workaround how to check interface
```typescript
interface Bird {
  type: 'bird'; // <-------------------------------------
  flyingSpeed: number;
}

interface Horse {
  type: 'horse'; // <-------------------------------------
  runningSpeed: number;
}

type Animal = Bird | Horse;

function moveAnimal(animal: Animal) {
  let speed;
  switch (animal.type) {
    case 'bird': // <-------------------------------------
      speed = animal.flyingSpeed;
      break;
    case 'horse': // <-------------------------------------
      speed = animal.runningSpeed;
  }
  console.log('Moving at speed: ' + speed);
}
```


## Generic (constraints)
- it is possible to use generic types for constrainsts
```typescript
function merge<T extends object, U extends object>(objA: T, objB: U) {
  return Object.assign(objA, objB);
}
```
- for example, object must contains length property
```typescript
interface Lengthy {
  length: number;
}

function countAndDescribe<T extends Lengthy>(element: T): [T, string] {
  let descriptionText = 'Got no value.';
  if (element.length === 1) {
    descriptionText = 'Got 1 element.';
  } else if (element.length > 1) {
    descriptionText = 'Got ' + element.length + ' elements.';
  }
  return [element, descriptionText];
}

console.log(countAndDescribe(['Sports', 'Cooking']));
```
- `keyof`
```typescript
function extractAndConvert<T extends object, U extends keyof T>(
  obj: T,
  key: U
) {
  return 'Value: ' + obj[key];
}

extractAndConvert({ name: 'Max' }, 'name');
```


# Utils (build in types)


## Partial
- with partial type is possible to set required values of class/interface in multiple steps
- after a new object is inited it can be converted back to the original class/interface
```typescript
interface CourseGoal {
  title: string;
  description: string;
  completeUntil: Date;
}

function createCourseGoal(
  title: string,
  description: string,
  date: Date
): CourseGoal {
  let courseGoal: Partial<CourseGoal> = {}; // muzeme inicializovat prazdnym objektem
  courseGoal.title = title;
  courseGoal.description = description;
  courseGoal.completeUntil = date;
  return courseGoal as CourseGoal; // prevedeni nazpet
}
```


## Readonly
- it protects variables from changes
```typescript
const names: Readonly<string[]> = ['Max', 'Anna'];
// names.push('Manu'); <------------------ error
// names.pop(); <------------------ error
```
