---
title: '빠른 시작: DirectX를 사용하여 HoloLens 앱 만들기'
description: 이 빠른 시작에서는 Object Anchors를 사용하여 HoloLens 앱을 빌드하는 방법을 알아봅니다.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 08/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 605acf95b521e150456a69e84d2008be7080589a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254223"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>빠른 시작: C++/WinRT 및 DirectX로 Azure Object Anchors를 사용하여 HoloLens 앱 만들기

이 빠른 시작에서는 C++/WinRT 및 DirectX로 [Azure Object Anchors](../overview.md)를 사용하여 HoloLens 앱을 만드는 방법을 다룹니다. Azure Object Anchors는 3D 자산을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 완료하면 Holographic Direct X 11(유니버설 Windows) 애플리케이션에서 개체와 해당 포즈를 검색할 수 있는 HoloLens 앱이 생성됩니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * HoloLens 애플리케이션 만들기 및 테스트용으로 로드
> * 개체 검색 및 해당 모델 시각화

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* 사용자 환경 및 해당 3D 모델(CAD 또는 스캔)의 물리적 개체
* 다음이 설치된 Windows 머신:
  * <a href="https://git-scm.com" target="_blank">Windows용 GIT</a>
  * **유니버설 Windows 플랫폼 개발** 워크로드 및 **Windows 10 SDK(10.0.18362.0 이상)** 구성 요소가 포함된 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>
* 최신 상태이고 [개발자 모드](/windows/mixed-reality/using-visual-studio#enabling-developer-mode)가 사용하도록 설정된 HoloLens 2 디바이스.
  * HoloLens의 최신 릴리스로 업데이트하려면 **설정** 앱을 열고 **업데이트 및 보안** 으로 이동한 다음, **업데이트 확인** 을 선택합니다.

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Visual Studio에서 `quickstarts/apps/directx/DirectXAoaSampleApp.sln`을 엽니다.

**솔루션 구성** 을 **릴리스** 로 변경하고 **솔루션 플랫폼** 을 **ARM64** 로 변경하고 배포 대상 옵션에서 **디바이스** 를 선택합니다.

## <a name="configure-the-account-information"></a>계정 정보 구성

다음 단계는 계정 정보를 사용하도록 앱을 구성하는 것입니다. ["Object Anchors 계정 만들기"](#create-an-object-anchors-account) 섹션에서 **계정 키**, **계정 ID** 및 **계정 도메인** 값을 기록해 두었습니다.

`Assets\ObjectAnchorsConfig.json`를 엽니다.

`AccountId` 필드를 찾아 `Set me`를 계정 ID로 바꿉니다.

`AccountKey` 필드를 찾아 `Set me`를 계정 키로 바꿉니다.

`AccountDomain` 필드를 찾아 `Set me`를 계정 도메인으로 바꿉니다.

이제 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드** 를 선택하여 **AoaSampleApp** 프로젝트를 빌드합니다.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="배포할 Visual Studio 프로젝트 구성":::

## <a name="deploy-the-app-to-hololens"></a>HoloLens에 앱 배포

샘플 프로젝트를 성공적으로 컴파일한 후에는 HoloLens에 앱을 배포할 수 있습니다.

HoloLens 디바이스의 전원이 켜져 있고 USB 케이블을 통해 PC에 연결되어 있는지 확인합니다. **디바이스** 가 선택한 배포 대상인지 확인합니다(위 참조).

**AoaSampleApp** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 팝업 메뉴에서 **배포** 를 클릭하여 앱을 설치합니다. Visual Studio의 **출력 창** 에 오류가 표시되지 않으면 앱이 HoloLens에 설치됩니다.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="HoloLens에 앱 배포":::

앱을 시작하기 전에 개체 모델(예: **chair.ou)** 을 HoloLens의 **3D Objects** 폴더에 업로드해야 합니다. 아직 수행하지 않은 경우 ["모델 업로드"](#upload-your-model) 섹션의 지침을 따릅니다.

앱을 실행하고 디버그하려면 **디버그 > 디버깅 시작** 을 선택합니다.

## <a name="ingest-object-model-and-detect-its-instance"></a>개체 모델 수집 및 해당 인스턴스 검색

이제 **AoaSampleApp** 앱이 HoloLens 디바이스에서 실행됩니다. 대상 개체(의자)에 가까이(2m 이내) 걸어가서 여러 관점에서 바라보면서 검사합니다. 개체 주변에 분홍색 경계 상자와 개체 표면 가까이에 렌더링된 노란색 점이 표시됩니다. 이는 개체가 검색되었음을 나타냅니다.

:::image type="content" source="./media/chair-detection.png" alt-text="의자 검색":::

그림: 경계 상자(분홍색), 점 구름(노란색) 및 검색 영역(큰 노란색 상자)으로 렌더링된 검색된 의자

오른손 또는 왼손으로 공중에서 손가락을 클릭하여 앱에서 개체에 대한 검색 공간을 정의할 수 있습니다. 검색 공간은 반경 2미터의 구, 4m^3 경계 상자 및 뷰 절두체 사이에서 전환됩니다. 자동차와 같은 더 큰 개체의 경우 가장 좋은 선택은 일반적으로 약 2미터 거리에서 개체의 모서리를 향한 상태에서 뷰 절두체 선택을 사용하는 것입니다.
검색 영역이 변경될 때마다 앱은 현재 추적 중인 인스턴스를 제거한 다음 새 검색 영역에서 다시 찾기를 시도합니다.

이 앱은 여러 개체를 한 번에 추적할 수 있습니다. 이렇게 하려면 여러 모델을 디바이스의 **3D 개체** 폴더에 업로드하고 모든 대상 개체를 포함하는 검색 영역을 설정합니다. 여러 개체를 검색하고 추적하는 데 시간이 더 오래 걸릴 수 있습니다.

앱은 3D 모델을 해당하는 실제 대응 개체에 가깝게 정렬합니다. 사용자는 왼손으로 에어 탭하여 고정밀 추적 모드를 켤 수 있으며, 이는 보다 정확한 포즈를 계산합니다. 이는 여전히 실험적인 기능으로, 더 많은 시스템 리소스를 소비하고 예상 포즈에서 더 높은 지터를 초래할 수 있습니다. 표준 추적 모드로 다시 전환하려면 왼손으로 다시 에어 탭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 3D 모델 수집](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
