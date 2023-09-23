# ComposeDictionary
## Composable
Composable은 컴포즈로 화면을 만들기 위한 핵심 함수로, @Composable로 지정할 수 있다.

## @Stable
Compose에서 Stable하다는 개념은 매우 중요한 개념이다.

Stable이 필요한 이유는 Smart Recomposition 때문이다. Smart Recomposition은 리컴포지션을 진행할 때, 모든 input이 stable이고 같은 값이라면 skip이 되게끔 하는 것이다. 이말은, 바뀐게 없으면 바꾸지 않겠다를 좀 더 효율적으로 한다는 말이다. 만약 바뀌지 않았다는 확신(certain)이 없다면 무조건 바꾼다.

---

공식 문서에서는 Stable에 대한 3가지 조건을 제시한다.

**1. The result of equals for two instances will forever be the same for the same two instances.**   
두 인스턴스의 equals가 같아야한다. 이는 짐짓 당연해 보이는 내용이다.

**2. If a public property of the type changes, Composition will be notified.**   
이 경우는 public property에 Snapshot의 State를 쓴 경우이다. State가 바뀌면 Composition will be notified 되기 때문이다.

**3. All public property types are also stable.**   
모든 public property가 Stable이어야한다.

또한, 다음 3가지는 기본적으로 Stable로 취급한다.

**1. All primitive value types: Boolean, Int, Long, Float, Char, etc.**

**2. Strings**

**3. All Function types (lambdas)**

---

@Stable은 Stable이지만 컴파일러가 인식을 못할 때 @Stable을 달면 된다고 보면 간단하다.

https://velog.io/@nilto/Android-Jetpack-Compose-Compose-Stable


