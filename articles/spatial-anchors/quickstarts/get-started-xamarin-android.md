---
title: '빠른 시작: Xamarin Android 앱 만들기'
description: 이 빠른 시작에서는 Spatial Anchors를 사용하여 Xamarin 지원 Android 앱을 빌드하는 방법을 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: mode-other
ms.openlocfilehash: 9f2cea8a45ff98da2182ff326d5bf2dd34dfe7c0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049906"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>빠른 시작: Azure Spatial Anchors를 사용하여 Xamarin Android 앱 만들기

이 빠른 시작에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 Xamarin 지원 Android 앱을 만드는 방법을 설명합니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 이 과정을 마치면, 공간 앵커를 저장하고 회수할 수 있는 Android 앱을 갖게 됩니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * Android 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- Windows 또는 macOS 컴퓨터:
  - Windows를 사용하는 경우:
    - 최신 버전의 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 이상</a>
    - <a href="https://git-scm.com/download/win" target="_blank">Windows용 Git</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - macOS를 사용하는 경우:
    - 최신 버전의 <a href="/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true" target="_blank">Mac용 Visual Studio 8.1 이상</a>
    - <a href="https://git-scm.com/download/mac" target="_blank">macOS용 Git</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- 선택한 플랫폼에 설치되어 실행 중인 최신 버전의 Xamarin.Android. Xamarin.Android를 설치하는 가이드는 [Xamarin.Android 설치](/xamarin/android/get-started/installation/index) 가이드를 참조하세요.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자 사용</a> 및 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스.
  - 컴퓨터가 Android 디바이스와 통신하려면 추가 디바이스 드라이버가 필요할 수 있습니다. 자세한 내용은 [여기](https://developer.android.com/studio/run/device.html)를 참조하세요.
- 앱은 ARCore **1.8** 을 대상으로 해야 합니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Visual Studio에서 `Xamarin/SampleXamarin.sln`을 엽니다.

## <a name="configure-account-identifier-and-key"></a>계정 식별자 및 키 구성

다음 단계는 계정 식별자 및 계정 키를 사용하도록 앱을 구성하는 것입니다. [Spatial Anchors 리소스 설정](#create-a-spatial-anchors-resource) 시 텍스트 편집기에 복사했습니다.

`Xamarin/SampleXamarin.Common/AccountDetails.cs`를 엽니다.

`SpatialAnchorsAccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`SpatialAnchorsAccountId` 필드를 찾아 `Set me`를 계정 식별자로 바꿉니다.

`SpatialAnchorsAccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

## <a name="deploy-the-app-to-your-android-device"></a>Android 디바이스에 앱 배포

Android 디바이스의 전원을 켜고 로그인한 후 USB 케이블을 사용하여 컴퓨터에 연결합니다.

시작 프로젝트를 **SampleXamarin.Android** 로 설정하고, **솔루션 구성** 을 **릴리스** 로 변경하고, 디바이스 선택기 드롭다운에서 배포할 디바이스를 선택합니다.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Windows에서 프로젝트 및 디바이스를 선택하는 메뉴를 보여주는 스크린샷.](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

**디버그** > **디버깅 시작** 을 선택하여 앱을 배포하고 시작합니다.

# <a name="macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio 구성](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

**실행** > **디버깅 없이 시작** 을 선택하여 앱을 배포하고 시작합니다.

---

앱에서 **기본** 을 선택하여 데모를 실행하고 지침에 따라 앵커를 배치하고 회수합니다.

> ![스크린샷 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![스크린샷 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![스크린샷 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)
