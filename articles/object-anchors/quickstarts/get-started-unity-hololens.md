---
title: '빠른 시작: Unity를 사용하여 HoloLens 앱 만들기'
description: 이 빠른 시작에서는 Object Anchors를 사용하여 HoloLens Unity 앱을 빌드하는 방법을 알아봅니다.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 09/08/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 53bcc84fc63666e64ffaf502a4348de87c1ab8c7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796997"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>빠른 시작: Unity에서 Azure Object Anchors를 사용하여 HoloLens 앱 만들기

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

Unity에서 `quickstarts/apps/unity/basic` 프로젝트를 엽니다.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Configure Account](../../../includes/object-anchors-get-started-configure-account.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

Unity 시작 화면 다음에는 Object Observer가 초기화되었음을 나타내는 메시지가 표시됩니다.

앱은 보기의 현재 필드에서 개체를 찾은 다음 검색된 후 추적합니다. 인스턴스는 사용자의 위치에서 6미터 떨어진 경우 제거됩니다. 디버그 텍스트는 ID, 업데이트된 타임스탬프 및 표면 적용 비율과 같은 인스턴스에 대한 세부 정보를 보여 줍니다.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MRTK를 사용한 Unity HoloLens](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [개념: SDK 개요](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [변환 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)

> [!div class="nextstepaction"]
> [개체 검색 문제 해결](../troubleshoot/object-detection.md)
