# mashumaro

Fast and well tested serialization framework on top of dataclasses.

```
pip install mashumaro
```

When using dataclasses, you often need to dump and load objects according to the described scheme.
This framework not only adds this ability to serialize in different formats, but also makes **serialization rapidly**.

Supported serialization formats
--------------------------------------------------------------------------------

This framework adds methods for dumping to and loading from the following formats:

* plain dict
* json
* yaml
* msgpack

Plain dict can be useful when you need to pass a dict object to a third-party library, such as a client for MongoDB.

Supported field types
--------------------------------------------------------------------------------

There is support for generic types from the standard *typing* module:
* List
* Tuple
* Set
* FrozenSet
* Deque
* Dict
* Mapping
* MutableMapping
* ChainMap
* Sequence

for special primitives from the *typing* module:
* Optional
* Any

for enumerations based on classes from the standard *enum* module:
* Enum
* IntEnum
* Flag
* IntFlag

for common built-in types:
* int
* float
* bool
* str
* bytes
* bytearray

for built-in datetime oriented types:
* datetime
* date
* time
* timedelta

for other specific types like *NoneType* and for nested dataclasses itself.

Usage example
--------------------------------------------------------------------------------

``` python
from enum import Enum
from typing import Set
from dataclasses import dataclass
from mashumaro import DataClassJsonMixin

class PetType(Enum):
    CAT = 'CAT'
    MOUSE = 'MOUSE'

@dataclass
class Pet(DataClassJsonMixin):
    name: str
    age: int
    pet_type: PetType

@dataclass
class Person(DataClassJsonMixin):
    first_name: str
    second_name: str
    age: int
    pets: Set[Pet]


tom = Pet(name='Tom', age=5, pet_type=PetType.CAT)
jerry = Pet(name='Jerry', age=3, pet_type=PetType.MOUSE)
john = Person(first_name='John', second_name='Smith', age=18, pets={tom, jerry})

dump = john.to_json()
person = Person.from_json(dump)
# person == john

Pet.from_json('{"name": "Tom", "age": 5, "pet_type": "CAT"}')
# Pet(name='Tom', age=5, pet_type=<PetType.CAT: 'CAT'>)
```

API
--------------------------------------------------------------------------------

Mashumaro provides a couple of mixins for each format.

#### `DataClassJsonMixin.to_json()`

Make a JSON formatted string from dataclass object based on the dataclass schema provided.

#### `DataClassJsonMixin.from_json(data: str)`

Make a new object from JSON formatted string based on the dataclass schema provided.

#### `DataClassMessagePackMixin.to_msgpack()`

Make a MessagePack formatted bytes object from dataclass object based on the dataclass schema provided.

#### `DataClassMessagePackMixin.from_msgpack(data: bytes)`

Make a new object from MessagePack formatted data based on the dataclass schema provided.

#### `DataClassYAMLMixin.to_yaml()`

Make an YAML formatted bytes object from dataclass object based on the dataclass schema provided.

#### `DataClassYAMLMixin.from_yaml(data: bytes)`

Make a new object from YAML formatted data based on the dataclass schema provided.

#### `DataClassDictMixin.to_dict(use_bytes: bool, use_enum: bool, use_datetime: bool)`

Make a dictionary from dataclass object based on the dataclass schema provided. Options include:
```python
use_bytes: false     # False - convert bytes/bytearray objects to base64 encoded string, True - keep untouched
use_enum: false      # False - convert enum objects to enum values, True - keep untouched
use_datetime: false  # False - convert datetime oriented objects to ISO 8601 formatted string, True - keep untouched
```

#### `DataClassDictMixin.from_dict(data: Mapping, use_bytes: bool, use_enum: bool, use_datetime: bool)`

Make a new object from dict object based on the dataclass schema provided. Options include:
```python
use_bytes: false     # False - load bytes/bytearray objects from base64 encoded string, True - keep untouched
use_enum: false      # False - load enum objects from enum values, True - keep untouched
use_datetime: false  # False - load datetime oriented objects from ISO 8601 formatted string, True - keep untouched
```

TODO
--------------------------------------------------------------------------------

* write about how it works
* write benchmarks
* add parameters to json serialization methods
* add optional validation
* write custom useful types such as URL, Email, UUID etc
* write documentation
