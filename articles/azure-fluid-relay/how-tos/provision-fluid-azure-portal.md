---
title: '방법: Azure 유체 Relay 서비스 프로 비전'
description: Azure Portal를 사용 하 여 Azure 유체 Relay 서비스를 프로 비전 하는 방법
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663014"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>방법: Azure 유체 Relay 서비스 프로 비전

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Azure 유체 Relay 서버에 앱을 연결 하려면 azure 계정에 Azure 유체 Relay 서버 리소스를 프로 비전 해야 합니다. 이 문서에서는 Azure 유체 Relay 서비스를 프로 비전 하 고 사용할 수 있도록 준비 하는 단계를 안내 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

Azure 유체 Relay 서비스를 만들려면 Azure 계정이 있어야 합니다. 계정이 없는 경우 [Azure를 무료로 사용해](https://azure.com/free)볼 수 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스에 대한 논리적 컬렉션입니다. 모든 리소스는 리소스 그룹에서 배포 및 관리됩니다. 리소스 그룹을 만들려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 영역에서 **리소스 그룹** 을 선택합니다. 그런 다음, **추가** 를 선택합니다.

    :::image type="content" source="../images/add-resource-group.png" alt-text="Azure Portal에 있는 리소스 그룹 페이지의 스크린샷":::

3. 구독에서 리소스 그룹을 만들려는 Azure 구독의 이름을 선택합니다.

    :::image type="content" source="../images/create-resource-group.png" alt-text="Azure Portal에서 리소스 그룹 만들기 페이지의 스크린샷":::

1. 리소스 그룹에 대한 고유한 이름을 입력합니다. 시스템에서 즉시 이 이름이 현재 선택한 Azure 구독에서 사용 가능한지 확인합니다.
1. 리소스 그룹에 대한 지역을 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. 검토 + 만들기 창에서 **만들기** 를 선택합니다.

## <a name="create-a-fluid-relay-resource"></a>유체 Relay 리소스 만들기
각 Azure 유체 Relay 서버 리소스는 유체 응용 프로그램에서 사용할 테 넌 트를 제공 합니다. 해당 테 넌 트 내에서 많은 컨테이너/세션을 만들 수 있습니다. 포털을 사용 하 여 리소스 그룹에서 유체 Relay를 만들려면 다음을 수행 합니다.

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** 를 선택합니다.
2. ' 유체 ' 검색
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="' 액량 ' 용어에 대 한 검색 결과가 포함 된 리소스 만들기 페이지의 스크린샷":::

3. **유체 Relay** 를 선택 하 고 **만들기** 를 선택 합니다.
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Azure 유체 Relay marketplace 세부 정보 페이지의 스크린샷":::

4. 만들기 페이지에서 다음 단계를 수행 합니다.

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="새 Azure 유체 Relay 서버를 구성 하는 방법의 스크린샷":::

    1. 네임스페이스를 만들려는 구독을 선택합니다.
    2. 이전 단계에서 만든 리소스 그룹을 선택합니다.
    3. 유체 Relay 리소스의 이름을 입력 합니다.
    4. 네임스페이스의 위치를 선택합니다.
    
    > [!NOTE]
    > 공개 미리 보기 중에는 미국 서 부 2, 유럽 서부 및 SoutheastAsia 지역이 유일 하 게 지원 됩니다.

5. 페이지 맨 아래에 있는 **검토 + 만들기** 단추를 클릭 합니다.

6. 검토 + 만들기 페이지에서 설정을 검토하고 *만들기* 를 선택합니다. 배포가 완료될 때가지 기다립니다.

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="유효성 검사가 성공적으로 완료 된 후 새 서비스 페이지의 스크린샷":::

7. 배포 페이지에서 **리소스로 이동** 을 선택하여 네임스페이스에 대한 페이지로 이동합니다.

    :::image type="content" source="../images/deployment-complete.png" alt-text="배포가 완료 되었음을 나타내는 Azure Portal의 스크린샷":::

8. 이 예와 비슷한 유체 Relay 페이지가 표시 되는지 확인 합니다.

    :::image type="content" source="../images/resource-details.png" alt-text="배포 된 유체 Relay 리소스에 대 한 예제 세부 정보 페이지의 스크린샷":::

## <a name="next-steps"></a>다음 단계
방금 리소스 그룹을 만들고 해당 그룹에 Azure 유체 Relay 리소스를 프로 비전 했습니다. 다음으로 [앱의 Azure 유체 Relay 서비스에 연결할](../quickstarts/quickstart-dice-roll.md)수 있습니다.
