---
title: API Management의 클라이언트 응답 시간 초과 및 오류 문제 해결
description: API Management에서 일시적 연결 오류 및 관련된 대기 시간 문제 해결
author: dlepow
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: danlep
ms.service: api-management
ms.openlocfilehash: 18f117f64c6f97ee847f2e82d0e8fea509e17514
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253443"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>API Management의 클라이언트 응답 시간 초과 및 오류 문제 해결

이 문서는 [Azure API Management](./api-management-key-concepts.md)에서 일시적 연결 오류 및 관련된 대기 시간 문제를 해결하는 데 도움이 됩니다. 특히 이 문서에서는 SNAT(source address network translation) 포트 소모 관련 정보와 문제 해결 방법을 제공합니다. 도움이 더 필요한 경우 [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의하거나 [Azure 지원](https://azure.microsoft.com/support/options/)에 지원 요청을 제출하세요.

## <a name="symptoms"></a>증상

APIM(API Management) 서비스를 통해 API를 호출하는 클라이언트 애플리케이션에 다음 증상 중 하나 이상이 나타날 수 있습니다.

* 일시적인 HTTP 500 오류
* 시간 초과 오류 메시지

이러한 증상은 [Azure Monitor 리소스 로그](../azure-monitor/essentials/resource-logs.md)에서 `BackendConnectionFailure` 인스턴스로 매니페스트됩니다.

## <a name="cause"></a>원인

이 증상 패턴은 APIM 서비스의 SNAT(source network address translation) 포트 한도로 인해 발생하는 경우가 많습니다.

클라이언트가 APIM API 중 하나를 호출할 때마다 Azure API Management 서비스는 백 엔드 API에 액세스하기 위해 SNAT 포트를 엽니다. [Azure에서의 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)에 설명된 대로 Azure는 SNAT(source network address translation) 및 Load Balancer(고객에게 노출되지 않음)를 사용하여 공용 IP 주소 공간에 있는 Azure 외부의 엔드포인트와 통신하고 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하지 않는 Azure 내부의 엔드포인트와도 통신합니다. 이 상황은 공용 IP에 노출된 백 엔드 API에만 해당됩니다.

Azure API Management 서비스의 각 인스턴스에는 처음에 미리 할당된 수의 SNAT 포트가 지정됩니다. 이 한도는 동일한 호스트 및 포트 조합에 대한 연결을 여는 데 영향을 미칩니다. 동일한 주소 및 포트 조합에 대한 호출을 반복한 경우 SNAT 포트가 고갈됩니다. 해제된 SNAT 포트는 필요에 따라 재사용할 수 있습니다. Azure 네트워크 부하 분산 장치는 4분 동안 기다린 후에만 닫힌 연결에서 SNAT 포트를 회수합니다.

API에 대한 클라이언트 요청이 빠르게 연속되면 해당 포트가 닫혀 있지 않고 충분히 빠르게 재생되지 않아 APIM 서비스에서 적시에 클라이언트 요청을 처리할 수 없는 경우 SNAT 포트의 미리 할당된 할당량이 소모될 수 있습니다.

## <a name="mitigations-and-solutions"></a>완화 및 해결 방법

SNAT 포트 소모 문제를 해결하려면 먼저 백 엔드 서비스의 성능을 진단하고 최적화해야 합니다.

SNAT 포트 소모를 완화하기 위한 일반적인 전략은 *Azure Load Balancer* 설명서의 [아웃바운드 연결 실패 문제 해결](../load-balancer/troubleshoot-outbound-connection.md)에 설명되어 있습니다. 이러한 전략 중 API Management에 적용할 수 있는 전략은 다음과 같습니다.

### <a name="scale-your-apim-instance"></a>APIM 인스턴스 스케일링

각 API Management 인스턴스에는 APIM 단위를 기준으로 여러 SNAT 포트가 할당됩니다. 추가 단위를 사용하여 API Management 인스턴스를 스케일링하면 추가 SNAT 포트를 할당할 수 있습니다. 자세한 내용은 [API Management 서비스 스케일링](upgrade-and-scale.md#scale-your-api-management-service)을 참조하세요.

> [!NOTE]
> SNAT 포트 사용량은 현재 자동 크기 조정 API Management 단위의 메트릭으로 사용할 수 없습니다.

### <a name="use-multiple-ips-for-your-backend-urls"></a>백 엔드 URL에 여러 IP 사용

APIM 인스턴스에서 백 엔드 서비스의 동일한 대상 IP 및 대상 포트로의 각 연결은 고유 트래픽 흐름을 유지하기 위해 SNAT 포트를 사용합니다. 백그라운드 서비스의 반환 트래픽에 사용할 다른 SNAT 포트가 없는 경우 APIM은 응답을 다른 응답과 분리할 수 없습니다.

대상 IP 또는 대상 포트가 다르면 SNAT 포트를 재사용할 수 있으므로 SNAT 포트 소모를 방지하는 또 다른 방법은 백 엔드 서비스 URL에 여러 IP를 사용하는 것입니다.

자세한 내용은 [아웃바운드 프록시 Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md)를 참조하세요.

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>APIM과 백 엔드 서비스를 동일한 VNet에 배치

백 엔드 API가 App Service와 같은 서비스 엔드포인트를 지원하는 Azure 서비스에서 호스트되는 경우 APIM 인스턴스와 백 엔드 서비스를 동일한 가상 네트워크에 배치하고 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 통해 노출하여 SNAT 포트 소모 문제를 방지할 수 있습니다. 공용 VNet을 사용하고 통합 서브넷에 서비스 엔드포인트를 배치하면 APIM 인스턴스에서 해당 서비스로의 아웃바운드 트래픽이 인터넷을 우회하므로 SNAT 포트 제한을 피할 수 있습니다. 마찬가지로 VNet과 프라이빗 엔드포인트를 사용하는 경우에는 해당 대상으로의 아웃바운드 SNAT 포트 문제가 발생하지 않습니다.

자세한 내용은 [가상 네트워크에서 Azure API Management를 사용하는 방법](api-management-using-with-vnet.md) 및 [Azure 가상 네트워크에 App Service 통합](../app-service/overview-vnet-integration.md)을 참조하세요.

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>가상 네트워크에 APIM을 배치하고 아웃바운드 호출을 Azure 방화벽으로 라우팅

가상 네트워크에 APIM 및 백 엔드 서비스를 배치하는 것과 마찬가지로 APIM 서비스가 있는 VNet에서 Azure 방화벽을 사용한 다음 아웃바운드 APIM 호출을 Azure 방화벽으로 라우팅할 수 있습니다. APIM과 Azure 방화벽(동일한 VNet에 있음) 간에는 SNAT 포트가 필요하지 않습니다. 백 엔드 서비스에 대한 SNAT 연결의 경우 Azure 방화벽에는 APIM 인스턴스에 할당된 것보다 훨씬 많은 64,000개의 사용 가능한 포트가 있습니다.

자세한 내용은 [Azure 방화벽](../firewall/overview.md) 설명서를 참조하세요.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>응답 캐싱 및 기타 백 엔드 성능 튜닝 고려

고려해야 할 또 다른 잠재적 완화 방법은 백 엔드 API의 처리 시간을 개선하는 것입니다. 이 작업을 수행하는 한 가지 방법은 응답 캐싱으로 특정 API를 구성하여 API를 호출하는 클라이언트 애플리케이션과 APIM 백 엔드 로드 사이의 대기 시간을 줄이는 것입니다.

자세한 내용은 [Azure API Management에서 캐싱을 추가하여 성능 향상](api-management-howto-cache.md)을 참조하세요.

### <a name="consider-implementing-access-restriction-policies"></a>액세스 제한 정책 구현 고려

비즈니스 시나리오에 적합한 경우 API Management 제품의 액세스 제한 정책을 구현할 수 있습니다. 예를 들어 `rate-limit-by-key` 정책을 사용하면 지정된 기간당 호출 속도를 제한하여 키 하나당 API 사용량 급증을 방지할 수 있습니다.

자세한 내용은 [Azure API Management 액세스 제한 정책](api-management-access-restriction-policies.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Load Balancer: 아웃바운드 연결 실패 문제 해결](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: 일시적 아웃바운드 연결 오류 문제 해결](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)