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

## LocalInspectionMode
안드로이드 스튜디오 Preview 로 보이고 있는지 Boolean 값을 제공   

```kotlin
@Composable
fun ExampleView(
    isPreview: Boolean = LocalInspectionMode.current
) {
    if (isPreview) {
        Text("This is a preview")
    } else {
        Text("This is not")
    }
}
```

https://developermemos.com/posts/checking-composable-render-preview   
https://sungbin.land/jetpack-compose-%EC%9A%B0%EB%A6%AC%EA%B0%80-%EB%AA%B0%EB%9E%90%EB%8D%98-compositionlocal-%EB%93%A4-343b1ea41e3a

## CompositionLocal
![image](https://github.com/jinukeu/ComposeDictionary/assets/81678959/405ec9d6-21f3-4079-99b0-99b3844bd2c8)   

colorState는 Composable1에 정의되어 있으며 Composable8에서만 이용된다.

사용하지 않는 Composable3, 5에도 colorState가 전달된다.

![image](https://github.com/jinukeu/ComposeDictionary/assets/81678959/60b842d9-30d2-475c-86c1-35c0413ca740)

하지만 `CompositioniLocal`을 이용하면 중간 자식 노드에 상태를 전달하지 않고도 트리의 가장 높은 노드에 선언되어 있는 데이터를 하위 노드에서 이용할 수 있다.

### CompositionLocal 이용하기
ProvidableCompositionalLocal 인스턴스를 생성해야 한다.
```kotlin
val LocalColor = compositionLocalOf { Color.Red }
val LocalColor = staticCompositionLocalOf { Color.Red }
```

`staticCompositionLocalOf` : 상탯값이 변경되면 해당 상태가 할당된 노드의 하위 노드를 모두 재구성     

`compositionLocalOf` : 현재 상태에 접근하는 컴포저블에 대해서만 재구성을 수행   

`staticCompositionLocalOf`은 Compose가 추적하지 않기 때문에 상탯값이 자주 변경되지 않을 때 사용하면 성능이 좋다고 한다.   


