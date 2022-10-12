## Stdin

`Stdin` 네임스페이스는 표준 입력에 관한 함수를 제공합니다.

```py
Stdout.input({ prefix: "Password: " })
```

## Stdout

`Stdout` 네임스페이스는 표준 출력에 관한 함수를 제공합니다.

```py
Stdout.print("Hello, World!")
```


## Unique

`Unique`클래스는 다른 어떤 값이나 객체와도 동일하지 않은 고유한 값을 나타낼 때 사용할 수 있습니다. 

```py
value1 = new Unique("a")
value2 = new Unique("a")

Stdout.print(value1 == value2) # False
```

## Deferred

`Deferred` 클래스의 인스턴스는 동기적으로 기다리는 과정 없이 미래에 언젠가 작업이 완료됨을 나타냅니다.  
이 객체를 반환하는 함수는 처리해야 할 작업이 완료되기를 기다리지 않고  
 다음 작업으로 차례를 양보할 수 있습니다.

이 사양은 **변경 중**에 있습니다.