---
layout: post
title: "[iOS][macOS] Bundle"
description: ""
category: "iOS/MacOS"
tags: [Bundle]
---
{% include JB/setup %}


## 패키지

> A package is any directory that the Finder presents to the user as if it were a single file.

패키지는 macOS의 Finder에서 디렉토리를 마치 하나의 파일인 것처럼 나타낸 것입니다.

.app, .kext, `.bundle`, .framework, .plugin 등의 확장자가 패키지의 일종입니다.

단, 모든 번들이 패키지인 것은 아닙니다.


## 번들

> A bundle is a directory with a standardized hierarchical structure that holds executable code and the resources used by that code.

번들은 실행가능한 코드와 코드가 사용하는 리소스를 캡슐화한 계층 구조를 갖는 디렉토리입니다.

번들에 대한 예시로, 앱 번들에 한 번 접근해 보겠습니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/01.png"></p>

**Xcode - Preferences - Location - Derived Data** 화살표 클릭

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/02.png"></p>

**Build - Product - Debug - AppName** 앱 번들 확인 가능

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/03.png"></p>

Info.plist의 정보를 가져오는 예시로, CFBundleIdentifier Key에 해당하는 값을 Xcode에서 Bundle.main.bundleIdentifier로 얻어올 수 있습니다.




## 번들의 구조

번들의 구조는 번들의 타입과 플랫폼에 따라 다르지만, 번들을 사용하는 방식은 같습니다.


### 앱 번들

Xcode가 앱을 빌드하면 앱 번들을 생성하게 되며, 앱 번들은 앱을 작동시키는데 필요한 모든 리소스를 저장하고 있습니다.

iOS 앱 번들과 macOS 앱 번들의 구조는 약간의 차이가 있으나 사용 방식에는 차이가 없습니다.


#### iOS 앱 번들

```
MyApp.app/
   Info.plist
   MyApp
   Default.png
   Icon.png
   Hand.png
   MainWindow.nib
   MyAppViewController.nib
   WaterSounds/
      Water1.aiff
      Water2.aiff
   en_GB.lproj/
      CustomView.nib
      bird.png
      Bye.txt
      Localizable.strings
   en_US.lproj/
      CustomView.nib
      bird.png
      Bye.txt
      Localizable.strings
   jp.lproj/
      CustomView.nib
      bird.png
      Bye.txt
      Localizable.strings
```

|파일|설명|
|:---|:---|
|AppName|(필수)앱의 코드를 갖고 있는 Executable 파일입니다.|
|App Icon|(필수/권장)홈 화면에서 보이는 앱 아이콘입니다.|
|Info.plist|(필수)bundle ID, version number, and display name 등 앱의 Configuration에 대한 정보를 가지고 있는 파일입니다.|
|Launch image|(권장)앱이 켜질 때 window와 UI를 로드하기 전에 보이는 이미지입니다. 없다면 검은 화면이 보입니다.|
|MainWindow.nib|(권장)앱의 launch time에 로드될 객체를 갖고 있는 nib 파일입니다. `UISceneStoryboardFile` 혹은 `UIMainStoryboardFile`에 사용자 정의 nib 이름을 사용할 수 있습니다. 포스팅 하단 `References` 에 달아놓은 도서에 의하면 `NSMainNibFile`는 더 이상 사용되지 않은 Property List Key입니다.|
|Settings.bundle|앱 설정과 관한 Preference를 추가할 수 있는 Plug-in 번들의 한 형태입니다. Property List 파일과 Preference 관련 리소스를 가지고 있습니다.|
|Custom resource files|리소스는 nib, image, sound 등의 데이터입니다. 지역화되지 않은 리소스는 번들의 루트레벨에 있게 되지만, 지역화된 리소스는 `lproj` 패키지 아래에 존재하게 됩니다. |

Internationalization(i18n)과 Localization(i10n)을 지원하기 위해선 `language_region.lproj` 패키지에 해당 언어에 맞는 리소스를 넣어줘야 합니다.

i18n를 위한 language는 `ISO 639` 컨벤션을 따르고 i10n을 위한 region은 `ISO 3166` 컨벤션을 따릅니다.

단, i18n/i10n를 신경쓰지 않을 경우 `Base.lproj` 패키지에 리소스가 존재하게 됩니다.


#### macOS 앱 번들

```
MyApp.app/
   Contents/
      Info.plist
      MacOS/
         MyApp
      Resources/
         Hand.tiff
         MyApp.icns
         WaterSounds/
            Water1.aiff
            Water2.aiff
         en.lproj/
            MyApp.nib
            bird.tiff
            Bye.txt
            InfoPlist.strings
            Localizable.strings
            CitySounds/
               city1.aiff
               city2.aiff
         jp.lproj/
            MyApp.nib
            bird.tiff
            Bye.txt
            InfoPlist.strings
            Localizable.strings
            CitySounds/
               city1.aiff
               city2.aiff
```

macOS 앱 번들의 구조는 번들의 루트에 Contents 디렉토리가 있고, 이 디렉토리가 Info.plist, Executable File, 리소스 등을 모두 가지고 있는 형태입니다.

Contents 디렉토리는 쓸모없어 보이지만 macOS 초기의 레거시 번들과 구분할 수 있게 해주는 역할을 합니다.

