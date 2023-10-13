# 함수 호출과 패턴 매칭

5장에서 익명 함수를 알아보면서 파라미터를 실제 인자에 바인딩할 때 패턴 매칭이 어떻게 사용되는지 살펴봤다. 기명 함수에도 같은 규칙이 적용된다. 단, 하나의 기명 함수에 여러 구현을 정의할 떄는 각각의 파라미터와 본문을 가진 함수를 따로 작성해야 한다는 점이 다르다. 겉보기에는 서로 다른 함수를 여러 개 정의한 것처럼 보이지만 순수주의자들은 하나의 함수에 정의에 여러 개의 구현이 붙어 있는 것이라고 주장할 것이고. 그 말이 맞다.

기명 함수를 호출할 때 엘릭서는 첫 번째 함수 정의(구현)의 파라미터 목록에 인자가 매칭되는지 확인한다. 매칭되지 않는다면 같은 함수의 다음 정의에 매칭되는지 확인한다(이 함수 정의들이 받는 파라미터 갯수가 모두 같아야 함에 유의하자). 이 과정을 매칭되는 함수가 나타날 때까지, 또는 매칭할 함수가 다 떨어질 때까지 반복한다.

이 과정을 실험해보자. `n!`으로 쓰는 n 팩토리얼은 1부터 n까지의 자연수를 모두 곱한 값이다. `0!`은 1로 정의된다. 이 정의를 다른 방식으로 나나내면 다음과 같다.

```exs
defmodule Factorial do
  def of(0), do: 1
  def of(n), do: n * of(n - 1)
end
```

하나의 함수에 대해 정의를 두 벌 만들었다. `Factorial.of(2)`를 호출하면 엘릭서는 2와 첫 번쨰 함수의 파라미터(0)를 매칭하려고 시도하는데, 이 시도는 실패한다. 이어서 두 번째 정의와 매칭을 시도하고, n에 2를 바인딩하면 매칭에 성공하므로 두 번째 정의의 본문이 실행된다. 본문에서 `Factorial.of(1)`을 호출하고, 이는 같은 과정에 따라 두 번째 정의에 매칭되어 실행된다. 이번에는 `Factorial.of(0)`이 호출되는데, 이는 첫 번째 정의에 매칭된다. 이 함수는 1을 반환하며 재귀를 종료한다. 엘릭서는 콜 스택을 거슬러 올라가며 곱셈을 수행한 뒤 최종 결과를 반환한다. 이 구현은 잘 동작하지만 개선의 여지가 많다. 나중에 꼬리 재귀를 알아보면서 개선하자.

이와 같이 재귀를 이용해 설계하고 코딩하는 패턴은 엘릭서(그리고 거의 모든 함수형 언어)에서 매우 일반적인 방법이다. 우선 정해진 답을 반환하는 가장 간단한 경우를 하나 찾는다. 이것이 일종의 기준점이 된다. 그리고 이 기준점에서 호출을 끄내는 재귀적인 답을 찾으면 된다. 예를 들어보자.

- 1부터 n까지의 자연수의 합
  - 0개 숫자의 합은 0이다.
  - n까지의 합은 n-1까지의 숫자의 합 + n이다.
- 리스트의 길이
  - 빈 리스트의 길이는 0이다.
  - 다른 리스트의 길이는 1 + 리스트 꼬리 부분의 길이다.

리스트의 '꼬리'는 리스트의 첫 번째 값을 제외한 나머지 리스트를 의미한다.

중요한 것은 이 정의들을 코드로 옮길 때 어떤 순서로 쓰는지에 따라 결과가 달라질 수 있다는 점이다. 엘릭서는 함수들에 대해 패턴 매칭을 수행할 때 코드의 위에서 아래 순서로 확인하며, 그중 처음으로 매칭되는 함수를 실행한다. 따라서 다음과 같은 코드는 제대로 동작하지 않는다.

```exs
defmodule BadFactorial do
  def of(n), do: n * of(n-1)
  def of(0), do: 1
end
```

어떤 값을 넣어 호출하든지 첫 번째 함수 정의에 매칭되므로 두 번째 함수 정의는 실행되지 않는다. 하지만 이런 경우에는 엘릭서 컴파일러가 도움을 준다. 이 코드를 컴파일하면 경고가 출력된다.