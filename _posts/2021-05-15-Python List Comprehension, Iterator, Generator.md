---
title: "Python List Comprehension, Iterator, Generator"
excerpt: "최근 Generator, yield 에 대한 글을 읽고 개인적으로 정리하려다가 이런 표현식들을 한번에 정리해놓으려고 한다."
toc: true
toc_sticky: true
categories:
  - Python
tags:
  - Python
  - '2021'
last_modified_at: 2021-05-15
---

파이썬을 더욱 강력하게 사용할 수 있는지? 파이썬 좀 써봤는지?(중급 사용자 이상인지) 보는 보편적인 기준은 아니겠지만.. 보통은 코드에서 같은 로직이라도 어떤 기능을 사용하는지 보고 판단하기도 한다.

최근 `Generator`, `yield` 에 대한 글을 읽고 개인적으로 정리하려다가 이런 표현식들을 한번에 정리해놓으려고 한다.

## List Comprehension

**Comprehension**이란 iterable한 오브젝트를 생성하기 위한 방법 중 하나로 파이썬에서 사용할 수 있는 유용한 기능 중 하나이다.

제목은 `List Comprehension`으로 해놓았지만 어떤 자료형이냐에 따라서 아래와 같이 4가지 종류의 Comprehension이 있다고 한다.

- List Comprehension (LC)
- Set Comprehension (SC)
- Dict Comprehension (DC)
- Generator Expression (GE)

`Generator`의 경우 Comprehension과 형태는 동일하지만 특별히 `Expression`이라고 부른다. 이는 후에 `Generator`에서 다시 설명하도록 하겠다.

`List Comprehension`은 말 그대로 새로운 리스트를 만들 때 사용할 수 있는 간단한 표현식으로 리스트와 마찬가지로 '[',']'를 사용하여 작성한다.

### Syntax

간단히 아래와 같은 문법으로 사용한다.

```python
newlist = [ expression for item in iterable ]
```

아래와 같이 `if` 문을 활용하여 조건문도 활용 가능하다.

```python
newlist = [expression for item in iterable if condition == True]
```

### Example code

일반적인 for 문을 이용하여 'fruit' 중 'a' 문자가 포함되 있는 단어를 newlist에 넣는 예제이다.

```python
fruits = ["apple", "banana", "cherry", "kiwi", "mango"]
newlist = []

for x in fruits:
  if "a" in x:
    newlist.append(x)
```

아래는 list comprehension 을 사용한 예제이다. 3줄에 달하던 for 문을 단 한줄에 표현할 수 있다.

```python
fruits = ["apple", "banana", "cherry", "kiwi", "mango"]

newlist = [x for x in fruits if "a" in x]
```

다른 예제로 'apple'을 제외한 모든 과일들을 newlist 배열에 넣어보자. `if`문의 not 조건을 이용하면 된다.

```python
newlist = [x for x in fruits if x != "apple"]
```

처음에는 어렵겠지만 익숙해지면 코딩 스킬을 늘려주는 기술중에 하나다.

## Iterator

`Iterator`란 반복 가능한 객체(`Iterable`), 즉 반복문을 이용하면서 데이터를 순회하면서 처리는것을 의미한다.

대표적인 `Iterable`로는 아래와 같은 object 들이 있다.

- sequence 타입인 list, str, tuple 등
- non-sequence 타입인 dict나 file도 itrable
- **iter()**나 **getitem()** 메소드로 정의된 class는 모두 itrable하다.

`Iterator`는 `next()` 메소드로 데이터를 순차적으로 호출 가능한 object이다. 만약 `next()`로 다음 데이터를 불러 올 수 없는 경우(가장 마지막 데이터인 경우) `StopIterationException`을 발생시킨다.

그렇다면 iterable한 object는 iterator일까? 반드시 그렇지는 않다.

```python
x = [1,2,3]
next(x)
>>> Traceback (most recent call last):
>>>   File "<stdin>", line 1, in <module>
>>> _TypeError: list object is not an iterator_
```

list는 `iterable`이지만 위와 같이 `next()` 메소드로 호출해도 
동작하지 않고 `iterator`가 아니라는 에러 메시지가 뜬다. `iterable`을 `iterator`로 변환하고 싶다면, `lter()`라는 built-in function을 사용하면 된다.

### Example code

```python
a = (1,2,3)
b = iter(a)
type(a)
>>> <type 'tuple'>
type(b)
>>> <type 'tupleiterator'>

print(next(a))
>>> Traceback (most recent call last):
>>>   File "<stdin>", line 1, in <module>
>>> TypeError: tuple object is not an iterator
print(next(b))
>>> 1
print(next(b))
>>> 2
print(next(b))
>>> 3
print(next(b))
>>> Traceback (most recent call last):
>>>   File "<stdin>", line 1, in <module>
>>> StopIteration
```

