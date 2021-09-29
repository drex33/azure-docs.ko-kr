---
title: '빠른 시작: Unity 및 MRTK를 사용하여 HoloLens 앱 만들기'
description: 이 빠른 시작에서는 MRTK 및 Object Anchors를 사용하여 HoloLens Unity 앱을 빌드하는 방법을 알아봅니다.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 09/08/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 987c8933040b71c8053728f64da2964e02ac6fa3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607314"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>빠른 시작: MRTK를 사용하는 Unity에서 Azure Object Anchors를 사용하여 HoloLens 앱 만들기

이 빠른 시작에서는 [Azure Object Anchors](../overview.md)를 사용하는 Unity HoloLens 앱을 만듭니다. Azure Object Anchors는 3D 자산을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 완료하면 실제 세계에서 개체를 검색할 수 있는 Unity로 빌드된 HoloLens 앱이 생성됩니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Unity 빌드 설정 준비
> * HoloLens Visual Studio 프로젝트 내보내기
> * HoloLens 2 디바이스에 앱을 배포하고 실행

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Unity에서 `quickstarts/apps/unity/mrtk` 프로젝트를 엽니다.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Configure Account](../../../includes/object-anchors-get-started-configure-account.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

"TMP 가져오기" 대화 상자에서 TextMesh Pro 리소스를 가져오라는 메시지가 표시되면 "TMP Essentials 가져오기"를 선택합니다.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="TextMesh Pro 리소스 가져오기":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

 Unity 시작 화면 후에 흰색 경계 상자가 표시됩니다. 손을 사용하여 경계 상자를 이동, 크기 조정 또는 회전할 수 있습니다. 검색하려는 개체를 포함하도록 상자를 배치합니다.

<a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">손 메뉴</a>를 열고 **검색 영역 잠금** 을 선택하여 경계 상자가 더 이상 움직이지 않도록 합니다. 개체 검색을 시작하려면 **검색 시작** 을 선택합니다. 개체가 검색되면 개체에서 메시가 렌더링됩니다. 검색된 인스턴스의 세부 정보가 화면에 표시됩니다(예: 업데이트된 타임스탬프 및 표면 적용 비율). 추적을 중지하기 위해 **검색 중지** 를 선택하면 검색된 모든 인스턴스가 제거됩니다.

#### <a name="the-app-menus"></a>앱 메뉴

<a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">손 메뉴</a>를 사용하여 다른 작업을 수행할 수도 있습니다.

##### <a name="primary-menu"></a>주 메뉴

* **검색 시작 / 검색 중지** – 개체 검색 프로세스를 시작하거나 중지합니다.
* **공간 매핑 전환** – 공간 매핑 렌더링을 표시하거나 숨깁니다. 검사가 완료된 경우 이 옵션을 사용하여 디버그할 수 있습니다.
* **추적기 설정** – 추적기 설정 메뉴 활성화를 토글합니다.
* **검색 영역 설정** – 검색 영역 설정 메뉴 활성화를 토글합니다.
* **추적 시작** – 진단 데이터를 캡처하여 디바이스에 저장합니다. 자세한 내용은 **디버그 검색 문제 및 캡처 진단** 섹션을 참조하세요.
* **추적 업로드** – 진단 데이터를 Object Anchors 서비스에 업로드합니다.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Unity 기본 손 메뉴":::

##### <a name="tracker-settings-menu"></a>추적기 설정 메뉴

* **높은 정확성** – 더 정확한 포즈를 얻기 위해 사용되는 실험적 기능입니다. 이 옵션을 사용하도록 설정하면 개체 검색 중에 더 많은 시스템 리소스가 필요합니다. 개체 메시는 이 모드에 있을 때 분홍색으로 렌더링됩니다. 일반 추적 모드로 다시 전환하려면 이 단추를 다시 선택합니다.
* **안정된 수직 정렬** – 사용하도록 설정하면 개체가 수직이 아닌 각도에서 검색될 수 있습니다. 램프에서 개체를 검색하는 데 유용합니다.
* **크기 변경 허용** – 추적기가 환경 정보를 기반으로 검색된 개체의 크기를 변경할 수 있습니다.
* **적용 비율 슬라이더** – 추적기가 개체를 검색하기 위해 일치해야 하는 표면 점의 비율을 조정합니다.  낮은 값을 사용하면 추적기가 HoloLens 센서가 검색하기 어려운 개체(예: 어두운 개체 또는 반사율이 높은 개체)를 더 잘 검색할 수 있습니다. 값이 높을수록 잘못된 검색 빈도가 줄어듭니다.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Unity 추적기 손 메뉴":::

##### <a name="search-area-settings-menu"></a>검색 영역 설정 메뉴

* **검색 영역 잠금** – 실수로 손으로 움직이는 것을 방지하기 위해 영역 경계 상자를 잠급니다.
* **검색 영역 자동 조정** – 개체 검색 중에 검색 영역 자체의 위치를 ​​변경할 수 있습니다.
* **메시 순환** – 검색 영역 내에서 로드된 메시 시각화를 순환합니다.  이 옵션은 사용자가 검색하기 어려운 개체에 대해 검색 상자를 정렬하는 데 도움이 될 수 있습니다.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Unity 검색 영역 손 메뉴":::

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 심층 MRTK 연습](in-depth-mrtk-walkthrough.md)

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)

> [!div class="nextstepaction"]
> [개체 검색 문제 해결](../troubleshoot/object-detection.md)
