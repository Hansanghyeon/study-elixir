# 머리와 꼬리를 사용해 리스트 처리하기

리스트를 머리와 꼬리로 나눌 수 있게 되었으니, 이제 값과 리스트를 각각 머리와 꼬리로 삼아 새로운 리스트를 만들어보자. 그런데 왜 리스트를 모듈과 함수 바로 다음에 소개하는 것일까? 그 이유는 리스트와 재귀 함수는 바늘과 실처럼 함께 다니기 떄문이다. 리스트의 길이를 구하는 알고리즘은 다음과 같다.

- 빈 리스트의 길이는 0이다.
- 리스트의 길이는 리스트 꼬리 길이에 1을 더한것이다.

리스트 길이를 구하는 알고리즘은 다음처럼 간단히 엘릭서 코드로 옮길 수 있다.

```exs
defmodule MyList do
  def len([]), do: 0
  def len([ _ | tail ]), do: 1 + len(tail)
end
```

len 함수의 두 번째 정의만이 약간 까다롭다.

```exs
def len([ head | tail ]) ...
```

이 코드는 비어 있지 않은 모든 리스트에 패턴 매칭된다. 여기에 매칭되면 변수 head는 리스트의 첫 번쨰 요소를 값으로 갖게 되고, tail은 리스트의 남은 부분을 받는다. (재귀가 끝나면 모든 리스트가 빈 리스트가 되므로, 리스트의 꼬리는 []가 될 수도 있다).


이 함수가 리스트 `[11, 12, 13, 14, 15]`에 어떻게 동작하는지 살펴보자. 각 단계마다 리스트의 첫 요소를 하나씩 떼어낸 뒤 꼬리 길이에 1을 더한다.

```exs
len([11, 12, 13, 14, 15])
= 1 + len([12, 13, 14, 15])
= 1 + 1 + len([113, 14, 15])
= 1 + 1 + 1 + len([14, 15])
= 1 + 1 + 1 + 1 + len([15])
= 1 + 1 + 1 + 1 + 1 + len([])
= 1 + 1 + 1 + 1 + 1 + 0
= 5
```

실제로 코드를 실행해서 예상대로 동작하는지 보자.