위의 첫줄에서 a라는 tuple을 선언하고 b라는 iter 객체로 만들었다. next() 메소드를 호출시에 iter 내부의 데이터를 순회하고 마지막에는 StopIterationException을 발생시킨다.

## Generator

`Generator`는 iterator를 생성해주는 함수를 의미한다. 파이썬에서는 함수안에 `yield` 키워드를 사용해 만들 수 있다.

>중요한 포인트는 *모든 값을 포함하여 변환하는 대신에 호출 할 때마다 한 개의 값을 리턴한다는 것이다.*

>즉, 호출 될 때마다 리턴하기 때문에 메모리가 충분하지 않은 상황에서도 대용량의 반복 가능한 구조로 순회가 가능하다.

일반적인 함수는 진입점이 하나라면 Generator는 진입점이 여러개라고 볼 수 있다. 이러한 특성때문에 Generator를 사용하면 원하는 시점에 원하는 데이터를 받을 수 있게 된다.

### Example code

```python
def generator():
  yield 1
  yield 'string'
  yield True

gen = generator()
gen
>>> <generator object generator at 0x10e31d960>
next(gen)
>>> 1
next(gen)
>>> 'string'
next(gen)
>>> True
next(gen)
>>> Traceback (most recent call last):
>>>  File "<stdin>", line 1, in <module>
>>> StopIteration
```

1. `yield` 문이 포함된 `generator` 함수를 실행하면 `generato` 객체가 반환되는데 이때 함수의 내용이 실행되지는 않는다.
2. `next()`라는 built-in 메서드를 통해 `generator`를 실행시킬 수 있으며 `next()` 메서드 내부적으로 `iterator`를 인자로 받아 `iterator`의 `__next__()` 메서드를 실행시킨다.
3. 처음 `__next__()` 메서드를 호출하면 함수의 내용을 실행하다 `yield` 문을 만났을 때 처리를 중단한다.
4. 이 때 모든 local state는 유지되는데 변수의 상태, 명령어 포인터, 내부 스택, 예외 처리 상태를 포함한다.
5. 그 후 제어권을 상위 컨텍스트로 양보(`yield`)하고 또 `__next()__`가 호출되면 `generator`는 중단된 시점부터 다시 시작한다.

> yield 문의 값은 어떤 메서드를 통해 제네레이터가 다시 동작했는지에 따라 다른데, `__next__()`를 사용하면 None 이고 `send()`를 사용하면 메서드로 전달 된 값을 갖게되어 외부에서 데이터를 입력받을 수 있게 된다.

### 사용의 이점

앞서 말했듯이 메모리를 효율적으로 사용할 수 있다. list, set, dict 표현식은 `iterable`하기에 for 문등에서 유용하게 쓸 수 있다. 하지만 `iterable` 객체는 모든 값을 메모리에 담고 있어야 하기 때문에 큰 값을 다룰 때는 별로 좋지 않을 것이다. `generator`를 사용하면 `yield`를 통해 그때그때 필요한 값만을 받아 쓰기 때문에 모든 값을 메모리에 들고 있을 필요가 없다.

```python
import sys
a = [i for i in range(100000)]
sys.getsizeof(a)
>>> 824472
b = (i for i in range(100000))
sys.getsizeof(b)
>>> 80
```
다만 `generator`는 그때그때 필요한 값을 던져주고 기억하지는 않기 때문에 a가 여러번 사용될 수 있는 반면 b는 한번 사용된 후 소진된다. 이는 모든 `iterator`가 마찬가지인데 List, Set 은 `iterable`하지만 `iterator`는 아니기에 소진되지 않는다.

```python
len(list(b))
>>> 100000
len(list(b))
>>> 0
```

또한 `while True` 구문으로 제공받을 데이터가 무한하거나, 모든 값을 한번에 계산하기엔 시간이 많이 소요되어 그때 그때 필요한 만큼만 받아 계산하고 싶을 때 `generator`를 활용 가능하다.

### Generator, Iterator, Iterable 간 관계

![relation_generator_iterator_iterable](/assets/images/2021/05/2021-05-15-16-03-11.png)

## Reference
- [파이썬의 Comprehension 소개](https://mingrammer.com/introduce-comprehension-of-python/){: target="_blank"}
- [Python - List Comprehension](https://www.w3schools.com/python/python_lists_comprehension.asp){: target="_blank"}
- [Generator](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Python#generator){: target="_blank"}