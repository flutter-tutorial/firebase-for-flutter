# firebase for flutter

## 1. Prerequisites

구글 코드랩스의 [firebase for flutter](https://codelabs.developers.google.com/codelabs/flutter-firebase/#0)
강의에 대한 소스코드 리포지토리 입니다.
주석 및 설명은 모두 한글을 기본으로 사용합니다.

[startup_namer](https://github.com/flutter-tutorial/startup_namer) 부터 
순서대로 연습하는것을 추천합니다.

## 2. Introduction

플러터 (flutter) 와 파이어베이스 (firebase) 는 매우 밀접히 연결되어 있어서 개발자가 빠르게 앱을 개발할 수 있도록 돕습니다.
[플러터](https://flutter.io/) 는 iOS와 안드로이드의 앱을 만들기 위해 google이 제공하는 SDK 입니다.
[파이어베이스](https://firebase.google.com/) 는 모바일 애플리케이션을 위한 백엔드 서비스들을 쉽게 구축하고 사용할 수 있도록
도와주는 서비스로, 인증/스토리지/데이터베이스/호스팅 등의 다양한 기능을 개인 서버 구축을 하지 않고 사용할 수 있도록 합니다.

이번 코드랩에서는 파이어베이스를 사용하는 플러터 앱을 만드는 방법을 다룹니다. 실제로 만들어볼 앱은 새로 태어날 아기의 이름을
주변 지인들의 투표로 결정하는 앱입니다. 앱에서는 [클라우드 파이어스토어(Cloud Firestore)](https://firebase.google.com/products/firestore/)
데이터베이스를 사용하여 데이터를 관리하게 됩니다.

안드로이드와 iOS에서 보여질 앱의 전체 형태는 아래와 같습니다. 아래에서 볼 수 있듯이, 플러터를 사용하면
iOS와 안드로이드 앱을 동시에 같은 코드로 개발할 수 있습니다.

![ios-intro](./imgs-for-doc/ios-intro.png) ![android-intro](./imgs-for-doc/android-intro.png)

비슷한 동영상 강좌는 [이 링크](https://youtu.be/DqJ_KjFzL9I?list=PLOU2XLYxmsIJ7dsVN4iRuA7BT8XHzGtCr)에서 확인하실 수 있습니다.
해당 동영상에서는 본 코드랩 강의 전체적인 스텝을 쉽게 이해할 수 있도록 돕는 좋은 영상입니다.

## 3. 플러터 개발 환경 세팅하기

플러터 개발 환경 세팅은 [이 링크](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..%2Findex#2)
를 참조하기 바랍니다.

## 새로운 플러터 앱 환경 만들고 패키지 의존성 설정하기

1. 플러터를 이용하여 새로운 앱을 개발하기 위해, [Get started](https://flutter.io/get-started/test-drive/) 가이드를
참조하여 플러터 프로젝트를 생성합니다. 앱의 이름은 `baby_names`로 설정합니다.
새로운 플러터 프로젝트를 생성하고 설정하는 것은 사용하는 코드 에디터에 따라 다르므로 매뉴얼을 참고하기 바랍니다.
> 안드로이드 스튜디오를 사용하는 경우 [플러그인](https://flutter.io/get-started/editor/#androidstudio) 을 설치하면
> 더욱 쉽게 개발할 수 있습니다.

2. 앱을 실행하기 전에 3, 4번 단계를 먼저 수행합니다.
3. `pubspec.yaml`을 연 뒤 dependencies 태그에 `cloud_firestore` 를 추가합니다. 
```yaml
dependencies:
    flutter:
        sdk: flutter
    cloud_firestore: ^0.8.2  #새롭게 추가할 코드
```
> cloud firestore의 최신 버전은 [여기](https://pub.dartlang.org/packages/cloud_firestore)에서 확인할 수 있습니다. 패키지 의존성 설정 관련해서는 [이 링크](https://www.dartlang.org/tools/pub/dependencies)
> 를 참조하기 바랍니다.

4. 사용하는 IDE에서나 커맨드 라인에서 `flutter packages get` 을 실행합니다.

새로운 `dependencies` 를 추가 할때는 tab이 아닌 스페이스를 사용하기 바랍니다. 

#### 안드로이드 sdk 버전 설정하기 

본 프로젝트를 위해서 안드로이드 에서는 **multi dex**를 지원해야 합니다. 
안드로이드 sdk 버전 21부터 지원되므로 아래의 순서로 안드로이드 컴파일 설정을 수정해야 합니다.
1. `android/app/build.gradle` 파일을 연뒤 `minSdkVersion 16` 부분을 찾습니다.
2. 해당 부분을 `minSdkVersion 21` 로 변경합니다.
3. 파일을 저장 합니다.

#### iOS를 위한 Xcode 빌드 에러 방지하기

1. 플러터 프로젝트의 최상위 폴더에서 `open ios/Runner.xcworkspace` 명령을 통해 Xcode 프로젝트를 엽니다.
2. Xcode가 열리면 `File > Workspace Settings` 메뉴에서 **build system** 을 `Legacy Build System` 으로 변경 합니다. (아래의 스크린샷 참조)

![iOS 세팅](imgs-for-doc/ios-xcode-setting.png)

4. Done 을 클릭하고 Xcode를 종료합니다. 

이후에는 정상적으로 플러터가 실행되어야 합니다.

## 5. 기본 앱을 변경하여 새로운 유저 인터페이스 적용하기

1. [Get started: 테스트 드라이브](https://flutter.io/docs/get-started/test-drive) 를 참조하여 일단 기본 상태의 앱을 디바이스나 에뮬레이터에 설치하고 실행시킵니다.

제일 처음에는 앱을 빌드하기위해 수분의 시간을 소요하지만, 이 이후에는 **hot-reloaded** 기능으로 인해 컴파일 시간을 기다릴 필요가 없어집니다.

앱이 구동되면 아래와 같은 화면을 볼 수 있습니다.

![초기 앱 구동 화면](imgs-for-doc/default-main.png)

2. 자신이 사용하는 IDE 나 에디터를 사용하여 `lib/main.dart` 파일을 열고 초기 코드를 수정합니다.
3. `main.dart` 의 코드를 아래의 코드로 모드 변경합니다.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

final dummySnapshot = [
 {"name": "Filip", "votes": 15},
 {"name": "Abraham", "votes": 14},
 {"name": "Richard", "votes": 11},
 {"name": "Ike", "votes": 10},
 {"name": "Justin", "votes": 1},
];

class MyApp extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
   return MaterialApp(
     title: 'Baby Names',
     home: MyHomePage(),
   );
 }
}

class MyHomePage extends StatefulWidget {
 @override
 _MyHomePageState createState() {
   return _MyHomePageState();
 }
}

class _MyHomePageState extends State<MyHomePage> {
 @override
 Widget build(BuildContext context) {
   return Scaffold(
     appBar: AppBar(title: Text('Baby Name Votes')),
     body: _buildBody(context),
   );
 }

 Widget _buildBody(BuildContext context) {
   // TODO: get actual snapshot from Cloud Firestore
   return _buildList(context, dummySnapshot);
 }

 Widget _buildList(BuildContext context, List<Map> snapshot) {
   return ListView(
     padding: const EdgeInsets.only(top: 20.0),
     children: snapshot.map((data) => _buildListItem(context, data)).toList(),
   );
 }

 Widget _buildListItem(BuildContext context, Map data) {
   final record = Record.fromMap(data);

   return Padding(
     key: ValueKey(record.name),
     padding: const EdgeInsets.symmetric(horizontal: 16.0, vertical: 8.0),
     child: Container(
       decoration: BoxDecoration(
         border: Border.all(color: Colors.grey),
         borderRadius: BorderRadius.circular(5.0),
       ),
       child: ListTile(
         title: Text(record.name),
         trailing: Text(record.votes.toString()),
         onTap: () => print(record),
       ),
     ),
   );
 }
}

class Record {
 final String name;
 final int votes;
 final DocumentReference reference;

 Record.fromMap(Map<String, dynamic> map, {this.reference})
     : assert(map['name'] != null),
       assert(map['votes'] != null),
       name = map['name'],
       votes = map['votes'];

 Record.fromSnapshot(DocumentSnapshot snapshot)
     : this.fromMap(snapshot.data, reference: snapshot.reference);

 @override
 String toString() => "Record<$name:$votes>";
}
```

4. 파일을 저장하고 hot-reload 되면 앱을 확인합니다.

- IDE를 사용하고 있다면 파일을 저장하는 것만으로도 핫리로드가 트리거 됩니다.
- 일반 에디터를 사용하고 있다면, `flutter run` 을 실행한 터미널에서 `r` 키를 눌러 핫 리로드를 수행합니다.

수행된 결과는 아래의 그림과 같아야 합니다.

![new-main](imgs-for-doc/5-new-main.png)

현재 앱은 아무런 기능이 추가되어 있지 않은 단순한 목업 상태입니다. 앱의 리스트를 클릭할 경우 해당 리스트의 내용이 터미널 콘솔에 표시됩니다.

다음으로 할 일은 앱을 파이어베이스의 클라우드 파이어스토어 서비스와 연동하는 것입니다. 그 전에 코드를 이해하기 위해 `main.dart` 의 코드 구조를 한번 살펴보길 바랍니다.

> **Optional**: 복사 붙여넣기한 `main.dart` 코드를 이해하기 위해 아래의 내용을 읽어보길 권장합니다.
>
> 처음 절반의 내용은 매우 알아보기 쉽게 직관적인 코드들로 구성되어 있습니다.
>
> - `cloud_firestore.dart` 패키지를 임포트 해서 클라우드 파이어스토어 서비스를 이용할 준비
> - 클라우드 파이어스토어와 연동하기 이전에 화면에 표시할 몇개의 더미 데이터
> - 앱의 이름을 **Baby Names** 로 설정
>
> 복잡해지고 흥미로워 지는 부분은 `_MyHomePageState` 부터 시작됩니다. `build` 메소드는 [위젯 계층 구조를 정의할 때 사용하여](https://flutter.io/widgets-intro/) 앱의 화면을 디스플레이에 표기할 때 플러터가 사용하는 메소드입니다.
>
> - **Baby Name Votes** 라고 정의된 `AppBar` 를 설정
> - 앱의 기본 body는 `ListView` 를 포함하여 더미 데이터 리스트의 각 아이템을 화면에 렌더링 하는데 사용
>
> 코드 중에서 가장 긴 메소드는 `_buildListItem` 으로 각 리스트에 더미 데이터들을 어떤식으로 빌드 할 것인지 설정하는 코드입니다. (본 코드에서는 원형 모서리를 가진 사각형으로 테두리를 표현하고, 내부에는 아기 이름과 투표 수를 보여주고 있습니다.)
>
> 마지막으로 `Record` 클래스는 각 아기 이름마다의 투표상황을 쉽게 관리하기 위한 도구입니다. 실제로 앱을 만들 때에는 이러한 클래스를 반드시 만들 필요는 없으나, 코드를 깔끔하게 짜는데 도움을 줍니다.

## 6. 새로운 파이어베이스 프로젝트 만들기

1. [파이어베이스에 로그인](https://firebase.google.com/) 합니다. (없다면 계정을 생성하세요. 무료 플랜을 사용하면 대부분의 테스트 앱을 개발하기에 충분합니다.)
2. [파이어베이스 콘솔](https://console.firebase.google.com/) 에서 **Add project** 를 클릭합니다.

![add project](imgs-for-doc/6-add-project.png)

3. 아래의 예제 화면에서 보여 주듯이 사용할 파이어베이스 프로젝트 이름을 편하게 설정하고, 국가/지역 설정도 사용자에 맞게 설정한 뒤 **Create Project** 버튼을 눌러 프로젝트를 생성합니다.
4. 몇 초에서 수분 사이의 시간이 지난뒤 파이어베이스 프로젝트가 성공적으로 생성되면 **Continue** 를 눌러서 다음 작업을 진행합니다.

![create project](imgs-for-doc/6-create-project.png)

## 7. 플랫폼 별로 파이어베이스 설정하기

생성한 파이어베이스 프로젝트를 다양한 앱들이 사용하도록 설정할 수 있습니다. 앱들이 직접 파이어베이스를 사요ㅗㅇ하기 위해서는 아래의 작업들을 수행하면 됩니다 .

- 개발하는 앱의 플랫폼 별 ID 를 파이어베이스에 등록
- 각 앱에 맞는 설정파일을 생성한뒤 프로젝트 폴더에 추가하기.

![firebase register main](imgs-for-doc/7-firebase-main.png)

안드로이드와 iOS 모두에 대한 앱을 만들고 있다면 각각에 대해서 따로 앱을 등록하고 설정파일을 만들어야 합니다. 하나의 플랫폼만 타겟으로 한다면, 타겟이 되지 않는 다른 플랫폼들은 무시해도 좋습니다.

> 지금은 연습을 위한 프로젝트 이기 때문에, 시간을 줄이기 위해 하나의 플랫폼을 위한 설정만 하기를 권장합니다. 다른 플랫폼에 대한 설정은 나중에 해도 상관없습니다.

플러터 프로젝트의 최상위 폴더에 보면 `ios` 와 `android` 폴더가 있는 것을 확인할 수 있습니다. 해당 폴더들이 각각의 플랫폼에 맞는 각종 설정파일들을 가지고 있습니다. 

#### iOS 설정하기

1. [파이어베이스 콘솔](https://console.firebase.google.com/) 에서 **Project Overview** 를 선택하고 iOS 추가 버튼을 클릭합니다. 아래의 그림과 같은 화면을 볼 수 있습니다.

![ios 등록](imgs-for-doc/7-add-ios.png)

2. 여기서 중요한 부분은 **iOS bundle ID** 부분으로 다음으로 진행할 3개의 스텝을 통해서 확인할 수 있습니다.

> iOS bundle ID 에 관한 내용은 [여기](https://cocoacasts.com/what-are-app-ids-and-bundle-identifiers/) 서 확인할 수 있습니다.

3. 커맨드라인에서 플러터 앱 프로젝트의 최상위 폴더로 이동합니다.
4. Mac 기준으로 `open iOS/Runner.xcworkspace` 명령을 통해 플러터 프로젝트를 xcode를 이용해 변경할 준비를 합니다.

> Xcode 설정에 대한 자세한 내용은 [여기](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html) 서 확인하세요

5. Xcode의 왼쪽 패널에서 최상위 레벨의 **Runner** 를 클릭 한 뒤 오른쪽 패널에서 **General** 탭을 클릭하여 아래의 그림과 같은 화면에서 **Bundle Identifier** 를 복사합니다.

![bundle id 획득](imgs-for-doc/7-ios-bundle.png)

6. 파이어베이스 설정창으로 다시 돌아가서 복사한 번들 아이디를 iOS bundle ID 필드에 붙여 넣고, 나머지 필드는 그대로 비워놓은 뒤 **Register app** 버튼을 클릭하여 등록 완료합니다.

> 실제 상용 앱을 개발할 때의 company 부분과 appname 부분은 플러터 앱 프로젝트를 만들 때 직접 설정하면 됩니다.

7. 파이어베이스 설정의 다음 화면에서 안내하는 인스트럭션을 따라서 진행한 뒤 `GoogleService-Info.plist` 설정 파일을 다운로드 합니다.
8. Xcode 로 돌아가서 **Runner** 의 하위 폴더에 있는 **Runner** 폴더가 있는 지 확인합니다.
9. 다운로드 받은 `GoogleService-Info.plist` 파일을 **Runner/Runner** (최상위가 아닌 하위폴더임) 로 복사합니다. (드래그 앤 드랍)
10. Xcode 에서 확인을 묻는 다이얼로그가 나올 경우 **Finish** 버튼을 눌러 완료합니다.
11. 파이어베이스 콘솔로 돌아가서 next 버튼을 글릭하고, 나머지 스텝은 진행하지 않아도 되므로 다 스킵하면 됩니다.

위의 순서를 통해 iOS를 위한 플러터 파이어베이스 설정을 완료할 수 있습니다.

#### 안드로이드 설정하기

1. iOS와 같은 순서로 아래 그림과 같은 안드로이드 앱 등록 화면으로 진행합니다.

![안드로이드 등록](imgs-for-doc/7-add-android.png)

2. 가장 중요한 설정 값은 **Android package name** 부분으로 아래의 두 스텝을 통해 획득할 수 있습니다.

> Package name 과 application id 에 관한 자세한 내용은 [이 링크](https://developer.android.com/studio/build/application-id.html) 를 통해 확인

3. 플러터 앱의 최상위 폴더에서 `android/app/src/main/AndroidManifest.xml` 파일을 엽니다.
4. `manifest` 요소에서 `package` 변수의 값을 확인합니다. 이 값이 Android package name 입니다. (주로 `com.yourcompany.yourproject` 형태). 이 값을 복사합니다.
5. 위에서 복사한 값을 파이어베이스 다이얼로그 창에서 **Android package name** 필드에 붙여넣습니다.
6. (안해도 되는 부분) 현재 프로젝트에서 다루지는 않을 내용이지만 파이어베이스의 추가 기능인 [Google Sign In](https://firebase.google.com/docs/auth/) 혹은 [Firebase Dynamic Links](https://firebase.google.com/products/dynamic-links/) 를 사용하고 싶을 경우 **Debug signing certificate SHA-1** 값도 설정해주어야 합니다. 해당 경우에는 [이 링크](https://developers.google.com/android/guides/client-auth) 를 참조하기 바랍니다.

7. **Register App** 버튼을 눌러 등록합니다.
8. 파이어베이스 인스트럭션에 따라 계속 진행한뒤 `google-services.json` 파일을 다운로드 합니다. 
9. 플러터 앱의 최상위 디렉토리에서 `android/app` 디렉토리로 다운로드 받은 `google-services.json` 파일을 복사합니다.
10. 파이어베이스 콘솔로 돌아가 나머지 단계를 모두 스킵하고 등록 완료를 합니다.
11. 등록한 `google-services.json` 파일을 앱에서 자동으로 사용할 수 있도록 하기 위해 [Google Services Gradle plugin](https://developers.google.com/android/guides/google-services-plugin) 을 등록해야 합니다.
12. 사용하는 에디터나 IDE 에서 `android/app/build.gradle` 파일을 열고 아래의 라인을 파일 최하단에 추가합니다.

```grad
apply plugin: 'com.google.gms.google-services'
```

13. `android/build.gradle` 파일을 열고 `buildscript` 안의 `dependencies` 항목에 아래의 한줄을 추가합니다.

```gradle
buildscript {
   repositories {
       // ...
   }

   dependencies {
       // ...
       classpath 'com.google.gms:google-services:3.2.1'   // 추가할 부분
   }
}
```

> 새롭게 라인을 추가할 때 기존의 classpath를 지우지 않도록 주의합니다.
>
> 본 예제에서 사용한 버전은 3.2.1 버전으로 더 높은 버전 번호를 쓰지 않도록 주의합니다. 버전 번호에 대한 자세한 내용은 [이 링크](https://firebase.google.com/docs/android/setup#manually_add_firebase) 를 참조합니다.

위의 단계를 완료하면 안드로이드에서 파이어베이스를 사용할 수 있습니다.

#### FlutterFire Plugins

플러터에서 파이어베이스 기능을 이용하기 위해서는 항상 [FlutterFire Github](https://github.com/flutter/plugins/blob/master/FlutterFire.md) 를 확인하여 최신 버전을 체크 하기 바랍니다.

기타 플러터 플러그인 들은 [이 링크](https://pub.dartlang.org/flutter) 에서 확인할 수 있습니다.

## 8. 클라우드 파이어스토어 데이터베이스 만들기

본 챕터에서는 클라우드 파이어스토어를 세팅하고 더미 값으로 초기화 하는 작업을 수행합니다.

1. [파이어베이스 콘솔](https://console.firebase.google.com/) 에서 생성한 파이어베이스 프로젝트로 접속합니다.
2. 왼쪽의 네비게이션 메뉴에서 **Develop** 섹션의 **Database** 부분을 선택합니다.

![파이어베이스 네비](imgs-for-doc/8-firebase-nav.png)

3. **Cloud Firestore** 에서 **Create Database** 버튼을 클릭합니다.
4. **Security rules for Cloude Firestore** 사이얼로그에서 **Start in test mode** 선택하고 **Enable** 버튼을 눌러 설정을 완료합니다.

> 테스트 모드에서는 어떤 유저도 데이터베이스에 읽기/쓰기 작업을 수행할 수 있으므로 보안상의 문제가 발생할수 있습니다. 앱을 실제로 릴리즈 할 때에는 [이 링크](https://firebase.google.com/docs/firestore/reference/security/) 를 참조하여 보안 설정을 해야 합니다.

본 프로젝트에서 사용할 데이터베이스는 'baby' 라는 콜렉션 하나만을 사용합니다. 해당 콜렉션에 아기 이름과 투표 수를 기록합니다.

5. **Add Collection** 을 클릭하고 `baby` 를 콜렉션 이름을 설정한뒤 **Next** 버튼을 클릭합니다.

콜렉션을 생성한 이후부터는 해당 콜렉션에 값이나 문서데이터를 저장할 수 있습니다. 각 문서데이터는 **Document ID** 를 가지고 있으며 본 프로젝트에서는 **name** 과 **votes** 의 두개 필드를 사용할 예정입니다. 아래의 이미지를 참조하여 설정합니다.

6. Document ID 부분은 소문자로 된 아기 이름을 입력합니다.

> Document ID를 입력하지 않을 경우에는 자동적으로 생성한 순서에 맞는 숫자가 생성됩니다. 이를 통해 생성 순서대로 정렬된 데이터를 볼 수 있습니다. 본 프로젝트에서는 보다 편한 뷰를 위해 아기 이름을 아이디로 사용하여 알파벳 순서대로 데이터를 정렬 하도록 합니다.

7. 첫 번째 필드를 `name` 으로 이름 짓고, `string` 타입으로 설정한 뒤 아기 이름으로 값을 설정합니다.
8. **Add Field** 를 클릭하여 두 번째 필드를 추가 한뒤 필드 이름을 `votes` 로 설정하고, `number` 타입에 값을 0으로 설정합니다.
9. **Save** 버튼을 눌러 기본 값을 저장합니다.

![콜렉션 세팅](imgs-for-doc/8-collection-setting.png)

10. 아기 이름을 더 추가하고 싶은 경우 **Add Document** 버튼을 눌러 위와 같은 방법으로 추가합니다.

완료가 되면 아래와 같은 클라우드 스토어 화면을 볼 수 있습니다.

![클라우드 스토어 초기화](imgs-for-doc/8-cloud-store.png)



## 9. 플러터앱과 클라우드 파이어스토어 연동하기

기본 설정을 통해 플러터 앱이 클라우드 파이어스토어와 연결이 된 상태까지 진행하였습니다. 이제 파이어스토어에 저장되는 콜렉션 (`baby`) 을 사용하여 아기 이름 목록을 출력하도록 변경해봅시다.

Dart 코드에서는 `Firestore.instance` 를 통해 파이어스토어에 접근할 수 있습니다. 특정 콜렉션을 얻어오고 싶은 경우에는 `Firestore.instance.collection('baby').snapshots()` 와 같은 코드를 통해 얻어 올 수 있으며, 해당 메소드는 [`stream`](https://www.dartlang.org/tutorials/language/streams) 객체를 반환합니다.

받아온 stream 객체는 [`StreamBuilder`](https://docs.flutter.io/flutter/widgets/StreamBuilder-class.html) 클래스를 통해 플러터 앱에 표시할 수 있습니다.

1. IDE 나 에디터에서 `lib/main.dart` 를 열고 `_buildBody` 메소드를 찾습니다.
2. 해당 메소드의 전체 내용을 아래의 코드로 변경합니다.

```dart
Widget _buildBody(BuildContext context) {
 return StreamBuilder<QuerySnapshot>(
   stream: Firestore.instance.collection('baby').snapshots(),
   builder: (context, snapshot) {
     if (!snapshot.hasData) return LinearProgressIndicator();

     return _buildList(context, snapshot.data.documents);
   },
 );
}
```

> `StreamBuilder` 위젯 클래스는 데이터베이스의 업데이트를 계속해서 확인하며 데이터가 변경될 때 자동적으로 리스트를 업데이트 합니다. 데이터가 없을 경우에는 프로그레스 표시기를 화면에 출력합니다.

3. 위의 코드만 변경할 경우 타입 오류가 발생합니다. 아래의 코드들을 추가로 변경합니다. `_buildList` 메소드의 두번째 파라미터를 아래의 코드처럼 변경합니다. (List<Map> 을 List<DocumentSnapshot> 으로)

```dart
Widget _buildList(BuildContext context, List<DocumentSnapshot> snapshot) {
    ... 기존 코드들
}
```

4. `_buildListItem` 메소드의 첫 줄을 아래의 코드로 교체합니다.

```dart
Widget _buildListItem(BuildContext context, DocumentSnapshot data) {
 final record = Record.fromSnapshot(data); // 변경된 부붙 fromMap 을 fromSnapshot 으로
```

5. 이제 코드의 상단부의 `dummySnapshot` 이 필요 없으므로 삭제해도 됩니다.
6. 파일을 저장하고 핫리로드 기능을 통해 변경된 앱을 확인합니다.

아래와 같이 파이어스토어에 저장된 데이터가 나올 경우 정상적으로 동작하는 상태입니다. 

추가적으로 데이터베이스 내용을 변경하고 싶을 경우 앞서 클라우드 스토어 세팅 때 처럼 파이어베이스 콘솔에서 수정할 수 있으며, 즉각적으로 앱에 반영되는 것을 볼 수 있습니다.

![파이어스토어 연동 완료](imgs-for-doc/9-firestore.png)

## 10. 유저 인터렉션 추가하기

본 챕터에서는 실제로 유저가 튜표할 수 있는 기능을 추가합니다. 매우 간단한 코드로 추가할 수 있습니다.

1. `lib/main.dart` 를 열고 `onTap: () => print(record)` 부분을 찾은 뒤 아래의 코드로 변경합니다.

```dart
onTap: () => record.reference.updateData({'votes': record.votes + 1})
```

2. 파일을 저장하고 핫리로드 하면 투표기능이 동작하는 것을 볼 수 있습니다.

![투표 기능 완료](imgs-for-doc/10-투표기능.png)

유저가 아기 이름을 탭할 때마다 클라우드 파이어스토어의 값을 매번 업데이트 한다. 클라우드 파이어스토어가 업데이트 될 때마다 reference를 참조하고 있는 클라이언트에 변경사항과 snapshot을 알리고, 플러터 앱은 이를 받아 `StreamBuilder` 를 통해 화면에 뿌려주게 된다.

## 11. 클라우드 파이어스토어의 트랜잭션 기능 사용하기

현재 코드는 단일 기기에서는 정상작동 하지만 다수의 기기에서는 비정상 동작을 하는 하나의 결점이 있다. 두 개 이상의 기기에서 동시에 투표를 진행할 경우 타이밍이 잘 맞으면 투표 수가 1만 증가할 수 있다. 현재 상태의 코드는 항상 현재 투표 수 만을 참고하여 데이터베이스를 업데이트 하기 때문에, 동시에 투표 할 경우 같은 값을 읽어서 1만 더하는 문제가 발생하기 때문이다. 이러한 문제는 단일기기 테스트를 통해서 확인하기는 매우 어려운 문제이고, 다수의 기기가 있더라도 정확하게 동일한 시간내에서 테스트를 해야 하기 때문에 발견하기 어려운 문제이지만 매우 심각한 부분이다.

기술적으로 **votes** 값은 여러기기에서 동시에 업데이트 가능한 *shared resource (공유 리소스)* 로, 데이터를 업데이트 할 때 (특히, 이전 값에 기초하여 새로운 값을 생성할 때) race condition 이 발생하는 문제가 있다. 이러한 값을 업데이트 할 때는 직접적으로 변경하지 말고 [트랜잭션](https://firebase.google.com/docs/firestore/manage-data/transactions) 을 사용하도록 한다.

1. `lib/main.dart` 코드의 `onTap: () => record.reference.updateData({'votes': record.votes + 1})` 부분을 아래의 코드로 변경합니다.

```dart
onTap: () => Firestore.instance.runTransaction((transaction) async {
     final freshSnapshot = await transaction.get(record.reference);
     final fresh = Record.fromSnapshot(freshSnapshot);

     await transaction
         .update(record.reference, {'votes': fresh.votes + 1});
   }),
```

이제 레이스 컨디션은 발생하지 않지만 투표값이 업데이트 되는데 약간의 시간이 걸리게 됩니다. 

하나의 트랜잭션으로 데이터를 읽고/쓰기 하는 부분을 감싸, 클라우드 파이어스토어가 트랜잭션 형태로 데이터를 처리하도록 변경했기 때문입니다. 두 명의 유저가 동시에 작업하지 않을 경우 각각에 대해서 업데이트를 수행합니다. 트랜잭션 중간에 다른 데이터가 업데이트를 할 경우 5번의 재시도를 하고, 그래도 실패할 경우 사용자에게 실패를 알립니다.

> [aggregation query](https://firebase.google.com/docs/firestore/solutions/aggregation) 를 이용해서도 해결 할 수 있습니다.

최종 코드는 [lib/main.dart](lib/main.dart) 에서 확인할 수 있습니다.