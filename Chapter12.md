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


## 12.3. 메뉴
### 12.3.1. 메뉴 작성 방법
### 12.3.2. MenuInflater 활용
### 12.3.3. 메뉴 다양하게 이용하기