|파일|설명|
|:---|:---|
|MacOS|(필수)앱의 코드를 갖고 있는 Executable 파일을 가지고 있는 디렉토리입니다.|
|Resources|앱의 모든 리소스를 가지고 있는 디렉토리입니다.|
|Frameworks|Executable 파일에 의해 사용되는 공유 라이브러리와 프레임워크를 갖고 있는 디렉토리입니다.|
|PlugIns|앱에 기능을 추가할 수 있는 로더블 번들을 갖고 있는 디렉토리입니다.|
|SharedSupport|앱을 실행하는데 크게 중요하지 않은 부가적인 리소스를 갖고 있는 디렉토리입니다.|


### 프레임워크 번들

```
MyFramework.framework/
   MyFramework  -> Versions/Current/MyFramework
   Resources    -> Versions/Current/Resources
   Versions/
      A/
         MyFramework
         Headers/
            MyHeader.h
         Resources/
            English.lproj/
               InfoPlist.strings
            Info.plist
      Current  -> A
```

프레임워크는 동적 공유 라이브러리와 그 라이브러리를 지원하기 위해 필요한 리소스를 캡슐화한 계층 디렉토리입니다.

프레임워크는 동적 공유 라이브러리와 마찬가지로 하나만 메모리에 적재되어도 다수의 앱에서 공유할 수 있으나, 동적 공유 라이브러리와는 다르게 라이브러리와 리소스를 하나의 파일에서 제공할 수 있는 장점이 있습니다.

해당 프레임워크를 사용하는 앱들이 서로 다른 프레임워크 버젼을 원할 수 있기 때문에 `Versions` 디렉토리에서 각기 다른 버젼의 프레임워크를 생성할 수 있습니다.

프레임워크의 코드와 읽기 전용 리소스만이 공유되며, writable한 변수가 있다면 다수의 앱이 의도치않은 공유를 하지 않도록 각 앱이 개별적으로 변수를 복사하여 갖게 됩니다.



### 로더블(Lodable) 번들

```
MyLoadableBundle.bundle
   Contents/
      Info.plist
      MacOS/
         MyLoadableBundle
      Resources/
         Lizard.jpg
         MyLoadableBundle.icns
         en.lproj/
            MyLoadableBundle.nib
            InfoPlist.strings
         jp.lproj/
            MyLoadableBundle.nib
            InfoPlist.strings
```

로더블 번들은 앱을 동적으로 확장시킬 수 있는 방법을 제공합니다.

로더블 번들의 최상위 계층에 있는 Contents 디렉토리가 실행가능한 코드와 그 코드를 실행하기 위해 필요한 리소스 갖고 있습니다.

iOS에서는 로더블 번들을 생성하거나 사용할 수 없습니다.




## 번들에 있는 localized된 리소스

```
Resources/
   MyApp.icns
   en_GB.lproj/
      MyApp.nib
      bird.tiff
      Localizable.strings
   en_US.lproj/
      MyApp.nib
      Localizable.strings
   en.lproj/
      MyApp.nib
      bird.tiff
      Bye.txt
      house.jpg
      InfoPlist.strings
      Localizable.strings
      CitySounds/
         city1.aiff
         city2.aiff
```

앱 번들을 소개하면서 i18n, i10n에 대하여 다뤘기 때문에 추가적인 내용만을 언급하자면, 리소스를 찾는 순서는 `language_region.lproj`, `language.lproj`, `region.lproj`, `non-localized resource` 입니다.



## 앱 번들 알아보기

공식 문서를 통해 번들에 대하여 알아봤으니 제 프로젝트의 앱 번들은 어떤 구조를 가지고 있는지 알아보겠습니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/04.png"></p>

좌측 앱 번들에는 Executable 파일과 Info.plist가 우선 눈에 들어옵니다.

**Project - Targets - App - Build Phases - Copy Bundle Resources** 에는 여러 리소스들이 들어있는데 otf, TTF 폰트 파일, 앱 아이콘 이미지 파일 등 리소스도 있지만 주목해야 할 것은 앱 번들이 만들어지기 위한 빌드 과정에서 `storyboard`는 `storyboardc`, `xib`는 `nib`, `Assets.xcassets`는 `Assets.car` 로 컴파일되었다는 점입니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/05.png"></p>

그 중에서 LaunchScreen, Main 스토리보드의 `storyboardc`는 Base.lproj 패키지 아래에 생성되었습니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/06.png"></p>

`storyboardc`는 nib 파일들과 nib 파일을 관리하기 위한 Info.plist를 가지고 있는 패키지입니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/07.png"></p>

`Assets.xcassets`에는 앱에서 사용하는 이미지나 색상 등의 리소스가 들어있으며 `Assets.car` 로 컴파일 됩니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/08.png"></p>

**Project - Targets - App - Build Settings - Asset Catalog Compiler** 에서 설정할 수 있습니다. 

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/09.png"></p>

`momd`는 CoreData의 `xcdatamodeld`, `mom`은 `NSManagedObjectModel` 가 컴파일된 형태입니다.

<p style="text-align:center;"><img src="{{ site.production_url }}/image/2022/01/24/10.png"></p>

(나중에)code sign.. mobileprovision



## References

* [Apple Bundle Programming Guide](https://developer.apple.com/library/archive/documentation/CoreFoundation/Conceptual/CFBundles/Introduction/Introduction.html#//apple_ref/doc/uid/10000123i-CH1-SW1)
* [도서: iOS 7 Programming Pushing the Limits: Develop Advance Applications for Apple iPhone, iPad, and iPod Touch 1st Edition - **72 Page**]
