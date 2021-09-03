---
title: 성능 문제 해결
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 인스턴스의 성능 문제 해결을 위한 팁입니다.
author: baanders
ms.author: baanders
ms.date: 5/11/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 252eeea15e5c9ebde19c5925423615cd7c50234c
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493102"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>Azure Digital Twins 문제 해결: 성능

Azure Digital Twins로 작업할 때 지연 또는 기타 성능 문제가 발생하는 경우 이 문서의 팁을 사용하여 문제를 해결하세요.

## <a name="isolate-the-source-of-the-delay"></a>지연 원인 격리

Azure Digital Twins 또는 솔루션의 다른 서비스에서 지연이 발생하는지 여부를 확인합니다. 이 지연을 조사하려면 Azure Portal을 통해 [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md)에서 **API 대기 시간** 메트릭을 사용하면 됩니다. Azure Digital Twins 인스턴스에 대한 Azure Monitor 메트릭을 보는 방법에 대한 지침은 [문제 해결: Azure Monitor를 사용하여 메트릭 보기](troubleshoot-metrics.md)를 참조하세요.

## <a name="check-regions"></a>지역 확인

솔루션이 다른 Azure 서비스(예: Azure Functions)에서 Azure Digital Twins를 사용하는 경우 각 서비스의 배포 지역을 확인합니다. 다른 지역에 배포되는 서비스는 솔루션 전체에서 지연이 발생할 수 있습니다. 의도적으로 분산 솔루션을 만드는 것이 아니라면 실수로 지연이 발생하지 않도록 동일한 지역 내에 모든 서비스 인스턴스를 배포하는 것이 좋습니다.

## <a name="check-logs"></a>로그 확인

Azure Digital Twins는 다른 데이터 중에서 성능을 모니터링하는 데 도움이 되도록 서비스 인스턴스에 대한 로그를 수집할 수 있습니다. [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) 또는 사용자 지정 스토리지 메커니즘으로 로그를 보낼 수 있습니다. 인스턴스에서 로깅을 사용하도록 설정하려면 [문제 해결: 진단 설정](troubleshoot-diagnostics.md)의 지침을 사용합니다. 로그의 타임스탬프를 분석하여 대기 시간을 측정하고, 일관성이 있는지 평가하고, 해당 원본을 이해할 수 있습니다.

## <a name="check-api-frequency"></a>API 빈도 확인

성능에 영향을 줄 수 있는 또 다른 요인은 API 호출에 권한을 다시 부여하는 데 걸린 시간입니다. API 호출의 빈도를 고려합니다. 호출 사이에 간격이 15분 이상인 경우 시스템은 각 호출을 통해 권한을 다시 부여하고 이를 수행하는 데 추가 시간이 걸릴 수 있습니다. 15분마다 한 번 이상 Azure Digital Twins를 호출하도록 코드에 타이머 또는 유사한 타이머를 추가하여 이 문제를 방지할 수 있습니다.

## <a name="contact-support"></a>지원에 문의

위의 단계를 사용하여 문제를 해결한 후에도 여전히 성능 문제가 발생하는 경우 추가 문제 해결 지원을 위해 Azure 도움말 + 지원에서 지원 요청을 만들 수 있습니다. 

다음 단계를 수행합니다.

1. 인스턴스에 대한 [메트릭](troubleshoot-metrics.md) 및 [로그](troubleshoot-diagnostics.md)를 수집합니다.
2. Azure Portal에서 [Azure 도움말+ 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동합니다. 프롬프트를 사용하여 문제에 대한 세부 정보를 제공하고, 권장 솔루션을 확인하고, 메트릭/로그 파일을 공유하고, 지원 팀이 문제를 조사하는 데 사용할 수 있는 기타 정보를 제출합니다. 지원 요청을 만드는 방법에 대한 자세한 내용은 [Azure 지원 요청 만들기](../azure-portal/supportability/how-to-create-azure-support-request.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 Azure Digital Twins 인스턴스 문제를 해결하는 다른 방법에 대해 알아보세요.
* [문제 해결: Azure Monitor의 메트릭 보기](troubleshoot-metrics.md)
* [문제 해결: 진단 설정](troubleshoot-diagnostics.md)
* [문제 해결: 경고 설정](troubleshoot-alerts.md)
* [문제 해결: 리소스 상태 이해](troubleshoot-resource-health.md)
