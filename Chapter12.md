# 12장 ActionBar와 메뉴
## 목차
[12.1.](#121-ActionBar) ActionBar
  - [12.1.1.](#1211-ActionBar-구성) ActionBar 구성
  - [12.1.2.](#1212-표시-옵션) 표시 옵션
  
[12.2.](#122-안드로이드-API-Level과-하위-호환성) 안드로이드 API Level과 하위 호환성
  
[12.3.](#123-메뉴) 메뉴
  - [12.3.1.](#1231-메뉴-작성-방법) 메뉴 작성 방법
  - [12.3.2.](#1232-MenuInflater-활용) MenuInflater 활용
  - [12.3.3.](#1233-메뉴-다양하게-이용하기) 메뉴 다양하게 이용하기


## 12.1. ActionBar
- API Level 11 버전부터 추가된 개념.
- 간단하게 이해한다면 능력이 많은 타이틀 바.
- 액티비티는 기본적으로 ActionBar를 출력해 주는데, 특별히 구성하지 않으면 타이틀 바처럼 보임.
- 하지만 단순한 타이틀 바가 아니라 이곳에 다양한 것들을 추가할 수 있음.

### 12.1.1. ActionBar 구성
- 액티비티가 출력되는 전체 창을 Window라 부르며, Window는 크게 Content 영역과 ActionBar 영역으로 나눔.
- Content 영역은 액티비티 내에서 setContentView() 함수로 출력하는 내용이 나오는 곳이고, ActionBar는 Window의 상단에 출력.
- 개발자가 특별하게 하지 않으면 기본 타이틀 문자열이 나오지만, App Icon, View Control, Action Button, Overflow Menu 등이 나오도록 구성할 수 있음.


res/values/styles.xml 파일에서 액티비티에 적용되는 테마를 다음처럼 지정하면 ActionBar는 화면에 출력되지 않습니다.
```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <!-- Customize your theme here. -->
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
</style>
```


화면에 출력된 ActionBar 객체를 프로그램에서 획득하고 싶다면 getSupportActionBar() 함수를 이용.
```java
actionBar = getSupportActionBar();
```
ActionBar 객체를 획득한 다음에는 다양하게 제어할 수 있는데, 기본적으로 show(), hide()가 있음.
```java
public void onClick(View v) {
    if (v == btn1) {
        actionBar.show();
    }
    else if {
        actionBar.hide();
    }
}
```
때로는 ActionBar가 Content 영역 위에 떠 있는 것처럼 보여야 하는 경우가 있는데, 예를 들면 전체 화면에 이미지가 출력되는데, ActionBar가 그 이미지 위에 표시되게 처리하고 싶은 경우에 AppTheme에 windowActionBarOverlay를 사용하면 됨.
```xml
<item name="windowActionBarOverlay">true</item>
```
ActionBar 자체의 색상값 때문에 Content의 일부분이 완전히 가려지는데 이때는 colorPrimary 값을 투명하게 지정하면 됨.
```xml
<item name="colorPrimary">#00000000</item>
```

### 12.1.2. 표시 옵션
- ActionBar에는 App Icon이나 타이틀 문자열 등 여러 항목이 표시되는데요, ActionBar를 어떻게 구성할지를 설정하는 함수들이 제공됨.
우선 AndroidManifest.xml 파일에 ActionBar를 위한 label을 설정.
```xml
<activity android:name=".MainActivity" android:label="상세 보기">
```

ActionBar를 다양하게 구성하기 위한 setter 함수를 이용해 구성요소가 화면에 나와야 하는지를 제어합니다.
함수 | 의미
| --- | --- |
| setDisplayHomeAsUpEnabled | 아이콘을 Up 이미지로 표시 설정 |
| setDisplayShowCustomEnabled | CustomView 표시 설정 |
| setDisplayShowHomeEnabled | 홈 아이콘 표시 설정 |
| setDisplayShowTitleEnabled | 타이틀 문자열 표시 설정 |
| setDisplayUseLogoEnabled | 로고 표시 설정 |
```java
ActionBar actionBar = getSupportActionBar();
actionBar.setDisplayShowHomeEnabled(true);
actionBar.setDisplayHomeAsUpEnabled(true);
actionBar.setIcon(R.drawable.icon);
```
위의 코드는 ActionBar 구성요소 중 무엇을 보이게 할 것인지를 설정하는 코드이며, 각 구성요소의 내용은 다음의 함수를 이용해서 지정할 수 있음.
함수 | 의미
| --- | --- |
| setLogo(@DrawableRes int resId) | 리소스로 로고 지정 |
| setLogo(Drawable logo) | 이미지 객체로 로고 지정 |
| setTitle(@StringRes int resId) | 리소스로 타이틀 지정 |
| setTitle(CharSequence title) |문자열로 타이틀 지정 |
| setSubtitle(int resId) | 리스소르 서브 타이틀 지정 |
| setSubtitle(CharSequence subtitle) | 문자열로 서브 타이틀 지정 |
| setCustomView(int layoutResId) | 리소스로 커스텀 뷰 지정 |
| setCustomView(View view) | 뷰로 커스텀 뷰 지정 |


HOME_AS_UP에 의해 왼쪽 화살표 아이콘이 표시되는데, 이 아이콘을 사용자가 클랙했을 때의 이벤트를 처리하려면 onOptionsItemSelected() 함수를 이용. 이 함수는 메뉴의 이벤트 처리 함수로 이후에 자세하게 다룸.
```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    if (item.getItemId() == android.R.id.home) {
        Toast t = Toast.makeText(this, "HOME AS UP Click", Toast.LENGTH_SHORT);
        t.show();
        return true;
    }
    return super.onContextItemSelected(item);
}
```
이벤트를 처리할 때 중요한 건 HOME_AS_UP 아이콘의 기본 id 값은 android.R.id.home으로 고정되어 있으므로 이 id값으로 식별하여 이용합니다.


## 12.2. 안드로이드 API Level과 하위 호환성
- 앞에서 ActionBar를 설명했는데, ActionBar는 API Level 11부터 제공하는 클래스로 하위 호환성 문제가 발생할 수 있음.
- API 하위 호환성과 관련된 문제는 ActionBar만의 문제가 아니므로 잘 정리해야 할 필요가 있음.
- minSdkVersion이라는 개념이 있는데, 이 설정은 아주 중요.
- minSdkVersion으로 설정된 하위 버전의 스마트폰에는 우리의 앱이 설치되지 않음.
- minSdkVersion을 9로 설정했다면 9버전까지는 에러가 발생하지 않게 신경 써 주어야 함, 이것이 하위 호환성이라고 함.

하위 버전에서 에러가 발생하지 않게 개발할 수 있는 방법.
- 구글의 Support 라이브러리의 도움을 받는다.
- 오픈소스 라이브러리의 도움을 받는다.
- 개발자 코드에서 버전을 직접 식별해서 처리한다.
여기서는 구글의 Support 라이브러리에 대한 소개와 개발자 코드에서 버전을 식별하는 방법을 소개하겠음.


Support라이브러리 이용.
- 표준 라이브러리 아니고, 구글의 라이브러리.
- 목적은 하위 호환성을 지원하기 위함.
안드로이드 표준 라이브러리 이외의 모든 라이브러리는 그레이들 파일에 dependencies 연결해야만 사용할 수 있음.
```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])

    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.1'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
```
- appcompat가 자동으로 추가되어 있는데, 이것이 support 라이브러리를 이용할 수 있게 함.
- ActionBar의 하위 호환성 문제 같은 경우, appcompat 라이브러리의 AppCompatActivity를 활용해 처리할 수 있음.
- 지금까지 만들었던 모든 액티비티의 선언을 보면 AppCompatActivity가 상속받아 만들어졌음.
- AppCompatActivity는 Activity의 서브 클래스이며 appcompat 라이브러리에서 제공하는 클래스.
- 결과적으로 AppCompat Activity가 액티비티의 ActionBar 하위 호환성을 처리해 준다고 보면 됨.

appcompat 라이브러리의 도움을 받아 작성하면 몇 가지 지켜야 하는 규칙이 있음.
- 액티비티를 위한 테마 설정이 appcompat에서 제공하는 테마를 상속받아 정의해야 함.
- ActionBar를 획득할 때 getActionBar()가 아닌 getSupportActionBar() 함수를 이용해야 함.

개발자 코드로 버전 확인.
- Support 라이브러리가 모든 하위 호환성을 제공한다고 볼 수는 없음.
- 때로는 개발자 코드에서 API Level을 체크해서 분기 처리해야 함.
```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    // ...
}
else {
    // ...
}
```


## 12.3. 메뉴
- 액티비티의 선택적 구성 요소.
- 액티비티가 출력될 창을 Window라고 부르며 Window는 상단의 ActionBar와 하단의 Content 구성.
- 메뉴를 추가하더라도 Activity Content 영역에 표시되지 않고 ActionBar에 포함되어 표시됨.

### 12.3.1. 메뉴 작성 방법
- 자바 코드로 작성하는 방법과 XML 파일을 이용하는 두 가지 방법이 있음.
함수 | 의미
| --- | --- |
| onCreateOptionsMenu | 메뉴가 만들어질 때 최초 한 번 호출 |
| onPrepareOptionsMenu | 메뉴가 화면에 보일 때마다 반복 호출 |
메뉴가 고정이면 한 번만 작업하면 되므로 onCreateOptionsMenu 함수를 이용하고, 메뉴를 상황에 따라 다르게 구성해야 한다면 onPrepareOptionsMenu 함수를 이용합니다.
```java
@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuItem item1 = menu.add(0, 0, 0, "슬라이드쇼");
        return true;
    }
```
두 함수 모두 매개변수로 Menu 객체가 넘어오고 이 객체에 메뉴를 넣으면 됩니다. 이때 다음과 같은 add() 함수를 사용.
- add(CharSequence title)
- add(int groupId, int itemId, int order, int titleRes)
- add(int groupId, int itemId, int order, CharSequence title)
itemId는 메뉴의 식별자.


액티비티에 메뉴를 추가하면 ActionBar 오른쪽에 메뉴가 있다는 표시로 오버플로 아이콘이 나타남.

아이콘을 클릭해서 나오는 것을 오버플로 메뉴라고 부릅니다.

다음은 MenuItem의 메뉴 구성을 위한 함수들입니다.
- setIcon(int iconRes)
- setShortcut(char numricChar, char alphaChar)
- setTitle(CharSequence title)
- setVisible(boolean visible)
- setEnabled(boolean enabled)
위 함수를 이용하여 메뉴를 다양하게 구성 가능, 아이콘 이미지 문자열, 화면에 보이는 상태와 활성 상태 등을 조정 가능.

사용자가 메뉴를 클릭했을 때 이벤트 처리는 onOptionsItemSelected() 함수에서 처리.
```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    if (item.getItemId() == 0) {
            
    }
    return super.onContextItemSelected(item);
}
```
메뉴가 선택된 순간 자동 호출되며 매개변수로 선택한 메뉴 객체가 전달됨.

### 12.3.2. MenuInflater 활용
- 액티비티가 실행될 때마다 다르지 않고 항상 똑같다면 코드에서 작업하지 않고 리소스 XML을 이용하여 메뉴를 구현하는 방법도 있습니다. 
- XML을 만들고 리소스화해서 메뉴를 구현하는 방법입니다.
- 메뉴 XML 파일이 저장될 위치는 res 폴더 하위의 menu 폴더입니다.
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item 
        android:id="@+id/menu1" 
        android:title="선택..."/>
    <item 
        android:id="@+id/menu2"
        android:title="레이아웃" />
</menu>
```
자바코드에서 작성했던 메뉴 구성을 XML로 작성. 액티비티에 메뉴를 적용하려면 자바 코드에서 MenuInflater를 이용.
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.game_menu, menu);
    return true;
}
```
item 태그에 메뉴를 식별하기 위한 id 값을 R 파일을 통해 등록하게 되며, 자바 코드에서 메뉴를 식별할 때 R 파일의 int 변수로 식별해서 이벤트 처리하면 됨.
```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    if (item.getItemId() == R.id.menu1) {
            
    }
    return super.onContextItemSelected(item);
}
```

### 12.3.3. 메뉴 다양하게 이용하기
아이콘 출력.
- 메뉴에 아이콘 이미지와 함께 나오게 할 때도 있음.
- 그런데 자바 코드나 XML에 명시하였더라도 화면에 출력되지는 않음.
- 아이콘을 명시하고 싶으면 다음의 코드처럼 android:icon 속성을 이용.

서브 메뉴.
액션 버튼.
ActionView.
actionLayout.
ContextMenu.
