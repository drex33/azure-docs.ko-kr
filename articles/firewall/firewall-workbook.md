---
title: Azure Firewall 통합 문서를 사용하여 로그 모니터링
description: Azure Firewall 통합 문서는 Azure Firewall이 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 11/01/2021
ms.author: victorh
ms.openlocfilehash: 207097258a78d79d77e56052fc254065ca8a293c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457628"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Azure Firewall 통합 문서를 사용하여 로그 모니터링

Azure Firewall 통합 문서는 Azure Firewall 데이터를 분석할 수 있도록 유연한 캔버스를 제공합니다. 이를 사용하여 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있습니다. Azure에 걸쳐 배포된 여러 방화벽을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다.

Azure Firewall 이벤트에 대한 인사이트를 얻고, 애플리케이션 및 네트워크 규칙에 관해 알아보고, URL, 포트 및 주소에서 방화벽 활동의 통계를 볼 수 있습니다. Azure Firewall 통합 문서를 사용하면 방화벽과 리소스 그룹을 필터링하고, 로그에서 문제를 조사할 때 읽기 쉬운 데이터 세트로 범주별로 동적으로 필터링할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 Azure Portal을 통해 [진단 로깅을 사용하도록 설정](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal)해야 합니다. 또한 [Azure Firewall 로그 및 메트릭](logs-and-metrics.md)에서 Azure Firewall에 사용할 수 있는 진단 로그와 메트릭의 개요를 읽어봅니다.

## <a name="get-started"></a>시작

통합 문서를 배포하려면 [Azure Firewall용 Azure Monitor 통합 문서](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook)로 이동하고 페이지의 지침을 따릅니다. Azure Firewall 통합 문서는 다중 테넌트, 다중 구독에서 작동하도록 디자인되며 여러 방화벽으로 필터링할 수 있습니다.

## <a name="overview-page"></a>개요 페이지

개요 페이지에서는 작업 영역, 시간 및 방화벽에서 필터링하는 방법을 제공합니다. 방화벽과 로그 유형(애플리케이션, 네트워크, 위협 인텔리전스, DNS 프록시)에서 시간별 이벤트를 보여 줍니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure Firewall 통합 문서 개요":::

## <a name="application-rule-log-statistics"></a>애플리케이션 규칙 로그 통계

이 페이지는 시간이 지남에 따른 IP 주소의 고유한 원본, 애플리케이션 규칙 수 사용량, 시간이 지남에 따른 거부된/허용된 FQDN, 필터링된 데이터 등이 표시됩니다. IP 주소를 기준으로 데이터를 필터링할 수 있습니다. 

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure Firewall 통합 문서 애플리케이션 규칙 로그":::

웹 범주 보기에는 방화벽 관리자가 구성한 심각도에 따라 모든 허용 및 거부 액세스 로그 작업이 요약되어 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-webcategory.png" alt-text="Azure Firewall 웹 범주 요약":::

## <a name="network-rule-log-statistics"></a>네트워크 규칙 로그 통계

이 페이지는 규칙 작업(허용/거부, 시간이 지남에 따른 IP 및 DNAT별 대상 포트)별 보기를 제공합니다. 작업, 포트 및 대상 유형별로 필터링할 수도 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure Firewall 통합 문서 네트워크 규칙 로그":::

시간 범위를 기준으로 로그를 필터링할 수도 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure Firewall 통합 문서 네트워크 규칙 로그 시간 범위":::

## <a name="idps-log-statistics"></a>IDPS 로그 통계

이 페이지에서는 IDPS 규칙과 일치하는 모든 트래픽에 대한 IDPS 작업 수에 대한 개요를 제공합니다. 프로토콜, 서명 ID, 원본 IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-idps.png" alt-text="통합 문서 idps 로그 Azure Firewall":::

## <a name="investigations"></a>조사

로그를 확인하고 원본 IP 주소를 기준으로 리소스에 관해 자세히 알아볼 수 있습니다. 가상 머신 이름, 네트워크 인터페이스 이름과 같은 정보를 얻을 수 있습니다. 로그에서 리소스로 필터링하는 것은 간단합니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure Firewall 통합 문서 조사":::


## <a name="next-steps"></a>다음 단계

- [Azure Firewall 진단](firewall-diagnostics.md)에 관해 자세히 알아봅니다.
