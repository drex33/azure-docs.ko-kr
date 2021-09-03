---
title: Azure Percept Audio 음성 도우미 애플리케이션 삭제
description: 이 문서에서는 이전에 만든 음성 도우미 애플리케이션을 삭제하는 방법을 보여 줍니다.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: e7f9f9dd0bf1caead19151eb8081e967963fae9a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224957"
---
# <a name="delete-your-azure-percept-audio-voice-assistant-application"></a>Azure Percept Audio 음성 도우미 애플리케이션 삭제

이 지침은 Azure Percept Audio 디바이스에서 음성 도우미 애플리케이션을 삭제하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [이전에 만든 음성 도우미 애플리케이션](./tutorial-no-code-speech.md)
- Azure Percept DK의 전원이 켜져 있고 Azure Percept Audio 액세서리가 USB 케이블을 통해 연결되어 있습니다.

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>Azure Portal에서 모든 음성 도우미 리소스 제거

음성 도우미 애플리케이션 작업이 끝났으면 다음 단계에 따라 애플리케이션을 만들 때 배포한 음성 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴 패널에서 **리소스 그룹** 을 선택하거나 검색 창에 리소스 그룹을 입력합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="왼쪽 메뉴 패널 및 리소스 그룹을 보여 주는 Azure Portal 홈페이지 스크린샷":::

1. 리소스 그룹을 선택합니다.

1. 해당하는 애플리케이션 접두사가 포함된 6개 리소스를 모두 선택하고 위쪽 메뉴 패널에서 **삭제** 아이콘을 선택합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="삭제하기 위해 선택한 음성 리소스를 보여 주는 스크린샷":::

1. 삭제를 확인하려면 확인 상자에 **예** 를 입력하고 올바른 리소스를 선택했는지 확인한 다음, **삭제** 를 선택합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="삭제 확인 창을 보여 주는 스크린샷":::

> [!WARNING]
> 그러면 삭제하려는 음성 리소스를 사용하여 만든 사용자 지정 키워드가 모두 제거되고, 음성 도우미 데모는 더 이상 작동하지 않습니다.


## <a name="next-steps"></a>다음 단계
이제 음성 도우미 애플리케이션을 제거했으므로 이 자습서에 따라 Azure Percept DK에서 다른 애플리케이션을 만들어 보세요.
- [코드 없는 비전 솔루션 만들기](./tutorial-nocode-vision.md)
- [코드 없는 음성 도우미 애플리케이션 만들기](./tutorial-no-code-speech.md)


