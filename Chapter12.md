# 12장 ActionBar와 메뉴
## 목차
[12.1.](#111-ActionBar) ActionBar
  - [12.1.1.](#1111-ActionBar-구성) ActionBar 구성
  - [12.1.2.](#1112-표시-옵션) 표시 옵션
  
[12.2.](#112-안드로이드-API-Level과-하위-호환성) 안드로이드 API Level과 하위 호환성
  
[12.3.](#113-메뉴) 메뉴
  - [12.3.1.](#1131-메뉴-작성-방법) 메뉴 작성 방법
  - [12.3.2.](#1132-MenuInflater-활용) MenuInflater 활용
  - [12.3.3.](#1133-메뉴-다양하게-이용하기) 메뉴 다양하게 이용하기

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
## 12.2. 안드로이드 API Level과 하위 호환성
## 12.3. 메뉴
### 12.3.1. 메뉴 작성 방법
### 12.3.2. MenuInflater 활용
### 12.3.3. 메뉴 다양하게 이용하기
