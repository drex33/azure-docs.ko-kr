---
title: Azure Resource Manager 템플릿 샘플 - Azure Front Door
description: 기본 Front Door를 만들고 Front Door 속도 제한을 구성하기 위한 템플릿을 포함하여 Azure Front Door용 Resource Manager 템플릿 샘플에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/01/2021
ms.author: duau
ms.openlocfilehash: db0bf4783de324af2ab4e5a5ec6bfbaa18ad33cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449776"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door에 대한 Azure Resource Manager 배포 모델 템플릿

다음 표에는 Azure Front Door에 대한 Azure Resource Manager 배포 모델 템플릿 링크가 포함되어 있습니다.

## <a name="azure-front-door"></a>Azure Front Door

| 템플릿 | Description |
| ---| ---|
| [기본 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| 단일 백 엔드를 사용하여 기본 Front Door 구성을 만듭니다. |
| [다중 백 엔드 및 백 엔드 풀과 URL 기반 라우팅을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| URL 경로를 기준으로 백 엔드 풀의 여러 백 엔드 및 백 엔드 풀 간에 부하 분산이 구성된 Front Door를 만듭니다. |
| [Front Door를 사용하여 사용자 지정 도메인 및 관리형 TLS 인증서 등록](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| 사용자 지정 도메인을 Front Door에 추가하고 Front Door 관리형 TLS 인증서를 사용합니다. |
| [Front Door를 사용하여 사용자 지정 도메인 및 고객 관리형 TLS 인증서 등록](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| 사용자 지정 도메인을 Front Door에 추가하고 Key Vault를 통해 사용자 고유의 TLS 인증서를 사용합니다. |
| [지역 필터링을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| 특정 국가/지역의 트래픽을 허용/차단하는 Front Door를 만듭니다. |
| [Front Door에서 백 엔드에 대한 상태 프로브 제어](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| 프로브 경로 및 프로브가 전송되는 간격을 업데이트하여 Front Door를 업데이트함으로써 상태 프로브 설정을 변경합니다. |
| [활성/대기 백 엔드 구성을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| 활성/대기 애플리케이션 토폴로지에 대한 우선 순위 기반 라우팅을 보여 주는 Front Door를 만듭니다. 이러한 Front Door는 기본적으로 기본(최고 우선 순위) 백 엔드가 사용 불가능해질 때까지 이 백 엔드로 모든 트래픽을 전송합니다. |
| [특정 경로에 대해 캐싱이 사용되도록 설정된 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| 정의된 라우팅 구성에 대해 캐싱이 사용되도록 설정되어 워크로드에 대한 정적 자산이 캐싱되는 Front Door를 만듭니다. |
| [Front Door 호스트 이름에 대한 세션 선호도 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Front Door를 업데이트하여 프론트 엔드 호스트에 대한 세션 선호도를 사용하도록 설정합니다. 이 경우 동일한 사용자 세션의 후속 트래픽이 동일한 백 엔드로 전송됩니다. |
| [클라이언트 IP 허용 목록 또는 차단 목록에 대한 Front Door 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| 클라이언트 IP를 사용하여 사용자 지정 액세스 제어를 사용하여 트래픽 특정 클라이언트 ID를 제한하도록 Front Door를 구성합니다. |
| [특정 http 매개변수로 작업을 수행하도록 Front Door 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| http 매개 변수와 함께 액세스 제어에 대한 사용자 지정 규칙을 사용하여 수신 요청에서 http 매개 변수를 기준으로 특정 트래픽을 허용하거나 차단하도록 Front Door를 구성합니다. |
| [Front Door 속도 제한 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| 제공된 프론트 엔드 호스트에 대한 수신 트래픽의 속도를 제한하도록 Front Door를 구성합니다. |
| | |

## <a name="azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)

| 샘플 | Description |
|-|-|
| [Front Door(빠른 생성)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | 엔드포인트, 원본 그룹, 원본 및 경로를 포함하는 기본 Front Door 프로필을 만듭니다.  |
| [규칙 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | Front Door 프로필 및 규칙 집합을 만듭니다.  |
| [관리형 규칙 집합이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | 관리형 규칙 집합이 있는 Front Door 프로필 및 WAF를 만듭니다.  |
| [사용자 지정 규칙이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | 사용자 지정 규칙이 있는 Front Door 프로필 및 WAF를 만듭니다.  |
| [요율 제한이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | 사용자 지정 규칙으로 Front Door 프로필과 WAF를 만들어 요율 제한을 수행합니다.  |
| [지역 필터링을 사용하는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | 사용자 지정 규칙으로 Front Door 프로필과 WAF를 만들어 지역 필터링을 수행합니다.  |
|**App Service 원본**| **설명** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
| [Private Link가 있는 App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
|**Azure Functions 원본**| **설명** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 Azure Functions 앱을 만듭니다.  |
| [Private Link가 있는 Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 Azure Functions 앱을 만듭니다.  |
|**API Management 원본**| **설명** |
| [API Management(외부)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | 외부 VNet 통합 및 Front Door 프로필을 사용하여 API Management 인스턴스를 만듭니다.  |
|**스토리지 원본**| **설명** |
| [스토리지 정적 웹 사이트](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 Azure Storage 계정 및 정적 웹 사이트를 만듭니다.  |
| [Private Link가 있는 스토리지 Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 Azure Storage 계정 및 Blob 컨테이너를 만듭니다.  |
|**Application Gateway 원본**| **설명** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | Application Gateway 및 Front Door 프로필을 만듭니다. |
|**가상 머신 원본**| **설명** |
| [Private Link 서비스가 있는 가상 머신](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | 가상 머신과 Private Link 및 Front Door 프로필을 만듭니다. |
| | |

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
