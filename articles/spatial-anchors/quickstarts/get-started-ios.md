---
title: '빠른 시작: iOS 앱 만들기'
description: Swift 또는 Objective-C에서 Azure Spatial Anchors를 사용하여 프로그래밍 방식으로 iOS 앱을 만드는 방법을 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref, devx-track-azurecli, mode-other
ms.openlocfilehash: 67fa0c9380c83da709da07a7a381ec544339b5e3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055562"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>빠른 시작: Swift 또는 Objective-C에서 Azure Spatial Anchors를 사용하여 iOS 앱 만들기

이 빠른 시작에서는 Swift 또는 Objective-C에서 [Azure Spatial Anchors](../overview.md)를 사용하여 iOS 앱을 만드는 방법을 다룹니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 완료되면, ARKit iOS 앱이 있어 공간 앵커를 저장하고 회수할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * iOS 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- 최신 버전의 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> 및 <a href="https://cocoapods.org" target="_blank">CocoaPods</a>가 설치된 개발자 지원 macOS 컴퓨터
- HomeBrew를 통해 설치된 Git:
  1. 터미널에서 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 명령을 단일 줄로 입력합니다. 
  1. `brew install git` 및 `brew install git-lfs`을 실행합니다.
  1. `git lfs install`(현재 사용자의 경우) 또는 `git lfs install --system`(전체 시스템의 경우)을 사용하여 git 구성을 업데이트합니다.
- 개발자가 사용 가능한 <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 호환</a> iOS 디바이스

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

터미널을 사용하여 다음 작업을 수행합니다.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

CocoaPods를 사용하여 필요한 포드를 설치합니다.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/`로 이동합니다.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/`로 이동합니다.

```bash
cd ./iOS/Objective-C/
```

---

`pod install --repo-update`를 실행하여 프로젝트용 CocoaPods를 설치합니다.

이제 Xcode에서 `.xcworkspace`를 엽니다.

> [!NOTE]
> macOS Catalina(10.15)로 업그레이드한 후 CocoaPod 문제가 발생하는 경우 [여기에](#cocoapods-issues-on-macos-catalina-1015) 문제 해결 단계를 참조하세요.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

다음 단계는 계정 식별자 및 계정 키를 사용하도록 앱을 구성하는 것입니다. [Spatial Anchors 리소스 설정](#create-a-spatial-anchors-resource) 시 텍스트 편집기에 복사했습니다.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`를 엽니다.

`spatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`spatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

`spatialAnchorsAccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/SampleObjC/BaseViewController.m`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

`SpatialAnchorsAccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

---

## <a name="deploy-the-app-to-your-ios-device"></a>iOS 디바이스에 앱 배포

iOS 디바이스를 Mac에 연결하고 **활성 스키마** 를 iOS 디바이스로 설정합니다.

![디바이스 선택](./media/get-started-ios/select-device.png)

**빌드를 선택한 다음, 현재 스키마를 실행** 합니다.

![배포 및 실행](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> `library not found for -lPods-SampleObjC` 오류가 표시되는 경우 `.xcworkspace` 대신 `.xcodeproj` 파일을 열었을 수 있습니다. `.xcworkspace`를 열고 다시 시도하세요.

Xcode에서 **중지** 를 눌러 앱을 중지합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>macOS Catalina의 CocoaPods 문제(10.15)

최근에 macOS Catalina(10.15)로 업데이트했으며 CocoaPods를 미리 설치한 경우 CocoaPods가 손상된 상태가 되며 pod 및 `.xcworkspace` 프로젝트 파일을 제대로 구성하지 못할 수 있습니다. 이 문제를 해결하려면 다음 명령을 실행하여 CocoaPods를 다시 설치해야 합니다.

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>개인 프로비저닝 프로필/개발자 계정에서 iOS 10.3.1에 배포할 때 앱이 충돌함 

개인 프로비저닝 프로필/개발자 계정에서 iOS 10.3.1에 iOS 앱을 배포하는 경우 `Library not loaded: @rpath/ADAL...` 오류가 표시될 수 있습니다. 

이 문제를 해결하려면

- 개인 팀 프로필이 아닌 프로비저닝 프로필(유료 개발자 계정)을 사용합니다.
- iOS 13.3 이전 버전을 실행하는 iOS 디바이스, iOS 13.4 베타 또는 릴리스 버전을 실행하는 iOS 디바이스에 앱을 배포합니다.
- [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid)에서 이 문제에 대해 자세히 알아봅니다.


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)
