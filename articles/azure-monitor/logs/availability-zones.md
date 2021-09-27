---
title: Azure Monitor의 가용성 영역
description: Azure Monitor의 가용성 영역
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: 6e10ace3ca0932cf5803719429a68b89a4118dfa
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059324"
---
# <a name="availability-zones-in-azure-monitor"></a>Azure Monitor의 가용성 영역

[Azure 가용성 영역은](../../availability-zones/az-overview.md) 데이터 센터 오류로부터 애플리케이션 및 데이터를 보호하고 Application Insights 같은 Azure Monitor 기능 및 Log Analytics 작업 영역에 의존하는 기타 기능에 대한 복원력을 제공할 수 있습니다. 작업 영역이 가용성 영역에 연결되면 특정 데이터 센터가 오작동하거나 완전히 다운되더라도 해당 지역의 다른 영역 가용성에 의존하여 활성 상태를 유지하고 작동합니다. 대체 영역으로 전환하거나 인시던트를 파악하기 위해 아무것도 수행할 필요가 없습니다. 


## <a name="regions"></a>영역
가용성 영역이 있는 Azure 지역은 [Azure의 지역 및 가용성 영역](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)을 참조하세요. Azure Monitor에서는 현재 다음 지역을 지원합니다. 

- 미국 동부 2
- 미국 서부 2

## <a name="dedicated-clusters"></a>Dedicated 클러스터
Azure Monitor에서 가용성 영역을 지원하려면 [Azure Monitor 전용 클러스터](logs-dedicated-clusters.md)에 연결된 Log Analytics 작업 영역이 있어야 합니다. 전용 클러스터는 가용성 영역을 포함하여 Azure Monitor 로그의 고급 기능을 사용 가능하게 하는 배포 옵션입니다.

일부 전용 클러스터에서는 가용성 영역을 사용할 수 없습니다. 2020년 10월 중순 이후에 만든 전용 클러스터는 생성 시 가용성 영역을 지원하도록 설정할 수 있습니다. 해당 날짜 이후에 만든 새 클러스터는 기본적으로 Azure Monitor가 지원하는 지역의 가용성 영역에 사용하도록 설정됩니다.


> [!NOTE]
> Application Insights 리소스는 작업 영역 기반이 고 작업 영역에서 전용 클러스터를 사용하는 경우에만 가용성 영역을 사용할 수 있습니다. Classic Application Insights 리소스는 가용성 영역을 사용할 수 없습니다.


## <a name="determine-current-cluster-for-your-workspace"></a>작업 영역의 현재 클러스터 결정
작업 영역의 현재 작업 영역 링크 상태를 확인하려면 [CLI, PowerShell 또는 REST](logs-dedicated-clusters.md#check-workspace-link-status)를 사용하여 [클러스터 세부 정보](logs-dedicated-clusters.md#check-cluster-provisioning-status)를 검색하세요. 클러스터에서 가용성 영역을 사용하는 경우 값이 `true`인 `isAvailabilityZonesEnabled`라는 속성이 있습니다. 클러스터를 만든 후에는 이 속성을 변경할 수 없습니다.

## <a name="create-dedicated-cluster-with-availability-zone"></a>가용성 영역을 사용하여 전용 클러스터 만들기
가용성 영역을 지원하는 지역에서 [새 전용 클러스터를 생성](logs-dedicated-clusters.md#create-a-dedicated-cluster)하여 작업 영역을 가용성 영역으로 이동합니다. 클러스터는 가용성 영역에 사용하도록 자동으로 설정됩니다. 그런 다음 [작업 영역을 새 클러스터에 링크](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster)합니다.

> [!IMPORTANT]
> 가용성 영역은 생성 시 클러스터에 정의되며 수정할 수 없습니다.

새 클러스터로 전환하는 프로세스는 점진적일 수 있습니다. 데이터가 제거될 때까지 이전 클러스터를 제거하지 마세요. 예를 들어 작업 영역 보존이 60일로 설정된 경우 이전 클러스터를 제거하기 전에 해당 기간 동안 계속 실행하는 것이 좋습니다.

작업 영역에 대한 모든 쿼리는 단일 통합 결과 세트를 제공하기 위해 필요한 대로 두 클러스터를 모두 쿼리합니다. 즉, 통합 문서 및 대시보드와 같은 작업 영역에 의존하는 모든 Azure Monitor 기능은 두 클러스터의 데이터를 기반으로 하는 전체 통합 결과 집합을 계속 가져옵니다.

## <a name="billing"></a>결제
[전용 클러스터 사용 비용](logs-dedicated-clusters.md#create-a-dedicated-cluster)이 있습니다. 500GB의 일일 용량 예약이 필요합니다. 

이미 전용 클러스터가 있고 데이터 액세스를 위해 유지하기로 선택한 경우 두 전용 클러스터에 대한 요금이 부과됩니다. 2021년 8월 4일부터 전용 클러스터에 필요한 최소 용량 예약이 1000GB/일별에서 500GB/일별로 감소하므로 요금을 줄이기 위해 이전 클러스터에 최소값을 적용하는 것이 좋습니다.

새 클러스터는 구성하는 동안 이중으로 청구되지 않도록 첫 번째 날에는 요금이 청구되지 않습니다. 마이그레이션이 완료되기 전에 수집된 데이터만 마이그레이션 날짜에 요금이 계속 청구됩니다. 


## <a name="next-steps"></a>다음 단계

- 사용자가 Log Analytics에서 쿼리 팩과 상호 작용하는 방법을 보려면 [Azure Monitor Log Analytics에서 쿼리 사용](queries.md)을 참조하세요.

