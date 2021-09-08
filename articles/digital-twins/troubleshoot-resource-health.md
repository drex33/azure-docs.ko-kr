---
title: 리소스 상태 이해
titleSuffix: Azure Digital Twins
description: Azure Resource Health를 사용하여 Azure Digital Twins 인스턴스의 상태를 확인하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 551193ebcddb023010f1cea1029571c99176afb9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113094"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure Digital Twins 문제 해결: 리소스 상태

[Azure Service Health](../service-health/index.yml)는 Azure 리소스에 영향을 미치는 서비스 문제를 진단하고 지원을 받는 데 도움이 되는 경험 모음입니다. 여기에는 **리소스 상태**, **서비스 상태** 및 **상태** 정보가 포함되며 현재 및 과거 상태 정보에 대한 보고서가 있습니다.

## <a name="use-azure-resource-health"></a>Azure Resource Health 사용

[Azure Resource Health](../service-health/resource-health-overview.md)를 사용하여 Azure Digital Twins 인스턴스가 실행 중인지 여부를 모니터링할 수 있습니다. 또한 지역 가동 중단이 인스턴스의 상태에 영향을 주는지도 확인할 수 있습니다.

인스턴스의 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Digital Twins 인스턴스로 이동합니다. 포털 검색 표시줄에 이름을 입력하여 찾을 수 있습니다. 

2. 인스턴스의 메뉴에서 지원 + 문제 해결아래에 있는 **리소스 상태** 를 선택합니다. 그러면 리소스 상태 기록을 볼 수 있는 페이지로 연결됩니다. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="'리소스 상태' 페이지를 보여주는 스크린샷. 지난 9일 동안의 일별 보고서를 표시하는 '상태 기록' 섹션이 있습니다.":::

위의 이미지에서 이 인스턴스는 **사용 가능** 으로 표시되며 지난 9일 동안의 상태였습니다. 사용 가능 상태 및 표시될 수 있는 기타 상태 유형에 대한 자세한 내용은 [Resource Health 개요](../service-health/resource-health-overview.md)를 참조하세요.

또한 [Azure Resource Health에서 리소스 유형 및 상태 검사](../service-health/resource-health-checks-resource-types.md)에서 다양한 유형의 Azure 리소스에 대한 리소스 상태를 확인하는 다양한 검사에 대해 자세히 알아볼 수 있습니다.

## <a name="use-azure-service-health"></a>Azure Service Health 사용

[Azure Service Health](../service-health/service-health-overview.md)를 사용하면 특정 지역에서 전체 Azure Digital Twins 서비스의 상태를 확인하고 진행 중인 서비스 문제 및 예정된 유지 관리와 같은 이벤트를 파악할 수 있습니다.

서비스 상태를 확인하려면 [Azure Portal](https://portal.azure.com)에 로그인하고 **Service Health** 서비스로 이동합니다. 포털 검색 창에 "서비스 상태"를 입력하여 찾을 수 있습니다. 

그런 다음 구독, 지역 및 서비스별로 서비스 문제를 필터링할 수 있습니다.

Azure Service Health 사용에 대한 자세한 내용은 [Service Health 개요](../service-health/service-health-overview.md)를 참조하세요.

## <a name="use-azure-status"></a>Azure 상태 사용

[Azure 상태](../service-health/azure-status-overview.md) 페이지는 Azure 서비스 및 지역의 상태에 대한 글로벌 보기를 제공합니다. Azure Service Health 또는 Azure Resource Health만큼 개인화되어 있지 않으며 다양한 영향을 미치는 사건을 이해하는 데 유용할 수 있습니다.

Azure 상태를 확인하려면 [Azure 상태](https://status.azure.com/status/) 페이지로 이동합니다. 페이지에는 지역별 상태 표시기와 함께 Azure 서비스 테이블이 표시됩니다. 페이지에서 해당 테이블 항목을 검색하여 Azure Digital Twins를 볼 수 있습니다.

Azure 상태 페이지 사용에 대한 자세한 내용은 [Azure 상태 개요](../service-health/azure-status-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 Azure Digital Twins 인스턴스를 모니터링하는 다른 방법에 대해 알아보세요.
* [문제 해결: Azure Monitor의 메트릭 보기](troubleshoot-metrics.md)
* [문제 해결: 진단 설정](troubleshoot-diagnostics.md)
* [문제 해결: 경고 설정](troubleshoot-alerts.md)
