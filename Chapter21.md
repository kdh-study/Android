# 21장 콘텐츠 프로바이더
## 목차
[21.1.](#211-콘텐츠-프로바이더-이해) 콘텐츠 프로바이더 이해
  - [21.1.1.](#2111-콘텐츠-프로바이더-구조) 콘텐츠 프로바이더 구조
  - [21.1.2.](#2112-콘텐츠-프로바이더-작성법) 콘텐츠 프로바이더 작성법
  - [21.1.3.](#2113-콘텐츠-프로바이더-이용) 콘텐츠 프로바이더 이용
  
[21.2.](#212-구글-기본-앱의-콘텐츠-프로바이더-이용) 구글 기본 앱의 콘텐츠 프로바이더 이용
  - [21.2.1.](#2121-주소록-앱-데이터-획득) 주소록 앱 데이터 획득
  - [21.2.2.](#2122-갤러리-앱-데이터-획득) 갤러리 앱 데이터 획득
  
[21.3.](#213-갤러리-앱-연동과-이미지-이용을-위한-라이브러리) 갤러리 앱 연동과 이미지 이용을 위한 라이브러리
  - [21.3.1.](#2131-Glide-라이브러리) Glide 라이브러리
  - [21.3.2.](#2132-Matisse-라이브러리) Matisse 라이브러리

## 21.1. 콘텐츠 프로바이더 이해
- 앱 간의 **데이터 공유**를 목적으로 사용되는 컴포넌트.
- 데이터를 외부 앱에 공유하거나 반대로 **외부 앱이 가지고 있는 데이터를 획득할 목적**으로 사용.

### 21.1.1. 콘텐츠 프로바이더 구조
- 기본적으로 보안 문제라던가 패키지의 구조 문제 같은 것들로 인해 다른 앱의 데이터에 접근할 수 없음.
- 하지만 콘텐츠 프로바이더를 이용하면 접근할 수 있음.
- 우선 A앱의 데이터를 이용하려면 데이터를 가지고 있는 A앱의 개발자가 콘텐츠 프로바이더를 만들어 주어야 함.
![콘텐츠 제공자가 저장소에 대한 액세스를 관리하는 방법의 개요 다이어그램.](https://user-images.githubusercontent.com/62408252/81697442-fd622500-949f-11ea-902c-6b0863ed2d90.png)
- 즉 자신의 앱에 콘텐츠 프로바이더를 만든다는 것은 자신의 데이터를 일정 정도 외부 앱에 오픈하겠다는 의미.
- 그리고 외부 앱인 B앱은 A앱의 데이터에 직접 접근하는 게 아니라, A앱에서 만들어준 콘텐츠 프로바이더의 함수를 이용하여 데이터를 이용하는 구조.
- 직접 구현하기 때문에 보안상 문제가 되는 데이터를 제외하고 오픈하는 등의 작업이 가능.

### 21.1.2. 콘텐츠 프로바이더 작성법
- 콘텐츠 프로바이더를 작성하는 방법은 안드로이드 DBMS프로그램과 유사.
- 콘텐츠 프로바이더 내부에서 접근하는 데이터는 파일, 데이터베이스, Preference 혹은 메모리의 데이터.
```java
public class MyContentProvider extends ContentProvider {
    public MyContentProvider() {

    }

    @Override
    public boolean onCreate() { return false; }

    @Nullable
    @Override
    public Cursor query(@NonNull Uri uri, @Nullable String[] projection, @Nullable String selection, @Nullable String[] selectionArgs, @Nullable String sortOrder) {
        return null;
    }

    @Nullable
    @Override
    public String getType(@NonNull Uri uri) {
        return null;
    }

    @Nullable
    @Override
    public Uri insert(@NonNull Uri uri, @Nullable ContentValues values) {
        return null;
    }

    @Override
    public int delete(@NonNull Uri uri, @Nullable String selection, @Nullable String[] selectionArgs) {
        return 0;
    }

    @Override
    public int update(@NonNull Uri uri, @Nullable ContentValues values, @Nullable String selection, @Nullable String[] selectionArgs) {
        return 0;
    }
}
```
- ContentProvider 클래스를 상속받아 작성.
- 위의 모든 함수를 재정의 해야 함.
- 생명주기 함수는 onCreate() 함수 하나만 있으며, 최초에 한 번만 호출됨.
- query(), insert(), update(), delete() 함수는 외부 앱에서 필요할 때 호출됨.
- 이곳에서 적절한 데이터 획득, 저장, 수정, 삭제 작업을 하면 됨.
- 데이터를 외부 앱에 전달하는 용도로만 쓰려면, insert(), update(), delete() 함수 부분을 비워두면 됨.
***
- 콘텐츠 프로바이더도 안드로이드 **컴포넌트이므로 AndroidManifest.xml에 등록**해서 사용해야 함.
- 액티비티나 서비스, 브로드캐스트 리시버는 AndroidManifest.xml 파일에 각각 \<activity\>, \<service\>, \<receiver\> 태그로 등록할 때 생략할 수 없는 속성이 name뿐.
- 하지만 콘텐츠 프로바이더는 \<provider\> 태그로 등록하는데 name 외에 authorities라는 속성을 꼭 정의해 주어야 함.
```xml
<provider
            android:authorities="com.example.mycontentprovider"
            android:name=".MyContentProvider"
            android:enabled="true"
            android:exported="true"></provider>
```
- authorities 속성값은 개발자 임의의 문자열이지만, **유일해야 함**.
- 다른 앱의 콘텐츠 프로바이더와 같은 값이 대입되면 앱 자체가 사용자 스마트폰에 설치되지 않음.

### 21.1.3. 콘텐츠 프로바이더 이용
- 안드로이드 컴퍼넌트 중 인텐트로 실행하는 컴포넌트는 액티비티와 서비스 그리고 브로드캐스트 리시버이며, **콘텐츠 프로바이더는 인텐트와 전혀 상관이 없음**.
- 이유는 콘텐츠 프로바이더의 독특한 생명주기 때문.
- 액티비티와 서비스, 브로드캐스트 리시버는 부팅 완료 시점부터 어느 앱에서 어떤 이름의 컴포넌트를 가지고 있다는 정보가 시스템에 등록되지만, 실제 객체가 생성되는 시점은 어디선가 그 컴포넌트를 이용하기 위해 인텐트를 발생시키는 순간.
- 하지만 컨텐츠 프로바이더는 시스템에서 인지하는 순간 이용하지 않더라도 미리 생성해 놓습니다.
- 따라서 스마트폰이 부팅되면 여러 앱의 모든 콘텐츠 프로바이더가 생성됨.
- 외부 앱의 콘텐츠 프로바이더를 이용하는 곳에는 이렇게 **시스템에서 미리 생성해 놓은 콘텐츠 프로바이더 객체를 ContentResolver를 이용해 획득**하여 사용하면 됨.
- ContentResolver는 콘텐츠 프로바이더로 생성된 객체들을 담고 있는 관리자 역할의 클래스로 이해하면 됨.
- ContentResolver에는 모든 앱의 모든 콘텐츠 프로바이더가 등록되어 있음.
- 이때 식별자로 이용되는 것이 Uri 객체, 그래서 콘텐츠 프로바이더를 흔히 "Uri 모델로 식별되어 이용되는 컴포넌트"라는 표현을 사용.
- Uri 객체를 적절한 URL로 잘 만들어 주어야 하는데, 콘텐츠 프로바이더를 식별하기 위해 사용되는 URL은 규칙이 있음.
```java
content://com.example.test.Provider
```
- 콘텐츠 프로바이더를 식별하기 위한 URL의 프로토콜명은 content를 이용.
- Scheme : content, host : com.example.test.Provider, path : user/1
- 'com.example.test.Provider' 문자열은 이용하고자 하는 콘텐츠 프로바이더가 자신의 AndroidManifest.xml 파일에 등록될 때 <provider> 태그의 authorities 속성값.
- host 하위의 path 정보는 필수 요소가 이니며, 콘텐츠 프로바이더를 이용하는 곳에서 임의로 추가할 수 있는 정보.
- path의 의미는 그 단어로 표현되는 모든 데이터를 지칭하게 되며 path의 끝부분이 숫자로 끝나면 id 값을 지칭하여 해당 id 값의 데이터를 지칭함.
- path로 일정 데이터의 where 조건을 명시할 수 있지 않느냐는 의도, 그런 의미에서 path 값이 설정되면 데이터의 조건으로 사용할 것을 권장.
```java
Cursor cursor = getContentResolver().query(uri, null, null, null, null);
```
- getContentResolver() 함수로 ContentResolver 객체를 획득하고 Uri 값을 첫 번째 매개변수로 주어, 어느 콘텐츠 프로바이더를 이용할 것인지 판단하게 됨.
- 나머지 매개변수는 데이터 획들을 위한 조건.
- query(@NonNull Uri uri, @Nullable String[] projection, @Nullable String selection, @Nullable String[] selectionArgs, @Nullable String sortOrder)
- insert(@NonNull Uri uri, @Nullable ContentValues values)
- delete(@NonNull Uri uri, @Nullable String selection, @Nullable String[] selectionArgs)
- update(@NonNull Uri uri, @Nullable ContentValues values, @Nullable String selection, @Nullable String[] selectionArgs)


## 21.2. 구글 기본 앱의 콘텐츠 프로바이더 이용


### 21.2.1. 주소록 앱 데이터 획득


### 21.2.2.  갤러리 앱 데이터 획득


## 21.3. 갤러리 앱 연동과 이미지 이용을 위한 라이브러리


### 21.3.1. Glide 라이브러리


### 21.3.2. Matisse 라이브러리
