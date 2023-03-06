# Basic Codelab

Composable 함수는 `@Composable` 주석이 달린 일반 함수이다. 이렇게 하면 함수가 내부에서 다른 @Composable 함수(`Text`와 같은)를 호출할 수 있다.

Compose를 사용하면 Activity가 Android 앱의 진입점으로 유지된다. `setContent`를 이용하여 레이아웃을 정의하지만, 기존 뷰 시스템에서 하던 것처럼 XML파일을 사용하는 대신 이 함수에서 Composable 함수를 호출한다.

Android 스튜디오 미리보기를 사용하려면 매개변수가 없는 Composable 함수 또는 기본 매개변수를 포함하는 함수를 `@Preview` 주석으로 표시하고 프로젝트를 빌드하면 된다. 동일한 파일에 미리보기를 여러 개 만들고 이름을 지정할 수 있다.

`Surface`를 이용하면 배경색을 지정할 수 있는데, `Surface`내부에 중첩된 구성요소는 배경 색상 위에 그려진다.

```kotlin
@Composable
fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(text = "Hello $name!")
    }
}
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6ef53caf-4f6d-431b-8180-3d94b1cc5951/Untitled.png)

이 때 텍스트의 색깔을 흰색으로 지정하지 않았음에도 흰색으로 변경된 것을 확인할 수 있다. 이는 androidx.compose.material3.Surface와 같은 Material 구성요소는 앱에 넣고자 하는 공통 기능(텍스트에 적절한 색상 선택과 같은)을 처리하여 더 나은 환경을 만들도록 빌드되기 때문이다.

## Modifier

`Surface`와 `Text`와 같은 대부분의 Compose UI요소는 `modifier`매개변수를 선택적으로 허용한다. 수정자는 **상위 요소 레이아웃 내에서** UI 요소가 배치되고 표시되고 동작하는 방식을 UI 요소에 알려준다.

```kotlin
@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}
```

## 재사용

Composable 함수는 기본적으로 빈 수정자가 할당되는 수정자 매개변수를 포함하는 것이 좋다. 이를 이용해 Composable 함수를 재사용하여 코드 중복을 피할 수 있다. 다음의 코드는 MyApp이라는 함수를 만들어 재사용한 예시이다.

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ComposeCodelabTheme {
                MyApp(modifier = Modifier.fillMaxSize())
            }
        }
    }
}

@Composable
private fun MyApp(modifier: Modifier = Modifier) {
    Surface(
        modifier = modifier,
        color = MaterialTheme.colorScheme.background
    ) {
        Greeting("Android")
    }
}
```

# Column, Row, Box

Compose의 세 가지 기본 표준 레이아웃 요소는 `Column`, `Row`, `Box`이다. 이 요소들은 Composable 함수이기 때문에 내부에 항목을 배치할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/29f26c6b-e5e3-4560-9085-06937b97f3ac/Untitled.png)

`Column` 의 경우 다음과 같이 사용할 수 있다.

```kotlin
@Composable
fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Column(modifier = Modifier.padding(24.dp)) {
            Text(text = "Hello, ")
            Text(text = name)
        }
    }
}
```

Composable 함수는 Kotlin의 다른 함수와 함께 사용할 수 있다. 이를 이용해 UI가 표시되는 방식에 영향을 주는 구문을 추가할 수 있다.

```kotlin
@Composable
private fun MyApp(
    modifier: Modifier = Modifier,
    names: List<String> = listOf("World", "Compose")
) {
    Column(modifier) {
				// Kotlin의 문법을 사용할 수 있다.
        for (name in names) {
            Greeting(name = name)
        }
    }
}

@Composable
fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Column(modifier = Modifier.padding(24.dp)) {
            Text(text = "Hello, ")
            Text(text = name)
        }
    }
}

// 소형 스마트폰의 너비인 320dp로 Preview가 변경된다.
@Preview(showBackground = true, widthDp = 320)
@Composable
fun DefaultPreview() {
    ComposeCodelabTheme {
        MyApp()
    }
}
```

