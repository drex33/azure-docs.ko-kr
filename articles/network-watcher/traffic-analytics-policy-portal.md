---
title: Azure Policy를 사용하여 Traffic Analytics 배포 및 관리
titleSuffix: Azure Network Watcher
description: 이 문서에서는 기본 제공 정책을 사용하여 Traffic Analytics의 배포를 관리하는 방법을 설명합니다.
services: network-watcher
author: moagra
ms.service: network-watcher
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: 9d916122714b1dddab7d1d9b6d13e6fd58d14d46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619366"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>Azure Policy를 사용하여 Traffic Analytics 배포 및 관리 

Azure Policy를 사용하면 조직의 표준을 적용하고 규정 준수를 규모에 맞게 평가할 수 있습니다. Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다. 이 문서에서는 [Traffic Analytics](./traffic-analytics.md)에 사용할 수 있는 세 가지 기본 제공 정책을 사용하여 설정을 관리합니다.

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

5. 유형 필터를 사용하고 “기본 제공”을 선택합니다. 그런 다음, "Traffic Analytics"를 검색합니다.

세 가지 기본 제공 정책을 포함하는 ![Traffic Analytics 정책 목록](./media/traffic-analytics/policy-filtered-view.png)이 표시됩니다.

6. 할당하려는 정책을 선택합니다.

- "Network Watcher 흐름 로그에서 Traffic Analytics를 사용하도록 설정해야 함"은 Traffic Analytics를 사용하도록 설정하지 않은 플래그 로그에 해당하는 비준수 플래그 로그에 플래그를 지정하는 감사 정책입니다.
-  "Traffic Analytics에 특정 작업 영역을 사용하도록 네트워크 보안 그룹 구성" 및 "Traffic Analytics를 사용하도록 네트워크 보안 그룹 구성"은 배포 작업에 대한 정책입니다. 모든 NSG에 대한 Traffic Analytics를 사용하도록 설정하여 설정된 정책에 따라 이미 구성된 설정을 덮어쓰거나 덮어쓰지 않도록 합니다.

각 정책에 대한 별도의 지침은 다음과 같습니다.  

## <a name="audit-policy"></a>감사 정책 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>Network Watcher 흐름 로그에 트래픽 분석을 사용할 수 있어야 함

정책은 "Microsoft.Network/networkWatchers/flowLogs" 유형의 기존 Azure Resource Manager 개체를 모두 감사하고 흐름 로그 리소스의 "networkWatcherFlowAnalyticsConfiguration.enabled" 속성을 통해 Traffic Analytics가 사용하도록 설정되어 있는지 확인합니다. 속성이 false로 설정된 흐름 로그 리소스에 플래그를 지정합니다.

정책의 전체 정의를 보려면 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)을 방문하여 “Traffic Analytics”를 검색하여 정책을 찾을 수 있습니다.

### <a name="assignment"></a>할당

1. 정책 세부 정보 입력

- 범위: 구독 또는 리소스 그룹일 수 있습니다. 리소스 그룹인 경우에는 네트워크 보안 그룹이 아닌 흐름 로그 리소스를 포함하는 리소스 그룹을 선택합니다.
- 정책 정의: “정책 찾기” 섹션에 나온 대로 선택해야 합니다.
- 할당 이름: 설명이 포함된 이름을 선택합니다. 

2. “검토 + 만들기”를 클릭하여 할당을 검토합니다.

정책에는 매개 변수가 필요하지 않습니다. 감사 정책을 할당할 때 “수정” 탭에서 세부 정보를 입력할 필요가 없습니다.  

