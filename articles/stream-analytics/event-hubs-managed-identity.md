---
title: 관리 ID를 사용하여 Azure Stream Analytics 작업에서 Event Hub에 액세스
description: 이 문서에서는 관리 ID를 사용하여 Azure Stream Analytics 작업을 Azure Event Hubs 입력 및 출력에 대해 인증하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 4b470ff5336807fd92ae14af2548cd3a4305c6ed
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113485908"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job"></a>관리 ID를 사용하여 Azure Stream Analytics 작업에서 Event Hub에 액세스

Azure Stream Analytics는 Azure Event Hubs 입력 및 출력에 대한 관리 ID 인증을 지원합니다. 관리 ID를 사용하면 암호 변경이나 90일마다 발생하는 사용자 토큰 만료로 인해 다시 인증해야 하는 것과 같이 사용자 기반 인증 방법에 관한 제한 사항이 제거됩니다. 수동으로 인증할 필요가 없어지면 Stream Analytics 배포를 완전히 자동화할 수 있습니다.  

관리 ID는 지정된 Stream Analytics 작업을 나타내는 Azure Active Directory에 등록된 관리형 애플리케이션입니다. 관리형 애플리케이션은 방화벽이나 VNet(가상 네트워크) 뒤에 있는 Event Hubs 포함하여 대상 리소스에 인증하는 데 사용됩니다. 방화벽을 무시하는 방법에 관한 자세한 내용은 [프라이빗 엔드포인트를 통한 Azure Event Hubs 네임스페이스 액세스 허용](../event-hubs/private-link-service.md#trusted-microsoft-services)을 참조하세요.

이 문서에서는 Azure Portal을 통해 Stream Analytics 작업의 Event Hubs 입력 및 출력에 관리 ID를 사용하도록 설정하는 방법을 보여 줍니다.관리 ID를 사용하도록 설정하기 전에 먼저 Stream Analytics 작업과 이벤트 허브 리소스가 있어야 합니다.

## <a name="create-a-managedidentity"></a>관리 ID 만들기  

먼저 Azure Stream Analytics 작업의 관리 ID를 만듭니다.  

1. Azure Portal에서 Azure Stream Analytics 작업을 엽니다.  

1. 왼쪽 탐색 메뉴에서  *구성* 아래에 있는  **관리 ID** 를 선택합니다. 그런 다음,  **시스템이 할당한 관리 ID 사용** 옆에 있는 확인란을 선택하고  **저장** 을 선택합니다.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="시스템 할당 관리 ID":::  

1. Azure Active Directory에서 Stream Analytics 작업의 ID에 대한 서비스 주체가 생성됩니다. 새로 생성된 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.  

   구성을 저장하면 서비스 주체의 OID(개체 ID)가 아래와 같이 보안 주체 ID로 나열됩니다.  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="보안 주체 ID":::

   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어, 작업 이름이  `MyASAJob`이면 서비스 주체 이름도  `MyASAJob`입니다.  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>이벤트 허브에 액세스할 수 있는 Stream Analytics 작업 권한 부여

Stream Analytics 작업이 관리 ID를 사용하여 이벤트 허브에 액세스하려면 직접 만든 서비스 주체에 이벤트 허브에 대한 특별한 권한이 있어야 합니다.

1. 이벤트 허브에서 **액세스 제어(IAM)** 로 이동합니다.

1. **+ 추가** 및 **역할 할당 추가** 를 선택합니다.

1. **역할 할당 추가** 페이지에서 다음 옵션을 입력합니다.

   |매개 변수|값|
   |---------|-----|
   |역할|Azure Event Hubs 데이터 소유자|
   |다음에 대한 액세스 할당|사용자, 그룹 또는 서비스 주체|
   |선택|Stream Analytics 작업의 이름 입력|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="역할 할당 추가":::

1. **저장** 을 선택하고 변경 내용이 전파될 때까지 1분 정도 기다립니다.

이벤트 허브 네임스페이스 수준에서 이 역할을 부여할 수도 있습니다. 그러면 자연스럽게 권한이 해당 역할로 생성된 모든 Event Hubs에 전파됩니다. 즉, 네임스페이스에 속한 모든 Event Hubs는 Stream Analytics 작업에서 관리 ID 인증 리소스로 사용할 수 있습니다.

> [!NOTE]
> 글로벌 복제 또는 캐싱 대기 시간으로 인해 권한이 취소되거나 부여될 때 지연이 발생할 수 있습니다. 변경 내용은 8분 내에 반영되어야 합니다.

## <a name="create-anevent-hub-input-or-output"></a>이벤트 허브 입력 또는 출력 만들기  

관리 ID를 구성했으므로 이제 이벤트 허브 리소스를 Stream Analytics 작업에 입력이나 출력으로 추가할 수 있습니다.  

### <a name="add-the-event-hub-as-an-input"></a>이벤트 허브를 입력으로 추가 

1. Stream Analytics 작업으로 이동하고 **작업 토폴로지** 에서 **입력** 페이지로 이동합니다.

1. **스트림 입력 추가 > 이벤트 허브** 를 선택합니다. 입력 속성 창에서 이벤트 허브를 검색하여 선택하고 ‘인증 모드’ 드롭다운 메뉴에서 **관리 ID** 를 선택합니다.

1. 나머지 속성을 입력하고 **저장** 을 선택합니다.

### <a name="add-the-event-hub-as-an-output"></a>이벤트 허브를 출력으로 추가

1. Stream Analytics 작업으로 이동하고 **작업 토폴로지** 에서 **출력** 페이지로 이동합니다.

1. **추가 > 이벤트 허브** 를 선택합니다. 출력 속성 창에서 이벤트 허브를 검색하여 선택하고 ‘인증 모드’ 드롭다운 메뉴에서 **관리 ID** 를 선택합니다.

1. 나머지 속성을 입력하고 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 Event Hubs 출력](event-hubs-output.md)
* [이벤트 허브에서 데이터 스트리밍](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
