---
title: VNet에서 Azure Spring Cloud를 실행하는 고객의 책임
description: 이 문서에서는 VNet에서 Azure Spring Cloud를 실행하는 고객의 책임에 대해 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: devx-track-java
ms.openlocfilehash: efcff17cd867deb885e8591db48dd23424a445a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452436"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>VNet에서 Azure Spring Cloud를 실행하는 고객의 책임

이 문서에는 가상 네트워크에서 Azure Spring Cloud 사용하기 위한 사양이 포함되어 있습니다.

Azure Spring Cloud가 가상 네트워크에 배포되면 가상 네트워크 외부의 서비스에 대한 아웃바운드 종속성이 있습니다. 관리와 운영을 목적으로 Azure Spring Cloud는 특정 포트와 FQDN(정규화된 도메인 이름)에 액세스해야 합니다. 이러한 엔드포인트는 Azure Spring Cloud 관리 플레인과 통신하거나 핵심 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 다운로드 및 설치하는 데 필요합니다.

기본적으로 Azure Spring Cloud는 무제한 아웃바운드(송신) 인터넷 액세스가 있습니다. 이러한 수준의 네트워크 액세스가 있으면 사용자가 실행하는 애플리케이션은 필요할 때마다 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한하려면 유지 관리 작업을 위해 제한된 수의 포트 및 주소에 액세스할 수 있어야 합니다. 아웃바운드 주소를 보호하는 가장 간단한 솔루션은 도메인 이름에 따라 아웃바운드 트래픽을 제어할 수 있는 방화벽 디바이스를 사용하는 것입니다. 예를 들어 Azure Firewall은 대상의 FQDN을 기반으로 아웃바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다. 이러한 필수 포트와 주소를 허용하도록 기본 방화벽 및 보안 규칙을 구성할 수도 있습니다.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud 리소스 요구 사항

다음 목록에서는 Azure Spring Cloud 서비스에 대한 리소스 요구 사항을 보여줍니다. 일반적으로 Azure Spring Cloud 및 기본 네트워크 리소스에서 만든 리소스 그룹을 수정해서는 안 됩니다.

- Azure Spring Cloud 만들고 소유한 리소스 그룹을 수정하지 마세요.
  - 기본적으로 이러한 리소스 그룹의 이름은 및 로 `ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*` 지정됩니다. `ap_[SERVICE-INSTANCE-NAME]_[REGION]*`
  - Azure Spring Cloud 이러한 리소스 그룹의 리소스를 업데이트하는 것을 차단하지 마세요.
- Azure Spring Cloud 사용하는 서브넷을 수정하지 마세요.
- 동일한 서브넷에 두 개 이상의 Azure Spring Cloud 서비스 인스턴스를 만들지 마세요.
- 방화벽을 사용하여 트래픽을 제어하는 경우 서비스 인스턴스를 작동, 유지 관리 및 지원하는 Azure Spring Cloud 구성 요소에 대한 다음 송신 트래픽을 차단하지 마세요.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud 네트워크 요구 사항

| 대상 엔드포인트                                         | 포트             | 사용                                       | 참고                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| \*:1194 *또는* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194         | 기본 Kubernetes 클러스터 관리 |                                                              |
| \*:443 *또는* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443          | Azure Spring Cloud 서비스 관리    | 서비스 인스턴스 "requiredTraffics"의 정보는 "networkProfile" 섹션 아래의 리소스 페이로드에서 알 수 있습니다. |
| \*:9000 *또는* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000         | 기본 Kubernetes 클러스터 관리 |                                                              |
| \*:123 *또는* ntp.ubuntu.com:123                                | UDP:123          | Linux 노드의 NTP 시간 동기화  |                                                              |
| \*.azure.io:443 또는  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443          | Azure Container Registry입니다.                 | 가상 네트워크에서 *Azure Container Registry* [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정하여 바꿀 수 있습니다. |
| \*.core.windows.net:443 및 \* .core.windows.net:445 *또는* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 및 Storage:445 | TCP:443, TCP:445 | Azure 파일                        | 가상 네트워크에서 *Azure Storage* [가상 네트워크의 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정하여 바꿀 수 있습니다. |
| \*.servicebus.windows.net:443 또는  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443          | Azure 이벤트 허브                          | 가상 네트워크에서 *Azure Event Hubs* [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정하여 바꿀 수 있습니다. |

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud FQDN 요구 사항/애플리케이션 규칙

Azure Firewall은 FQDN 태그 **AzureKubernetesService** 를 제공하여 다음 구성을 간소화합니다.

| 대상 FQDN                  | 포트      | 사용                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| <i>*.azmk8s.io</i>                | HTTPS:443 | 기본 Kubernetes 클러스터 관리                    |
| <i>mcr.microsoft.com</i>          | HTTPS:443 | MCR(Microsoft Container Registry)                          |
| <i>*.cdn.mscr.io</i>              | HTTPS:443 | Azure CDN에서 지원하는 MCR 스토리지                         |
| <i>*.data.mcr.microsoft.com</i>   | HTTPS:443 | Azure CDN에서 지원하는 MCR 스토리지                         |
| <i>management.azure.com</i>       | HTTPS:443 | 기본 Kubernetes 클러스터 관리                    |
| <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory 인증                       |
| <i>*login.microsoft.com</i>       | HTTPS:443 | Azure Active Directory 인증                       |
| <i>packages.microsoft.com</i>     | HTTPS:443 | Microsoft 패키지 리포지토리                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS:443 | kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치하는 데 필요한 리포지토리 |
| *mscrl.microsoft.com*<sup>1</sup> | HTTPS:80  | 필수 Microsoft 인증서 체인 경로                  |
| *crl.microsoft.com*<sup>1</sup>   | HTTPS:80  | 필수 Microsoft 인증서 체인 경로                  |
| *crl3.digicert.com*<sup>1</sup>   | HTTPS:80  | 타사 TLS/SSL 인증서 체인 경로입니다.                 |

<sup>1</sup> 이러한 FQDN은 FQDN 태그에 포함되지 않습니다.


## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>타사 애플리케이션 성능 관리를 위한 Azure Spring Cloud 옵션 FQDN

Azure Firewall은 FQDN 태그 **AzureKubernetesService** 를 제공하여 다음 구성을 간소화합니다.

| 대상 FQDN                   | 포트       | 사용                                                          |
| ---------------------------------- | ---------- | ------------------------------------------------------------ |
| <i>collector*.newrelic.com</i>     | TCP:443/80 | 미국 지역에서 New Relic APM 에이전트의 필수 네트워크는 [APM 에이전트 네트워크](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)를 참조하세요. |
| <i>collector*.eu01.nr-data.net</i> | TCP:443/80 | EU 지역에서 New Relic APM 에이전트의 필수 네트워크는 [APM 에이전트 네트워크](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)를 참조하세요. |
| <i>*.live.dynatrace.com</i>        | TCP:443    | Dynatrace APM 에이전트의 필수 네트워크.                    |
| <i>*.live.ruxit.com</i>            | TCP:443    | Dynatrace APM 에이전트의 필수 네트워크.                    |
| <i>*. saas.appdynamics.com</i>      | TCP:443/80 | AppDynamics APM 에이전트의 필수 네트워크, [SaaS 도메인 및 IP 범위](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges)도 참조 하세요. |

## <a name="see-also"></a>참고 항목

* [개인 네트워크에서 애플리케이션에 액세스](access-app-virtual-network.md)
* [Application Gateway 및 Azure Firewall을 사용하여 앱 노출](expose-apps-gateway-azure-firewall.md)
