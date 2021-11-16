---
title: Service Fabric 관리형 클러스터
description: Service Fabric 관리형 클러스터는 배포 및 클러스터 관리 환경을 간소화하는 Azure Service Fabric 클러스터 리소스 모델의 진화입니다.
ms.topic: overview
ms.date: 10/22/2021
ms.openlocfilehash: 5c94254e470e625a844b345c452e252fcd69b626
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308127"
---
# <a name="service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터

Service Fabric 관리형 클러스터는 배포 및 클러스터 관리 환경을 간소화하는 Azure Service Fabric 클러스터 리소스 모델의 진화입니다.

기존 Service Fabric 클러스터에 대한 ARM(Azure Resource Model) 템플릿은 클러스터와 서비스가 제대로 작동하기 위해 여러 지원 리소스를 함께 사용하여 클러스터 리소스를 정의해야 합니다. 이 모든 구성 요소는 배포 시 클러스터 수명 주기 전체에 걸쳐 올바르게 “연결”되어야 합니다. 이와 대조적으로 Service Fabric 관리형 클러스터의 캡슐화 모델은 단일 *Service Fabric 관리형 클러스터* 리소스로 구성됩니다. 클러스터에 대한 모든 기본 리소스는 사용자를 대신하여 Azure에서 추상화되고 관리됩니다.

**Service Fabric 기존 클러스터 모델**
![Service Fabric 기존 클러스터 모델][sf-composition]

**Service Fabric 관리형 클러스터 모델**
![Service Fabric 캡슐화된 클러스터 모델][sf-encapsulation]

크기와 복잡성 측면에서 Service Fabric 관리형 클러스터에 대한 ARM 템플릿은 일반적인 Service Fabric 클러스터를 정의하는 데 필요한 1000줄 대비 약 100줄의 JSON입니다.

| Service Fabric 리소스 | Service Fabric 관리형 클러스터 리소스 |
|----------|-----------|
| Service Fabric 클러스터 | Service Fabric 관리형 클러스터 |
| 가상 머신 확장 집합 | |
| 부하 분산 장치 | |
| 공용 IP 주소 | |
| 스토리지 계정 | |
| 가상 네트워크 | |

## <a name="service-fabric-managed-cluster-advantages"></a>Service Fabric 관리형 클러스터 장점
Service Fabric 관리형 클러스터는 기존 클러스터에 비해 다음과 같은 여러 가지 이점을 제공합니다.

**간소화된 클러스터 배포 및 관리**
- 단일 Azure 리소스 배포 및 관리
- 클러스터 인증서 관리 및 90일 자동 순환
- 간소화된 크기 조정 작업
- 자동 OS 이미지 업그레이드 지원
- 현재 위치 OS SKU 변경 지원

**작업 오류 방지**
- 기본 리소스와의 구성 불일치 방지
- 안전하지 않은 작업의 차단(예: 시드 노드 삭제)

**기본 모범 사례**
- 단순화된 안정성 및 내구성 설정

클러스터에 필요한 기본 리소스의 비용 외에 Service Fabric 관리형 클러스터에 대한 추가 비용은 없으며 관리형 클러스터에도 동일한 Service Fabric SLA가 적용됩니다.

> [!NOTE]
> 기존 Service Fabric 클러스터에서 관리형 클러스터로의 마이그레이션 경로가 없습니다. 이 새 리소스 유형을 사용하려면 새 Service Fabric 관리형 클러스터를 만들어야 합니다.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric 관리형 클러스터 SKU

Service Fabric 관리형 클러스터는 기본 및 표준 SKU에서 모두 사용할 수 있습니다.

| 기능 | Basic | Standard |
| ------- | ----- | -------- |
| 네트워크 리소스([Load Balancer](../load-balancer/skus.md), [공용 IP](../virtual-network/ip-services/public-ip-addresses.md)용 SKU) | Basic | Standard |
| 최소 노드(VM 인스턴스) 수 | 3 | 5 |
| 노드 유형별 최대 노드 수 | 100 | 1000 |
| 최대 노드 유형 개수 | 1 | 20 |
| 노드 형식 추가/제거 | 예 | 예 |
| 영역 중복 | 예 | 예 |

## <a name="feature-support"></a>기능 지원

관리형 클러스터의 기능은 계속 확장됩니다. 자세한 내용은 [관리형 클러스터 구성 옵션 설명서](how-to-managed-cluster-configuration.md) 또는 managedClusters [Bicep 및 ARM 템플릿](/azure/templates/microsoft.servicefabric/allversions)을 참조하세요.


## <a name="next-steps"></a>다음 단계

Service Fabric 관리형 클러스터를 시작하려면 빠른 시작:

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 만들기](quickstart-managed-cluster-template.md)

그리고 [관리형 클러스터를 구성하는 방법](how-to-managed-cluster-configuration.md)을 참조하세요.

[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png