# 11장 다양한 뷰 활용
## 목차
[11.1.](#111-spannable) Spannable
  - [11.1.1.](#1111-Spannable의-필요성) Spannable의 필요성
  - [11.1.2.](#1112-Spannable-적용) Spannable 적용
  - [11.1.3.](#1113-fromHtml()-함수로-적용) fromHtml() 함수로 적용
  
[11.2.](#112-spannable의-필요성) WebView
  - [11.2.1.](#1121-WebView-활용) WebView 활용
  - [11.2.2.](#1122-자바스크립트와-자바-연동) 자바스크립트와 자바 연동
  - [11.2.3.](#1123-이벤트-처리) 이벤트 처리
  
[11.3.](#113-기타-유용한-뷰) 기타 유용한 뷰
  - [11.3.1.](#1131-콤보박스-Spinner) 콤보박스 Spinner
  - [11.3.2.](#1132-텍스트-자동완성-AutoCompleteTextView) 텍스트 자동완성 AutoCompleteTextView
  - [11.3.3.](#1133-프로그레스바-ProgressBar) 프로그레스바 ProgressBar
  - [11.3.4.](#1134-값을-입력받는-프로그레스바-SeekBar) 값을 입력받는 프로그레스바 SeekBar


## 11.1. Spannable
- 뷰가 아닌 문자열 데이터를 표현하기 위한 클래스들입니다.
- TextView의 문자열 데이터를 Spannable 클래스를 이용해 얼마나 다양하게 표시할 수 있는지 보겠습니다.


### 11.1.1. Spannable의 필요성
- 문자열 데이터뿐만 아니라, UI 정보까지 포함.
- Hello World.
  - 'H'는 굵게, 'W'는 붉은색.
- TextView.
  - textStyle="bold", textColor="#FF000"
  - 뷰의 설정으로 UI를 조정하면 모든 문자열이 굵게, 붉은색으로 표시됨.
- UI를 뷰의 설정으로 표현하지 않고, 데이터를 표현하는 정보가 담긴 데이터소를 가지고, 뷰는 그 정보를 참조해서 화면에 출력하는 방식이 필요.


### 11.1.2. Spannable 적용
TextView가 Spannable을 참조해서 화면에 출력하려면 bufferType이라는 속성을 지정해야 합니다.
 ``` xml
 <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="TextView" 
        android:bufferType="spannable" />
```
- TextView의 bufferType 기본값은 "normal", 문자열의 Spannable 값을 적용하지 않는다는 의미.
- Spannable 정보를 이용하여 문자열을 나타내려면 bufferType을 "spannable"로 지정해야 합니다.
- EditText에도 문자열 데이터가 출력, 사용자 입력 문자열이지만 Spannable 적용 가능.
- 그런데 EditText의 bufferType은 기본값이 "editable"이고 editable은 spannable을 내장하는 개념이므로 EditText에는 bufferType을 선언하지 않아도 Spannable 적용 가능.


다음은 코드에서 TextView에 출력되는 문자열에 Spannable을 적용하는 방법.
``` java
String data = "복수초 \n img \n 이른봄 설산에서 만나는 복수초는 모든 야생화 찍사들의 로망이 아닐까 싶다.";
// Spannable을 포함하는 문자열.
SpannableStringBuilder builder = new SpannableStringBuilder(data);

// img 문자열의 시작위치.
int start = data.indexOf("img");
if (start > -1) {
    // img 문자열의 끝 위치.
    int end = start + "img".length();
    // 이미지 획득.
    Drawable dr = ResourcesCompat.getDrawable(getResources(), R.drawable.img1, null);
    // 이미지의 화면 출력정보 설정.
    dr.setBounds(0, 0, dr.getIntrinsicWidth(), dr.getIntrinsicHeight());
    // ImageSpan 준비.
    ImageSpan span = new ImageSpan(dr);
    // SpannableStringBuilder에 ImageSpan 적용.
    builder.setSpan(span, start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
}
```

``` java
builder.setSpan(span, start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```
이미지 정보를 가지는 ImageSpan을 위치를 지정해서 SpannableStringBuilder에 적용하였으며, 마지막 매개변수 부분은 span이 적용된 좌우측에 문자열이 추가될 때, 이 span 값을 적용할 건지에 대한 설정입니다.

설정값의 종류와 의미는 다음과 같습니다.
설정값 | 의미
| --- | --- |
| SPAN_EXCLUSIVE_EXCLUSIVE | 왼쪽 제거, 오른쪽 제거 |
| SPAN_EXCLUSIVE_INCLUSIVE | 왼쪽 제거, 오른쪽 포함 |
| SPAN_INCLUSIVE_EXCLUSIVE | 왼쪽 포함, 오른쪽 제거 |
| SPAN_INCLUSIVE_INCLUSIVE | 왼쪽 포함, 오른쪽 포함 |


라이브러리에서 문자열을 다양하게 꾸미기 위해 다음과 같은 Span 클래스를 제공.
클래스 | 의미
| --- | --- |
| ForegroundColorSpan | 전경 색상값 적용 |
| BackgroundColorSpan | 배경 색상값 적용 |
| UnderlineSpan | 밑줄 적용 |
| ClickableSpan | 문자열 클릭 이벤트 적용 |
| AbsoluteSizeSpan | 크기 변경 적용 |
| ImageSpan | 이미지 데이터 적용 |
| RelativeSizeSpan | 크기 적용 |
| StyleSpan | 스타일 적용 |
| URLSpan | URL 링크 모양과 클릭 이벤트 적용 |


### 11.1.3. fromHtml() 함수로 적용
- Spannable 외에 다른 방법.
- fromHtml() 함수를 이용해 HTML 태그로 표현하는 방법.
- 내부적으로는 Spannable을 이용.
``` java
String html = "<font color='RED'>얼레지</font> <br/> <img src='img1'/> <br/> 곰배령에서 만난 봄꽃";
```
문자열 중간중간 HTML 태그가 삽입되어 있는 구조.
- formHtml()함수는 세 가지 형태로 제공.
  - fromHtml(String source)
  - fromHtml(String source, Html.ImageGetter imageGetter, Html.TagHandler tagHandler)
  - fromHtml(String source, int flages, Html.ImageGetter imageGetter, Html.TagHandler gatHandler)
두 번째 함수는 API Lev 24에서 deprecated 되었으며, 세 번째 함수로 대체되었습니다.

하지만 하위 호환성 문제로 두 번째 함수를 계속 이용할 수도 있습니다.
``` java
htmlView.setText(Html.fromHtml(html, new MyImageGetter(), null));
```
fromHtml() 함수의 두 번째 매개변수는 이미지를 획득하기 위한 사용자 정의 클래스입니다.

다음처럼 Html.ImageGetter를 구현하여 이미지 획득 클래스를 만듭니다.
``` java
class MyImageGetter implements Html.ImageGetter {

    @Override
    public Drawable getDrawable(String source) {
        if (source.equals("img1")) {
            // 이미지 획득.
            Drawable dr = ResourcesCompat.getDrawable(getResources(), R.drawable.img2, null);
            // 이미지의 화면 출력정보 설정.
            dr.setBounds(0, 0, dr.getIntrinsicWidth(), dr.getIntrinsicHeight());
            // 리턴시킨 dRAWABLE 객체가 <img> 위치에 출력.
            return dr;
        }
        // 이미지가 없으면 null 반환.
        return null;
    }
}
```


## 11.2. WebView
- 앱에서 웹 페이지를 보여주는 내장 브라우저 역할.
- 브라우저 능력을 가지고 있지만, URL 입력 바(Navigation Bar), 뒤로가기 등의 각종 버튼을 제공하지 않으며 화면에 HTML이 뜨는 영역만 제공하는 뷰.
- 액티비티 화면을 구성할 때 화면 전체나 일부분을 HTML로 구성하기 위해 사용하는데요, 특히, 하이브리드 앱으로 개발할 때 핵심 기술입니다.


### 11.2.1. WebView 활용
- 코드에서 HTML 파일을 명시해 주면 HTML 파일을 파싱하여 화면에 출력.
- 혹은 URL을 지정해 주면 HTTP 통신으로 서버에서 넘오오는 HTML로 화면을 구성.
- WebView를 이용하려면 HTML 파일이 있어야 함.
- 서버 파일을 이용할 수도 있고, 앱에 내장한 파일을 이용할 수도 있음.

서버 URL로 HTML 파일을 가져와 WebView에 띄우면 앱 내부에서 네트워킹이 발생하는데, 이를 위한 퍼미션이 AndroidManifest.xml 파일에 설정되어 있어야 합니다.
``` xml
<uses-permission android:name="android.permission.INTERNET"/>
```

화면에 출력하기 위해 레이아웃 XML 파일에 WebView를 준비.
``` xml
<WebView
        android:id="@+id/webview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

이렇게 하면 화면에 WebView가 출력되는데, 위의 정보만으로는 화면에 아무것도 나오지 않고, 코드에서 URL을 지정해 주어야 합니다.
``` java
WebSettings settings = webview.getSettings();
// 자바스크립트를 가능하게 설정해 주어야 합니다.
settings.setJavaScriptEnabled(true);
webview.loadUrl("https://m.daum.net/");
```

다음은 앱 내부의 HTML 파일을 이용하는 방법입니다.
- HTML 파일을 앱 내부에 둘려면 assets 폴더를 이용.
- assets 폴더는 리소스 대상이 아닌 나머지 개발자 파일이 위치하는 곳이므로 HTML, CSS, JavaScript 파일 등을 이곳에 두면 됨.
``` java
webview.loadUrl("file:///android_asset/text.html");
```
assets에 있는 파일도 loadUrl() 함수를 이용해 로딩할 수 있습니다.

### 11.2.2. 자바스크립트와 자바 연동

### 11.2.3. 이벤트 처리
## 11.3. 기타 유용한 뷰
### 11.3.1. 콤보박스 Spinner
### 11.3.2. 텍스트 자동완성 AutoCompleteTextView
### 11.3.3. 프로그레스바 ProgressBar
### 11.3.4. 값을 입력받는 프로그레스바 SeekBar
