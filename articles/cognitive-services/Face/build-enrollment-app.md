---
title: React 앱을 빌드하여 Face 서비스에 사용자 추가
titleSuffix: Azure Cognitive Services
description: 개발 환경을 설정하고 Face 앱을 배포하여 고객의 동의를 받는 방법에 대해 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: db6ee292a088d530ea858d75e6649684e03d92b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540481"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>React 앱을 빌드하여 Face 서비스에 사용자 추가

이 가이드에서는 샘플 Face 등록 애플리케이션을 시작하는 방법을 설명합니다. 앱은 사용자를 얼굴 인식 서비스에 추가하고 높은 정확도의 얼굴 데이터를 얻기 위해 의미 있는 동의를 얻는 것과 관련된 모범 사례를 보여 줍니다. 통합 시스템은 이와 같은 앱을 사용하여 얼굴 데이터를 기반으로 비접촉 액세스 제어, ID 확인, 참석 추적 또는 개인 설정 키오스크를 제공할 수 있습니다.

애플리케이션을 시작하면 사용자에게 자세한 동의 화면이 표시됩니다. 사용자가 동의하면 앱은 사용자 이름 및 암호를 묻는 메시지를 표시한 다음, 디바이스의 카메라를 사용하여 고화질 얼굴 이미지를 캡처합니다.

샘플 앱은 JavaScript 및 React Native 프레임워크를 사용하여 작성되었습니다. 현재 Android 및 iOS 디바이스에 배포할 수 있습니다. 향후에는 더 많은 배포 옵션을 사용할 수 있을 것입니다.

