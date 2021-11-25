---
title: '빠른 시작: 심층 MRTK 연습'
description: 이 빠른 시작에서는 Azure Object Anchors MRTK Unity 샘플 애플리케이션에 대해 자세히 설명합니다.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.custom: mode-other
ms.openlocfilehash: 1e5c39c08a0e536c81530a7d08922772adcacb17
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133073822"
---
# <a name="quickstart-in-depth-mrtk-walkthrough"></a>빠른 시작: 심층 MRTK 연습

이 가이드에서는 [Azure Object Anchors MRTK Unity 샘플 애플리케이션](get-started-unity-hololens-mrtk.md)에 대해 자세히 설명합니다. 샘플 디자인에 대한 인사이트를 제공하기 위한 것입니다. 이 가이드를 읽으면 개발자는 샘플의 주요 Azure Object Anchors 개념에 대한 이해를 가속화할 수 있습니다.

## <a name="project-layout"></a>프로젝트 레이아웃

Azure Object Anchors MRTK Unity 샘플에 대해 만들어진 자산은 `Assets\MixedReality.AzureObjectAnchors`에 저장됩니다. 하위 폴더는 다음과 같습니다.

- **아이콘**
  - 사용자용 메뉴에 사용되는 일부 사용자 지정 아이콘이 포함되어 있습니다.
- **재질**
  - 표면 재구성 시각화를 위한 셰이더 및 재질과 텍스트 주변의 홀로그램 안정화를 돕기 위해 깊이 버퍼에 쓰는 *깊이 전용* 셰이더가 포함되어 있습니다.
- **Prefabs**
  - 재사용 가능한 Unity `GameObjects`가 포함되어 있습니다. 특히 `TrackableObjectPrefab`은 Azure Object Anchors가 개체를 검색할 때 만들어지는 개체를 나타냅니다.
- **프로필**
  - 애플리케이션을 사용하도록 설정하는 데 필요한 최소한의 MRTK 기능을 설명하는 사용자 지정 MRTK 프로필이 포함되어 있습니다.
- **장면**
  - 샘플의 기본 장면인 `AOASampleTestScene`을 포함합니다.
- **스크립트**
  - 샘플용으로 작성된 스크립트가 포함되어 있습니다.

## <a name="unity-scene"></a>Unity 장면

**Mixed Reality 재생 공간** – 대부분 상용구 MRTK입니다.

- <a href="/windows/mixed-reality/develop/unity/mrtk-getting-started" target="_blank">Unity용 MRTK 소개</a>.
- Azure Object Anchors의 전체 상태를 자세히 설명하는 UI가 카메라에 연결되어 있습니다(`OverlayDebugText.cs` 참조).

**개체 Mixed Reality 재생 공간** – 대부분 Azure Object Anchors와 관련이 있지만 일부 MRTK 컨트롤이 있습니다. 상위 항목에 연결된 두 개의 스크립트 `TrackableObjectSearch` 및 `ObjectTracker`는 Azure Object Anchors와의 기본 인터페이스를 나타냅니다.

- **메뉴**
  - 주로 MRTK 코드이지만 UI 상호 작용은 Azure Object Anchors 기능으로 전달됩니다.
  - 연결된 `TrackableObjectMenu` 스크립트는 MRTK UI 이벤트를 적절한 Azure Object Anchors 호출로 라우팅하는 기본 작업을 수행합니다.
  - <a href="/windows/mixed-reality/design/hand-menu" target="_blank">MRTK 손 메뉴</a>.
- **WorkspaceBoundingBox**
  - 경계 상자 제어와 관련된 MRTK 스크립트를 포함합니다.
  - 또한 까다로운 검색 중에 정렬을 돕기 위해 검색이 발생하기 전에 Azure Object Anchors 모델을 시각화하는 데 사용되는 `ModelVis` 하위 개체도 포함합니다.

## <a name="menu-walkthrough"></a>메뉴 둘러보기

스크립트를 살펴보기 전에 먼저 메뉴 항목을 살펴보겠습니다. 이를 통해 이러한 메뉴 항목이 스크립트와 상호 작용하는 방식을 참조할 수 있습니다.

:::image type="content" source="./media/mrtk-menus.png" alt-text="MRTK 메뉴":::

하단 및 오른쪽 하위 메뉴는 자동으로 나타나지 않지만 각각 `Search Area Settings` 및 `Tracker Settings`로 토글됩니다.

### <a name="main-menu"></a>주 메뉴

- **검색 시작**
  - 지정된 검색 영역에서 개체 검색을 시작합니다.
- **공간 매핑 토글**
  - 검색하는 동안 공간 매핑 표시, 항상 공간 매핑 표시, 공간 매핑 표시 안 함 사이를 순환합니다.
- **추적기 설정/검색 영역 설정**
  - 해당 하위 메뉴를 토글합니다.
- **추적 시작/추적 중지**
  - 진단 추적을 시작하거나 종료합니다.
- **추적 업로드**
  - 디버그 분석을 위해 진단 추적을 Microsoft에 업로드합니다.

### <a name="tracker-settings"></a>추적기 설정

- **높은 정확도**
  - 사용하도록 설정되면 `ObjectInstanceTrackingMode`가 `HighLatencyAccuratePosition`으로 설정됩니다.
  - 사용하지 않도록 설정되면 `ObjectInstanceTrackingMode`가 `LowLatencyCoarsePosition`으로 설정됩니다.