이 상태에서 행의 마지막에 버튼을 추가해보자. `Button`은 material3 패키지에서 제공하는 컴포저블로, 컴포저블을 마지막 인수로 사용한다. 컴포저블은 함수이기 때문에 마지막 인수에 함수가 들어가면 후행 람다를 통해 괄호 밖으로 이동할 수 있으므로 모든 콘텐츠를 버튼에 하위 요소로 추가할 수 있다.

행 끝에 버튼을 배치하기 위해서는 `weight`를 사용하면 된다. weight 수정자는 요소를 유연하게 만들기 위해 가중치가 없는 다른 요소(현 코드에서의 버튼)를 효과적으로 밀어내어 요소의 사용 가능한 모든 공간을 채운다. 이 수정자는 `fillMaxWidth`수정자와 중복되기도 한다.

```kotlin
@Composable
private fun Greeting(name: String) {

    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { /* TODO */ }
            ) {
								// 후행 람다로 빠진 부분
                Text("Show more")
            }
        }
    }
}
```

## Compose에서의 상태

이 코드랩에서는 버튼을 누르면 항목을 펼쳐지도록 하고 다시 버튼을 누르면 항목을 접도록 만드려고 한다. 이를 구현하기 위해서는 각 항목이 펼쳐진 상태인지를 가리키는 값을 어딘가에 저장해야 하는데 이 값을 항목의 **상태(State)**라고 한다.

Compose 앱은 Composable 함수를 호출하여 데이터를 UI로 변환한다. 데이터가 변경되면 Compose는 새 데이터로 이러한 함수를 다시 실행하여 업데이트된 UI를 만드는데 이를 **리컴포지션(Recomposition)**이라고 한다. 또한 Compose는 데이터가 변경된 구성요소만 다시 구성하고 영향을 받지 않는 구성요소는 다시 구성하지 않고 건너뛰도록 개별 컴포저블에서 필요한 데이터를 확인한다. **또한 Composable 함수는 자주 실행될 수 있고 순서와 관계없이 실행될 수 있으므로 코드가 실행되는 순서 또는 이 함수가 다시 구성되는 횟수에 의존해서는 안된다.**

Composable 함수에 내부 상태를 추가해 변수를 추적하려면 `mutableStateOf`함수를 사용하면 된다. 이 함수를 사용하면 Compose가 이 `State`를 읽는 함수를 재구성한다.

`State`및 `MutableState` 는 어떤 값을 보유하고 그 값이 변경될 때마다 UI 업데이트(리컴포지션)를 유발하는 인터페이스이다.

하지만 Composable 함수 내의 변수에 `mutableStateOf`만 할당한다고 우리가 원하는 구현을 할 수는 없다. 왜냐하면 false 값(초기값)을 가진 변경 가능한 새 상태로 상태를 재설정하여 Composable 함수를 다시 호출하면 언제든 리컴포지션이 일어나기 때문이다. 따라서 리컴포지션에 따른 상태를 유지하기 위해서는 `remember`를 이용해 변경 가능한 상태를 기억해야 한다.

```kotlin
@Composable
fun Greeting() {
    val expanded = remember { mutableStateOf(false) }
    /* 
    ...
     */
}
```

remember는 리컴포지션을 방지하는데 사용되기 때문에 상태가 재설정되지 않는다.

화면의 서로 다른 부분에서 동일한 Composable 함수를 호출하는 경우 자체 상태 버전을 가진 UI 요소를 가지기 때문에 **내부 상태는 클래스의 private 변수**로 생각하면 된다.

`remember`가 가지고 있는 문제점이 있다. `remember`함수는 Composable이 컴포지션에 유지되는 동안에만 작동하기 때문에 앱에 configuration change가 발생하면 모든 상태가 손실된다. configuration change에서도 상태를 유지하기 위해서는 `remember` 대신 `rememberSaveable`을 사용하면 된다.

## 상태 호이스팅(Hoisting)

