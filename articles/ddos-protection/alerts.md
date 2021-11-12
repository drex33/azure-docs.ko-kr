---
title: Azure DDoS Protection 표준에 대한 DDoS 보호 경고 보기 및 구성
description: Azure DDoS Protection 표준에 대한 DDoS 보호 경고를 보고 구성하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 315f97e6c392578e10159c767c56111e6c34f544
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286936"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>DDoS 보호 경고 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. DDoS 공격 기간 동안 상세 메트릭을 비롯한 다양한 원격 분석이 Azure Monitor를 통해 노출됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. 로깅은 Azure Monitor Diagnostics 인터페이스를 통해 고급 분석을 위해 [Microsoft Sentinel,](../sentinel/data-connectors-reference.md#azure-ddos-protection)Splunk(Azure Event Hubs), OMS Log Analytics 및 Azure Storage 추가로 통합할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Monitor를 통해 경고 구성
> * 포털을 통해 경고 구성
> * 클라우드용 Microsoft Defender에서 경고 보기
> * 경고 유효성 검사 및 테스트

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료하려면 먼저 [Azure DDoS 표준 보호 계획](manage-ddos-protection.md)을 만들고 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정해야 합니다.
- DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service Environment를 제외하고 [Azure 서비스용 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)(백 엔드 가상 머신이 가상 네트워크에 있는 Azure Load Balancer 포함)에 나열된 Resource Manager(클래식 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.     

## <a name="configure-alerts-through-azure-monitor"></a>Azure Monitor를 통해 경고 구성

이러한 템플릿을 사용하여 진단 로깅을 사용하도록 설정한 모든 공용 IP 주소에 대한 경고를 구성할 수 있습니다. 따라서 이러한 경고 템플릿을 사용하려면 먼저 진단 설정을 사용하는 Log Analytics 작업 영역이 필요합니다. [DDoS 진단 로깅 보기 및 구성](diagnostic-logging.md)을 참조하세요.

### <a name="azure-monitor-alert-rule"></a>Azure Monitor 경고 규칙

이 [Azure Monitor 경고 규칙](https://aka.ms/DDOSmitigationstatus)은 활성 DDoS 완화를 검색하는 간단한 쿼리를 실행합니다. 이는 잠재적 공격을 나타냅니다. 작업 그룹은 경고의 결과로 작업을 호출하는 데 사용할 수 있습니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>논리 앱을 사용한 Azure Monitor 경고 규칙

이 [템플릿](https://aka.ms/ddosalert)은 보강된 DDoS 완화 경고의 필수 구성 요소(Azure Monitor 경고 규칙, 작업 그룹 및 논리 앱)를 배포합니다. 이 프로세스의 결과는 해당 IP와 연결된 리소스에 대한 정보 등 공격을 받는 IP 주소에 대한 세부 정보를 포함하는 이메일 경고입니다. 리소스 소유자는 보안 팀과 함께 이메일의 받는 사람으로 추가됩니다. 기본 애플리케이션 가용성 테스트도 수행되며 결과는 이메일 경고에 포함됩니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>포털을 통해 경고 구성

Azure Monitor 경고 구성을 사용하면 공격 중에 활성 완화가 있는 경우 알리는 데 사용할 수 있는 DDoS 보호 메트릭 중 하나를 선택할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 DDoS 보호 계획으로 이동합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. 회색 탐색 모음에서 **새 경고 규칙** 을 선택합니다. 
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력하고, 나머지 기본값을 적용한 다음, **경고 규칙 만들기** 를 선택합니다.

    |설정                  |값                                                                                               |
    |---------                |---------                                                                                           |
    | 범위                   | **리소스 선택** 을 선택합니다. </br> 로깅할 공용 IP 주소가 포함된 **구독** 을 선택하고 **리소스 종류** 로 **공용 IP 주소** 를 선택한 다음, 메트릭을 로깅할 공용 IP 주소를 선택합니다. </br> **완료** 를 선택합니다. | 
    | 조건 | **조건 선택** 을 선택합니다. </br> 신호 이름 아래에서 **DDoS 공격 진행 여부** 를 선택합니다. </br> **연산자** 아래에서 **크거나 같음** 을 선택합니다. </br> **집계 유형** 아래에서 **최대** 를 선택합니다. </br> **임계값** 아래에서 *1* 을 입력합니다. **DDoS 공격 진행 여부** 메트릭의 경우, **0** 은 공격을 받고 있지 않다는 의미이고 **1** 은 공격을 받고 있다는 의미입니다. </br> **완료** 를 선택합니다. | 
    | 동작 | **작업 그룹 추가** 를 선택합니다. </br> **작업 그룹 만들기** 를 선택합니다. </br> **알림** 아래의 **알림 유형** 에서 **이메일/SMS 메시지/푸시/음성** 을 선택합니다. </br> **이름** 아래에 _MyUnderAttackEmailAlert_ 를 입력합니다. </br> 편집 단추를 클릭하고 **이메일** 및 필요한 대로 다음 옵션을 선택한 다음 **확인** 을 선택합니다. </br> **검토 + 만들기** 를 선택합니다. | 
    | 경고 규칙 세부 정보 | **경고 규칙 이름** 아래에 _MyDdosAlert_ 를 입력합니다. |

공격 탐지 후 몇 분 내에 Azure Monitor 메트릭에서 다음 그림과 비슷한 이메일을 받게 됩니다.

![공격 경고](./media/manage-ddos-protection/ddos-alert.png)

경고를 만들기 위해 [웹후크 구성](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아볼 수도 있습니다.

## <a name="view-alerts-in-microsoft-defender-for-cloud"></a>클라우드용 Microsoft Defender에서 경고 보기

Microsoft Defender for Cloud는 문제를 조사하고 해결하는 데 도움이 되는 정보와 함께 [보안 경고](../security-center/security-center-managing-and-responding-alerts.md)목록을 제공합니다. 이 기능을 통해 DDoS 공격 관련 경고, 가까운 시간에 공격을 완화하기 위해 취한 조치를 포함한 경고의 통합 보기를 얻을 수 있습니다.
DDoS 공격 탐지 및 완화에 대해 다음과 같은 두 가지 경고가 표시됩니다.

- **공용 IP에 대한 DDoS 공격이 탐지됨**: 이 경고는 DDoS 보호 서비스에서 공용 IP 주소 중 하나가 DDoS 공격의 대상인 것을 탐지할 때 생성됩니다.
- **공용 IP에 대한 DDoS 공격이 완화됨**: 이 경고는 공용 IP 주소에 대한 공격이 완화된 경우에 생성됩니다.
경고를 보려면 Azure Portal **Defender for Cloud를** 엽니다. **위협 보호** 아래에서 **보안 경고** 를 선택합니다. 다음 스크린샷은 DDoS 공격 경고의 예를 보여줍니다.

![클라우드용 Microsoft Defender의 DDoS 경고](./media/manage-ddos-protection/ddos-alert-asc.png)

경고에는 공격을 받는 공용 IP 주소에 대한 일반 정보, 지역 및 위협 인텔리전스 정보 및 수정 단계가 포함됩니다.

## <a name="validate-and-test"></a>유효성 검사 및 테스트

DDoS 공격을 시뮬레이션하여 경고의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](test-through-simulations.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- Azure Monitor를 통해 경고 구성
- 포털을 통해 경고 구성
- 클라우드용 Microsoft Defender에서 경고 보기
- 경고 유효성 검사 및 테스트

DDoS 공격을 테스트하고 시뮬레이션하는 방법을 알아보려면 시뮬레이션 테스트 가이드를 참조하세요.

> [!div class="nextstepaction"]
> [시뮬레이션을 통해 테스트](test-through-simulations.md)
