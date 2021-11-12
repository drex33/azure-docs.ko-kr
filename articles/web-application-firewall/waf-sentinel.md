---
title: Azure Web Application Firewall Microsoft Sentinel 사용
description: 이 문서에서는 WAF(Azure Web Application Firewall)에서 Microsoft Sentinel을 사용하는 방법을 보여줍니다.
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c888b364bfac5687996bb88472b5725244ea1bcb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305468"
---
# <a name="using-microsoft-sentinel-with-azure-web-application-firewall"></a>Azure Web Application Firewall Microsoft Sentinel 사용

Microsoft Sentinel과 결합된 WAF(Azure Web Application Firewall)는 WAF 리소스에 대한 보안 정보 이벤트 관리를 제공할 수 있습니다. Microsoft Sentinel은 LOG Analytics를 사용하여 보안 분석을 제공하며, 이를 통해 WAF 데이터를 쉽게 분석하고 볼 수 있습니다. Microsoft Sentinel을 사용하여 미리 빌드된 통합 문서에 액세스하고 조직의 요구에 가장 잘 맞게 수정할 수 있습니다. 통합 문서는 Azure CDN(Content Delivery Network)의 WAF, Azure Front Door의 WAF, 그리고 여러 구독 및 작업 영역에 걸친 Application Gateway의 WAF에 대한 분석을 표시할 수 있습니다.

## <a name="waf-log-analytics-categories"></a>WAF 로그 분석 범주

WAF 로그 분석은 다음과 같은 범주로 분류됩니다.  

- 수행된 모든 WAF 작업 
- 상위 40개 차단된 요청 URI 주소 
- 상위 50개 이벤트 트리거  
- 시간당 메시지 
- 전체 메시지 정보 
- 메시지의 공격 이벤트  
- 시간당 공격 이벤트 
- 추적 ID 필터 
- 추적 ID 메시지 
- 상위 10개 공격 IP 주소 
- IP 주소의 메시지 공격 

## <a name="waf-workbook-examples"></a>WAF 통합 문서 예제

다음 WAF 통합 문서 예제는 샘플 데이터를 보여 줍니다.

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF 작업 필터":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="상위 50개 이벤트":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="공격 이벤트":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="상위 10개 공격 IP 주소":::

## <a name="launch-a-waf-workbook"></a>WAF 통합 문서 시작

WAF 통합 문서는 모든 Azure Front Door, Application Gateway, CDN WAF에서 작동합니다. 이러한 리소스에서 데이터를 연결하기 전에 리소스에서 로그 분석을 사용하도록 설정해야 합니다. 

각 리소스에 대해 로그 분석을 사용하도록 설정하려면 개별 Azure Front Door, Application Gateway 또는 CDN 리소스로 이동합니다.

1. **진단 설정** 을 선택합니다.
2. **+ 진단 설정 추가** 를 선택합니다. 
3. 진단 설정 페이지에서 다음을 수행합니다.
   1. 이름을 입력합니다. 
   1. **Log Analytics에 보내기** 를 선택합니다. 
   1. 로그 대상 작업 영역을 선택합니다. 
   1. 분석할 로그 유형을 선택합니다.
      1. Application Gateway: ‘ApplicationGatewayAccessLog’와 ‘ApplicationGatewayFirewallLog’
      1. Azure Front Door: ‘FrontDoorAccessLog’와 ‘FrontDoorFirewallLog’
      1. CDN: ‘AzureCdnAccessLog’
   1. **저장** 을 선택합니다.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="진단 설정":::

4. Azure 홈페이지의 검색 창에 *Microsoft Sentinel을* 입력하고 **Microsoft Sentinel** 리소스를 선택합니다. 
2. 이미 활성화된 작업 영역을 선택하거나 새 작업 영역을 만듭니다. 
3. **구성** 아래의 왼쪽 패널에서 **데이터 커넥터** 를 선택합니다.
4. **Microsoft 웹 애플리케이션 방화벽** 을 검색하고 **Microsoft WAF**(웹 애플리케이션 방화벽)를 선택합니다. 오른쪽 아래에서 **커넥터 페이지 열기** 를 선택합니다.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="데이터 커넥터":::

8. 이전에 수행하지 않은 경우 로그 분석 데이터를 포함하려는 각 WAF 리소스에 대한 **구성** 의 지침을 따릅니다.
6. 개별 WAF 리소스 구성이 완료되면 **다음 단계** 탭을 선택합니다. 권장되는 통합 문서 중 하나를 선택합니다. 이 통합 문서는 이전에 사용하도록 설정된 모든 로그 분석 데이터를 사용합니다. 이제 WAF 리소스에 작동하는 WAF 통합 문서가 있어야 합니다.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF 통합 문서":::


## <a name="next-steps"></a>다음 단계

- [Microsoft Sentinel에 대한 자세한 정보](../sentinel/overview.md)
- [Azure Monitor 통합 문서에 대해 알아봅니다](../azure-monitor/visualize/workbooks-overview.md)