Composable 함수에서 여러 함수가 읽거나 수정하는 상태는 공통의 상위 항목에 위치해야 한다. 이렇게 상위 항목에 위치시키는 프로세스를 상태 호이스팅(들어 올린다, 끌어올린다)이라고 한다. 상태를 호이스팅할 수 있게 만들면 상태가 중복되지 않고 버그가 발생하는 것을 방지할 수 있으며 Composable 함수를 재사용할 수 있고 훨씬 쉽게 테스트할 수 있다.

상태를 저장하는 변수를 만들 때 `=` 대신 `by` 키워드를 사용하면 .value를 이용하지 않아도 값에 접근할 수 있다.

```kotlin
// shouldShowOnBoarding로 바로 접근 가능
var shouldShowOnBoarding by remember { mutableStateOf(true) }
```

온보딩 화면을 만들고 상태를 저장하는 변수를 만들어서 continue 버튼을 눌렀는지의 여부에 따라 온보딩 화면을 보여줄 것인지, Greetings 화면을 보여줄 것인지 구현해보았다. 각각의 화면은 Preview를 이용해 미리보기를 할 수 있도록 했으며 MyApp이라는 최상위 Composable 함수를 만들어 상태(continue 버튼을 눌렀는지에 대한)를 호이스팅하도록 했다.

```kotlin
@Composable
fun MyApp(modifier: Modifier = Modifier) {
		// 상태 호이스팅
    var shouldShowOnBoarding by remember { mutableStateOf(true) }

    Surface(modifier) {
        if (shouldShowOnBoarding) {
            OnboardingScreen(onContinueClicked = { shouldShowOnBoarding = false })
        } else {
            Greetings()
        }
    }
}

@Composable
private fun Greetings(
    modifier: Modifier = Modifier,
    names: List<String> = listOf("World", "Compose")
) {
    Column(modifier = modifier.padding(vertical = 4.dp)) {
        for (name in names) {
            Greeting(name = name)
        }
    }
}

@Preview(showBackground = true, widthDp = 320)
@Composable
private fun GreetingsPreview() {
    ComposeCodelabTheme {
        Greetings()
    }
}

@Preview
@Composable
fun MyAppPreview() {
    ComposeCodelabTheme {
        MyApp(Modifier.fillMaxSize())
    }
}

@Composable
fun OnboardingScreen(
    onContinueClicked: () -> Unit,
    modifier: Modifier = Modifier
) {
    Column(
        modifier = modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("Welcome to the Basics Codelab!")
        Button(
            modifier = Modifier.padding(vertical = 24.dp),
            onClick = onContinueClicked
        ) {
            Text("Continue")
        }
    }
}

@Preview(showBackground = true, widthDp = 320, heightDp = 320)
@Composable
fun OnboardingPreview() {
    ComposeCodelabTheme {
        OnboardingScreen(onContinueClicked = {})
    }
}
```

MyApp에서 shouldShowOnBoarding은 온보딩 화면과 공유해야 하지만, 직접 전달하지 않는다. OnboardingScreen이 상태를 변경하도록 하는 대신, 사용자가 continue 버튼을 눌렀을 때 상태가 변경되도록 코드를 작성하는 것이 좋다. 이러한 이벤트를 전달하기 위해서는 콜백을 이용하면 된다.

OnboardingScreen Composable 함수의 매개변수에 `onContinueClicked: () -> Unit` 다음과 같이 추가해준다. →을 기준으로 좌측은 인자 타입이고 우측은 반환 타입이다. 아무 것도 없기 때문에 인자가 필요없는 함수이고 Unit이 반환타입이므로 void와 같이 반환 값이 없어도 됨을 의미한다. 코틀린은 함수를 변수처럼 이용할 수 있고 매개변수로 지정할 수 있다.

OnboardingScreen을 사용하고 있는 MyApp으로 가서 추가된 `onContinueClicked: () -> Unit` 에 대한 값을 넣어주어야 한다. 해당 함수는 continue 버튼이 클릭 되었을 때 나타나는 결과에 대한 함수이고 함수의 결과가 onContinueClicked 변수에 저장되는 것이기 때문에 `OnboardingScreen(onContinueClicked = { shouldShowOnBoarding = false })`  왼쪽과 같이 설정할 수 있다.

