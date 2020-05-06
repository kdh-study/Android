
# 19장 브로드캐스트 리시버와 알림
## 목차
[19.1.](#191-브로드캐스트-리시버) 브로드캐스트 리시버
  - [19.1.1.](#1911-브로드캐스트-리시버-이해) 브로드캐스트 리시버 이해
  - [19.1.2.](#1912-브로드캐스트-리시버-작성-방법) 브로드캐스트 리시버 작성 방법
  - [19.1.3.](#1913-시스템-브로드캐스트-인텐트) 시스템 브로드캐스트 인텐트
  - [19.1.4.](#1914-백그라운드-서비스-제한) 백그라운드 서비스 제한
  
[19.2.](#192-알림) 알림
  - [19.2.1.](#1921-알림의-기본-구성) 알림의 기본 구성
  - [19.2.2.](#1922-NotificationChannel) NotificationChannel
  - [19.2.3.](#1923-기본적인-알림-구성) 기본적인 알림 구성
  - [19.2.4.](#1924-알림의-다양한-구성) 알림의 다양한 구성

## 19.1. 브로드캐스트 리시버
- 메인 소프트웨어 아키택처는 컴포넌트 기반의 개발.
- 4개의 컴포넌트로 앱을 구성하는데, 우리는 이제껏 액티비티에 대해서만 살펴봄.
- 액티비티의 이용비율이 가장 높으나 서비스, 브로드캐스트 리시버, 콘텐츠 프로바이더에 대한 이해도 필수.
- 작성 방법도 중요하지만, 내부 수행 원리를 이해하는 것도 중요.

### 19.1.1. 브로드캐스트 리시버 이해
- 실제 개발하다 보면 4개의 컴포넌트 중 가장 비번하게 이용.
- 앱 내에서 브로드캐스트 리시버를 작성하거나 실행도 많이 하며, 특히 시스템의 특정 상황을 대부분 브로드캐스트 리시버로 알려줍니다.

### 19.1.2. 브로드캐스트 리시버 작성 방법
- 브로드캐스트 리시버는 BroadcastReceiver를 상속받아 작성하는 클래스.
- 클래스 내에 onReceive()라는 함수만 정의해 주면 됨.
- 브로드캐스트 리시버가 인텐트로 인해 수행될 때 자동으로 호출되는 함수.

```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```

- 컴포넌트 클래스이므로 AndroidManifest.xml 파일에 <receiver> 태크로 등록.
- <activity>와 마찬가지로 암시적 인텐트에 의해 실행되어야 한다면, <intent-filter>를 사용 가능.
```xml
<receiver
            android:name=".MyReceiver"
            android:enabled="true"
            android:exported="true">
        </receiver>
```

- sendBroadcast() 함수로 실행.
- 인텐트 부분은 다른 컴포넌트와 같음, putExtra() 함수를 이용하여 데이터를 넘길 수도 있음.
```java
Intent intent = new Intent(this, MyReceiver.class);
sendBroadcast(intent);
```

### 19.1.3. 시스템 브로드캐스트 인텐트
- 브로드캐스트 리시버는 앱 내부에서 사용하려고 자주 만들지만 시스템에서 실행하는 인텐트에 반응하여 각종 시스템 상황을 감지하려고도 자주 사용.
- 시스템에서 많은 브로드캐스트 리시버 인텐트를 발생시키는데, 이곳에서는 자주 이용되는 몇 가지를 살펴보겠음.

#### 부팅 완료
- 스마트폰의 부팅 완료 시점에 앱이 동작하여 간단한 업무를 수행하거나 서비스 등을 구동해야 할 때가 있음.
- 부팅 완료 시점에 시스템에서 발생시키는 브로드캐스트 인텐트에 반응할 브로드캐스트리시버만 정의해주면 쉽게 구현할 수 있습니다.

```xml
<receiver 
            android:name=".MyReceiver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
```
- 시스템에서 띄우는 인텐트의 Action 문자열과 같은 문자열로 intent-filter를 구성하여 등록.

```xml
<user-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
- 부팅 완료 시점에 브로드캐스트 리시버가 동작하게 하려면 퍼미션이 등록되어 있어야 함.

#### 화면 On/Off
- 사용자 스마트폰의 화면이 On/Off 되는 상황은 빈번하며 개발 시 이 상황을 체크해서 특정 로직의 수행을 구동하거나 정지해야 하는 경우도 많음.
- 화면이 On/Off되는 상황에 시스템에서 브로드캐스트 인텐트를 발생해 주며 앱에서 브로드캐스트 리시버를 이용하여 이 상황을 감지할 수 있음.
- 다른 브로드캐스트 리시버와 다르게 AndroidManifest.xml 파일에 <receiver> 태그로 등록하면 실행되지 않습니다.
- 액티비티, 서비스 등의 코드에서 동적으로 등록해야만 실행됩니다.

```java
BroadcastReceiver brOn = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d("kkang", "screen on...");
    }
};
```
- 액티비티나 서비스 클래스 내부의 코드로 보면 됩니다.
- 자바 코드에서 브로드캐스트 리시버를 정의한 후 registerReceiver() 함수로 시스템에 등록하면 됩니다.

```java
registerReceiver(brOn, new IntentFilter(Intent.ACTION_SCREEN_ON));
```
- 시스템에서 띄우는 인텐트의 Action 문자열은 android.intent.action.SCREEN_ON, android.intent.action.SCREEN_OFF.
- IntentFilter 객체를 만들어 registerReceiver() 함수로 동적 등록.

```java
unregisterReceiver(brOn);
```
- 동적으로 등록한 브로드캐스트리시버는 동적으로 해제도 가능.

#### 전화 수신/발신
- 사용자가 스마트폰에서 전화를 수신 또는 발신하는 상황은 스마트폰 관점에서 중요한 상황.
- 개발자 앱이 전화 기능을 제공하지 않는다고 하더라도 이 상황을 감지해야 할 때가 있음.

- 우선 퍼미션 선언.
```xml
<user-permission android:name="android.permission.PROCESS_OUTGOING_CALLS"/>
        <user-permission android:name="android.permission.READ_PHONE_STATE"/>
```
- 각각 발신, 수신을 감지하는 퍼미션.
***
```xml
<receiver android:name=".MyReceiver">
            <intent-filter>
                <action android:name="android.intent.action.NEW_OUTGOING_CALL" />
                <action android:name="android.intent.action.PHONE_STATE" />
            </intent-filter>
        </receiver>
```
- 발신 상황이 발생하면 시스템에서 android.intent.action.NEW_OUTGOING_CALL이라는 action 문자열로 브로드캐스트 인텐트를 발생해 줍니다.
- 수신 상황이 발생하면 시스템에서 android.intent.action.PHONE_STATE라는 action 문자열로 브로드캐스트 인텐트를 발생해 줍니다.
***
```java
if (action.equals("android.intent.action.NEW_OUTGOING_CALL")) {
            String phoneNumber = intent.getStringExtra(Intent.EXTRA_PHONE_NUMBER);
        }
        else if (action.equals("android.intent.action.PHONE_STATE")) {
            Bundle bundle = intent.getExtras();
            String state = bundle.getString(TelephonyManager.EXTRA_STATE);
            String phoneNumber = bundle.getString(Intent.EXTRA_PHONE_NUMBER);
        }
```
- 발신 전화번호는 intent.getStringExtra(Intent.EXTRA_PHONE_NUMBER)로 얻을 수 있음.
- 수신 전화번호는 Bundle 객체를 통해 얻을 수 있음.

#### 배터리
- 스마트폰에 USB 케이블 등으로 전원이 공급되고 있는 상황과 전원 공급이 끊어진 상황 등을 앱에서 인지해야 할 때도 있습니다.
- 배터리와 관련된 각종 상황이 발생할 때 시스템에서는 앱에서 이런 상황을 인지할 수 있도록 브로드캐스트 인텐트를 발생해 줍니다.
- 배터리와 관련된 브로드캐스트 인텐트의 action 문자열은 여러 가지입니다.
문자열 | 의미
| --- | --- |
| android.intent.action.BATTERY_LOW | 스마트폰의 배터리가 낮은 상태가 되었을 때 |
| android.intent.action.BATTERY_OKAY | 스마트폰 배터리가 낮은 상태에서 벗어날 때 |
| android.intent.action.BATTERY_CHANGED | 스마트폰 배터리의 충전 상태가 변경되었을 때 |
| android.intent.action.ACTION_POWER_CONNECTED | 케이블 등으로 외부 전원공급이 연결되었을 때 |
| android.intent.action.ACTION_POWER_DISCONNECTED | 외부 전원공급이 끊어질 때 |

```java
registerReceiver(batteryReceiver, new IntentFilter(Intent.ACTION_POWER_CONNECTED));
registerReceiver(batteryReceiver, new IntentFilter(Intent.ACTION_POWER_DISCONNECTED));
```

- 여러 action 문자열을 등록하여 실행되는 브로드캐스트 리시버에서는 onReceive()함수에서 action 문자열을 추출하여 자신이 어떤 인텐트 정보로 인해 실행된 것인지 구분할 수 있습니다.
```java
if (action.equals("android.intent.action.NEW_OUTGOING_CALL")) {
            String phoneNumber = intent.getStringExtra(Intent.EXTRA_PHONE_NUMBER);
        }
        else if (action.equals("android.intent.action.PHONE_STATE")) {
            Bundle bundle = intent.getExtras();
            String state = bundle.getString(TelephonyManager.EXTRA_STATE);
            String phoneNumber = bundle.getString(Intent.EXTRA_PHONE_NUMBER);
        }
```

### 19.1.4. 백그라운드 서비스 제한

## 19.2. 알림

### 19.2.1. 알림의 기본 구성

### 19.2.2. NotificationChannel

### 19.2.3. 기본적인 알림 구성

### 19.2.4. 알림의 다양한 구성
