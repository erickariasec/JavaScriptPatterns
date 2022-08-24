# Proxy Pattern  

**Intercept and control interactions to target objects**  

<hr>

With a Proxy object, we get more control over the interactions with certain objects. A proxy object can determine the behavior whenever we're interacting with the object, for example when we're getting a value, or setting a value.

<hr>

Generally speaking, a proxy means a stand-in for someone else. Instead of speaking to that person directly, you'll speak to the proxy person who will represent the person you were trying to reach. The same happens in JavaScript: instead of interacting with the target object directly, we'll interact with the Proxy object.  

<hr>  

Let's create a person object, that represents John Doe.  

```js
const person = {
    name: "John Doe",
    age: 42,
    nationality: "American"
};
```  

Instead of interacting with this object directly, we want to interact with a proxy object. In JavaScript, we can easily create a new proxy by creating a new instance of `Proxy`.  

```js
const person = {
    name: "John Doe",
    age: 42,
    nationality: "American"
};

const personProxy = new Proxy(person, {});
```  

The second argument of `Proxy` is an object that represents the handler. In the handler object, we can define specific behavior based on the type of interaction. Although there are many methods that you can add to the Proxy handler, the two most common ones are `get` and `set`:

- `get`: Gets invoked when trying to **access** a property
- `set`: Gets invoked when trying to **modify** a property  

Instead of interacting with the `person` object directly, we'll be interacting with the `personProxy`.

Let's add handlers to the `personProxy` Proxy. When trying to modify a property, thus invoking the `set` method on the `Proxy`, we want the proxy to log the previous value and the new value of the property. When trying to access a property, thus invoking the `get` method on the `Proxy`, we want the proxy to log a more readable sentence that contains the key and value of the property.  

```js
const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        console.log(`The value of ${prop} is ${obj[prop]}`);
    },
    set: (obj, prop, value) => {
        console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
        obj[prop] = value;
    }
});
```  

```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        console.log(`The value of ${prop} is ${obj[prop]}`);
    },
    set: (obj, prop, value) => {
        console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
        obj[prop] = value;
        return true;
    }
});

personProxy.name;
personProxy.age = 43;
```  

When accessing the `name` property, the Proxy returned a better sounding sentence: `The value of name is John Doe`.

When modifying the `age` property, the Proxy returned the previous and new value of this property: `Changed age from 42 to 43`.  

<hr>  

A proxy can be useful to add **validation**. A user shouldn't be able to change `person`'s age to a string value, or give them an empty name. Or if the user is trying to access a property on the object that doesn't exist, we should let the user know.  

```js
const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        if (!obj[prop]) {
            console.log(
            `Hmm.. this property doesn't seem to exist on the target object`
        );
        } else {
            console.log(`The value of ${prop} is ${obj[prop]}`);
        }
    },
    set: (obj, prop, value) => {
        if (prop === "age" && typeof value !== "number") {
            console.log(`Sorry, you can only pass numeric values for age.`);
        } else if (prop === "name" && value.length < 2) {
            console.log(`You need to provide a valid name.`);
        } else {
            console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
            obj[prop] = value;
        }
    }
});
```  

Let's see what happens when we're trying to pass faulty values!  

```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        if (!obj[prop]) {
            console.log(`Hmm.. this property doesn't seem to exist`);
        } else {
            console.log(`The value of ${prop} is ${obj[prop]}`);
        }
    },
    set: (obj, prop, value) => {
        if (prop === "age" && typeof value !== "number") {
            console.log(`Sorry, you can only pass numeric values for age.`);
        } else if (prop === "name" && value.length < 2) {
            console.log(`You need to provide a valid name.`);
        } else {
            console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
            obj[prop] = value;
        }
        return true;
    }
});

personProxy.nonExistentProperty;
personProxy.age = "44";
personProxy.name = "";
```  

The proxy made sure that we weren't modifying the `person` object with faulty values, which helps us keep our data pure!  

## Reflect  
