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
![image](https://github.com/jinukeu/ComposeDictionary/assets/81678959/566b0ba1-cceb-4463-938a-87198a862226)

colorState는 Composable1에 정의되어 있으며 Composable8에서만 이용된다.

사용하지 않는 Composable3, 5에도 colorState가 전달된다.

![image](https://github.com/jinukeu/ComposeDictionary/assets/81678959/b3083e87-e32b-4bf3-9248-4ca8121a5d61)

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


## Shape
Shape는 컴포저블을 특정 모양으로 그리는 데 사용할 수 있습니다.

https://foso.github.io/Jetpack-Compose-Playground/foundation/shape/

## Box
프레임 레이아웃처럼 여러 뷰들을 겹칠 수 있는 용도로 쓸 수 있다.

https://onlyfor-me-blog.tistory.com/688

## hiltViewModel
hiltViewModel()로 생성된 viewModel은 NavBackStackEntry의 스코프를 가지게된다. Compose Navigation과 함께 사용할 때 유용하다.

https://velog.io/@wlsrhkd4023/Compose-hiltViewModel%EA%B3%BC-viewModel-%EC%B0%A8%EC%9D%B4#viewmodel-%EC%83%9D%EC%84%B1

## LaunchedEffect
LaunchedEffect는 Composable에서 컴포지션이 일어날 때 suspend fun을 실행해주는 Composable이다.

리컴포지션은 Composable의 State가 바뀔 때마다 일어나므로, 만약 매번 리컴포지션이 일어날 때마다 이전 LaunchedEffect가 취소되고 다시 수행된다면 매우 비효율적일 것이다. 이를 해결하기 위해 LaunchedEffect는 key라 불리는 기준값을 두어 key가 바뀔 때만 LaunchedEffect의 suspend fun을 취소하고 재실행한다.

https://kotlinworld.com/246

## collectAsStateWithLifecycle
collectAsState는 Composition의 라이프사이클을 따르고, composable이 Composition 단계에 진입할 때 flow를 수집하기 시작합니다. collectAsState는 Android앱이 백그라운드에 있는 동안 recomposition을 중지하더라고 수집 작업이 활성화된 상태로 유지하기 때문에 나머지 레이어에 대한 리소스를 확보할 수 없으며, collectAsState는 flow를 수집하는데 플랫폼에 구애받지 않는 API라고 합니다.  

 

하지만 Android앱에서 라이프사이클은 리소스를 관리하는 방법에서 중요한 역할을 맡고 있기 때문에 앱이 백그라운드에 있는 동안 flow 수집 활동을 중지할 수 있는 collectAsStateWithLifecycle를 사용한다면, 안전하고 불필요한 리소스 낭비를 방지할 수 있다고 합니다.

두 가지 API 모두 Compose에서 사용하지만, collectAsStateWithLifecycle은 Android 앱을 개발할 때, collectAsState는 다른 플랫폼을 위해 개발할 때 사용하는 것이 좋다고 합니다.

https://reco-dy.tistory.com/13

## State
Jetpack Compose에서 상태(State)란 UI의 업데이트와 관련있다. 상태 값이 변경될 때 마다 UI가 업데이트되기 때문이다.

상태 값은 어떤 타입이든 될 수 있다. Boolean, String 같은 단순한 값일 수도 있고, 렌더링된 화면 상태에 대한 여러 값을 포함한 data class 일 수도 있다.

컴포즈가 상태 변경을 인식하기 위해서는 상태 값을 mutableStateOf()를 사용해서 State Object로 감싸면 된다. mutableStateOf() 함수는 `MutableState<T>` 객체를 리턴하고, 컴포즈는 값이 변경될 때마다 변경 사항을 추적해서 UI를 업데이트한다.

### State를 만드는 방법
```kotlin
@Composable
fun MyComponent() {
    var enabled by remember { mutableStateOf(true) } // <- this line

    // ...
    Text("Enabled is ${enabled}")
}
```

```kotlin
val flow = MutableStateFlow("")
// ...
val state by flow.collectAsState()

// for lifecycle aware version
val state by flow.collectAsStateWithLifecycle()
```

https://m1nzi.tistory.com/8

## 
