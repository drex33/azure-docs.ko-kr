---
title: '빠른 시작: Android 앱 만들기'
description: 이 빠른 시작에서는 Spatial Anchors를 사용하여 Android 앱을 빌드하는 방법을 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: mode-other
ms.openlocfilehash: 4b4fdc380e491d1b652bad09d16474f0527c2409
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047827"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>빠른 시작: Azure Spatial Anchors를 사용하여 Android 앱 만들기

이 빠른 시작에서는 Java 또는 C++/NDK에서 [Azure Spatial Anchors](../overview.md)를 사용하여 Android 앱을 만드는 방법을 다룹니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 완료되면, ARCore Android 앱이 있어 공간 앵커를 저장하고 회수할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * Android 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>가 설치된 Windows 또는 macOS 머신.
  - Windows에서 실행하는 경우 <a href="https://git-scm.com/download/win" target="_blank">Windows용 Git</a> 및 <a href="https://git-lfs.github.com/">Git LFS</a>도 필요합니다.
  - macOS에서 실행하는 경우 HomeBrew를 통해 Git이 설치됩니다. 터미널의 한 줄에 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 명령을 입력합니다. 그런 다음, `brew install git` 및 `brew install git-lfs`를 실행합니다.
  - NDK 샘플을 빌드하려면 Android Studio에 NDK 및 CMake 3.6 이상의 SDK Tools를 설치해야 합니다.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자 사용</a> 및 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스.
  - 컴퓨터가 Android 디바이스와 통신하려면 추가 디바이스 드라이버가 필요할 수 있습니다. 추가 정보 및 지침은 [여기](https://developer.android.com/studio/run/device.html)를 참조하세요.
- 앱은 ARCore **1.11.0** 을 대상으로 해야 합니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[여기](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h)에서 `arcore_c_api.h`를 다운로드하여 `Android\NDK\libraries\include`에 배치합니다.

새로 복제된 리포지토리 내에서 다음 명령을 실행하여 하위 모듈를 초기화합니다.

```console
git submodule update --init --recursive
```

---

Android Studio를 엽니다.

# <a name="java"></a>[Java](#tab/openproject-java)

**기존 Android Studio 프로젝트 열기** 를 선택하여 `Android/Java/`에 있는 프로젝트를 선택합니다.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

**기존 Android Studio 프로젝트 열기** 를 선택하여 `Android/NDK/`에 있는 프로젝트를 선택합니다.

---

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

다음 단계는 계정 식별자 및 계정 키를 사용하도록 앱을 구성하는 것입니다. [Spatial Anchors 리소스 설정](#create-a-spatial-anchors-resource) 시 텍스트 편집기에 복사했습니다.

# <a name="java"></a>[Java](#tab/openproject-java)

`Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

`SpatialAnchorsAccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

`Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

`SpatialAnchorsAccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

---

## <a name="deploy-the-app-to-your-android-device"></a>Android 디바이스에 앱 배포

Android 디바이스의 전원을 켜고 로그인한 후 USB 케이블을 사용하여 PC에 연결합니다.

Android Studio 도구 모음에서 **실행** 을 선택합니다.

![Android Studio 배포 및 실행](./media/get-started-android/android-studio-deploy-run.png)

**배포 대상 선택** 대화 상자에서 Android 디바이스를 선택하고 **확인** 을 선택하여 Android 디바이스에서 앱을 실행합니다.

앱의 지침에 따라 앵커를 배치하고 회수합니다.

Android Studio 도구 모음에서 **중지** 를 선택하여 앱을 중지합니다.

![Android Studio 중지](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)