- **완화된 세로 맞춤**
  - 사용하도록 설정되면 `AllowedVerticalOrientationInDegrees`를 10도로 설정합니다. 이 기능을 사용하면 램프에 있는 개체를 검색할 수 있습니다.
  - 사용하지 않도록 설정되면 `AllowedVerticalOrientationInDegrees`를 0도로 설정합니다.
- **크기 조정 변경 허용**
  - 사용하도록 설정되면 `MaxScaleChange`를 0.1로 설정합니다. 이 기능을 사용하면 Azure Object Anchors가 HoloLens 추적 배율 조정을 기반으로 개체의 크기를 조정할 수 있습니다.
  - 사용하지 않도록 설정되면 `MaxScaleChange`를 0으로 설정합니다.
- **적용 범위 비율 슬라이더**
  - 개체 검색이 일치를 고려하는 데 필요한 적용 범위의 비율을 조정합니다. 값이 낮을수록 검색 빈도가 높아집니다. 이 기능은 검색하기 어려운 개체에 적합할 수 있지만 가양성 개체 검색을 증가시킬 수도 있습니다.

### <a name="search-area-settings"></a>검색 영역 설정

- **검색 영역 잠금**
  - 사용하도록 설정되면 사용자가 검색 영역을 변경할 수 없습니다.
- **검색 영역 자동 조정**
  - 사용하도록 설정되면 스크립트가 검색 영역을 이동하여 검색(detection) 프로세스를 구체화할 수 있습니다.
- **메시 순환**
  - 검색할 수 있고 메시가 없는 `.ou` 개체에 대해 메시를 순환합니다.

## <a name="scripts"></a>스크립트

- **AutonomousSearchArea.cs**
  - 이 스크립트는 `WorkspaceBoundingBox`에 연결됩니다. 스크립트는 자동으로 `WorkspaceBoundingBox`의 크기를 조정하고 배치하려고 시도합니다. `Auto-Adjust Search Area`가 사용하도록 설정되면 사용하도록 설정됩니다.
- **ObjectAnchorsSubscription.cs**
  - 이 스크립트는 진단 데이터를 업로드하는 데 필요한 정보를 래핑합니다.
- **ObjectTracker.cs**
  - 이 스크립트는 Unity와 Azure Object Anchors SDK의 검색 측면을 연결합니다.
- **ObjectTrackerDiagnostics.cs**
  - 이 스크립트는 Azure Object Anchors SDK의 진단 기능을 관리합니다.
- **OverlayDebugText.cs**
  - 이 스크립트는 기본 카메라에 연결됩니다. 샘플 및 Azure Object Anchors의 전체 상태를 사용자에게 표시하는 역할을 합니다.
- **PositionDebugInfo.cs**
  - 이 스크립트는 단순히 검색된 개체에 연결된 디버그 텍스트가 사용자에게 표시되도록 합니다.
- **SearchAreaController.cs**
  - 이 스크립트는 Azure Object Anchors가 개체를 검색해야 하는 위치를 나타내는 데 사용되는 `WorkspaceBoundingBox` 상태를 관리합니다.
- **SearchAreaModelVisualization.cs**
  - 이 스크립트는 `Search Area Settings`에서 `Cycle Mesh` 기능을 사용하도록 설정합니다.
- **SpatialMappingController.cs**
  - 이 스크립트는 `Main Menu` 아래의 `Toggle Spatial Mapping`과의 상호 작용을 기반으로 공간 매핑을 사용하도록 설정해야 하는 시기를 관리합니다.
- **TextToSpeech.cs**
  - 이 스크립트는 텍스트를 받아 음성으로 변환합니다.
- **TrackableObjectData.cs**
  - 이 스크립트는 추적할 수 있는 개체에 대한 데이터를 나타냅니다.
- **TrackableObjectDataLoader.cs**
  - 이 스크립트는 `.ou` 파일을 로드하고 `TrackableObjectData` 항목으로 변환하는 작업을 수행합니다.
- **TrackableObjectMenu.cs**
  - 대부분의 사용자 상호 작용은 UI에서 이 스크립트로 이동한 다음 적절한 스크립트로 이동합니다. 예를 들어 `TrackableObjectMenu`에는 `SpatialMappingController`로 라우팅되는 `ToggleSpatialMapping`이 있습니다.
- **TrackableObjectSearch.cs**
  - 이 스크립트는 검색 영역의 몇 가지 가벼운 관리를 수행합니다. 특히, 사용자가 공간을 탭하고 검색 상자가 사용자 앞에 배치되는 상호 작용이 있습니다. 또한 검색 중 또는 개체가 검색되는 동안 검색 상자 내부가 렌더링되지 않도록 하여 전체 렌더링이 개체를 가리는 것을 방지합니다.
- **TrackedObject.cs**
  - `TrackableObjectPrefab`의 기본 스크립트입니다. Azure Object Anchors에서 검색한 개체의 시각화 상태를 유지 관리합니다.
- **TrackedObjectData.cs**
  - 이 스크립트에는 Azure Object Anchors가 현재 추적 중인 개체에 대한 정보가 포함되어 있습니다.

## <a name="prefabs"></a>Prefab

- **TrackableObjectPrefab**
  - AzureObjectAnchors에서 개체를 검색할 때 만들어지는 Prefab입니다. 이 Prefab의 하위 항목 `LogicalCenter`는 개체의 중심을 나타내며 탐지된 개체에 하위 콘텐츠를 연결하려고 할 때 시작하기에 적절한 위치입니다. 샘플에 의해 표시되는 정보 텍스트는 이 논리 센터에 연결됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
