# Derive Language Concepts

**제안(proposal)** 에서 승격되었지만 **확정 사양(specification)** 단계에는 아직 추가되지 않은 요소들에 관해 다룹니다. 

## 네임스페이스

Derive의 모든 내장 함수들과 상수들은 네임스페이스에 속합니다.  
`namespace` 키워드를 사용하여 사용자 정의 네임스페이스를 정의할 수 있습니다.  

네임스페이스는 모듈과 비슷하게 작동합니다.  
`export` 지정자로 내보낼 멤버를 지정할 수 있으며,  
지정되지 않은 멤버는 바깥에서 접근할 수 없도록 가려집니다.
> Python
```py
print("Hello, World!")
input("Command: ")
```

> Derive
```ts
Stdout.print("Hello, World!")
Stdin.input({ prefix: "Command:" })

namespace Graphics:
    export def init(self):
        pass
    PRIVATE_CONSTANT = 2

Graphics.init()
```
## 열거형

보다 직관적이고 편리한 상수의 선언 및 사용을 위해,  
Derive는 언어적인 차원에서 열거형을 지원합니다.  

열거형은 `enum` 키워드를 사용하여 선언할 수 있습니다.

> Python
```py
from enum import Enum

class Color(Enum):
    Red = 0,
    Green = 1,
    Blue = 2

Color.Red
```

> Derive

```ts
enum Color:
    Red,
    Green,
    Blue

Color.Red
```
## 딕셔너리와 인스턴스의 통일
파이썬은 딕셔너리의 속성에 접근하기 위한 문법과  
인스턴스에 속성에 접근하기 위한 문법을 구분짓고 있습니다.  

그러나 딕셔너리의 속성 접근법은 프로그래머의 피로를 유발하고,  
일관성을 떨어뜨리며 코드를 난잡하게 만듭니다.    

딕셔너리 또한 `dict` 클래스의 인스턴스이기 때문에,  
Derive는 딕셔너리와 인스턴스의 속성 접근 문법을 통일하기로 했습니다.  
또한, 딕셔너리는 더 이상 속성의 키(key)작성에 문자열 형식을 강제하지 않습니다.

**점 접근법(Dot access)** 을 통해 속성에 접근할 수 있으며,  
**대괄호 접근법(Bracket access)** 을 통해 동적인 속성 접근을 제공합니다. 
> Python 
```py
dict1 = {
    "key1": "hello"
}
instance = SomeClass()


dict1.key1 # error
instance.key1

dict1["key1"]
instance["key1"] # error
```

> Derive
```ts
dict1 = {
    key1: "hello"
}
instance = new SomeClass()

dict1.key1
instance.key1

dict1["key1"]
instance["key1"]
```
## 캡슐화


파이썬과는 다르게, Derive는 객체와 연관된 함수들을 객체와 함께 캡슐화합니다.  
이러한 캡슐화는 대상 객체를 직관적으로 나타낼 뿐만 아니라, 기능을 그룹화하는 데에 유용합니다.
> Python
```py
reduce(lambda acc, curr: [*acc, curr + 1], [1, 2, 3], [])
```

> Derive
```py
[1, 2, 3].reduce((acc, curr): [...acc, curr + 1], [])
```

## 🧪 개선된 익명 함수

파이썬에서 익명 함수를 사용하기 위해서는 `lambda` 키워드를 사용해야 합니다.  
그러나 `lambda`를 통해 사용하는 익명 함수의 문법은 너무 길고,  
여러 줄에 걸쳐 작성이 불가능하기 때문에 유연성이 크게 떨어집니다.  

이러한 문제점들을 해결하기 위해서,  
Derive에서는 익명 함수를 작성할 때 `lambda` 키워드 없이  
 `(...args): {}` 형식의 문법을 사용합니다.  

인자가 하나만 존재할 경우 괄호를 생략할 수 있습니다.

> Python
```py
func(lambda: 1)
func(lambda v, k: v + k)
```

> Derive
```py
func((): 1)
func(((v, k): v + k)

func((): {
    Stdout.print(1)
    return 1
})
func((v, k): {
    Stdout.print(2)
    return v + k
})
```

## 🧪 가독성 높은 한 줄 문법

이 사양은 **변경 중**에 있습니다.

## 불변성 지향
파이썬은 선언하는 모든 변수를 가변적으로 취급합니다.  

모든 변수는 재선언이 가능하며, 언제든 원하는 값을 대입할 수 있고, 언제든지 변경될 수 있음을 암시합니다.  

파이썬의 변수 가변성은 코드의 변화율을 높이고 변수의 추적을 어렵게 만들기 때문에,  
Derive는 모든 변수를 불변적인 상수로 취급하여 정적인 코드 설계를 유도합니다.  

원하는 경우 `mut` 키워드를 변수 앞에 사용하여 가변 변수를 선언할 수 있습니다.  
가변 변수는 기존 파이썬의 변수와 동일하게 작동합니다.  
> Python
```py
a = 1 # should be immutable
a = 2 # ...
```

> Derive
```py
a = 1 
a = 2 # throw error: Constant "a" cannot be re-assgined.

mut b = 1
b = 2 
```
## 명시성 지향
파이썬의 디자인 철학에 나타나 있듯이,  
대부분의 경우 명시적인 것이 암시적인 것보다 낫습니다.  

파이썬은 클래스와 함수의 호출 시 문법이 동일합니다.   
이러한 파이썬의 특징은 함수와 클래스를 구별하기 힘들게 하고, 해당 구문을 모호하게 만듭니다.  

따라서 Derive는 클래스와 함수를 명확히 구분짓도록 `new` 키워드를 강제하고,  
런타임에 별다른 영향을 주지 않는 명시적인 키워드를 제공합니다.

