## react-entity - Create entities base on [React](https://github.com/facebook/react) [propTypes](https://facebook.github.io/react/docs/reusable-components.html)

[![Build Status](https://travis-ci.org/scup/react-entity.svg?branch=master)](https://travis-ci.org/scup/react-entity)

This package let you create entities with schema validator like React PropTypes.

* [Installing](#installing)
* [Using](#using)
* [Examples](#examples)

### Installing
    $ npm install react-entity

### Using

#### Sample Entities
```javascript
import { PropTypes } from 'react';

class MyEntity extends ReactEntity {
  static SCHEMA = {
    field: PropTypes.string,
    otherField: {
      type: PropTypes.number,
      defaultValue: 10
    }
  }
}

class FatherEntity extends ReactEntity {
  static SCHEMA = {
    children: {
      validator: PropTypes.arrayOf(PropTypes.instanceOf(MyEntity)),
      type: MyEntity
    }
  }
}
```

#### Get default values
```javascript
const niceInstance = new MyEntity();
console.log(niceInstance.fetch()); // { field: undefined, otherField: 10 }
console.log(niceInstance.errors); // {}
```

#### Validations
```javascript
const buggedInstance = new MyEntity({ field: 10, otherField: 'value' });
console.log(buggedInstance.fetch()); // { field: 10, otherField: 'value' }
console.log(buggedInstance.errors); /* or buggedInstance.getErrors() -- but... getErrors also includes children errors
  {
    field: {
      errors: [ 'Invalid undefined `field` of type `number` supplied to `MyEntityEntity`, expected `string`.' ]
    },
    otherField: {
      errors: [ 'Invalid undefined `otherField` of type `string` supplied to `MyEntityEntity`, expected `number`.' ]
    }
  }
*/
```

#### Validate on change value
```javascript
const otherInstance = new MyEntity({ field: 'myString' });
console.log(otherInstance.errors); // {}
console.log(otherInstance.valid); // true

otherInstance.field = 1;
console.log(otherInstance.errors); // {field: { errors: [ 'Invalid undefined `field` of type `number` supplied to `MyEntityEntity`, expected `string`.' ] }}
console.log(otherInstance.valid); // false
```

#### Parse children to Entity
```javascript
const fatherInstance = new FatherEntity({
  children: [{
    field: 'A',
    otherField: 2
  }, {
    field: 'B',
    otherField: 3
  }]  
})
console.log(fatherInstance.children[0]); //An instance of MyEntity
console.log(fatherInstance.children[1].fetch());
//{ field: 'B', otherField: 3 }
```

#### Clean unexpected values
```javascript
const anotherInstance = new MyEntity({ field: 'myString', fake: 'fake' });
console.log(anotherInstance.fetch()); // { field: 'myString', otherField: 10 }
```
To understand the validators [React PropTypes](https://facebook.github.io/react/docs/reusable-components.html)

### Well known issues
  - Create helpers for relationships validations(Like, mininum, maximum)
  - Create identifier and equal comparison
