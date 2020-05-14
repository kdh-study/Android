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
- 스마트폰에 기본으로 설치된 앱 중 이용비율이 높은 주소록과 갤러리의 데이터를 이용하는 방법을 살펴보겠음.
- 주소록, 갤러리 앱이 아닌 다른 어떤 앱이라도 모두 콘텐츠 프로바이더를 이용하는 방법이므로 작성 방법은 같으며 Uri 값만 다르게 설정하는 것.

### 21.2.1. 주소록 앱 데이터 획득
- 앱을 개발할 때 외부 앱 연동의 대부분은 외부 앱의 화면을 실행하거나, 데이터를 이용하기 위함.
- 화면 연동은 액티비티 실행이고, 인텐트를 살펴보았던 곳에서 다룸.
- 기본으로 설치된 앱 중 데이터 연동에 가장 많이 이용되는 앱 중 하나가 주소록 앱.
- 우선 인텐트로 주소록 앱의 목록 액티비티를 띄우고 다시 사용자가 선택한 홍길동의 전화번호 등을 콘텐츠 프로바이더를 이용하여 획득해야 함.
***
- 퍼미션 등록이 필요.
```xml
<uses-permission android:name="android.permission.READ_CONTACTS"/>
```
- 인텐트를 발생시켜 주소록의 목록 액티비티를 띄워야 함.
```java
Intent intent = new Intent(Intent.ACTION_PICK);
        intent.setData(Uri.parse("content://com.android.contacts/data/phones"));
        startActivityForResult(intent, 10);
```
- 사용자가 목록 화면에서 홍길동을 눌렀을 때 화면이 자동으로 되돌아와야 하므로 **startActivityForResult()** 함수를 이용.
- startActivityForResult() 함수로 인해 화면이 다시 되돌아올 때 자동으로 호출되는 onActivityResult()함수에서 콘텐츠 프로바이더를 이용해 구체적으로 원하는 데이터를 획득.
```java
String id = Uri.parse(data.getDataString()).getLastPathSegment();
```
- 사용자가 선택한 홍길동의 결과를 받는 코드.
- 식별자 값만 넘어옴.
- 데이터가 너무 많기 때문에 식별자 값만 넘겨 받는데, 이 값을 조건으로 구체적으로 원하는 데이터를 다시 주소록 쪽에 요청해야 함.
- 이때 콘텐츠 프로바이더가 이용됨.
- URL 형태로 전달되며, **URL의 맨 마지막 단어가 식별자 값**임.
```java
Cursor cursor = getContentResolver().query(
                ContactsContract.Data.CONTENT_URI,
                new String[] {
                        ContactsContract.Contacts.DISPLAY_NAME
                },
                ContactsContract.Data._ID + "=" + id, null, null);
```
- 첫 번째, ContactsContract.Data.CONTENT_URI를 지정하여 주소록의 콘텐츠 프로바이더를 식별.
- 두 번째, 획득하고자 하는 데이터를 문자열 배열로 명시.
- 세 번째, where 조건으로 홍길동의 식별자 값을 지정.
***
- Cursor를 이용하여 데이터를 획득하는 구문은 안드로이드 DBMS 프로그램과 차이가 없음.
```java
cursor.moveToFirst();
String name = cursor.getString(0);
```

### 21.2.2. 갤러리 앱 데이터 획득
- 주소록 못지않게 많이 이용되는 앱이 갤러리, 이용되는 형태는 주소록과 비슷.
- 사용자 화면에 사진의 목록을 띄워주고 그중 사용자가 선택한 사진의 데이터를 획득하는 구조.
- 인텐트를 발생시키고 화면이 되돌아왔을 때 콘텐츠 프로바이더를 이용해 구체적으로 원하는 데이터를 획득.
- 갤러리 앱은 이미지를 이용한다는 면에서 좀 복잡한 점이 있음.
- 여러 개를 선택할 수도 있으므로 데어티를 받는 방법에도 고민이 있음.
- 버전에 따른 변경사항도 있음.
***
#### API Level 16 하위 버전
- API Level 16 이전까지는 사진 하나만 선택할 수 있었음.
- 여러 장을 선택하게 하려면 개발자가 이 화면을 직접 만들어 적용해야 했음.
- 또한 여러 장 선택되었을 때 데이터를 받는 방법도 API Level 19부터 변경됨.
- 또한 화면에 ImageView 등을 이용하여 출력할 거라면 OOM(OutOfMemoryException)문제를 꼭 고려해서 작성해야 함.
  - 14장에서 다룸.
