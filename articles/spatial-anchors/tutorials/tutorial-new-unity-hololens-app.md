---
title: '자습서: 새 HoloLens Unity 앱 만들기'
description: 이 자습서에서는 Azure Spatial Anchors를 사용하여 새 HoloLens Unity 앱을 만드는 방법에 대해 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: d0a69f2dc52103366c10a016e357a738cf6c335f
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954054"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>자습서: Azure Spatial Anchors를 사용하여 새 HoloLens Unity 앱을 만드는 단계별 지침

이 자습서에서는 Azure Spatial Anchors를 사용하여 새 HoloLens Unity 앱을 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

1. [개발자 모드](/windows/mixed-reality/using-visual-studio)가 설정된 HoloLens 디바이스. 이 문서에는 [Windows 10 2020년 5월 업데이트](/windows/mixed-reality/whats-new/release-notes-may-2020)가 있는 HoloLens 디바이스가 필요합니다. HoloLens의 최신 릴리스로 업데이트하려면 **설정** 앱을 열고 **업데이트 및 보안** 으로 이동한 다음, **업데이트 확인** 단추를 선택합니다.
2. **유니버설 Windows 플랫폼 개발** 워크로드 및 **Windows 10 SDK(10.0.18362.0 이상)** 구성 요소와 함께 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 이상</a>이 설치된 Windows 머신 및 <a href="https://git-scm.com/download/win" target="_blank">Windows용 Git</a>.
3. Visual Studio용 [C++/WinRT Visual Studio 확장(VSIX)](https://aka.ms/cppwinrt/vsix)은 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)에서 설치합니다.
4. Unity 설치. 지원되는 버전 및 필수 기능은 [Unity 프로젝트 설정 페이지](../how-tos/setup-unity-project.md)를 참조하세요.


## <a name="getting-started"></a>시작

먼저 프로젝트와 Unity 장면을 설정합니다.
1. Unity를 시작합니다.
2. **새로 만들기** 를 선택합니다.
4. **3D** 를 선택했는지 확인합니다.
5. 프로젝트 이름을 지정하고 저장 **위치** 를 입력합니다.
6. **프로젝트 만들기** 를 선택합니다.
7. 다음을 사용하여 비어 있는 기본 장면을 새 파일에 저장합니다. **파일** > **다른이름으로 저장**.
8. 새 장면 이름을 **Main** 으로 지정하고 **저장** 단추를 누릅니다.

**프로젝트 설정 지정**

이제 개발에 Windows Holographic SDK를 사용할 수 있도록 하는 일부 Unity 프로젝트 설정을 지정합니다.

먼저 애플리케이션에 대한 품질 설정을 지정해 보겠습니다.
1. **편집** > **프로젝트 설정** > **품질** 을 선택합니다.
2. **Windows 스토어** 로고 아래의 열에서 **기본값** 행에 있는 화살표를 클릭하고 **매우 낮음** 을 선택합니다. **Windows 스토어** 열 및 **매우 낮음** 행의 상자가 녹색이면 설정이 올바르게 적용된 것입니다.

2D 보기가 아닌 몰입형 보기를 사용하여 Unity 앱을 구성해야 합니다. Unity에서 Windows 10 SDK를 대상으로 하는 가상 현실 지원을 사용하도록 설정하여 몰입형 보기를 만들 수 있습니다.
1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다.
2. **플레이어 설정** 의 **검사기 패널** 에서 **Windows** 아이콘을 선택합니다.
3. **XR 설정** 그룹을 확장합니다.
4. **렌더링** 섹션에서 **가상 현실 지원** 확인란을 선택하여 새 **가상 현실 SDK** 목록을 추가합니다.
5. 목록에 **Windows Mixed Reality** 가 나타나는지 확인합니다. 그렇지 않은 경우 목록 아래쪽에서 **+** 단추를 선택하고 **Windows Mixed Reality** 를 선택합니다.

> [!NOTE]
> Windows 아이콘이 표시되지 않으면 설치하기 전에 Windows .NET 스크립팅 백 엔드를 선택했는지 한 번 더 확인합니다. 이 항목을 선택하지 않은 경우 올바른 Windows 설치를 사용하여 Unity를 다시 설치해야 할 수 있습니다.

**스크립팅 백 엔드 구성 확인**
1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다(이전 단계에서 **플레이어** 를 열어 두었을 수도 있음).
2. **플레이어 설정** 의 **검사기 패널** 에서 **Windows 스토어** 아이콘을 선택합니다.
3. **기타 설정** 구성 섹션에서 **스크립팅 백 엔드** 가 **IL2CPP** 로 설정되어 있는지 확인합니다.

**기능 설정**
1. **편집** > **프로젝트 설정** > **플레이어** 로 이동합니다(이전 단계에서 **플레이어** 를 열어 두었을 수도 있음).
2. **플레이어 설정** 의 **검사기 패널** 에서 **Windows 스토어** 아이콘을 선택합니다.
3. **게시 설정** 구성 섹션에서 **InternetClientServer** 및 **SpatialPerception** 을 선택합니다.
> [!IMPORTANT]
> 비공개로 구성된 네트워크를 사용하는 경우 **PrivateNetworkClientServer** 기능도 사용하도록 설정해야 합니다.

**주 가상 카메라 설정**
1. **계층 구조 패널** 에서 **주 카메라** 를 선택합니다.
2. **검사기** 에서 해당 변환 위치를 **0,0,0** 으로 설정합니다.
3. **플래그 지우기** 속성에서 드롭다운을 **Skybox** 에서 **단색** 으로 변경합니다.
4. **백그라운드** 필드를 클릭하여 색 선택을 엽니다.
5. **R, G, B 및 A** 를 **0** 으로 설정합니다.
6. **구성 요소 추가** 를 선택하고, **공간 매핑 Collider** 를 검색하여 추가합니다.

**스크립트 만들기**
1. **프로젝트** 창의 **Assets** 폴더 아래에 새 폴더 **Scripts** 를 만듭니다.
2. 폴더를 마우스 오른쪽 단추로 클릭한 다음, **만들기 >** , **C# 스크립트** 를 선택합니다. 제목을 **AzureSpatialAnchorsScript** 로 지정합니다.
3. **GameObject** -> **빈 항목 만들기** 로 이동합니다.
4. 선택한 후 **검사기** 에서 이름 **GameObject** 를 **MixedRealityCloud** 로 바꿉니다. **구성 요소 추가** 를 선택하고 **AzureSpatialAnchorsScript** 를 검색한 후 추가합니다.

**구체 prefab 만들기**
1. **GameObject** -> **3D 개체** -> **구체** 로 이동합니다.
2. **검사기** 에서 크기를 **0.25, 0.25, 0.25** 로 설정합니다.
3. **계층** 창에서 **구체** 개체를 찾습니다. 구체 개체를 클릭하여 **프로젝트** 창의 **Assets** 폴더로 끕니다.
4. **계층** 창에서 만든 원래 구체를 마우스 오른쪽 단추로 클릭하여 **삭제** 합니다.

이제 **프로젝트** 창에 구체 prefab이 생겼습니다.

## <a name="trying-it-out"></a>사용해 보기
모든 항목이 작동하는지 테스트하려면 **Unity** 에서 앱을 빌드하고 **Visual Studio** 에서 배포합니다. [**MR 기본 사항 100: Unity 시작** 과정](/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio)의 6장을 진행하여 이 작업을 수행합니다. Unity 시작 화면이 표시된 후 명확한 화면이 표시됩니다.

## <a name="place-an-object-in-the-real-world"></a>실제 환경에 개체 배치
앱을 사용하여 개체를 만들고 배치해 보겠습니다. [앱을 배포](#trying-it-out)할 때 만든 Visual Studio 솔루션을 엽니다.

먼저, 다음 가져오기를 `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`에 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

다음 멤버 변수를 `AzureSpatialAnchorsScript` 클래스에 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

계속하기 전에, spherePrefab 멤버 변수에서 만든 구체 prefab을 설정해야 합니다. **Unity** 로 돌아갑니다.
1. **Unity** 의 **계층** 창에서 **MixedRealityCloud** 개체를 선택합니다.
2. **프로젝트** 창에서 저장한 **구체** prefab을 클릭합니다. 클릭한 **구체** 를 **검사기** 창의 **Azure Spatial Anchors Script(스크립트)** 아래에 있는 **구체 Prefab** 영역으로 끕니다.

이제 스크립트에 prefab으로 설정된 **구체** 가 생겼습니다. **Unity** 에서 빌드한 다음, [사용해 보기](#trying-it-out)에서 했던 것처럼 **Visual Studio** 솔루션을 다시 엽니다.

**Visual Studio** 에서 `AzureSpatialAnchorsScript.cs` 파일을 다시 엽니다. 다음 코드를 `Start()` 메서드에 추가합니다. 이 코드는 `GestureRecognizer`를 연결하며, 에어 탭을 감지하면 `HandleTap`을 호출합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

이제 `Update()` 아래에 다음 `HandleTap()` 메서드를 추가해야 합니다. 광선 캐스트를 수행하고 구를 배치할 적중 지점을 가져옵니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

이제는 구를 만들어야 합니다. 구는 처음에는 흰색이지만 나중에 이 값이 조정됩니다. 다음 `CreateAndSaveSphere()` 메서드를 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

**Visual Studio** 에서 앱을 실행하여 유효성을 한 번 더 검사합니다. 이번에는 화면을 탭하여 원하는 표면에 흰색 구를 만들고 배치합니다.

## <a name="set-up-the-dispatcher-pattern"></a>디스패처 패턴 설정

Unity로 작업할 때 모든 Unity API(예: UI 업데이트를 수행하는 데 사용하는 API)가 주 스레드에서 발생해야 합니다. 그러나 작성하게 될 코드에서는 다른 스레드에서 콜백을 가져옵니다. 이러한 콜백에서 UI를 업데이트하려고 하므로 부 스레드에서 주 스레드로 이동할 방법이 필요합니다. 부 스레드로부터 주 스레드에서 코드를 실행하기 위해 디스패처 패턴을 사용합니다.

작업 큐인 멤버 변수 `dispatchQueue`를 추가합니다. 작업을 큐에 밀어넣은 후 큐에서 제거하고 주 스레드에서 실행합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

다음으로, 큐에 작업을 추가하는 방법을 추가해 보겠습니다. `Update()` 바로 뒤에 `QueueOnUpdate()`를 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Update() 루프를 사용하여 큐에 대기 중인 작업이 있는지 확인할 수 있습니다. 작업이 있으면 큐에서 제거한 후 실행합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Azure Spatial Anchors SDK 다운로드

### <a name="choose-asa-version"></a>ASA 버전 선택
[!INCLUDE [Choose SDK Version](../../../includes/spatial-anchors-unity-choose-sdk-version.md)]

### <a name="download-packages"></a>패키지를 다운로드합니다.
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>패키지 가져오기
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="prepare-code"></a>코드 준비
사용자의 **Visual Studio** 솔루션에서 `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`에 다음 가져오기를 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

다음으로, 다음 멤버 변수를 `AzureSpatialAnchorsScript` 클래스에 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>로컬 Azure Spatial Anchor를 로컬 앵커에 연결

Azure Spatial Anchor의 CloudSpatialAnchorSession을 설정해보겠습니다. 먼저 `AzureSpatialAnchorsScript` 클래스 내에 다음 `InitializeSession()` 메서드를 추가합니다. 호출되면 앱을 시작하는 동안 Azure Spatial Anchors 세션이 만들어지고 제대로 초기화됩니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

이제 대리자 호출을 처리하는 코드를 작성해야 합니다. 계속 진행하면서 코드를 더 추가하게 됩니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

이제 `initializeSession()` 메서드를 `Start()` 메서드에 후크하겠습니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

마지막으로, 다음 코드를 `CreateAndSaveSphere()` 메서드에 추가합니다. 이렇게 하면 로컬 Azure Spatial Anchor가 실제 환경에 배치하는 구에 연결됩니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

계속 진행하기 전에 Azure Spatial Anchors 계정을 만들어 계정 식별자, 키 및 도메인을 가져와야 합니다. 해당 값이 아직 없는 경우 다음 섹션에 따라 가져옵니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>클라우드에 로컬 앵커 업로드

Azure Spatial Anchors 계정 식별자, 키 및 도메인이 있으면 `Account Id`를 `SpatialAnchorsAccountId`에, `Account Key`를 `SpatialAnchorsAccountKey`에, `Account Domain`을 `SpatialAnchorsAccountDomain`에 붙여넣습니다.

마지막으로, 모든 것을 함께 후크해 보겠습니다. `CreateAndSaveSphere()` 메서드에서 다음 코드를 추가합니다. 구가 만들어지는 즉시 `CreateAnchorAsync()` 메서드를 호출합니다. 메서드가 반환되면 아래 코드에서 구를 마지막으로 업데이트하여 구의 색을 파란색으로 변경합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

**Visual Studio** 에서 앱을 한 번 더 실행합니다. 머리를 움직여본 후 에어 탭을 움직여 구를 배치합니다. 충분한 프레임이 있으면 구의 색이 노란색으로 변하고 클라우드 업로드가 시작됩니다. 업로드가 완료되면 구의 색이 파란색으로 변합니다. 필요에 따라 **Visual Studio** 내에서 디버깅하는 동안 [출력 창](/visualstudio/ide/reference/output-window)을 사용하여 앱에서 보내는 로그 메시지를 모니터링할 수도 있습니다. 로그 메시지를 보려면 Visual Studio에서 앱의 `Debug` 구성을 배포해야 합니다. `RecommendedForCreateProgress`를 시청할 수 있고 업로드가 완료되면 클라우드에서 반환된 앵커 식별자를 볼 수 있습니다.

> [!NOTE]
> "DllNotFoundException: DLL 'AzureSpatialAnchors'를 로드할 수 없습니다. 지정된 모듈을 찾을 수 없습니다."가 표시되면 솔루션을 다시 **정리** 및 **빌드** 해야 합니다.

## <a name="locate-your-cloud-spatial-anchor"></a>클라우드 공간 앵커 찾기

하나의 앵커가 클라우드에 업로드되면 다시 찾을 준비가 되었습니다. 다음 코드를 `HandleTap()` 메서드에 추가해 보겠습니다. 이 코드에서 수행하는 작업은 다음과 같습니다.

* `ResetSession()`을 호출합니다. `CloudSpatialAnchorSession`이 중지되고 화면에서 기존 파란색 구가 제거됩니다.
* `CloudSpatialAnchorSession`을 다시 초기화합니다. 이를 통해 만든 로컬 앵커가 아닌 클라우드에서 제공되는 앵커를 찾아 사용할 수 있습니다.
* Azure Spatial Anchors에 업로드한 앵커를 찾는 **Watcher** 를 만듭니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

이제 `ResetSession()` 및 `CleanupObjects()` 메서드를 추가해 보겠습니다. 이러한 메서드를 `QueueOnUpdate()` 아래에 배치할 수 있습니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

이제 쿼리하는 앵커를 찾을 때 호출될 코드를 후크해야 합니다. `InitializeSession()` 내부에 다음 콜백을 추가합니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


이제 CloudSpatialAnchor를 찾으면 녹색 구를 만들고 배치하는 코드를 추가합니다. 또한 화면 태핑을 다시 활성화할 수 있으므로 전체 시나리오를 다시 한 번 반복할 수 있습니다. 즉, 다른 로컬 앵커를 만들고, 업로드하고, 다시 찾을 수 있습니다.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

이것으로 끝입니다. 앱을 마지막으로 **Visual Studio** 에서 실행하여 엔드투엔드 시나리오 전체를 진행합니다. 디바이스 주위를 이동하고 흰색 구를 배치합니다. 그런 다음, 구가 노란색으로 변할 때까지 머리를 계속 움직이면서 환경 데이터를 캡처합니다. 로컬 앵커가 업로드되고 구가 파란색으로 변합니다. 마지막으로, 화면을 한 번 더 탭하여 로컬 앵커를 제거하고 해당 클라우드에 대한 쿼리를 시작합니다. 클라우드 공간 앵커를 찾을 때까지 디바이스 주위를 계속 이동합니다. 녹색 구가 올바른 위치에 나타나야 하며 전체 시나리오를 다시 반복할 수 있습니다.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]