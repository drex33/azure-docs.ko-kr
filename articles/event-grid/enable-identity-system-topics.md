---
title: Azure Event Grid 시스템 항목에서 관리 ID 사용
description: 이 문서에서는 Azure Event Grid 시스템 항목에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 825b1d22568b110f22e596d424a2f781b65de625
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157136"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Event Grid 시스템 항목에 시스템 관리 ID 할당
이 문서에서는 시스템 할당 또는 사용자 할당 ID를 Event Grid 시스템 항목에 할당하는 방법을 알아봅니다. 일반적으로 관리 ID에 대해 알아보려면 [Azure 리소스에 대한 관리 ID란?을 참조하세요.](../active-directory/managed-identities-azure-resources/overview.md)  

> [!NOTE]
> - 시스템 할당 ID 1개와 사용자 할당 ID를 최대 2개까지 시스템 토픽에 할당할 수 있습니다. 
> - Azure 구독, 리소스 그룹 또는 Azure 지도 같은 글로벌 Azure 리소스와 연결된 시스템 토픽에 대한 ID를 사용하도록 설정할 수 있습니다. 이러한 글로벌 원본에 대한 시스템 항목도 특정 지역과 연결되지 않습니다.

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>기존 시스템 항목에서 관리 ID 사용
이 섹션에는 기존 시스템 항목에서 관리 ID를 사용하도록 설정하는 방법이 나와 있습니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **이벤트 그리드 시스템 항목** 을 검색합니다.
3. 관리 ID를 사용하도록 설정할 **시스템 항목** 을 선택합니다. 
4. 왼쪽 메뉴에서 **ID** 를 선택합니다.  

### <a name="enable-system-assigned-identity"></a>시스템이 할당한 ID 사용
1. 스위치를 **켜면** ID가 활성화됩니다. 
1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="시스템 항목에 대한 ID 페이지."::: 
1. 확인 메시지에서 **예** 를 선택합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="시스템 항목에 ID 할당 - 확인."::: 
1. 시스템 할당 관리 ID의 개체 ID와 역할을 할당하는 링크가 표시되는지 확인합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="시스템 항목에 ID 할당 - 완료."::: 

### <a name="enable-user-assigned-identity"></a>사용자가 할당한 ID 사용

1. 먼저, [사용자 할당 관리 ID 관리](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 문서의 지침에 따라 사용자가 할당한 ID를 만듭니다. 
1. **ID** 페이지의 오른쪽 창에서 **사용자 할당** 탭으로 전환한 다음 도구 모음에서 **+ 추가** 를 선택합니다.

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="ID 페이지의 사용자 할당 탭에서 선택한 추가 단추를 보여주는 이미지.":::
1. **사용자 관리 ID 추가** 창에서 다음 단계를 수행합니다.
    1. 사용자 할당 ID가 있는 **Azure 구독** 을 선택합니다. 
    1. **사용자 할당 ID** 를 선택합니다. 
    1. **추가** 를 선택합니다. 
1. **사용자 할당** 탭에서 목록을 새로 고쳐 추가된 사용자가 할당한 ID를 확인합니다.

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>시스템 항목을 만들 때 관리 ID 사용

1. Azure Portal의 검색 표시줄에서 **Event Grid 시스템 항목** 을 검색하여 선택합니다. 
1. **Event Grid 시스템 항목** 페이지의 도구 모음에서 **만들기** 를 선택합니다. 
1. 만들기 마법사의 **기본** 페이지에서 다음 단계를 수행합니다. 
    1. **항목 종류** 에서 시스템 항목을 지원하는 항목의 종류를 선택합니다. 다음 예제에서는 **스토리지 계정** 을 선택합니다. 
    2. **구독** 에서 Azure 리소스를 포함하는 Azure 구독을 선택합니다. 
    1. **리소스 그룹** 에서 프로필을 포함하는 리소스 그룹을 선택합니다. 
    1. **리소스** 에서 리소스를 선택합니다. 
    1. 시스템 항목의 **이름** 을 지정합니다.
    1. 관리 ID 사용:
        1. 시스템이 할당한 ID를 사용하도록 설정하려면 **시스템이 할당한 ID 사용** 을 선택합니다. 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="시스템이 할당한 ID 옵션이 선택된 시스템 항목 만들기 마법사의 스크린샷을 보여 주는 이미지.":::            
        1. 사용자가 할당한 ID를 사용하려면 다음을 수행합니다. 
            1. **사용자가 할당한 ID** 를 선택한 다음 **사용자 ID 추가** 를 선택합니다. 
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="사용자가 할당한 ID 옵션이 선택된 시스템 항목 만들기 마법사의 스크린샷을 보여 주는 이미지.":::            
            1. **사용자 관리 ID 추가** 창에서 다음 단계를 수행합니다.
                1. 사용자 할당 ID가 있는 **Azure 구독** 을 선택합니다. 
                1. **사용자 할당 ID** 를 선택합니다. 
                1. **추가** 를 선택합니다.                         

> [!NOTE]
> - 현재 Azure Portal 시스템 항목을 만들 때 시스템 할당 ID와 사용자 할당 ID를 모두 할당할 수 없습니다. 시스템 토픽을 만든 후 둘 다 할당할 수 있습니다. 
> - 현재 시스템 항목을 지원하는 Azure 리소스에서 이벤트 구독을 만들 때 새 시스템 항목에 관리 ID를 사용하도록 설정할 수 없습니다. 


## <a name="next-steps"></a>다음 단계
대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 단계는 [관리 ID에 Event Grid 대상에 대한 액세스 권한 부여](add-identity-roles.md)를 참조하세요. 