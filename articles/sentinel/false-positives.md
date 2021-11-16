---
title: Microsoft Sentinel에서 가양성 처리
description: 자동화 규칙을 만들거나 분석 규칙을 수정하여 예외를 지정하여 Microsoft Sentinel에서 가양성 문제를 해결하는 방법을 알아봅니다.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 009cb9d66f4294bc72bafdcf515a68131d9b7e38
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521162"
---
# <a name="handle-false-positives-in-microsoft-sentinel"></a>Microsoft Sentinel에서 가양성 처리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Microsoft Sentinel 분석 규칙은](detect-threats-built-in.md) 네트워크에서 의심스러운 문제가 발생하면 사용자에게 알립니다. 완벽한 분석 규칙은 없으며 처리가 필요한 가양성 알림을 받을 수밖에 없습니다. 이 문서에서는 자동화를 사용하거나 예약된 분석 규칙을 수정하여 가양성을 처리하는 방법을 설명합니다.

## <a name="false-positive-causes-and-prevention"></a>가양성 원인 및 방지

올바르게 작성된 분석 규칙에서도 가양성은 규칙에서 제외해야 하는 특정 엔터티(예: 사용자 또는 IP 주소)에서 비롯되는 경우가 많습니다.

일반적인 시나리오는 다음과 같습니다.

- 특정 사용자(대개 서비스 주체)의 정상적인 활동이 의심스러운 패턴을 보입니다.
- 알려진 IP 주소에서 들어오는 의도적인 보안 검색 활동이 악성으로 탐지됩니다.
- 개인 IP 주소를 제외하는 규칙이 개인 IP 주소가 아닌 내부 IP 주소도 제외합니다.

이 문서에서는 가양성을 방지하는 두 가지 방법을 설명합니다.

- **자동화 규칙** 은 분석 규칙을 수정하지 않고 예외를 생성합니다.
- **예약된 분석 규칙 수정** 을 통해 보다 상세하고 영구적인 예외를 허용합니다.

각 방법의 특성이 다음 표에 설명되어 있습니다.


|메서드|특성|
|-|-|
|**자동화 규칙**|<ul><li>여러 분석 규칙에 적용할 수 있습니다.</li><li>감사 내역을 유지합니다. 예외가 발생하면 인시던트가 생성되지 않지만 경고는 감사 목적으로 계속 기록됩니다.</li><li>분석가에 의해 생성되는 경우가 많습니다.</li><li>제한된 시간 동안 예외 적용을 허용합니다. 예를 들어, 유지 관리 작업으로 인해 가양성(유지 관리 기간이 아니라면 실제 인시던트일 수 있음)이 발생할 수 있습니다.</li></ul>|
|**분석 규칙 수정**|<ul><li>고급 부울 식 및 서브넷 기반 예외를 허용합니다.</li><li>관심 목록을 사용하여 예외 관리를 중앙 집중화할 수 있습니다.</li><li>일반적으로 SOC(보안 운영 센터) 엔지니어가 구현해야 합니다.</li><li>가장 유연하고 완전한 가양성 솔루션이지만 더 복잡합니다.</li></ul>|

## <a name="add-exceptions-by-using-automation-rules"></a>자동화 규칙을 사용하여 예외 추가

예외를 추가하는 가장 간단한 방법은 가양성 인시던트가 보이면 [자동화 규칙을 추가](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules)하는 것입니다.

가양성을 처리하는 자동화 규칙을 추가하려면:

1. Microsoft Sentinel의 **인시던트** 에서 예외를 만들 인시던트 를 선택합니다.
1. **자동화 규칙 만들기** 를 선택합니다.
1. **새 자동화 규칙 만들기** 사이드바에서 필요에 따라 예외를 식별하도록 새 규칙 이름을(경고 규칙 이름이 아닌) 수정합니다.
1. **조건** 에서 필요에 따라 예외를 적용할 **분석 규칙 이름** 을 더 추가합니다.
1. 사이드바에 가양성을 유발할 수 있는 현재 인시던트의 특정 엔터티가 표시됩니다. 자동 제안을 유지하거나 예외를 미세 조정하도록 수정합니다. 예를 들어 IP 주소의 조건을 변경하여 전체 서브넷에 적용할 수 있습니다.

   :::image type="content" source="media/false-positives/create-rule.png" alt-text="Microsoft Sentinel에서 인시던트 자동화 규칙을 만드는 방법을 보여주는 스크린샷":::

1. 트리거를 정의한 후에는 규칙이 수행하는 작업을 계속 정의할 수 있습니다.

   :::image type="content" source="media/false-positives/apply-rule.png" alt-text="Microsoft Sentinel에서 자동화 규칙 만들기 및 적용을 완료하는 방법을 보여주는 스크린샷":::

   - 예외 조건을 충족하는 인시던트를 닫도록 규칙이 이미 구성되어 있습니다.
   - 자동으로 닫힌 인시던트에 예외를 설명하는 주석을 추가할 수 있습니다. 예를 들어 알려진 관리 활동에서 인시던트가 발생했다고 명시할 수 있습니다.
   - 기본적으로 규칙은 24시간 후에 자동으로 만료되도록 설정됩니다. 이 만료 시간은 사용자가 원하는 대로 지정할 수 있으며, 거짓 부정 오류가 발생할 가능성을 줄여줍니다. 예외를 더 길게 설정하려면 **규칙 만료** 를 나중으로 설정합니다.

