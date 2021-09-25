---
title: 자체 호스팅 게이트웨이 개요 | Azure API Management
description: 조직이 Azure API Management의 자체 호스팅 게이트웨이 기능을 통해 하이브리드 및 다중 클라우드 환경에서 API를 관리하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.author: danlep
ms.openlocfilehash: fde1ee4c7b19c27b278fe6728ee244d03be246e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663652"
---
# <a name="self-hosted-gateway-overview"></a>자체 호스팅 게이트웨이 개요

이 문서에서는 Azure API Management의 자체 호스팅 게이트웨이 기능이 하이브리드 및 다중 클라우드 API 관리를 지원하고, 대략적인 아키텍처를 제공하고, 해당 기능을 강조 표시하는 방법을 설명합니다.

## <a name="hybrid-and-multi-cloud-api-management"></a>하이브리드 및 다중 클라우드 API 관리

자체 호스팅 게이트웨이 기능은 하이브리드 및 다중 클라우드 환경에 대한 API Management 지원을 확장하고, 조직이 Azure의 단일 API Management 서비스를 통해 온-프레미스 및 클라우드에서 호스트되는 API를 효율적이고 안전하게 관리할 수 있게 해줍니다.

자체 호스팅 게이트웨이를 통해 고객은 API를 호스트하는 환경에 컨테이너화된 버전의 API Management 게이트웨이 구성 요소를 유연하게 배포할 수 있습니다. 모든 자체 호스팅 게이트웨이는 페더레이션된 API Management 서비스에서 관리되므로 모든 내부 및 외부 API에서 고객에게 가시성 및 통합 관리 환경을 제공합니다. API 근처에 게이트웨이를 배치하면 고객이 API 트래픽 흐름을 최적화하고 보안 및 규정 준수 요구 사항을 해결할 수 있습니다.

각 API Management 서비스는 다음과 같은 주요 구성 요소로 구성됩니다.

-   API로 노출되는 관리 평면은 Azure Portal, PowerShell 및 기타 지원되는 메커니즘을 통해 서비스를 구성하는 데 사용됩니다.
-   게이트웨이(또는 데이터 평면)는 API 요청 프록시, 정책 적용 및 원격 분석 수집을 담당합니다.
-   개발자가 API를 검색하고, 학습시키고, 온보딩하는 데 사용하는 개발자 포털

기본적으로 이러한 모든 구성 요소는 Azure에 배포되어 API를 구현하는 백 엔드가 호스트되는 위치에 관계없이 모든 API 트래픽(아래 그림에 검은색 실선 화살표로 표시)이 Azure를 통과하게 합니다. 이 모델은 운영 편의성을 제공하는 대가로 대기 시간 증가, 규정 준수 문제를 야기하며, 추가 데이터 전송 요금이 발생하는 경우도 있습니다.

![자체 호스팅 게이트웨이가 없는 API 트래픽 흐름](media/self-hosted-gateway-overview/without-gateways.png)

백 엔드 API 구현이 호스트되는 환경에 자체 호스팅 게이트웨이를 배포하면 API 트래픽이 백 엔드 API로 직접 이동하므로 대기 시간이 개선되고, 데이터 전송 비용이 최적화되고, 규정 준수가 지원되며, 구현이 호스트되는 위치에 관계없이 조직 내 모든 API를 한 곳에서 관리하고, 관찰하고, 검색할 수 있는 이점을 유지할 수 있습니다.

