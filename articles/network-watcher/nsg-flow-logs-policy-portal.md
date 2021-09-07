---
title: 빠른 시작 - Azure Policy를 사용하여 NSG 흐름 로그 배포 및 관리
titleSuffix: Azure Network Watcher
description: 이 문서에서는 기본 제공 정책을 사용하여 NSG 흐름 로그의 배포를 관리하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: a507f0845fad1e43ce775e5d46f9fa8a548e369f
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607477"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>빠른 시작: Azure Policy를 사용하여 NSG 흐름 로그 배포 및 관리 

## <a name="overview"></a>개요
Azure Policy를 사용하면 조직의 표준을 적용하고 규정 준수를 규모에 맞게 평가할 수 있습니다. Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다. 이 문서에서는 NSG 흐름 로그에 사용할 수 있는 두 가지 기본 제공 정책을 사용하여 흐름 로그 설정을 관리합니다. 첫 번째 정책은 흐름 로그가 없는 모든 NSG에 플래그를 지정합니다. 두 번째 정책은 흐름 로그가 없는 NSG에 흐름 로그를 자동으로 배포합니다. 

처음으로 Azure Policy 정의를 만드는 경우 다음 내용을 읽어 보면 됩니다. 
- [Azure Policy 개요](../governance/policy/overview.md) 
- [Azure Policy 할당을 만드는 방법에 대한 자습서](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>정책 찾기
1. Azure Portal([portal.azure.com](https://portal.azure.com))로 이동합니다. 

상단 검색 표시줄 ![Policy 홈페이지](./media/network-watcher-builtin-policy/1_policy-search.png)에서 정책을 검색하여 Azure Policy 페이지로 이동합니다.

2. 왼쪽 창에서 **할당** 탭으로 이동합니다.

![할당 탭](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. **정책 할당** 단추를 클릭합니다. 

![정책 할당 단추](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. “정책 정의”에서 세 개의 점 메뉴를 클릭하여 사용 가능한 정책을 확인합니다.

5. 유형 필터를 사용하고 “기본 제공”을 선택합니다. 그런 다음, “흐름 로그”를 검색합니다.

![정책 목록](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)에 흐름 로그에 대한 두 가지 기본 제공 정책이 표시됩니다.

6. 할당하려는 정책을 선택합니다.

- “모든 네트워크 보안 그룹에 로그 흐름이 구성되어야 함”은 준수하지 않는 NSG, 즉 흐름 로그를 사용하지 않는 NSG에 플래그를 지정하는 감사 정책입니다.
- “대상 네트워크 보안 그룹을 사용하여 흐름 로그 리소스 배포”는 흐름 로그가 없는 모든 NSG에 흐름 로그를 제공하는 배포 작업이 있는 정책입니다.

각 정책에 대한 별도의 지침은 다음과 같습니다.  

## <a name="audit-policy"></a>감사 정책 

### <a name="how-the-policy-works"></a>정책 작동 방식

정책은 “Microsoft.Network/networkSecurityGroups” 형식의 모든 기존 ARM 개체를 확인합니다. 즉, 지정된 범위의 모든 NSG를 확인하고 NSG의 흐름 로그 속성을 통해 연결된 흐름 로그가 있는지 확인합니다. 속성이 없으면 해당 NSG에 플래그가 지정됩니다.

정책의 전체 정의를 보려면 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)을 방문해 “흐름 로그”를 검색하여 정책을 찾을 수 있습니다.

### <a name="assignment"></a>할당

1. 정책 세부 정보 입력

- 범위: 구독이 일반적인 옵션이며 여러분과 관련된 관리 그룹 또는 리소스 그룹을 선택할 수도 있습니다.  
- 정책 정의: “정책 찾기” 섹션에 나온 대로 선택해야 합니다.
- 할당 이름: 설명이 포함된 이름을 선택합니다. 

2. “검토 + 만들기”를 클릭하여 할당을 검토합니다.

정책에는 매개 변수가 필요하지 않습니다. 감사 정책을 할당할 때 “수정” 탭에서 세부 정보를 입력할 필요가 없습니다.  

![감사 정책 검토](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>결과

결과를 확인하려면 준수 탭을 열고 할당 이름을 검색합니다.
정책이 실행되면 다음 스크린샷과 비슷한 내용이 표시됩니다. 정책이 실행되지 않는 경우 일정 시간 동안 기다립니다. 

![감사 정책 결과](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>없는 경우 배포하는 정책 

### <a name="policy-structure"></a>정책 구조

정책은 “Microsoft.Network/networkSecurityGroups” 형식의 모든 기존 ARM 개체를 확인합니다. 즉, 지정된 범위의 모든 NSG를 확인하고 NSG의 흐름 로그 속성을 통해 연결된 흐름 로그가 있는지 확인합니다. 속성이 없으면 정책에 따라 흐름 로그를 배포합니다. 

정책의 전체 정의를 보려면 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)을 방문해 “흐름 로그”를 검색하여 정책을 찾을 수 있습니다. 

### <a name="assignment"></a>할당

1. 정책 세부 정보 입력

- 범위: 구독이 일반적인 옵션이며 여러분과 관련된 관리 그룹 또는 리소스 그룹을 선택할 수도 있습니다.  
- 정책 정의: “정책 찾기” 섹션에 나온 대로 선택해야 합니다.
- 할당 이름: 설명이 포함된 이름을 선택합니다. 

2. 정책 매개 변수 추가 

Network Watcher 서비스는 지역별 서비스입니다. 관련 매개 변수를 사용하면 흐름 로그를 배포하는 정책 작업을 실행할 수 있습니다. 
- NSG 지역: 정책을 대상으로 하는 Azure 지역
- 스토리지 ID: 스토리지 계정의 전체 리소스 ID입니다. 참고: 이 스토리지 계정은 NSG와 동일한 영역에 있어야 합니다. 
- Network Watcher RG: Network Watcher 리소스를 포함하는 리소스 그룹의 이름입니다. 이름을 변경하지 않은 경우 기본값인 ‘NetworkWatcherRG’를 입력하면 됩니다.
- Network Watcher 이름: 지역별 Network Watcher 서비스의 이름입니다. 형식: NetworkWatcher_RegionName. 예: NetworkWatcher_centralus. 전체 목록을 참조하세요.

![DINE 정책 매개 변수](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 수정 세부 정보 추가

- 정책이 기존 리소스에 영향을 주려면 “수정 작업 만들기” 표시를 선택합니다. 
- “관리 ID 만들기”를 미리 선택해야 합니다.
- 관리 ID에 대해 이전과 동일한 위치를 선택합니다. 
- 이 정책을 사용하려면 기여자 또는 소유자 권한이 있어야 합니다. 이러한 권한이 있는 경우 오류가 표시되지 않습니다.

![DINE 정책 수정](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. “검토 + 만들기”를 클릭하여 할당을 검토합니다. 다음 스크린샷과 비슷한 내용이 표시되어야 합니다.

![DINE 정책 검토](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>결과

결과를 확인하려면 준수 탭을 열고 할당 이름을 검색합니다.
정책에 따라 다음과 같은 스크린샷이 표시됩니다. 정책이 실행되지 않는 경우 일정 시간 동안 기다립니다.

![DINE 정책 결과](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>다음 단계 

-   [트래픽 분석 기본 제공 정책](./traffic-analytics-policy-portal.md)에 대해 알아보기
-   이 [자습서](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md)에서는 ARM 템플릿을 사용하여 흐름 로그와 트래픽 분석을 배포하는 방법을 자세히 설명합니다.
-   [Network Watcher](./index.yml)에 대해 자세히 알아보기