1. **적용** 을 선택하여 예외를 활성화합니다.

> [!TIP]
> 자동화 규칙을 인시던트에서 시작하지 않고 처음부터 새로 만들 수도 있습니다. Microsoft Sentinel 왼쪽 탐색 메뉴에서 **Automation을** 선택한 다음, 새 규칙 **추가 만들기를**  >  선택합니다.

## <a name="add-exceptions-by-modifying-analytics-rules"></a>분석 규칙을 수정하여 예외 추가

예외를 구현하는 또 다른 옵션은 분석 규칙 쿼리를 수정하는 것입니다. 규칙에 직접 예외를 포함하거나 가능한 경우 [관심 목록](watchlists.md#use-watchlists-in-analytics-rules)에 대한 참조를 사용하는 것이 좋습니다. 그런 다음, 관심 목록에서 예외 목록을 관리할 수 있습니다.

### <a name="modify-the-query"></a>쿼리 수정

기존 분석 규칙을 편집하려면 Microsoft Sentinel 왼쪽 탐색 메뉴에서 **Automation을** 선택합니다. 편집할 규칙을 선택한 다음, 오른쪽 아래에서 **편집** 을 선택하여 **분석 규칙 마법사** 를 엽니다.

**분석 규칙 마법사** 를 사용하여 분석 규칙을 만들고 편집하는 방법에 대한 자세한 지침은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.

일반적인 규칙 프리앰블에서 예외를 구현하려면 규칙 쿼리의 시작 부분 근처에 `where IPAddress !in ('<ip addresses>')`과 같은 조건을 추가할 수 있습니다. 이 줄은 규칙에서 특정 IP 주소를 제외합니다.

```kusto
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in ('10.0.0.8', '192.168.12.1')
...
```

이런 유형의 예외는 IP 주소에 국한되지 않습니다. `UserPrincipalName` 필드를 사용하여 특정 사용자를 제외하거나 `AppDisplayName`을 사용하여 특정 앱을 제외할 수 있습니다.

여러 특성을 제외할 수도 있습니다. 예를 들어 IP 주소 `10.0.0.8` 또는 사용자 `user@microsoft.com`의 경고를 제외하려면 다음을 사용합니다.

```kusto
| where IPAddress !in ('10.0.0.8')
| where UserPrincipalName != 'user@microsoft.com'
```

해당하는 경우 더 세분화된 예외를 구현하고 거짓 부정이 발생할 가능성을 줄이기 위해 특성을 조합할 수 있습니다. 다음 예외는 두 값이 동일한 경고에 나타나는 경우에만 적용됩니다.

```kusto
| where IPAddress != '10.0.0.8' and UserPrincipalName != 'user@microsoft.com'
```

### <a name="exclude-subnets"></a>서브넷 제외

조직에서 사용하는 IP 범위를 제외하려면 서브넷 제외가 필요합니다. 다음 예는 서브넷을 제외하는 방법을 보여줍니다.

`ipv4_lookup` 연산자는 필터링 연산자가 아닌 보강 연산자입니다. `where isempty(network)` 줄은 실제로 일치가 보이지 않는 이벤트를 검사하여 필터링을 수행합니다.

```kusto
let subnets = datatable(network:string) [ "111.68.128.0/17", "5.8.0.0/19", ...];
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| evaluate ipv4_lookup(subnets, IPAddress, network, return_unmatched = true)
| where isempty(network)
...
```

### <a name="use-watchlists-to-manage-exceptions"></a>관심 목록을 사용하여 예외 관리

관심 목록을 사용하여 규칙 자체 외부의 예외 목록을 관리할 수 있습니다. 해당하는 경우 이 솔루션에는 다음과 같은 이점이 있습니다.

- 분석가는 규칙을 편집하지 않고 예외를 추가할 수 있습니다. 이것이 SOC 모범 사례에 더 가깝습니다.
- 동일한 관심 목록을 여러 규칙에 적용할 수 있으므로 중앙 예외 관리가 가능합니다.

관심 목록을 사용하는 것은 직접 예외를 사용하는 것과 비슷합니다. 관심 목록을 호출하려면 `_GetWatchlist('<watchlist name>')`를 사용합니다.

```kusto
let timeFrame = 1d;
let logonDiff = 10m;
let allowlist = (_GetWatchlist('ipallowlist') | project IPAddress);
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in (allowlist)
...
```

관심 목록을 사용하여 서브넷 필터링을 수행할 수도 있습니다. 예를 들어 앞의 서브넷 제외 코드에서 서브넷 `datatable` 정의를 관심 목록으로 바꿀 수 있습니다.

```kusto
let subnets = _GetWatchlist('subnetallowlist');
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.
- [UEBA 데이터를 사용하여 가양성 분석](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [자동화 규칙을 사용하여 Microsoft Sentinel에서 인시던트 처리 자동화](automate-incident-handling-with-automation-rules.md)
- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
- [Microsoft Sentinel 감시 목록 사용](watchlists.md)
