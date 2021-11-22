---
title: Microsoft 센티널 관심 목록 및 플레이 북를 함께 사용 하 여 활동 자동화
description: 클라우드 인시던트 생성 규칙에 대 한 Microsoft 관심 목록 및 플레이 북를 만들어 보안 경고의 리소스 소유자에 게 알리는 방법에 대해 알아봅니다.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/18/2021
ms.openlocfilehash: 2d8e7171d19b438bea2388c1573246fc283abdfd
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132904720"
---
# <a name="use-microsoft-sentinel-watchlists-and-playbooks-together-to-automate-activity"></a>Microsoft 센티널 watchlists 및 플레이 북를 함께 사용 하 여 작업 자동화

이 문서에서는 watchlists 및 플레이 북를 함께 사용 하 여 시스템의 작업을 자동화 하는 데 도움이 되는 두 가지 일반적인 상황을 설명 합니다. 리소스 소유자에 게 경고에 대해 설명 하 고 allowlists로 인시던트에 응답

> [!TIP]
> 사용자 고유의 시스템에서 자동화를 만들고 필요에 따라 값을 수정 하는 방법에 대 한 예제 나 템플릿으로 이러한 샘플 플레이 북을 사용 합니다.
>
## <a name="automatically-inform-owners-of-alerts"></a>경고 소유자에 게 자동으로 알립니다.

[Microsoft Defender For Cloud alerts](/azure/defender-for-cloud/defender-for-cloud-introduction) 는 보안 운영 센터 (SOC)에 Azure 리소스에 대 한 가능한 보안 공격에 대해 알려줍니다.

SOC에 잠재적으로 손상 된 리소스에 대 한 액세스 권한이 없는 경우 경고 조사 중 리소스 소유자에 게 문의 하 여 리소스에서 검색 된 활동에 대해 잘 알고 있는지 확인 하 고 해당 리소스에 대 한 완화 단계를 수행 하도록 요청 해야 합니다.

이 문서에서는 관련 연락처를 수동으로 찾고 새 경고가 발생할 때마다이에 연결 하는 대신 Microsoft 센티널 watchlists 및 플레이 북를 사용 하 여 해당 연락처를 자동으로 만드는 방법을 보여 줍니다. 편의상이 문서에서는 구독 소유자 수준을 사용 하지만 지정 된 리소스 소유자에 대해이 솔루션을 구현할 수 있습니다.