```java
if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN) {
            // JELLY_BEAN 하위에서는 하나만 선택하게..
            Intent intent = new Intent(Intent.ACTION_PICK);
            intent.setType(MediaStore.Images.Media.CONTENT_TYPE);
            intent.setData(MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
            startActivityForResult(intent, 20);
        }
```
- onActivityResult() 함수 내부의 코드로 API Level 16 하위 버전일 때의 처리 코드는 다음과 같음.
```java
// JELLY_BEAN 이전 버전 하나만 선택해서 들어오는 경우.
        String[] projection = {MediaStore.Images.Media.DATA};
        Cursor cursor = getContentResolver().query(data.getData(), projection, null, null, null);
        cursor.moveToFirst();
        String filePath = cursor.getString(0);
```
- query()함수의 세 번째 매개변수가 where 조건인데, 이 값을 null로 지정하였음, 조건이 없는데 어떻게 사용자가선택한 파일에 대한 정보가 넘어오냐면..
- 첫 번째 매개변수의  Uri 객체에 설정된 URL 경로에 사용자가 선택한 이미지의 식별자 값이 포함되어 있기 때문.
**
#### API Level 16 이상 버전.
- 사용자의 스마트폰 버전이 Build.VERSION_CODES.JELLY_BEAN 이상일 때는 여러 장의 이미지를 선택할 수 있으므로 인텐트의 Extra 정보로 **Intent.EXTRA_ALLOW_MULTIPLE** 값을 true 지정하여 인텐트를 발생.
```java
Intent intent = new Intent();
        intent.setType("image/*");
        intent.putExtra(Intent.EXTRA_ALLOW_MULTIPLE, true);
        intent.setAction(Intent.ACTION_GET_CONTENT);
        startActivityForResult(intent, 30);
```
***
- 사용자가 이미 목록 화면에서 여러 장을 한꺼번에 선택하면, 해당정보가 ClipData 타입으로 전달됨.
- 하나만 선택하면 사용자가 선택한 이미지 정보를 포함한 Uri 값이 그대로 전달됨.
```java
if (data.getClipData() != null) {
            ClipData clipData = data.getClipData();
            for (int i = 0; i < clipData.getItemCount(); i++) {
                ClipData.Item item = clipData.getItemAt(i);
                Uri uri = item.getUri();
                
                if ("com.android.providers.media.documents".equals(uri.getAuthority()) && Build.VERSION.SDK_INT >= 19) {
                    
                }
                else if ("external".equals(uri.getPathSegments().get(0))) {
                    
                }
                else {
                    
                }
            }
        }
```
- 그리고 데이터가 ClipData 타입으로 전달되었을 때 API Level 19부터 결과 데이터의 Uri 표현식이 이전 버전과 달라져서 이 또한 구분해서 처리해 주어야 함.
- 도큐먼트식 경로.
  - content://com.android.providers.media.documents/document/image:3A35260
- 세그먼트식 경로.
  - content://media/external/images/media/...
- 19 버전부터는 ClipData에 포함된 이미지으 Uri 값이 도큐먼트식으로 전달됨.
- 하위 버전이나 19 버전 이상의 스마트폰이더라도 스마트폰에 따라 Uri 값이 세그먼트식으로 전달될 수 있음.
- 이 책에서는 편의를 위해 각각의 경로를 '도큐먼트식', '세그먼트식' 경로라고 칭하겠음.
- Uri 값을 형식에 따라 구분해서 처리해야 함.
***
#### 세그먼트식 이미지 파일 경로 획득.
- 결과가 세그먼트식 경로로 넘어올 때 사용자가 선택한 이미지의 파일 경로를 콘텐츠 프로바이더를 이용하여 획득하는 구문은 다음과 같음.
```java
String selection = MediaStore.Images.Media._ID + "=?";
        String[] selectionArgs = new String[] { uri.getLastPathSegment() };
        
        String column = "_data";
        String[] projection = { column };
        
        Cursor cursor = getContentResolver().query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, projection, selection, selectionArgs, null);
        
        String filePath = null;
        if (cursor != null && cursor.moveToFirst()) {
            int column_index = cursor.getColumnIndexOrThrow(column);
            filePath = cursor.getString(column_index);
        }
        cursor.close();
```
- Uri 값의 맨 마지막 세그먼트가 사용자가 선택한 이미지의 식별자.
- 코드처럼 식별자 값을 획득하여 query() 함수를 호출할 때 where 조건의 데이터로 사용하면 됨.
***
#### 도큐먼트식 이미지 파일 경로 획득.
- 결과가 도큐먼트식 경로로 넘어올 때 사용자가 선택한 이미지의 파일 경로를 콘텐츠 프로바이더를 이용하여 획득하는 구문은 다음과 같음.
```java
String docId = DocumentsContract.getDocumentId(uri);
        String[] split = docId.split(":");
        String type = split[0];
        Uri contentUri = null;
        if ("image".equals(type)) {
            contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
        }
        
        String selection = MediaStore.Images.Media._ID + "=?";
        String[] selectionArgs = new String[]{split[1]};
        
        String column = "_data";
        String[] projection = {column};
        
        Cursor cursor = context.getContentResolver().query(contentUri, projection, selection, selectionArgs, null);
        
        String filePath = null;
        if (cursor != null && cursor.moveToFirst()) {
            int column_index = cursor.getColumnIndexOrThrow(column);
            filePath = cursor.getString(column_index);
        }
        cursor.close();
```
- 결과가 도큐먼트식 경로로 넘어올 때 Uri 값은 다음과 같음.
  - content://com.android.providers.media.documents/document/image:3A35260
- 이 문자열에서 우리에게 필요한 정보는 "document" 뒤에 있는 단어.
- 이를 String docId = DocumentsContract.getDocumentId(uri); 구문으로 추출.
- 이렇게 추출하면 image:3A35260 단어만 추출되며, 이를 다시 String[] split = docId.split(":");구문을 사용하여, 콜론 구분자로 문자열을 잘라서 앞단어느 데이터의 타입, 뒷 단어는 데이터의 식별자로 사용됨.
- API Level 19 버전부터 데이터의 Uri 값을 이러한 형식으로 표현하는 이유는 이미지 이외에 audio, video 타입의 데이터도 모두 같은 형태로 표현하며, 데이터 식별자 이외에 데이터 타입도 Uri 객체에 답기 위한 의도.

## 21.3. 갤러리 앱 연동과 이미지 이용을 위한 라이브러리


### 21.3.1. Glide 라이브러리


### 21.3.2. Matisse 라이브러리
