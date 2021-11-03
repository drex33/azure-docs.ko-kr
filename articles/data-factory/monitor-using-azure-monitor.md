---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링
description: Data Factory 정보를 사용하여 진단 로그를 사용하도록 설정하여 Azure Monitor 사용하여 Azure Data Factory 파이프라인을 모니터링하는 방법을 알아봅니다.
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: a3e2fe3f005467e82ebe5fbc2e502c764a7322b3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033136"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Monitor를 사용하여 Data Factory 모니터링 및 경고

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

클라우드 애플리케이션은 복잡하며 이동하는 부분이 많습니다. 모니터는 애플리케이션이 정상 상태로 계속 실행되도록 하는 데 도움이 되는 데이터를 제공합니다. 또한 모니터는 잠재적인 문제를 방지하고 이전 문제를 해결하는 데도 도움이 됩니다. 모니터링 데이터를 사용하여 애플리케이션에 대해 심층적인 인사이트를 얻을 수 있습니다. 이러한 지식은 애플리케이션 성능 및 유지 관리를 향상시키는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대한 기본 수준의 인프라 메트릭 및 로그를 제공합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. ADF(Azure Data Factory)는 Azure Monitor에서 진단 로그를 작성할 수 있습니다. 7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory 메트릭 및 파이프라인 실행 데이터 유지

Data Factory는 파이프라인 실행 데이터를 45일 동안만 저장합니다. 해당 데이터를 더 오랫동안 유지하려는 경우 Azure Monitor를 사용해야 합니다. Monitor를 사용하면 분석을 위한 진단 로그를 여러 다양한 대상으로 라우팅할 수 있습니다.

* **스토리지 계정**: 감사 또는 수동 검사를 위해 스토리지 계정에 진단 로그를 저장합니다. 진단 설정을 사용하여 보존 시간(일)을 지정할 수 있습니다.
* **이벤트 허브**: 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 Power BI와 같은 파트너 서비스/사용자 지정 분석 솔루션에 입력됩니다.
* **Log Analytics**: Log Analytics를 사용하여 로그를 분석합니다. Azure Monitor와 Data Factory 통합은 다음과 같은 시나리오에서 유용합니다.
  * Data Factory에서 Monitor에 게시한 다양한 메트릭 세트에 대해 복잡한 쿼리를 작성하려고 합니다. Monitor를 통해 해당 쿼리에 대한 사용자 지정 경고를 생성할 수 있습니다.
  * 데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리의 데이터를 단일 Monitor 작업 영역으로 라우팅할 수 있습니다.

또한 로그를 내보내는 리소스의 구독에 없는 스토리지 계정 또는 이벤트 허브 네임스페이스를 사용할 수도 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대한 적절한 Azure RBAC(Azure 역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 메트릭 및 경고](monitor-metrics-alerts.md)
- [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