> [!NOTE]
> 이 문서에 설명 된 플레이 북은 [Microsoft 센티널 인시던트 트리거](/connectors/azuresentinel/#triggers)를 사용 합니다. 클라우드 용 Microsoft Defender를 사용 하 여 예약 된 경고를 만든 다음 경고 트리거를 사용 하 여 비슷한 솔루션을 구현할 수 있습니다.
>

### <a name="prerequisites"></a>전제 조건

이 섹션에 설명 된 프로세스를 사용 하려면 다음 필수 구성 요소가 있어야 합니다.

- Microsoft 센티널 커넥터와 함께 사용 하 여 Azure Logic Apps [수 있는 사용자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 또는 등록 된 응용 프로그램

- [Defender For Cloud 데이터 커넥터](connect-azure-security-center.md) 및 [인시던트 생성 규칙](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) 사용

- Microsoft Teams에 대해 인증할 사용자 및 Office 365에 인증 하는 사용자 Outlook

### <a name="process-summary-and-playbook-steps"></a>프로세스 요약 및 플레이 북 단계

이 섹션에서 설명 하는 프로세스에는 다음 단계가 포함 됩니다.

1. Microsoft 센티널 관심 목록는 조직의 각 구독을 소유자 및 해당 연락처 전자 메일 주소로 매핑합니다.

1. **Watchlists-InformSubowner-IncidentTrigger** 플레이 북는 클라우드 인시던트 생성 규칙에 대해 Defender에 연결 됩니다.

   Microsoft 센티널로 이동 하는 클라우드 경고에 대 한 Defender의 모든 새 인스턴스는 Microsoft 센티널 인시던트를 만듭니다.

1. 그런 다음 플레이 북는 포함 된 경고를 입력으로 사용 하 여 인시던트를 수신 합니다.

1. 플레이 북는 관심 목록를 쿼리하고 관련 리소스의 구독 소유자 세부 정보를 찾습니다.

1. 플레이 북는 구독 소유자에 게 잠재적 리소스 손상에 대 한 세부 정보를 포함 하는 Teams 메시지 및 전자 메일을 보냅니다.


다음 이미지는 논리 앱 디자이너에서 **Watchlists-InformSubowner-IncidentTrigger** 플레이 북를 보여 줍니다.

![Watchlists-InformSubowner-IncidentTrigger 플레이 북의 스크린샷](media/automate-playbook-watchlist/inform-subowner-playbook.png)

플레이 북는 다음 단계를 실행 합니다.

1. **Microsoft 센티널 인시던트 생성 규칙이 트리거된 경우** 플레이 북는 생성 된 인시던트를 입력으로 받습니다.

1. 일반적으로 하나의 경고 인 인시던트의 **각 경고에 대해** 플레이 북는 다음 단계를 실행 합니다.

   1. **Remove-azureresource identifier를 가져오는 필터 배열** 입니다. 클라우드 경고에 대 한 Microsoft Defender에는 두 가지 종류의 식별자 `AzureResource` , 즉 Log Analytics에 표시 되는 리소스 ID 및 경고를 저장 하는 작업 영역에 대 한 Log Analytics 정보가 있을 수 있습니다. 이 작업은 `AzureResource` 나중에 사용할 식별자의 배열을 반환 합니다.

   1. **Json을 구문 분석 하 여 subscriptionId를 가져옵니다**. 이 단계에서는 클라우드 경고에 대 한 Defender의 *추가 데이터* 에서 구독 ID를 가져옵니다.

   1. **쿼리 실행 및 결과 나열-Get 관심 목록**. Azure Monitor Log Analytics 커넥터는 관심 목록 있는 Microsoft 센티널 작업 영역 세부 정보에 대 한 **구독**, **리소스 그룹** 및 **리소스 이름을** 포함 하 여 관심 목록 항목을 가져옵니다. 인수를 사용 하 여 `project` 사용과 관련 된 필드를 지정 합니다.

      ![쿼리 실행 및 결과 나열 플레이 북 작업의 스크린샷](media/automate-playbook-watchlist/inform-subowner-run-query.png)

   1. **관련 구독 소유자를 가져오기 위한 필터 배열** 입니다. 이 단계에서는 검색 중인 구독에 대 한 관심 목록 결과를 유지 합니다. 오른쪽의 Logic Apps 식 인수는 다음과 같습니다.

      `string(body('Parse_JSON_to_get_subscriptionId')?['properties']?['effectiveSubscriptionId'])`

      ![필터 배열 플레이 북 작업을 보여 주는 스크린샷](media/automate-playbook-watchlist/filter-array.png)

   1. **메시지를 사용자에 게 flow bot로 게시** 합니다. 이 단계에서는 새 경고에 대해 공유 하려는 세부 정보를 사용 하 여 Microsoft Teams의 구독 소유자에 게 메시지를 보냅니다.

      ![플레이 북 단계에서 Teams 메시지를 만드는 과정을 보여 주는 스크린샷](media/automate-playbook-watchlist/create-message.png)

   1. **전자 메일을 보냅니다**. 이 단계에서는 새 경고에 대해 공유 하려는 세부 정보를 사용 하 여 Outlook의 구독 소유자에 게 메시지를 보냅니다.

      ![플레이 북 단계에서 전자 메일 메시지를 만드는 과정을 보여 주는 스크린샷](media/automate-playbook-watchlist/create-email.png)

### <a name="set-up-your-watchlist-and-deploy-the-playbook"></a>관심 목록를 설정 하 고 플레이 북을 배포 합니다.

다음 단계를 사용 하 여 관심 목록를 만들고 업로드 하 고, 플레이 북를 배포한 후 API 연결을 확인 합니다.

**관심 목록을 만들고 업로드 하려면:**

1. **SubscriptionId**, **SubscriptionName**, **OwnerName**, **소유자 전자 메일** 열을 포함 하는 입력 쉼표로 구분 된 값 (CSV) 파일을 만듭니다. 여기서 각 행은 Azure 테 넌 트의 구독을 나타냅니다. 예를 들면 다음과 같습니다.

   ```csv
   SubscriptionId,SubscriptionName,OwnerName,OwnerEmail
   00000000-0000-0000-0000-000000000001,DemoSubscription1,Megan Bowen,mbowen@contoso.com
   00000000-0000-0000-0000-000000000002,DemoSubscription2,MOD Admin,MODadmin@contoso.com
   ```

1. Microsoft 센티널 **관심 목록** 영역에 테이블을 업로드 합니다. **관심 목록 별칭** 으로 사용 하는 값을 기록해 둡니다 .이 별칭을 사용 하 여 플레이 북에서이 관심 목록를 쿼리할 수 있습니다.

   자세한 내용은 [Microsoft 센티널 Watchlists 사용](watchlists.md)을 참조 하세요.

**플레이 북를 배포 하려면**:

1. Microsoft 센티널 **Automation** 페이지의 **플레이 북 Templates (미리 보기)** 탭에서 **Watchlists-InformSubowner-IncidentTrigger** 플레이 북를 검색 하 여 찾습니다.

1. 오른쪽 아래에서 **플레이 북 만들기** 를 선택 하 고 마법사를 사용 하 여 작업 영역에 플레이 북을 배포 합니다.

    플레이 북에 대 한 의미 있는 이름을 입력 하 고 API 연결 리소스의 이름을 결정 하는 **사용자 이름을** 입력 해야 합니다.

1. 배포가 완료 되 면 새 플레이 북를 선택 하 여 Logic Apps 디자이너에서 엽니다.

1. 논리 앱의 **개발 도구** 왼쪽에 있는 **API 연결** 을 선택 하 고 플레이 북의 각 제품에 대 한 연결을 선택 합니다.

    연결 되지 않은 모든 제품에 대해 **권한 부여**, 로그인을 차례로 선택 하 고 논리 앱을 저장 합니다.

## <a name="automate-incident-response-with-an-allowlist"></a>Allowlist를 사용 하 여 인시던트 응답 자동화

허용 목록은 특정 id 또는 소스가 중요 한 리소스에 액세스 하거나 보안 보호에서 제외할 수 있도록 하는 데 유용한 전략입니다. 예를 들어 allowlist의 특정 IP 주소가 새 경고를 트리거하는 경우 시스템은 allowlist를 사용 하 여 승인 된 원본으로 SOC에 알려진 것으로 인식할 수 있습니다. 경고에 해당 엔터티에 승인 된 IP 주소만 포함 된 경우 인시던트를 자동으로 닫아 시간을 절약할 수 있습니다.

이 섹션에서는 관심 목록 및 플레이 북를 사용 하 여 승인 된 IP 주소에 대 한 인시던트를 자동으로 종결 하는 방법에 대해 설명 합니다.

> [!TIP]
> 이 섹션에 설명 된 플레이 북에 더 많은 단계를 추가 하거나 거부 목록 시나리오의 비슷한 단계를 위해이를 수정할 수 있습니다.

### <a name="prerequisites"></a>전제 조건

이 섹션에서 설명 하는 프로세스를 사용 하려면 Microsoft 센티널 커넥터를 사용 하 여 Azure Logic Apps 하는 데 사용할 사용자 또는 등록 된 응용 프로그램이 [Microsoft 센티널 참가자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 역할을가지고 있어야 합니다.

### <a name="process-summary-and-playbook-steps"></a>프로세스 요약 및 플레이 북 단계

이 문서에서 설명 하는 프로세스에는 다음 단계가 포함 됩니다.

1. Microsoft 센티널 관심 목록는 승인 된 모든 IP 주소를 나열 합니다.

1. **관심 목록-CloseIncidentKnownIPs** 플레이 북는 결과 경고에 IP 주소를 연결 하는 분석 규칙에 연결 됩니다.

   1. 분석 규칙에 대 한 새 경고가 생성 될 때마다 플레이 북가 트리거되고 경고 세부 정보를 가져옵니다.

   1.  플레이 북는 수집 된 경고의 각 IP 주소에 대해 관심 목록를 쿼리하여 승인 되었는지 여부를 확인 합니다.

   1.  관심 목록에 있는 모든 IP 주소가 *금고* ip 배열에 추가 됩니다. 관심 목록에서 찾을 수 없는 IP 주소는 *not 금고* ip 배열에 추가 됩니다.

   1. 경고를 포함 하는 인시던트에 정보 주석을 추가 하 여 각 범주의 IP 주소에 대 한 세부 정보를 추가 합니다 (*금고* / *금고*).

1. 나열 된 모든 IP 주소를 안전 하 게 사용 하는 경우 인시던트는 **무해 한 긍정** 분류 이유로 종결 됩니다.


다음 이미지는 논리 앱 디자이너의 **Watchlists-CloseIncidentKnownIPs** 플레이 북을 보여 줍니다.

![Watchlists-CloseIncidentKnownIPs 플레이 북의 스크린샷](media/automate-playbook-watchlist/playbook-known-ips.png)

플레이 북는 다음 단계를 실행 합니다.

1. **Microsoft 센티널 경고에 대 한 응답이 트리거되면** 플레이 북는 경고를 입력으로 받습니다.

1. 다음을 포함 하 여 플레이 북에 사용 될 값을 저장할 **변수를 초기화** 합니다.

    - **관심 목록 name**: Log Analytics 쿼리에 사용 되는 변수 문자열입니다.
    - **금고/안전 하지 않은** ip: 변수 배열은 찾은 IP 주소를 저장 하는 데 사용 됩니다.

1. **엔터티-Ip 가져오기** 는 경고에 있는 모든 엔터티를 사용 하 고 IP 주소만 구문 분석 하 여 이후 작업에 대 한 동적 값으로 사용할 준비가 됩니다.

1. **각 Ip에 대해** 플레이 북는 경고에 있는 각 ip 주소를 반복 하 여 ip 주소가 watchlists 중 하나에 있는지 확인 하 고 관련 작업을 수행 합니다.

   이 단계에서 플레이 북는 Log Analytics에서 관심 목록 항목을 가져옵니다. 관심 목록가 있는 Microsoft 센티널 작업 영역에 대 한 구독, 리소스 그룹 및 리소스 이름을 입력 해야 합니다.

   :::image type="content" source="media/automate-playbook-watchlist/known-ip-run-query.png" alt-text="쿼리 실행 및 결과 나열 단계의 스크린샷":::

   플레이 북가 관심 목록에서 ip 주소를 찾으면 ip 주소가 *금고* 배열에 추가 됩니다. Allowlist 관심 목록에서 찾을 수 없는 경우 IP 주소가 *안전 하지 않은* 배열에 추가 됩니다.

1. 관심 목록에 있는 안전 IP 주소를 나열 하는 **의견이 인시던트에 추가 됩니다**.

1. **조건**. 플레이 북은 *안전 하지 않음* 목록에 IP 주소가 있는지 여부를 확인 합니다. *안전 하지 않은* 목록이 비어 있는 경우 플레이 북는 해당 인시던트를 닫고 분류 이유는 *무해* 합니다.

### <a name="set-up-your-watchlist-and-deploy-the-playbook"></a>관심 목록를 설정 하 고 플레이 북을 배포 합니다.

다음 단계를 사용 하 여 관심 목록를 만들고 업로드 하 고 플레이 북을 배포 합니다.

**관심 목록을 만들고 업로드 하려면:**

1. 쉼표로 구분 된 입력 (CSV) 파일을 만듭니다. 여기서 각 행은 IP 주소를 나타냅니다.

1. Microsoft 센티널 **관심 목록** 영역에 테이블을 업로드 합니다. **관심 목록 별칭** 으로 사용 하는 값을 기록해 둡니다 .이 별칭을 사용 하 여 플레이 북에서이 관심 목록를 쿼리할 수 있습니다.

   자세한 내용은 [Microsoft 센티널 Watchlists 사용](watchlists.md)을 참조 하세요.

**플레이 북를 배포 하려면**:

1. Microsoft 센티널 **Automation** 페이지의 **플레이 북 Templates (미리 보기)** 탭에서 **Watchlists-CloseIncidentKnownIPs** 플레이 북를 검색 하 여 찾습니다.

1. 오른쪽 아래에서 **플레이 북 만들기** 를 선택 하 고 마법사를 사용 하 여 작업 영역에 플레이 북을 배포 합니다.

    플레이 북에 대 한 의미 있는 이름을 입력 하 고 API 연결 리소스의 이름을 결정 하는 **사용자 이름을** 입력 해야 합니다.

1. 배포가 완료 되 면 새 플레이 북를 선택 하 여 Logic Apps 디자이너에서 엽니다.

1. 논리 앱의 **개발 도구** 왼쪽에 있는 **API 연결** 을 선택 하 고 플레이 북의 각 제품에 대 한 연결을 선택 합니다. 이 경우 확인에 대 한 유일한 연결은 Microsoft 센티널입니다.

   연결 되지 않은 모든 제품에 대해 **권한 부여**, 로그인을 차례로 선택 하 고 논리 앱을 저장 합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Sentinel Logic Apps 커넥터](/connectors/azuresentinel)
- [Microsoft Teams Logic Apps 커넥터](/connectors/teams/)
- [Office 365 Outlook Logic Apps 커넥터](/connectors/office365)
- [Microsoft 센티널의 경고에서 인시던트 만들기](create-incidents-from-alerts.md)