## <a name="prerequisites"></a>사전 요구 사항 

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)  
* Azure 구독을 보유한 후에는 Azure Portal에서 [Face 리소스를 만들어](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.  
  * 애플리케이션을 Face API에 연결하려면 생성한 리소스의 키와 엔드포인트가 필요합니다.  
  * 로컬 개발 및 테스트의 경우에만 API 키와 엔드포인트는 환경 변수입니다. 최종 배포의 경우 API 키를 안전한 위치에 저장하고 코드 또는 환경 변수에는 저장하지 않습니다.  

### <a name="important-security-considerations"></a>중요한 보안 고려 사항
* 로컬 개발 및 초기 제한된 테스트의 경우 환경 변수를 사용하여 API 키와 엔드포인트를 보유하는 것이 좋습니다(모범 사례는 아님). 파일럿 및 최종 배포의 경우 API 키를 안전하게 저장해야 합니다. 여기에는 중간 서비스를 사용하여 로그인 중에 생성된 사용자 토큰의 유효성을 검사하는 작업이 포함될 수 있습니다. 
* API 키 또는 엔드포인트를 코드에 저장하거나 버전 제어 시스템(예: Git)에 커밋하지 마십시오. 실수로 발생하는 경우 새 API 키/엔드포인트를 즉시 생성하고 이전 API 키/엔드포인트를 철회해야 합니다.
* 모범 사례로 개발 및 프로덕션을 위해 별도의 API 키를 두는 것이 좋습니다.

## <a name="set-up-the-development-environment"></a>개발 환경 설정

#### <a name="android"></a>[Android](#tab/android)
 
1. [샘플 앱](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)에 대한 git 리포지토리를 복제합니다.
1. 개발 환경을 설정하려면 <a href="https://reactnative.dev/docs/environment-setup"  title="React Native 설명서"  target="_blank">React Native 설명서<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요. **React Native CLI 빠른 시작** 을 선택합니다. 개발 OS 및 **Android** 를 대상 OS로 선택합니다. **종속성 설치** 및 **Android 개발 환경** 섹션을 완료합니다.
1. [Visual Studio Code](https://code.visualstudio.com/)와 같은 원하는 텍스트 편집기를 다운로드합니다.
1. 리소스의 **개요** 탭에 있는 Azure Portal에서 FaceAPI 엔드포인트 및 키를 검색할 수 있습니다. Face API 키를 원격 리포지토리에 체크 인하지 마십시오.
1. Android Studio의 Android 가상 디바이스 에뮬레이터나 사용자의 Android 디바이스를 사용하여 앱을 실행합니다. 물리적 디바이스에서 앱을 테스트하려면 관련 <a href="https://reactnative.dev/docs/running-on-device"  title="React Native 설명서"  target="_blank">React Native 설명서<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 따르세요.

#### <a name="ios"></a>[iOS](#tab/ios)

1. [샘플 앱](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)에 대한 git 리포지토리를 복제합니다.
1. 개발 환경을 설정하려면 <a href="https://reactnative.dev/docs/environment-setup"  title="React Native 설명서"  target="_blank">React Native 설명서<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요. **React Native CLI 빠른 시작** 을 선택합니다. 개발 OS로 **macOS** 를 선택하고 대상 OS로 **iOS** 를 선택합니다. **종속성 설치** 섹션을 완료합니다.
1. [Visual Studio Code](https://code.visualstudio.com/)와 같은 원하는 텍스트 편집기를 다운로드합니다. Xcode도 다운로드해야 합니다. 
1. 리소스의 **개요** 탭에 있는 Azure Portal에서 FaceAPI 엔드포인트 및 키를 검색할 수 있습니다. Face API 키를 원격 리포지토리에 체크 인하지 마십시오.
1. Xcode에서 시뮬레이션된 디바이스 또는 사용자 고유의 iOS 디바이스를 사용하여 앱을 실행합니다. 물리적 디바이스에서 앱을 테스트하려면 관련 <a href="https://reactnative.dev/docs/running-on-device"  title="React Native 설명서"  target="_blank">React Native 설명서<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 따르세요.

---

## <a name="create-a-user-add-experience"></a>사용자 추가 환경 만들기  

이제 샘플 앱을 설정했으므로 사용자 고유의 요구에 맞게 조정할 수 있습니다.

예를 들어 동의 페이지에서 상황에 맞는 정보를 추가하려고 할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![앱 동의 페이지](./media/enrollment-app/1-consent-1.jpg)

대부분의 얼굴 인식 문제는 품질이 낮은 참조 이미지 때문에 발생합니다. 모델 성능을 저하시킬 수 있는 몇 가지 요소는 다음과 같습니다.
* 얼굴 크기(카메라에서 멀리 떨어진 얼굴)
* 얼굴 방향(카메라에서 얼굴이 돌아갔거나 기울어짐)
* 이미지의 노출이 부족하거나 노이즈가 너무 많은 조명이 부족한 조건(적은 빛 또는 역광)
* 모자나 테가 두꺼운 안경을 포함한 얼굴 가림(부분적으로 숨겨지거나 가려진 얼굴)
* 흐린 얼굴(예: 사진을 찍은 때 빠르게 움직인 얼굴) 

이 서비스는 고객을 추가하거나 얼굴 인식을 시도하기 위해 위의 요인에 따라 이미지가 충분한 품질인지 여부를 선택하는 데 도움이 되는 이미지 품질 검사를 제공합니다. 이 앱은 디바이스 카메라에서 프레임에 액세스하고, 품질을 감지하고, 사용자에게 사용자 인터페이스 메시지를 표시하여 더 높은 품질의 이미지를 캡처하고, 최고 품질의 프레임을 선택하고, 감지된 얼굴을 Face API 서비스에 추가하는 방법을 보여줍니다. 


> [!div class="mx-imgBorder"]
> ![앱 이미지 캡처 명령 페이지](./media/enrollment-app/4-instruction.jpg)

앱은 사용자의 정보를 삭제하는 기능과 다시 추가하는 옵션을 제공합니다.

> [!div class="mx-imgBorder"]
> ![프로필 관리 페이지](./media/enrollment-app/10-manage-2.jpg)

전체 환경을 포함하도록 앱 기능을 확장하려면 구현할 수 있는 추가 기능과 모범 사례에 대한 [개요](enrollment-overview.md)를 참조하세요.

## <a name="deploy-the-app"></a>앱 배포

#### <a name="android"></a>[Android](#tab/android)

먼저 앱이 프로덕션을 배포할 준비가 되었는지 확인합니다. 앱 코드에서 키 또는 비밀을 제거하고 [보안 모범 사례](../cognitive-services-security.md?tabs=command-line%2ccsharp)를 따르고 있는지 확인합니다.

프로덕션용 앱을 릴리스할 준비가 되면 Android 앱용 패키지 파일 형식인 릴리스 가능 APK 파일을 생성합니다. 이 APK 파일은 프라이빗 키로 서명해야 합니다. 이 릴리스 빌드를 사용하여 디바이스에 직접 앱을 배포할 수 있습니다. 

<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="릴리스 준비"  target="_blank">릴리스 준비<span class="docon docon-navigate-external x-hidden-focus"></span></a> 설명서에 따라 프라이빗 키를 생성하고, 애플리케이션에 서명하고, 릴리스 APK를 생성하는 방법을 알아보세요.  

서명된 APK를 만들었으면 <a href="https://developer.android.com/studio/publish"  title="앱 게시"  target="_blank">앱 게시<span class="docon docon-navigate-external x-hidden-focus"></span></a> 설명서를 참조하여 앱을 릴리스하는 방법에 대해 자세히 알아보세요.

#### <a name="ios"></a>[iOS](#tab/ios)

먼저 앱이 프로덕션을 배포할 준비가 되었는지 확인합니다. 앱 코드에서 키 또는 비밀을 제거하고 [보안 모범 사례](../cognitive-services-security.md?tabs=command-line%2ccsharp)를 따르고 있는지 확인합니다. 배포를 준비하려면 앱 아이콘, 시작 화면을 만들고, 배포 정보 설정을 구성해야 합니다. [Xcode의 설명서](https://developer.apple.com/documentation/Xcode/preparing_your_app_for_distribution)에 따라 배포할 앱을 준비합니다. 

프로덕션용 앱을 릴리스할 준비가 되면 앱의 보관 파일을 빌드합니다. 앱을 배포하기 위한 보관 파일 빌드 및 옵션을 만드는 방법에 대한 [Xcode 설명서](https://developer.apple.com/documentation/Xcode/distributing_your_app_for_beta_testing_and_releases)를 따릅니다.  

---

## <a name="next-steps"></a>다음 단계  

이 가이드에서는 개발 환경을 설정하고 샘플 앱을 시작하는 방법을 배웠습니다. React Native에 익숙하지 않은 경우 [시작하기 Docs](https://reactnative.dev/docs/getting-started)를 참조하여 자세한 배경 정보를 확인할 수 있습니다. 또한 [Face API](Overview.md)를 익히는 데도 도움이 될 것입니다. 개발을 시작하기 전에 사용자 추가의 다른 섹션을 읽어 보세요.
