
## 프로토콜


프로토콜은 클래스가 구현해야 하는 멤버에 관한 정보를 담은 규약입니다.  

`protocol` 키워드로 프로토콜을 정의할 수 있으며,  
 프로토콜은 구현을 강제하고자 하는 메서드 및 속성의 이름들을 포함해야 합니다.  

어떠한 프로토콜을 준수하고자 하는 클래스는 `<>` 기호를 사용하여 대상 프로토콜을 지정해야 합니다.

상속과는 다르게 클래스는 한 번에 여러 프로토콜을 준수할 수도 있습니다.  

추가적으로, 프로토콜은 다른 프로토콜을 상속할 수도 있습니다.  
중복되는 멤버가 있을 경우 자식 프로토콜의 멤버로 덮어씌워집니다.  
```py
protocol ActionTransceiver:
    subscribed_actions,
    def transmit(self, action_type),
    def receive(self, action_type, data)

class Test<ActionTransceiver>:
    def init(self):
        self.subscribed_actions = ...
    
    def transmit(self, action_type): 
        ...
    def receive(self, action_type, data):
        ...
```
```py
protocol A:
    def first(self)

protocol B: 
    def second(self)

protocol C(A, B):
    def third(self)

class Body<C>:
    def first(self):
        ...
    def second(self):
        ...
    def third(self):
        ...
```

## 이터레이터 프로토콜과 이터러블 프로토콜

`Iteration.Iterable` 프로토콜을 준수하는 객체를 **반복 가능한 객체**(iterable)라고 부릅니다.  

이터러블은 `[Iteration.iterator]` 메서드를 통해 `[Iteration.Iterator]` 프로토콜을 준수하는 객체를 반환해야 하며,   
해당 객체는 **반복자**(iterator)라고 부릅니다.  

이터레이터는 `[Iteration.next]` 메서드를 통해 저장된 요소를 순회하며 접근하는 동작을 구현하게 됩니다.  

> Python

```py
class Array:
    def __init__(self, elements):
        self.__index = 0
        self.__elements = elements

    def __iter__(self): # iterable protocol
        return self

    def __next__(self): # iterator protocol
        self.__index += 1
        return self.__elements[self.__index]


arr = Array([1, 2, 3, 4, 5])

for e in arr:
    print(e) # 1, 2, 3, 4, 5 
```

> Derive

```py
class Array<Iteration.Iterator, Iteration.Iterable>:
    def init(self, elements):
        self.~index = 0
        self.~elements = elements
    
    def [Iteration.iterator](self): # iterable protocol
        return self

    def [Iteration.next](self): # iterator protocol
        self.~index += 1
        return self.~elements[self.~index]

arr = new Array([1, 2, 3, 4, 5])

for e in arr:
    Stdout.print(e) # 1, 2, 3, 4, 5
```

## 누산기 프로토콜과 누산 연산자

**누산기**는 적재만이 가능한 연산을 담당하는 함수를 말합니다.   
데이터를 연속적으로 적재해야 하는 상황에서 유용할 수 있습니다.  

`Data.Accumulator` 프로토콜을 준수하는 객체는 **누산 가능**(accumulatable)하다고 하며 `->` 연산자를 통해 누산 연산의 대상이 될 수 있습니다.  

누산 연산의 실행은 오른쪽에서 시작하며,  
`->` 연산자는 **연산 주체**(연산자의 왼쪽)의 `[Data.accumulator]` 메서드를 **연산 인자**(연산자의 오른쪽)를 인자로 넘기며 호출합니다. 

```py
class Array<Data.Accumulator>:
    def init(self, init = []):
        self.~arr = init

    def [Data.accumulator](self, value):
        self.~arr.unshift(value)
        return self

arr = new Array([1, 2, 3])

Stdout.print(4 -> "a" -> arr) # [4, "a", 1, 2, 3]
```

Derive의 모든 함수는 `Function<Data.Accumulator>`의 인스턴스이기 때문에 누산 가능한 객체입니다.  
함수는 파라미터가 1개일 때만 누산 가능하며, 함수의 누산 연산은 특별히 **합성 연산**이라고 부릅니다. 

> Python

```py
a(b(c(d)))
```

> Derive

```py
d -> c -> b -> a
```

## 🧪 소멸자 프로토콜
**소멸자**는 객체가 메모리에서 할당 해제될 때 호출되는 함수입니다.  

Derive는 참조되지 않는 데이터에 대해 GC(Garbage Collection)를 수행합니다.  
GC가 수행될 때 객체가 소멸자 프로토콜을 준수하고 있는 경우,  
객체의 `[GC.destructor]` 메서드를 암시적으로 호출하게 됩니다. 

```py
class A<GC.Destruct>:
    def [GC.destructor](self):
        Stdout.print("destructed")
```
