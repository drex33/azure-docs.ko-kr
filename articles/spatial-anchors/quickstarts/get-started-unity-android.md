---
title: '빠른 시작: Unity Android 앱 만들기'
description: 이 빠른 시작에서는 Spatial Anchors를 사용하여 Unity 지원 Android 앱을 빌드하는 방법을 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57961321c427163b110d5d67a8c5e6542132ffaa
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956636"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>빠른 시작: Azure Spatial Anchors를 사용하여 Unity Android 앱 만들기

이 빠른 시작에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 Unity Android 앱을 만드는 방법을 다룹니다. Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 완료되면, Unity 지원 ARCore Android 앱이 있어 공간 앵커를 저장하고 회수할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Spatial Anchors 계정 만들기
> * Unity 빌드 설정 준비
> * Spatial Anchors 계정 식별자 및 계정 키 구성
> * Android Studio 프로젝트 내보내기
> * Android 디바이스 배포 및 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자 사용</a> 및 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스.
  - 컴퓨터가 Android 디바이스와 통신하려면 추가 디바이스 드라이버가 필요할 수 있습니다. 추가 정보 및 지침은 [여기](https://developer.android.com/studio/run/device.html)를 참조하세요.
- Windows 또는 macOS 머신.
  - Windows에서 실행하는 경우 <a href="https://git-scm.com/download/win" target="_blank">Windows용 Git</a> 및 <a href="https://git-lfs.github.com/">Git LFS</a>도 필요합니다.
  - macOS에서 실행하는 경우 HomeBrew를 통해 Git이 설치됩니다. 터미널의 한 줄에 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 명령을 입력합니다. 그런 다음, `brew install git` 및 `brew install git-lfs`를 실행합니다.
- **Android SDK 및 NDK 도구** 및 **OpenJDK** 모듈이 있는 **Android 빌드 지원** 을 포함한 Unity 설치. 지원되는 버전 및 필수 기능은 [Unity 프로젝트 설정 페이지](../how-tos/setup-unity-project.md)를 참조하세요.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity 샘플 프로젝트 다운로드 및 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[여기](../how-tos/setup-unity-project.md#download-asa-packages)의 지침에 따라 Android 플랫폼에 필요한 ASA SDK 패키지를 다운로드하여 가져옵니다.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Android Studio 프로젝트 내보내기

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**디바이스 실행** 에서 디바이스를 선택한 다음, **빌드 및 실행** 을 선택합니다. 이름을 선택할 수 있는 `.apk` 파일을 저장하라는 메시지가 표시됩니다.

앱에서 화살표를 사용하여 **BasicDemo** 를 선택한 다음, **Go!** 를 누릅니다. 데모를 실행하는 단추입니다. 지침에 따라 앵커를 배치하고 회수합니다.

![스크린샷 1](./media/get-started-unity-android/screenshot-1.jpg)
![스크린샷 2](./media/get-started-unity-android/screenshot-2.jpg)
![스크린샷 3](./media/get-started-unity-android/screenshot-3.jpg)

앱의 지침에 따라 앵커를 배치하고 회수합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="rendering-issues"></a>렌더링 문제

앱 실행 시 백그라운드로 카메라가 보이지 않으면(대신 공백, 파랑 또는 기타 질감 등이 보이면) Unity에서 자산을 다시 가져와야 할 가능성이 높습니다. 앱을 중지합니다. Unity의 맨 위 메뉴에서 **자산 -> 모두 다시 가져오기** 를 선택합니다. 그런 다음, 앱을 다시 실행합니다.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [자습서: 여러 디바이스에서 Spatial Anchors 공유](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [방법: Unity 프로젝트에서 Azure Spatial Anchors 구성](../how-tos/setup-unity-project.md)