![자체 호스팅 게이트웨이가 있는 API 트래픽 흐름](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>패키징 및 기능

자체 호스팅 게이트웨이는 모든 API Management 서비스의 일부로 Azure에 배포된 관리형 게이트웨이와 기능적으로 동등하게 컨테이너화된 버전입니다. 자체 호스팅 게이트웨이는 Microsoft Container Registry에서 Linux 기반 Docker [컨테이너](https://aka.ms/apim/sputnik/dhub)로 사용할 수 있습니다. 이 도구는 온-프레미스, 클라우드 인프라의 서버 클러스터에서 실행되거나 평가 및 개발 목적으로 개인용 컴퓨터에서 실행되는 Docker, Kubernetes 또는 다른 컨테이너 오케스트레이션 솔루션에 배포할 수 있습니다. 또한 자체 호스팅 게이트웨이를 [Azure Arc 지원 Kubernetes 클러스터](./how-to-deploy-self-hosted-gateway-azure-arc.md)에 클러스터 확장으로 배포할 수도 있습니다.

관리형 게이트웨이에 있는 다음 기능은 자체 호스팅 게이트웨이에서 **사용할 수 없습니다**.

- Azure Monitor 로그
- 업스트림(백 엔드 쪽) TLS 버전 및 암호화 관리
- API Management 서비스에 업로드된 [CA 루트 인증서](api-management-howto-ca-certificates.md)를 사용하여 서버 및 클라이언트 인증서의 유효성 검사. 자체 호스팅 게이트웨이에 대한 [사용자 지정 인증 기관](api-management-howto-ca-certificates.md#create-custom-ca-for-self-hosted-gateway) 및 [클라이언트 인증서 유효성 검사](api-management-access-restriction-policies.md#validate-client-certificate) 정책을 구성하여 적용할 수 있습니다.
- [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)과 통합
- TLS 세션 재개
- 클라이언트 인증서 재협상. 이는 [클라이언트 인증서 인증](api-management-howto-mutual-certificates-for-clients.md)이 작동하려면 API 소비자가 초기 TLS 핸드셰이크의 일부로 인증서를 제공해야 함을 의미합니다. 이렇게 하려면 자체 호스팅 게이트웨이 사용자 지정 호스트 이름을 구성할 때 클라이언트 인증서 협상 설정을 사용하도록 설정합니다.
- 기본 제공 캐시. 자체 호스팅 게이트웨이에서 외부 캐시를 사용하는 방법에 대해 알아보려면 이 [문서](api-management-howto-cache-external.md)를 참조하세요.

## <a name="connectivity-to-azure"></a>Azure 연결

자체 호스팅 게이트웨이에는 포트 443의 Azure에 대한 아웃바운드 TCP/IP 연결이 필요합니다. 각 자체 호스팅 게이트웨이는 단일 API Management 서비스와 연결되어야 하며 해당 관리 평면을 통해 구성됩니다. 자체 호스팅 게이트웨이는 다음을 위해 Azure에 대한 연결을 사용합니다.

-   1분마다 하트비트 메시지를 전송하여 상태 보고
-   구성 업데이트를 정기적으로 확인하고(10초 간격) 사용할 수 있을 때마다 적용
-   요청 로그 및 메트릭을 Azure Monitor로 전송(전송하도록 구성된 경우)
-   이벤트를 Application Insights로 전송(전송하도록 구성된 경우)

Azure에 대한 연결이 끊기면 자체 호스팅 게이트웨이가 구성 업데이트를 수신하거나, 상태를 보고하거나, 원격 분석을 업로드할 수 없습니다.

자체 호스팅 게이트웨이는 "일시적 장애에도 안정을 유지(fail static)"하도록 설계되었으며 Azure에 대한 연결이 일시적으로 끊겨도 안정을 유지할 수 있습니다. 로컬 구성 백업을 사용하거나 사용하지 않고 배포할 수 있습니다. 전자의 경우 자체 호스팅 게이트웨이가 컨테이너 또는 Pod에 연결된 영구 볼륨에 다운로드한 최신 구성의 백업 복사본을 정기적으로 저장합니다.

구성 백업이 해제된 상태에서 Azure에 대한 연결이 중단되는 경우:

-   실행 중인 자체 호스팅 게이트웨이가 구성의 메모리 내 복사본을 사용하여 계속 작동합니다.
-   중지된 자체 호스팅 게이트웨이는 시작할 수 없습니다.

구성 백업이 설정된 상태에서 Azure에 대한 연결이 중단되는 경우:

-   실행 중인 자체 호스팅 게이트웨이가 구성의 메모리 내 복사본을 사용하여 계속 작동합니다.
-   중지된 자체 호스팅 게이트웨이가 구성의 백업 복사본을 사용하여 시작할 수 있습니다.

연결이 복원되면 가동 중단의 영향을 받는 각 자체 호스팅 게이트웨이가 연결된 API Management 서비스에 자동으로 다시 연결되고 게이트웨이가 "오프라인" 상태였을 때 발생한 모든 구성 업데이트를 다운로드합니다.

## <a name="next-steps"></a>다음 단계

-   [이 항목에 대한 추가 배경 정보를 설명하는 백서 읽기](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Docker에 자체 호스팅 게이트웨이 배포](how-to-deploy-self-hosted-gateway-docker.md)
-   [Kubernetes에 자체 호스팅 게이트웨이 배포](how-to-deploy-self-hosted-gateway-kubernetes.md)
-   [Azure Arc 지원 Kubernetes 클러스터에 자체 호스팅 게이트웨이 배포](how-to-deploy-self-hosted-gateway-azure-arc.md)
