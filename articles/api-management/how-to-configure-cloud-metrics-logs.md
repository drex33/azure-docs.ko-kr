---
title: Azure API Management 자체 호스팅 게이트웨이의 클라우드 메트릭 및 로그 구성 | Microsoft Docs
description: Azure API Management 자체 호스팅 게이트웨이에 대한 클라우드 메트릭 및 로그 구성 방법 알아보기
services: api-management
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: danlep
ms.openlocfilehash: 5661218fd9e1849eb3133496d8e2640753eb2fa1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659933"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management 자체 호스팅 게이트웨이에 대한 클라우드 메트릭 및 로그 구성

이 문서에서는 [자체 호스팅 게이트웨이](./self-hosted-gateway-overview.md)의 클라우드 메트릭과 로그를 구성하는 방법에 대한 세부 정보를 제공합니다.

자체 호스팅 게이트웨이는 API 관리 서비스와 연결되어야 하며 포트 443의 Azure에 대한 아웃바운드 TCP/IP 연결이 필요합니다. 게이트웨이는 아웃바운드 연결을 활용하여 원격 분석을 Azure로 보냅니다(구성된 경우). 

## <a name="metrics"></a>메트릭
기본적으로 자체 호스팅 게이트웨이는 [클라우드에서](api-management-howto-use-azure-monitor.md) 관리되는 게이트웨이와 동일한 [Azure Monitor](https://azure.microsoft.com/services/monitor/)를 통해 여러 메트릭을 내보냅니다. 

게이트웨이 배포의 ConfigMap에서 `telemetry.metrics.cloud` 키를 사용하여 이 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 다음은 사용 가능한 구성 내역입니다.

| 필드  | 기본값 | 설명 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Azure Monitor를 통해 로깅을 사용합니다. 값은 `true`, `false`가 될 수 있습니다. |


다음은 샘플 구성입니다.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

자체 호스팅 게이트웨이는 현재 Azure Monitor를 통해 다음 메트릭을 내보냅니다.

| 메트릭  | Description |
| ------------- | ------------- |
| 요청  | 해당 기간의 API 요청 수 |
| 게이트웨이 요청의 기간 | 게이트웨이에서 요청을 수신한 순간부터 응답이 완전히 전송될 때까지 걸린 시간(밀리초) |
| 백엔드 요청의 기간 | 전체 백 엔드 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초)  |

## <a name="logs"></a>로그

자체 호스팅 게이트웨이에서는 현재 클라우드로 [진단 로그](./api-management-howto-use-azure-monitor.md#activity-logs)를 보내지 않습니다. 하지만 자체 호스팅 게이트웨이가 배포되는 위치에서 [로컬로 로그를 구성하고 보존](how-to-configure-local-metrics-logs.md)할 수 있습니다. 

게이트웨이가 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)에 배포되는 경우 [컨테이너용 Azure Monitor](../azure-monitor/containers/container-insights-overview.md)를 사용하도록 설정하여 컨테이너에서 로그를 수집하고 Log Analytics에서 로그를 볼 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* [로컬로 로그를 구성하고 보존하는](how-to-configure-local-metrics-logs.md) 방법 알아보기