![감사 정책 검토 Traffic Analytics](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>결과

결과를 확인하려면 준수 탭을 열고 할당 이름을 검색합니다.
정책이 실행되면 다음 스크린샷과 비슷한 내용이 표시됩니다. 정책이 실행되지 않는 경우 일정 시간 동안 기다립니다. 

![감사 정책 결과 Traffic Analytics](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>없는 경우 배포하는 정책 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>트래픽 분석에 특정 작업 영역을 사용하도록 네트워크 보안 그룹 구성 

Traffic Analytics를 사용하도록 설정되지 않은 NSG에 플래그가 지정됩니다. 플래그가 지정된 NSG의 경우 해당 흐름 로그 리소스가 존재하지 않거나 흐름 로그 리소스가 존재하지만 Traffic Analytics가 사용하도록 설정되지 않았음을 의미합니다. 정책을 기존 리소스에 적용하려면 수정 작업을 만듭니다.
Network Watcher는 지역 서비스이므로 이 정책은 선택한 범위의 특정 지역에 속하는 NSG에만 적용됩니다. 다른 지역에 대해서는 다른 정책 할당을 만듭니다.
 
정책을 할당하는 동안이나 정책이 할당되고 평가된 후에 수정을 할당할 수 있습니다. 수정 작업을 수행하면 제공된 매개 변수를 사용하여 플래그가 지정된 모든 리소스에 대해 Traffic Analytics를 사용하도록 설정됩니다. NSG의 특정 스토리지 ID에 흐름 로그를 사용하도록 설정되어 있지만 Traffic Analytics이 사용하도록 설정되어 있지 않은 경우 수정 작업을 수행하면 제공된 매개 변수로 이 NSG에 대해 Traffic Analytics가 사용하도록 설정됩니다. 플래그가 지정된 NSG의 경우 매개 변수에 제공된 스토리지 ID가 흐름 로그에 대해 이미 사용하도록 설정된 것과 다른 경우, 흐름 로그의 ID가 수정 작업에서 제공된 스토리지 ID로 덮어쓰여집니다. 덮어쓰지 않으려면 아래에 설명된 "Traffic Analytics를 사용하도록 네트워크 보안 그룹 구성" 정책을 사용합니다.

정책의 전체 정의를 보려면 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)을 방문해 “Traffic Analytics”를 검색하여 정책을 찾을 수 있습니다. 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>Traffic Analytics를 사용하도록 네트워크 보안 그룹 구성

수정을 수행하는 동안을 제외하고는 위 정책과 동일하며, 플래그 로그가 사용하도록 설정되어 있지만 Traffic Analytics는 사용하도록 설정되지 않은 플래그가 지정된 NSG에 대한 흐름 로그 설정을 정책 할당에 제공된 매개 변수로 덮어쓰지 않습니다.

### <a name="assignment"></a>할당

1. 정책 세부 정보 입력

- 범위: 구독 또는 리소스 그룹일 수 있습니다.  
- 정책 정의: “정책 찾기” 섹션에 나온 대로 선택해야 합니다.
- 할당 이름: 설명이 포함된 이름을 선택합니다. 

2. 정책 매개 변수 추가 

- NSG 지역: 정책을 대상으로 하는 Azure 지역
- 스토리지 ID: 스토리지 계정의 전체 리소스 ID입니다. 이 스토리지 계정은 NSG와 동일한 영역에 있어야 합니다.
- Network Watcher RG: Network Watcher 리소스를 포함하는 리소스 그룹의 이름입니다. 이름을 변경하지 않은 경우 기본값인 ‘NetworkWatcherRG’를 입력하면 됩니다.
- Network Watcher 이름: 지역별 Network Watcher 서비스의 이름입니다. 형식: NetworkWatcher_RegionName. 예: NetworkWatcher_centralus.
- 작업 영역 리소스 ID: Traffic Analytics을 사용하도록 설정해야 하는 작업 영역의 리소스 ID입니다. 형식 `/subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>`
- WorkspaceID: 작업 영역 GUID
- WorkspaceRegion: 작업 영역의 지역(NSG의 지역과 같을 필요는 없음)
- TimeInterval: 처리된 로그가 작업 영역으로 푸시되는 빈도입니다. 현재 허용되는 값은 60분~10분입니다. 기본값은 60분입니다.
- Effect: DeployIfNotExists(이미 할당된 값)

3. 수정 세부 정보 추가

- 정책을 기존 리소스에 적용하려면 “수정 작업 만들기” 표시를 선택합니다.
- “관리 ID 만들기”를 미리 선택해야 합니다.
- 관리 ID에 대해 이전과 동일한 위치를 선택합니다.
- 이 정책을 사용하려면 기여자 또는 소유자 권한이 있어야 합니다. 이러한 권한이 있는 경우 오류가 표시되지 않습니다.

4. “검토 + 만들기”를 클릭하여 할당을 검토합니다. 다음 스크린샷과 비슷한 내용이 표시되어야 합니다.

![DINE 정책 검토 Traffic Analytics](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>결과

결과를 확인하려면 준수 탭을 열고 할당 이름을 검색합니다.
정책에 따라 다음과 같은 스크린샷이 표시됩니다. 정책이 실행되지 않는 경우 일정 시간 동안 기다립니다.

![DINE 정책 결과 Traffic Analytics](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>수정

수동으로 수정하려면 위에 표시된 규정 준수 탭에서 "수정 작업 만들기"를 선택합니다.

![DINE 정책 수정 Traffic Analytics](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>문제 해결

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>"PolicyAuthorizationFailed" 오류 코드를 나타내며 수정이 실패했습니다.

샘플 오류 예제 "정책 할당 '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' 리소스 ID에 배포를 만드는 데 필요한 권한이 없습니다."

이러한 시나리오에서는 할당의 관리 ID에 수동으로 액세스 권한을 부여해야 합니다. 적절한 구독/리소스 그룹(정책 매개 변수에 제공된 리소스 포함)으로 이동한 후 기여자에게 정책에 의해 생성된 관리 ID에 대한 액세스 권한을 부여합니다. 위의 예제에서 "b67334e8770a4afc92e7a929"는 기여자로 지정해야 합니다.


## <a name="next-steps"></a>다음 단계 

-   [NSG 흐름 로그 기본 제공 정책](./nsg-flow-logs-policy-portal.md)에 대해 알아보기
-   [Traffic Analytics](./traffic-analytics.md)에 대해 알아보기
-   [Network Watcher](./index.yml)에 대해 자세히 알아보기