즉, MyApp에서는 shouldShowOnBoarding이라는 상태를 가지고 있고, OnboardingScreen에서는 continue 버튼을 누르면(onclick) 콜백으로 인해 shouldShowOnBoarding의 값이 변하게 된다. OnboardingPreview에서는 shouldShowOnBoarding의 상태가 변하면 안되기 때문에 (미리보기 용도로 계속 온보딩 화면이 나와야 하므로)`OnboardingScreen(onContinueClicked = {})` 다음과 같은 콜백을 지정했다.

## LazyColumn과 LazyRow

**LazyColumn**과 **LazyRow**는 `RecyclerView`와 동일하다.

LazyColumn API는 범위 내에서 items 요소를 제공한다. LazyColumn은 RecyclerView와 같은 하위 요소를 재활용하지 않는다. Composable을 방출하는 것은 Android Views를 인스턴스화하는 것보다 상대적으로 비용이 적게 들기 때문에 LazyColumn은 스크롤 할 때 새 Composable을 방출하고 계속 성능을 유지한다.

```kotlin
@Composable
private fun Greetings(
    modifier: Modifier = Modifier,
    names: List<String> = List(1000) { "$it" }
) {
    LazyColumn(modifier = modifier.padding(vertical = 4.dp)) {
        items(items = names) { name ->
            Greeting(name = name)
        }
    }
}
```

## Animation 적용

크기 변경 애니메이션을 적용하기 위해 `animateDpAsState`라는 Composable 함수를 사용한다. 이 함수는 애니메이션이 완료될 때까지 애니메이션에 의해 객체의 value가 계속 업데이트되는 상태 객체를 반환한다. animateDpAsState는 애니메이션을 맞춤설정할 수 있는 animationSpec 매개변수를 선택적으로 사용한다. 스프링 기반의 애니메이션을 추가할 수 있는데, spring 사양은 시간과 관련된 매개변수를 사용하지 않고 물리적 속성(감쇠 및 강성)을 사용하여 애니메이션을 더 자연스럽게 만든다.

```kotlin
@Composable
fun Greeting(name: String) {
    var expanded by remember { mutableStateOf(false) }

    val extraPadding by animateDpAsState(
        if (expanded) 48.dp else 0.dp,
        animationSpec = spring(
            dampingRatio = Spring.DampingRatioMediumBouncy,
            stiffness = Spring.StiffnessLow
        )
    )

    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(
                modifier = Modifier
                    .weight(1f)
                    .padding(bottom = extraPadding.coerceAtLeast(0.dp))
            ) {
                Text(text = "Hello, ")
                Text(text = name, style = MaterialTheme.typography.headlineMedium)
            }
            ElevatedButton(
                onClick = { expanded = !expanded }
            ) {
                Text(if (expanded) "Show less" else "Show more")
            }
        }
    }
}
```

추가적으로 애니메이션을 공부하고 싶다면 spring용 매개변수, tween, repeatable, animateColorAsState 등을 찾아보자.

## 앱의 스타일 및 테마 설정

`ui/theme/Theme.kt` 파일을 보면 ComposeCodelabTheme안에 MaterialTheme을 사용하고 있는 것을 확인할 수 있다. MaterialTheme은 Material 디자인 사양의 스타일 지정 원칙을 반영한 Composable한 함수이다. 스타일 지정 정보는 content의 내부에 있는 컴포넌트로 하향 적용된다. 즉, content에서 ComposeCodelabTheme을 사용하고 있고 ComposeCodelabTheme안에 MaterialTheme이 있기 때문에 모든 하위 Composable 함수에서 MaterialTheme의 세 가지 속성, `colorScheme`, `typography`, `shapes`를 가져올 수 있다.

```kotlin
Column(modifier = Modifier
	    .weight(1f)
      .padding(bottom = extraPadding.coerceAtLeast(0.dp))
) {
    Text(text = "Hello, ")
    Text(text = name, style = MaterialTheme.typography.headlineMedium)
}
```

