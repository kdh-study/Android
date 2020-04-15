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
        android:bufferType="spannable"/>
```
- TextView의 bufferType 기본값은 "normal", 문자열의 Spannable 값을 적용하지 않는다는 의미.
- Spannable 정보를 이용하여 문자열을 나타내려면 bufferType을 "spannable"로 지정해야 합니다.
- EditText에도 문자열 데이터가 출력, 사용자 입력 문자열이지만 Spannable 적용 가능.
- 그런데 EditText의 bufferType은 기본값이 "editable"이고 editable은 spannable을 내장하는 개념이므로 EditText에는 bufferType을 선언하지 않아도 Spannable 적용 가능.


다음은 코드에서 TextView에 출력되는 문자열에 Spannable을 적용하는 방법.
 ``` java
  String data = "복수초 \n img \n 이른봄 설산에서 만나는 복수초는 모든 야생화 찍사들의 로망이 아닐까 싶다.";

  SpannableStringBuilder builder = new SpannableStringBuilder(data);

  int start = data.indexOf("img");
  if (start > -1) {
      int end = start + "img".length();
      Drawable dr = ResourcesCompat.getDrawable(getResources(), R.drawable.img1, null);
      dr.setBounds(0, 0, dr.getIntrinsicWidth(), dr.getIntrinsicHeight());
      ImageSpan span = new ImageSpan(dr);
      builder.setSpan(span, start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
  }
 ```
 
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

- fromHtml(String source)
- fromHtml(String source, Html.ImageGetter imageGetter, Html.TagHandler tagHandler)
- fromHtml(String source, int flages, Html.ImageGetter imageGetter, Html.TagHandler gatHandler)

## 11.2. WebView
### 11.2.1. WebView 활용
### 11.2.2. 자바스크립트와 자바 연동
### 11.2.3. 이벤트 처리
## 11.3. 기타 유용한 뷰
### 11.3.1. 콤보박스 Spinner
### 11.3.2. 텍스트 자동완성 AutoCompleteTextView
### 11.3.3. 프로그레스바 ProgressBar
### 11.3.4. 값을 입력받는 프로그레스바 SeekBar
