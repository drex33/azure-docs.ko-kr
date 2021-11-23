---
title: Unity용 Azure Spatial Anchors 설치
description: Azure Spatial Anchors를 사용하도록 Unity 프로젝트 구성
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/12/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 10aba027ee2adbf2faaab36732274d6f8269f03a
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954312"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity 프로젝트에서 Azure Spatial Anchors 구성

이 가이드에서는 Unity 프로젝트에서 Azure Spatial Anchors SDK를 시작하는 방법을 보여줍니다.

## <a name="project-requirements"></a>프로젝트 요구 사항

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>프로젝트 구성

Unity 프로젝트에 Azure Spatial Anchors SDK를 포함하려면 먼저 Unity 패키지 관리자를 통해 [필요한](#project-requirements) 패키지를 설치해야 합니다.

### <a name="choose-asa-version"></a>GLOBAL.ASA 버전 선택
[!INCLUDE [Choose SDK Version](../../../includes/spatial-anchors-unity-choose-sdk-version.md)]

### <a name="download-asa-packages"></a>ASA 패키지 다운로드
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA 패키지 가져오기
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="hololens-only"></a>HoloLens만

#### <a name="configure-your-unity-project-xr-settings"></a>Unity 프로젝트 XR 설정 구성
HoloLens에서 MixedReality 앱을 개발할 때 Unity에서 XR 구성을 설정해야 합니다. 자세한 내용은 [XR 구성 설정-혼합 현실 |을 참조 하세요. Microsoft Docs](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr) 하 고 [Unity 버전 및 XR 플러그 인-혼합 현실 선택 | Microsoft Docs](/windows/mixed-reality/develop/unity/choosing-unity-version).

azure 공간 고정 SDK 버전 2.9.0 또는 이전 버전은 Windows XR 플러그 인 ()에 대 한 지원도 제공 `com.unity.xr.windowsmr` 하므로 azure 공간 고정 Windows 패키지는 Windows XR 플러그 인에 대 한 명시적 종속성이 있습니다.

Azure 공간 고정 SDK 버전 2.10.0 이상에서는 혼합 현실 OpenXR 플러그 인 ([mixedreality](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) 및 Windows XR 플러그[인 (](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html))에 대 한 지원을 제공 합니다. `com.microsoft.mixedreality.openxr` `com.unity.xr.windowsmr` 선택한 항목에 따라 패키지 또는 패키지를 프로젝트에 포함 해야 합니다.

#### <a name="configure-your-unity-project-capabilities"></a>Unity 프로젝트 기능 구성

Unity 프로젝트에서 다음 기능을 사용 하도록 설정 해야 합니다.
- SpatialPerception
- InternetClient
- PrivateNetworkClientServer

> [!WARNING]
> PrivateNetworkClientServer 기능을 사용 하도록 설정 하지 않으면 장치가 비공개로 구성 된 네트워크를 사용 하는 경우 앵커를 쿼리하지 못할 수 있습니다.
### <a name="android-only-configure-the-maintemplategradle-file"></a>Android 전용: mainTemplate.gradle 파일 구성

1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다.
2. **플레이어 설정** 의 **검사기 패널** 에서 **Android** 아이콘을 선택합니다.
3. **빌드** 섹션 아래에서 **사용자 지정 주 Gradle 템플릿** 확인란을 선택하여 `Assets\Plugins\Android\mainTemplate.gradle`에서 사용자 지정 gradle 템플릿을 생성합니다.
4. 텍스트 편집기에서 `mainTemplate.gradle` 파일을 엽니다.
5. `dependencies` 섹션에서 다음 종속성을 붙여넣습니다.

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

모든 작업이 완료되면 `dependencies` 섹션은 다음과 같이 표시됩니다.

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: Unity에서 앵커 만들기 및 찾기](./create-locate-anchors-unity.md)