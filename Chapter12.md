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


## 12.3. 메뉴
### 12.3.1. 메뉴 작성 방법
### 12.3.2. MenuInflater 활용
### 12.3.3. 메뉴 다양하게 이용하기
