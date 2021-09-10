---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 08/02/2021
ms.author: crtreasu
ms.openlocfilehash: ce942908ffa068b0502bdac7f9472543355dbbce
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254203"
---
## <a name="upload-your-model"></a>모델 업로드

앱을 실행하기 전에 앱에서 모델을 사용할 수 있어야 합니다.
Object Anchors 모델이 아직 없는 경우 [모델 만들기](../articles/object-anchors/quickstarts/get-started-model-conversion.md)의 지침에 따라 모델을 만듭니다. 그런 다음, 여기로 돌아옵니다.

HoloLens의 전원이 켜져 있고 개발 디바이스(PC)에 연결된 상태에서 다음 단계에 따라 HoloLens의 **3D Objects** 폴더에 모델을 업로드합니다.

1. Ctrl 키와 C(Ctrl + C)를 함께 눌러 작업할 모델을 선택하고 복사합니다.

2. Windows 로고 키와 E(Win + E)를 함께 눌러 파일 탐색기를 시작합니다. 왼쪽 창에 다른 드라이브 및 폴더와 함께 나열된 HoloLens가 표시되어야 합니다.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-launch.png" alt-text="파일 탐색기":::

3. HoloLens 링크를 탭하여 오른쪽 창에 HoloLens 디바이스의 스토리지를 표시합니다.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-internal-storage.png" alt-text="HoloLens 내부 스토리지 열기":::

4. 파일 탐색기에서 **내부 스토리지 > 3D 개체** 로 이동합니다. 이제 Ctrl 키와 V(Ctrl + V)를 함께 눌러 **3D Objects** 폴더에 모델을 붙여넣을 수 있습니다.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-open-3d-objects.png" alt-text="3D 개체 폴더에 모델 붙여넣기":::