> Python
```py
class Parent:
    def say(self):
        print(1)

class Child(Parent):
    def say(self): # overrided
        print(2)

instance = Child()
```

> Derive
```py
class Parent:
    def say(self):
        print(1)

class Child(Parent):
    override def say(self):
        print(2)

instance = new Child()
```

## 문자열 보간
Derive는 **F String**이라고 불리는 파이썬의 문자열 보간 문법을 채택하는 대신  새로운 문자열 보간 문법을 제공합니다.
> Python
```py
send(f"{name}: {content}")
```

> Derive
```py
send("*{name}: *{content}")
```
## 선택적 내보내기
파이썬은 무언가를 내보내는 모듈 파일에서 멤버를 숨길 방법을 따로 제공하지 않습니다.  
따라서 구현을 숨기고 일부 인터페이스만을 제공하고자 하는 대부분의 모듈의 경우 구성에 따로 신경을 써야 하는 등의 문제가 발생합니다.  

Derive는 `export` 지정자를 통해 내보낼 멤버를 지정하도록 하여 내보내지 않을 멤버들을 숨길 수 있도록 합니다.  
> Python
```py
# second.py
a = 1
b = 2

# main.py
from second import a, b
```

> Derive
```py
# second.dr
export a = 1
b = 2

# main.dr
import a from second # b cannot be imported
```

## 접근 제한자와 수정자 제공
파이썬은 너무 많은 기능을 데코레이터로 구현하도록 설계되었습니다.  

데코레이터 의존적인 문법은 일관성과 안전성을 떨어뜨리기 때문에,  
Derive는 데코레이터 남용을 막고자 접근 제한자와 수정자를 제공합니다.

자세한 내용은 [수정자](/docs/spec/kr/syntax/modifiers.md) 문서에서 다룹니다.
> Python
```py
class Page(Box):
    current_page = None

    @staticmethod
    def change(page):
        Page.current_page = page

    def update(self): # private
        for child in self.children:
            child.update()

    @property
    def pages_format(self):
        return f"{self.current} / {self.end}"
    
    @name.setter
    def name(self, value):
        self.__name = value
```

> Derive
```py
class Page(Box):
    current_page = None

    static def change(page):
        Page.current_page = page

    private def update(self):
        for child in self.children:
            child.update()

    getter def pages_format(self):
        return "*{self.current} / *{self.end}"
    
    setter def name(self, value):
        self.~name = value
```

## 내부 멤버
파이썬은 접근 제한자를 제공하지 않기 때문에,  
 은닉된(private) 멤버를 표현하기 위해서는   
멤버의 이름 앞에 `__`를 붙이는 약속이 필요했습니다. 

`__`가 많은 파이썬의 코드는 코드를 복잡하게 만듭니다.  
또한 프로그래머 사이의 규칙/관습에 의존하기 때문에,  
 실질적으로 접근을 제한할 수 없어 런타임에서의 안전성을 보장할 수 없다는 문제점이 있습니다.  

따라서 Derive는 멤버 은닉을 위한 수단을 언어 차원에서 제공합니다.  

`private` 접근 제한자를 통해 메서드의 접근을 객체 내부로 제한할 수 있습니다.    
은닉된 속성(property)은 `~` 키워드를 속성의 이름 앞에 붙여 선언할 수 있습니다.
> Python
```py
class A:
    def __init__(self):
        self.__private_member = 1
    
    def __private_method(self):
        pass
```

> Derive
```py
class A:
    def init(self):
        self.~private_member = 1

    private def private_method(self):
        pass
```
## 중간자 함수

객체와 객체 사이에서 키워드처럼 사용되는 함수를 **중간자 함수**(infix function)라고 부릅니다.  

중간자 함수는 파라미터가 최대 2개까지만 허용됩니다.

```py
infix def merge(first, second):
    return "*{first}*{second}"

result = "Hello, " merge "World!"

Stdout.print(result) # Hello, World!
```

```py
class Array:
    def init(self, init = []):
        self.~arr = init

    infix def map(self, target):
        obj = {}
        mut i = 0

        for element in self.~arr:
            obj[element] = target[target[i]]
            i += 1
        return obj

keys = new Array(["name", "height", "age"])
values = new Array(["lotinex", 173, 18])

obj = keys map values # {"name": "lotinex", "height": 173, "age": 18}
```


## 🧪 분기점

파이썬과 마찬가지로 Derive에서는 `match` 문법을 사용하여 특정 값에 대해 다르게 작동할 로직을 작성할 수 있습니다.  

이와 비슷하지만 조금 다른 `branch`문은 **분기점**이라고 부르며,   
특정 값에 대해 다른 값을 조건에 따라 지정해 주는 역할을 합니다.  

```hs
level = get_level()

picked = branch level {
    level >= 100: "S"
    level >= 90: "A"
    ...
}
```

## 🧪 비동기 프로그래밍 

이 사양은 **변경 중**에 있습니다.

## 프로토콜

Derive는 동적 타입 프로그래밍 언어입니다.  
따라서 자료형 명시 기능을 가지고 있지 않으며,  
 인터페이스와 같은 추상적인 자료형을 정의하고 다룰 수도 없습니다.  

그러나 어떤 클래스가 특정한 조건을 만족하도록 강제하고 싶은 경우 사용할 수 있는
대안을 제공합니다.  

클래스가 반드시 구현해야 하는 속성 및 메서드를 강제할 수 있도록 **프로토콜** 이라고 불리는 특별한 문법을 사용할 수 있습니다.  

자세한 내용은 [프로토콜](/docs/syntax/protocol.md) 문서에서 다룹니다.
