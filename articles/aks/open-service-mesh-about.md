---
title: 서비스 메시 열기
description: AKS(Azure Kubernetes Service)의 OSM(Open Service Mesh)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.author: pgibson
ms.openlocfilehash: 68bb34bc5619523fbc9db0feaacc8396b7f01b7e
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753952"
---
# <a name="open-service-mesh-aks-add-on"></a>Open Service Mesh AKS 추가 기능

[OSM(Open Service Mesh)](https://docs.openservicemesh.io/)은 확장 가능한 네이티브 클라우드 서비스 메시이며, 사용자는 매우 동적인 마이크로 서비스 환경을 일관되게 관리하고, 안전하게 만들고, 즉시 사용 가능한 가시성 기능을 사용할 수 있습니다.

OSM은 Kubernetes에서 Envoy 기반 컨트롤 플레인을 실행하고, [SMI](https://smi-spec.io/) API를 사용하여 구성될 수 있으며, 애플리케이션의 각 인스턴스 옆에 사이드카 컨테이너로 Envoy 프록시를 삽입하여 작동합니다. Envoy 프록시는 액세스 제어 정책에 대한 규칙을 포함 및 실행하고 라우팅 구성을 구현하며 메트릭을 캡처합니다. 컨트롤 플레인은 정책 및 라우팅 규칙이 최신 상태를 유지하도록 프록시를 지속적으로 구성하고 프록시가 정상 상태인지 확인합니다.

OSM 프로젝트는 Microsoft에서 시작되었으며 그 이후 [CNCF(Cloud Native Computing Foundation)의](https://www.cncf.io/)관리가 되었습니다. OSM 오픈 소스 프로젝트는 기능 및 프로젝트에 대한 기여를 환영하고 권장하는 커뮤니티 주도 협업으로 계속 진행됩니다. 참여 방법에 대한 [기여자 참가자](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md) 가이드를 참조하세요.

## <a name="capabilities-and-features"></a>기능 및 기능

OSM은 AKS(Azure Kubernetes Service) 클러스터에 대한 네이티브 클라우드 서비스 메시를 제공하는 다음과 같은 기능 및 기능 집합을 제공합니다.

- OSM은 AKS 서비스에 통합되어 AKS 기능 추가 기능의 편의를 통해 완벽하게 지원되고 관리되는 서비스 메시 환경을 제공합니다.

- mTLS를 사용하도록 설정하여 서비스 간 통신 보호

- Envoy 프록시에 자동 사이드카 주입을 사용하도록 설정하여 애플리케이션을 메시에 쉽게 온보딩

- 배포 시 트래픽 이동에 대한 쉽고 투명한 구성

- 서비스에 대한 세부적인 액세스 제어 정책을 정의하고 실행하는 기능

- 디버깅 및 모니터링 서비스에 대한 애플리케이션 메트릭에 대한 가시성 및 인사이트

- 플러그형 인터페이스를 사용하여 외부 인증서 관리 서비스/솔루션과의 통합

## <a name="scenarios"></a>시나리오

OSM은 다음과 같은 시나리오를 통해 AKS 배포를 지원할 수 있습니다.

- 클러스터에 배포된 서비스 엔드포인트 간의 암호화된 통신 제공

- 메시의 HTTP/HTTPS 및 TCP 트래픽 모두에 대한 트래픽 권한 부여

- A/B 또는 카나리아 배포에 대해 둘 이상의 서비스 간에 가중치가 적용된 트래픽 컨트롤 구성

- 애플리케이션 트래픽에서 KPI 수집 및 보기

## <a name="osm-service-quotas-and-limits-preview"></a>OSM 서비스 할당량 및 제한(미리 보기)

서비스 할당량 및 한도에 대한 OSM 미리 보기 제한은 AKS [할당량 및 지역 제한 페이지](./quotas-skus-regions.md)에서 찾을 수 있습니다.

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
