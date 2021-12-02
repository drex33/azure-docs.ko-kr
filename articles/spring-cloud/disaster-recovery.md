---
title: Azure Spring Cloud 지리적 재해 복구 | Microsoft Docs
description: 지역 중단으로부터 Spring Cloud 애플리케이션을 보호하는 방법을 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 64275d3b85fe9a045941b6211227a42db9fba306
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133438138"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud 재해 복구

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 가동 중지 시간이 발생 하지 않도록 Azure 스프링 클라우드의 응용 프로그램을 보호 하는 데 사용할 수 있는 몇 가지 전략을 설명 합니다.  지역 또는 데이터 센터에서 지리적 재해로 인한 가동 중지 시간이 발생할 수 있지만 신중하게 계획하여 고객에 미치는 영향을 완화할 수 있습니다.

## <a name="plan-your-application-deployment"></a>애플리케이션 배포 계획

Azure 스프링 클라우드의 응용 프로그램은 특정 지역에서 실행 됩니다.  Azure는 전 세계 여러 지역에서 운영됩니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지리적 위치 내의 영역입니다.  각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. Azure는 지역 쌍에서 플랫폼 업데이트(계획된 유지 관리)를 직렬화하여 한 번에 각 쌍의 한 지역만 업데이트되도록 합니다. 여러 지역에 영향을 주는 중단의 경우 각 쌍에서 하나 이상의 지역에 복구 우선 순위가 지정됩니다.

고가용성 및 재해 방지를 보장하려면 Spring Cloud 애플리케이션을 여러 지역에 배포해야 합니다.  Azure는 지역 쌍에 Spring Cloud 배포를 계획할 수 있도록 [쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md) 목록을 제공합니다.  마이크로 서비스 아키텍처를 디자인할 때 지역 가용성, Azure 쌍을 이루는 지역 및 서비스 가용성의 세 가지 주요 요소를 고려하는 것이 좋습니다.

* 지역 가용성:  네트워크 지연 및 전송 시간을 최소화하기 위해 사용자에게 가까운 지리적 영역을 선택합니다.
* Azure 쌍을 이루는 지역:  선택한 지리적 영역 내에서 쌍을 이루는 지역을 선택하여 필요한 경우 조정된 플랫폼 업데이트 및 우선 순위가 지정된 복구 작업을 보장합니다.
* 서비스 가용성:   쌍을 이루는 지역이 핫/핫, 핫/웜 또는 핫/콜드를 실행해야 하는지 여부를 결정합니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)는 DNS 기반 트래픽 부하 분산을 제공하며 네트워크 트래픽을 여러 지역에 분산할 수 있습니다.  Azure Traffic Manager를 사용하여 가장 가까운 Azure Spring Cloud 서비스 인스턴스를 고객에게 전달합니다.  최상의 성능 및 중복도를 위해 Azure Spring Cloud 서비스로 보내기 전에 Azure Traffic Manager를 통해 모든 애플리케이션 트래픽을 전달합니다.

여러 지역에서 실행 되는 Azure 스프링 클라우드의 응용 프로그램이 있는 경우 Azure Traffic Manager를 사용 하 여 각 지역의 응용 프로그램에 대 한 트래픽 흐름을 제어할 수 있습니다.  서비스 IP를 사용하여 각 서비스의 Azure Traffic Manager 엔드포인트를 정의합니다. 고객은 Azure Spring Cloud 서비스를 가리키는 Azure Traffic Manager DNS 이름에 연결해야 합니다.  Azure Traffic Manager는 정의된 엔드포인트에서 트래픽 부하를 분산합니다.  데이터 센터에 재해가 발생하면 Azure Traffic Manager는 해당 지역에서 해당 쌍으로 트래픽을 전달하여 서비스 연속성을 보장합니다.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Azure Spring Cloud용 Azure Traffic Manager 만들기

1. 두 지역에 Azure Spring Cloud를 만듭니다.
두 지역(미국 동부 및 서유럽)에 배포된 두 개의 Azure Spring Cloud 서비스 인스턴스가 필요합니다. Azure Portal를 사용 하 여 Azure 스프링 클라우드에서 기존 응용 프로그램을 시작 하 여 두 개의 서비스 인스턴스를 만듭니다. 각 인스턴스는 트래픽의 기본 및 장애 조치(failover) 엔드포인트로 사용됩니다.

**두 개의 서비스 인스턴스 정보:**

| 서비스 이름 | 위치 | 애플리케이션 |
|--|--|--|
| service-sample-a | 미국 동부 | gateway / auth-service / account-service |
| service-sample-b | 서유럽 | gateway / auth-service / account-service |

2. 서비스 팔로우용 사용자 지정 도메인([사용자 지정 도메인 문서](./tutorial-custom-domain.md))을 설정하여 두 개의 기존 서비스 인스턴스용 사용자 지정 도메인을 설정합니다. 설정한 후 서비스 인스턴스는 둘 다 사용자 지정 도메인 bcdr-test.contoso.com에 바인딩됩니다.

3. Traffic Manager 및 두 개의 엔드포인트를 만듭니다. [Azure Portal을 사용하여 Traffic Manager 프로필 만들기](../traffic-manager/quickstart-create-traffic-manager-profile.md)

Traffic Manager 프로필은 다음과 같습니다.
* Traffic Manager DNS 이름: `http://asc-bcdr.trafficmanager.net`
* 엔드포인트 프로필:

| 프로필 | Type | 대상 | 우선 순위 | 사용자 지정 헤더 설정 |
|--|--|--|--|--|
| 엔드포인트 A 프로필 | 외부 엔드포인트 | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| 엔드포인트 B 프로필 | 외부 엔드포인트 | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. DNS 영역에 CNAME 레코드를 만듭니다. bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net.

5. 이제 환경이 완전하게 설정되었습니다. 고객은 bcdr-test.contoso.com을 통해 앱에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure 스프링 클라우드에서 첫 번째 스프링 부팅 앱 배포](./quickstart.md)
