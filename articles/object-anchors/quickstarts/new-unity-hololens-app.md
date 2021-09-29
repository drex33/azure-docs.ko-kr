---
title: '빠른 시작: 새 HoloLens Unity 앱 만들기'
description: 이 빠른 시작에서는 Object Anchors를 사용하여 새 HoloLens Unity 앱을 만드는 방법을 알아봅니다.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: a83606d76beb4fd9cead19293636b9e5348d4990
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777567"
---
# <a name="quickstart-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-object-anchors"></a>빠른 시작: Azure Object Anchors를 사용하여 새 HoloLens Unity 앱을 만드는 단계별 지침

이 빠른 시작에서는 [Azure Object Anchors](../overview.md)를 사용하여 새 HoloLens Unity 앱을 만드는 방법을 보여줍니다. Azure Object Anchors는 3D 자산을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 완료하면 실제 세계에서 개체를 검색할 수 있는 Unity로 빌드된 HoloLens 앱이 생성됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* [Unity HoloLens](get-started-unity-hololens.md) 또는 [MRTK가 포함된 Unity HoloLens](get-started-unity-hololens-mrtk.md) 빠른 시작의 모든 필수 구성 요소.
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2020.3.8f1 이상이 설치된 Unity Hub</a>

## <a name="getting-started"></a>시작

먼저 프로젝트와 Unity 장면을 설정합니다.

1. Unity Hub를 시작합니다.
1. **새로 만들기** 를 선택하고 Unity 2020.3.8f1 이상을 선택합니다.
1. **3D** 를 선택했는지 확인합니다.
1. 프로젝트 이름을 지정하고 저장 **위치** 를 입력합니다.
1. **만들기** 를 선택합니다.
1. **Unity 편집기** 가 열리면 **파일** > **다른 이름으로 저장** 을 사용하여 빈 기본 장면을 새 파일에 저장합니다.
1. **Scenes** 폴더를 선택하고, 새 장면의 이름을 **Main** 으로 지정하고, **저장** 단추를 누릅니다.

## <a name="configure-as-uwp"></a>UWP로 구성

1. **파일 > 빌딩 설정** 을 선택합니다.
1. **유니버설 Windows 플랫폼** 을 선택한 다음, **플랫폼 전환** 을 선택합니다.
1. Unity 편집기에서 일부 구성 요소를 먼저 다운로드해야 한다고 표시되면 해당 구성 요소를 다운로드하여 설치합니다.

## <a name="install-mixed-reality-feature-tool-feature-packages"></a>Mixed Reality Feature 도구 기능 패키지 설치

1. <a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality 기능 도구</a> 설명서에 따라 도구를 설정하고 사용 방법을 알아보세요.
1. **플랫폼 지원** 섹션에서 **Mixed Reality OpenXR 플러그 인** 기능 패키지(버전 1.0.0 이상)를 Unity 포로젝트 폴더에 설치합니다.
1. **Azure Mixed Reality Services** 섹션에서 Unity 프로젝트 폴더에 **Microsoft Azure Object Anchors** 기능 패키지를 설치합니다.
1. **Unity 편집기** 로 돌아갑니다. **Mixed Reality Feature 도구** 기능 패키지가 설치되는 동안 몇 분 정도 걸릴 수 있습니다.
1. 새 입력 시스템을 활성화할 것인지 묻는 대화 상자가 표시됩니다.
1. **예** 단추를 선택합니다.
1. 설치 프로세스가 완료되면 Unity가 자동으로 다시 시작됩니다.

## <a name="configure-openxr"></a>OpenXR 구성

1. 여전히 **빌드 설정** 창 안에 있어야 합니다.
1. **플레이어 설정...** 단추를 선택합니다.
1. **프로젝트 설정** 창이 열립니다.
1. **XR 플러그 인 관리** 항목을 선택합니다.
1. <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">OpenXR용 XR 플러그 인 관리 구성</a> 설명서에 따라 **플러그 인 공급자** 목록에 있는 **Microsoft HoloLens 기능 집합** 을 사용하여 **OpenXR** 을 설정합니다.

## <a name="set-capabilities"></a>기능 설정

1. 여전히 **프로젝트 설정** 창 안에 있어야 합니다.
1. **플레이어** 항목을 선택합니다.
1. **플레이어 설정** 의 **검사기 패널** 에서 **유니버설 Windows 플랫폼 설정** 아이콘이 선택되어 있는지 확인합니다.
1. **게시 설정** 기능 섹션에서 **InternetClientServer**, **WebCam** 및 **SpatialPerception** 이 선택되어 있는지 확인합니다.

## <a name="set-up-the-project-settings"></a>프로젝트 설정 지정

1. 여전히 **프로젝트 설정** 창 안에 있어야 합니다.
1. **품질** 항목을 선택합니다.
1. **유니버설 Windows 플랫폼** 로고 아래 열에서 **기본** 행의 화살표를 선택하고 **매우 낮음** 을 선택합니다. **유니버설 Windows 플랫폼** 열 및 **매우 낮음** 행의 상자가 녹색이면 설정이 올바르게 적용된 것입니다.
1. **프로젝트 설정** 및 **빌드 설정** 창을 닫습니다.
1. <a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">최적화</a> 설명서에 따라 HoloLens 2에 권장되는 프로젝트 설정을 적용합니다.

## <a name="set-up-the-main-virtual-camera"></a>주 가상 카메라 설정

1. **계층 구조** 패널에서 **주 카메라** 를 선택합니다.
1. **검사기** 에서 해당 변환 위치를 **0,0,0** 으로 설정합니다.
1. **플래그 지우기** 속성에서 드롭다운을 **Skybox** 에서 **단색** 으로 변경합니다.
1. **백그라운드** 필드를 선택하여 색 선택을 엽니다.
1. **R, G, B 및 A** 를 **0** 으로 설정합니다.
1. **클리핑 플레인 근처** 속성을 0.1로 변경합니다.
1. **구성 요소 추가** 를 선택하고 **추적된 자세 드라이버** 를 검색하여 추가합니다.
1. **파일** > **저장** 을 선택합니다.

## <a name="trying-it-out"></a>사용해 보기

모든 항목이 작동하는지 테스트하려면 **Unity** 에서 앱을 빌드하고 **Visual Studio** 에서 배포합니다. <a href="/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio" target="_blank">**MR 기본 사항 100: Unity 시작** 과정</a>의 6장을 진행하여 이 작업을 수행합니다. Unity 시작 화면이 표시된 후 명확한 화면이 표시됩니다.

## <a name="create-your-script"></a>스크립트 만들기

1. **프로젝트** 창의 **Assets** 폴더 아래에 새 폴더 **Scripts** 를 만듭니다.
1. 폴더를 마우스 오른쪽 단추로 클릭한 다음, **만들기 >** , **C# 스크립트** 를 선택합니다. 이름을 **ObjectSearch** 로 지정합니다.
1. **GameObject** -> **빈 항목 만들기** 로 이동합니다.
1. 선택한 후 **검사기** 에서 이름 **GameObject** 를 **Object Observer** 로 바꿉니다.
1. **구성 요소 추가** 를 선택하고 **ObjectSearch** 스크립트를 검색하여 추가합니다.
1. **파일** > **저장** 을 선택합니다.

## <a name="start-implementing-your-app"></a>앱 구현 시작

Object Anchors Runtime SDK를 사용하여 **ObjectSearch** 스크립트에 사용자 고유의 코드를 추가할 준비가 되었습니다. [SDK 개요](../concepts/sdk-overview.md)를 시작점으로 참조하여 기본 사항을 학습하고 샘플 코드 중 일부를 통해 사용해 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
