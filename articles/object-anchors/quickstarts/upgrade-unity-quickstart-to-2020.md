---
title: '빠른 시작: 빠른 시작 앱을 Unity 2020으로 업그레이드'
description: 이 빠른 시작에서는 빠른 시작 앱을 Unity 2020으로 업그레이드하여 Object Anchors를 사용해 HoloLens Unity 앱을 빌드하는 방법을 알아봅니다.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.custom: mode-other
ms.openlocfilehash: 0e3449954760e83e45b0e21059c2ff019efeed97
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041432"
---
# <a name="quickstart-upgrade-quickstart-app-to-unity-2020"></a>빠른 시작: 빠른 시작 앱을 Unity 2020으로 업그레이드

이 빠른 시작에서는 [Azure Object Anchors](../overview.md)를 사용하는 Unity HoloLens 앱을 Unity 2019에서 Unity 2020으로 업그레이드합니다. Azure Object Anchors는 3D 자산을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 완료하면 실제 세계에서 개체를 검색할 수 있는 Unity로 빌드된 HoloLens 앱이 생성됩니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 앱을 Unity 2019에서 Unity 2020으로 업그레이드합니다.
> * 패키지 종속성을 업그레이드합니다.
> * Unity 빌드 설정을 업데이트합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* [Unity HoloLens](get-started-unity-hololens.md) 또는 [MRTK가 포함된 Unity HoloLens](get-started-unity-hololens-mrtk.md) 빠른 시작의 모든 필수 구성 요소.
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2020.3.8f1 이상이 설치된 Unity Hub</a>

## <a name="open-and-upgrade-the-sample-project"></a>샘플 프로젝트 열기 및 업그레이드

[Unity HoloLens](get-started-unity-hololens.md) 또는 [MRTK가 포함된 Unity HoloLens](get-started-unity-hololens-mrtk.md) 빠른 시작의 단계에 따라 [샘플 리포지토리](https://github.com/Azure/azure-object-anchors)를 복제하고 Unity용 Azure Object Anchors 패키지를 다운로드합니다.

Unity Hub를 엽니다. **추가** 단추를 선택하고 `quickstarts/apps/unity/basic` 또는 `quickstarts/apps/unity/mrtk` 프로젝트를 선택합니다. 그런 다음, **Unity 버전** 열 아래의 머신에 설치된 드롭다운에서 Unity 2020 버전을 선택합니다. **대상 플랫폼** 열에서 **유니버설 Windows 플랫폼** 을 선택합니다. 마지막으로 **프로젝트 이름** 열을 선택하고 Unity에서 샘플을 엽니다.

:::image type="content" source="./media/upgrade-unity-2020.png" alt-text="Unity 2020 업그레이드":::

프로젝트를 업그레이드할 것인지 확인하는 대화 상자가 표시됩니다. **확인** 단추를 선택합니다.

:::image type="content" source="./media/confirm-unity-upgrade.png" alt-text="Unity 업그레이드 확인":::

## <a name="upgrade-package-dependencies"></a>패키지 종속성 업그레이드

업그레이드 프로세스가 완료되면 **Unity 편집기** 가 열립니다.

<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality 기능 도구</a> 설명서에 따라 도구를 설정하고 사용 방법을 알아보세요.

**플랫폼 지원** 섹션에서 **Mixed Reality OpenXR 플러그 인** 기능 패키지(버전 1.0.0 이상)를 Unity 포로젝트 폴더에 설치합니다. `quickstarts/apps/unity/mrtk` 프로젝트를 사용하는 경우 **Mixed Reality Toolkit** 섹션도 열어 **Mixed Reality Toolkit Foundation** 및 **Mixed Reality Toolkit 도구** 기능 패키지를 찾아 버전 2.7.0 이상으로 업그레이드합니다.

**Unity 편집기** 로 돌아갑니다. **Mixed Reality Feature 도구** 기능 패키지가 설치되는 동안 몇 분 정도 걸릴 수 있습니다.

새 입력 시스템을 활성화할 것인지 묻는 대화 상자가 표시됩니다. **예** 단추를 선택합니다.

:::image type="content" source="./media/new-input-system.png" alt-text="새 입력 시스템":::

 MRTK 셰이더를 덮어쓸지 묻는 대화 상자가 나타나면 **예** 를 선택합니다.

:::image type="content" source="./media/mrtk-shaders.png" alt-text="mrtk 셰이더":::

설치 프로세스가 완료되면 Unity가 자동으로 다시 시작됩니다.

## <a name="update-configuration-settings"></a>구성 설정 업데이트

**Unity 편집기** 로 돌아가서 <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">OpenXR용 XR 플러그 인 관리 구성</a> 설명서에 따라 **프로젝트 설정** 에서 **XR 플러그 인 관리** 를 설정합니다. 그런 다음, <a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">최적화</a> 설명서에 따라 HoloLens 2에 대해 권장되는 프로젝트 설정을 적용합니다.

## <a name="update-mrtk-settings"></a>MRTK 설정 업데이트

`quickstarts/apps/unity/mrtk` 프로젝트를 사용하는 경우 MRTK에도 약간의 조정이 필요합니다. 이 경우에 다음 단계를 따르세요. 그렇지 않으면 **앱 빌드, 배포 및 실행** 섹션으로 건너뜁니다.

**Unity 편집기** 에서 `Assets/MixedReality.AzureObjectAnchors/Scenes`로 이동하여 **AOASampleScene** 을 엽니다. **계층** 창 아래에서 **MixedRealityToolkit** 개체를 선택합니다.

:::image type="content" source="./media/open-sample-scene.png" alt-text="샘플 장면 열기":::

**검사기** 창에서 **카메라** 단추를 선택하고 프로필을 **ObsoleteXRSDKCameraProfile** 에서 **DefaultMixedRealityCameraProfile** 로 변경합니다.

:::image type="content" source="./media/update-camera-profile.png" alt-text="카메라 프로필 업데이트":::

여전히 **검사기** 창 아래에서 **입력** 단추를 선택하고 **입력 데이터 공급자** 드롭다운을 확장합니다. 그런 다음, <a href="/windows/mixed-reality/mrtk-unity/configuration/getting-started-with-mrtk-and-xrsdk#configuring-mrtk-for-the-xr-sdk-pipeline" target="_blank">XR SDK 파이프라인에 대한 MRTK 구성</a> 설명서에 따라 적절한 입력 데이터 공급자(**OpenXRDeviceManager** 및 **WindowsMixedRealityDeviceManager**)를 설정합니다.

:::image type="content" source="./media/update-input-profile.png" alt-text="입력 프로필 업데이트":::

## <a name="build-deploy-and-run-the-app"></a>앱 빌드, 배포 및 실행

이제 프로젝트가 Unity 2020으로 완전히 업그레이드되었습니다. [Unity HoloLens](get-started-unity-hololens.md) 또는 [MRTK가 포함된 Unity HoloLens](get-started-unity-hololens-mrtk.md) 빠른 시작의 지침에 따라 앱을 빌드, 배포 및 실행합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