일반적으로 MaterialTheme 내부의 색상, 모양, 글꼴 스타일을 유지하는 것이 권장된다. 다크모드는 색상을 하드 코딩하는 경우 구현하기 어렵고 오류가 발생하기 쉬운 작업이 많이 요구되기 때문이다.

하지만 기존의 색상과 스타일에서 벗어나야 하는 경우가 반드시 생기는데, 이럴 때는 `copy` 함수를 사용하여 미리 정의된 스타일을 수정할 수 있다.

```kotlin
Text(
    text = name,
    style = MaterialTheme.typography.headlineMedium.copy(
        fontWeight = FontWeight.ExtraBold
    )
)
```

다크모드를 미리보기 하고 싶은 경우에는 @Preview안에 `uiMode = UI_MODE_NIGHT_YES`를 추가하면 된다.

```kotlin
@Preview(
    showBackground = true,
    widthDp = 320,
    uiMode = UI_MODE_NIGHT_YES,
    name = "Dark"
)
@Preview(showBackground = true, widthDp = 320)
@Composable
fun DefaultPreview() {
    BasicsCodelabTheme {
        Greetings()
    }
}
```

Color.kt 파일에서 16진수 하드 코딩 값을 넣어 새로운 색상을 정의할 수 있다.

```kotlin
val Navy = Color(0xFF073042)
val Blue = Color(0xFF4285F4)
val LightBlue = Color(0xFFD7EFFE)
val Chartreuse = Color(0xFFEFF7CF)
```

이렇게 하드코딩한 Color타입 변수들은 Theme.kt에서 할당하여 사용할 수 있다.

```kotlin
// 라이트 모드
private val LightColorScheme = lightColorScheme(
    surface = Blue,
    onSurface = Color.White,
    primary = LightBlue,
    onPrimary = Navy
)

// 다크 모드
private val DarkColorScheme = darkColorScheme(
    surface = Blue,
    onSurface = Navy,
    primary = Navy,
    onPrimary = Chartreuse
)
```

preview에서는 동적 색상(dynamic color)가 사용되기 때문에 미리보기에서 색상이 변경된 것을 보고 싶다면 `dynamicColor`의 Boolean값을 설정해야 한다.

```kotlin
@Composable
fun BasicsCodelabTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    // Dynamic color is available on Android 12+
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            (view.context as Activity).window.statusBarColor = colorScheme.primary.toArgb()
            ViewCompat.getWindowInsetsController(view)?.isAppearanceLightStatusBars = darkTheme
        }
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

## 버튼을 아이콘으로 대체와 문자열 리소스 사용

`IconButton` 이라는 Composable 함수를 사용하면 된다. 하위 요소에 `Icon`을 가지고 있다. 문자열을 하드 코딩하지 않고 string.xml에서 꺼내 쓰는 방법은 다음과 같이 `stringResource` 속성을 활용하면 된다.

```kotlin
@Composable
private fun cardContent(name: String) {
    var expanded by remember { mutableStateOf(false) }

    Row(
        modifier = Modifier
            .padding(12.dp)
            .animateContentSize(
                animationSpec = spring(
                    dampingRatio = Spring.DampingRatioMediumBouncy,
                    stiffness = Spring.StiffnessLow
                )
            )
    ) {
        Column(
            modifier = Modifier
                .weight(1f)
                .padding(12.dp)
        ) {
            Text(text = "Hello, ")
            Text(
                text = name, style = MaterialTheme.typography.headlineMedium.copy(
                    fontWeight = FontWeight.ExtraBold
                )
            )
            if (expanded) {
                Text(
                    text = ("Composem ipsum color sit lazy, " +
                            "padding theme elit, sed do bouncy. ").repeat(4),
                )
            }
        }
        IconButton(onClick = { expanded = !expanded }) {
            Icon(
                imageVector = if (expanded) Icons.Filled.ExpandLess else Icons.Filled.ExpandMore,
                contentDescription = if (expanded) {
                    stringResource(id = R.string.show_less)
                } else {
                    stringResource(id = R.string.show_more)
                }
            )
        }
    }
}
```