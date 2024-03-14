## Patterns

Factory: Already covered

Builder:
- Create subclass
- Subclass `.build()` method returns the class

Prototype
- Clones instances
- Java with `implements Cloneable` interface

Adapter ***
- interface that describes functionality
- Adapter implements original interface eg. `SortListAdapter implements Sorter`
- Uses two conversions to convert between the two "types"

Composite ***
- For a tree like structure
- Element is the overarching interface 
- Leaf (`File`) is the leaf node
- Composite `Folder` contains other elements 

Command ***
- Classes for the things you want to *command*
- `comand` interface describes ` void execute()` action
- Each command for each device (`LightOnCommand`) implements `command` interface
- Invoker has `setCommand(Command)` and another function to trigger the `.execute()`
- Can be extended with queues/history using lists
-  You can also add `.unexecute()` as another function to undo the execute

Strategy: Covered

## Structures
#### Set
Collection of unordered unique items.  
Create:  `Set<Integer> numbers = new HashSet<Integer>()`
Add: `numbers.add(0)`
Check if contains (boolean): `numbers.contains(22)`

|  Returns | Method  |  Desc |
|---|---| --- |
|`boolean`|`add` | Adds the specified element to this set if it is not already present (optional operation).|
|`boolean`|`addAll` | Adds all of the elements in the specified collection to this set if they're not already present (optional operation).|
|`void`|`clear` | Removes all of the elements from this set (optional operation).|
|`boolean`|`contains` | Returns true if this set contains the specified element. | 
|`boolean` |`equals` | Compares the specified object with this set for equality.|
|`boolean`|`isEmpty` |Returns true if this set contains no elements.|
|`boolean`|`remove` | Removes the specified element from this set if it is present (optional operation).|
| `int`| `size` | Returns the number of elements in this set (its cardinality).|
|`Object[]`|`toArray` | Returns an array containing all of the elements in this set.|


#### Map
Key value pairs
`clear()`, `containsKey(Object o)`, `containsValue(Object o)`, `entrySet()`, `keySet()`, `equals(Objects o)`, `get(Object key)`, `isEmpty()`, `put(K key, V value)`, `remove(Object key)`, `size()`, `values()` 


#### `hashCode()`
Multiply a prime number by previous result or 1, then add it `id`

#### `.equals()`
1. Check if same object
2. Check if given object is null
3. Check instances with `.getClass()`
4. Cast Object to Type
5. Compare equality values. 

