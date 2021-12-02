---
title: 유지 관리 및 업데이트
description: Azure에서 실행 중인 가상 머신의 유지 관리 및 업데이트 개요입니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: shants
ms.openlocfilehash: bff5d44aad7bc0426c1eae5409071796003f6344
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133439297"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure에서 가상 머신 유지 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure에서는 가상 머신에 대한 호스트 인프라의 안정성, 성능 및 보안을 개선하기 위해 주기적으로 플랫폼을 업데이트합니다. 이러한 업데이트의 용도는 호스팅 환경의 소프트웨어 구성 요소 패치에서 네트워킹 구성 요소 업그레이드 또는 하드웨어 서비스 해제에 이르기까지 다양합니다. 

업데이트는 호스트된 VM에 거의 영향을 주지 않습니다. 업데이트에 영향을 주는 경우 Azure는 가장 영향이 적은 업데이트 방법을 선택합니다.

- 업데이트에 재부팅이 필요하지 않으면, 호스트가 업데이트되는 동안 VM이 일시 중지되거나, VM이 이미 업데이트된 호스트로 실시간 마이그레이션됩니다. 
- 유지 관리에 재부팅이 필요한 경우 계획된 유지 관리에 대한 알림이 제공됩니다. Azure는 사용자가 편리한 시간에 유지 관리를 직접 시작할 수 있는 시간 범위도 제공합니다. 유지 관리가 긴급한 경우를 제외하고 셀프 유지 관리 기간은 일반적으로 35일입니다. Azure는 계획된 플랫폼 유지 관리를 위해 VM을 다시 부팅해야 하는 경우를 줄이기 위해 기술에 투자하고 있습니다. 계획된 유지 관리를 관리하는 방법에 대한 지침은 Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용하여 계획된 유지 관리 알림 처리를 참조하세요.

이 페이지에서는 Azure에서 두 가지 유형의 유지 관리를 모두 수행하는 방법을 설명합니다. 계획되지 않은 이벤트(중단)에 대한 자세한 내용은 [Windows용 VM의 가용성 관리](./availability.md) 또는 [Linux](./availability.md)에 대한 관련 문서를 참조하세요.

VM 내에서 [Linux](./linux/scheduled-events.md)용 또는 [Windows용 Scheduled Events를 사용](./windows/scheduled-events.md)하여 예정된 유지 관리에 대한 알림을 받을 수 있습니다.



## <a name="maintenance-that-doesnt-require-a-reboot"></a>재부팅이 필요 없는 유지 관리

대부분의 플랫폼 업데이트는 고객 VM에 영향을 주지 않습니다. 영향을 주지 않는 업데이트가 불가능한 경우 Azure는 고객 VM에 미치는 영향이 가장 적은 업데이트 메커니즘을 선택합니다. 

영향이 거의 없는 유지 관리는 VM을 10초 미만으로 일시 중지합니다. 경우에 따라 Azure는 메모리 보존 유지 관리 메커니즘을 사용합니다. 이러한 메커니즘은 일반적으로 30초 동안 VM을 일시 중지하고 RAM에서 메모리를 유지합니다. 그런 다음, VM이 다시 시작되고 클록이 자동으로 동기화됩니다. 

메모리 보존 유지 관리는 90%를 초과하는 Azure VM에서 작동합니다. G, L, M, N 및 H 시리즈에서는 작동하지 않습니다. Azure는 실시간 마이그레이션 기술을 점점 더 많이 사용하며 메모리 보존 유지 관리 메커니즘을 개선하여 일시 중지 기간을 줄이고 있습니다.  

다시 부팅할 필요가 없는 유지 관리 작업은 한 번에 하나의 장애 도메인에 적용됩니다. 플랫폼 모니터링 도구에서 경고 상태 신호를 수신하면 중지됩니다. 

이러한 종류의 업데이트는 일부 애플리케이션에 영향을 줄 수 있습니다. VM을 다른 호스트로 실시간 마이그레이션하는 경우 일부 중요한 워크로드는 VM 일시 중지까지 몇 분간 성능이 약간 저하될 수 있습니다. VM 유지 관리를 준비하고 Azure 유지 관리 중 영향을 줄이려면 해당 애플리케이션에 [Linux](./linux/scheduled-events.md) 또는 [Windows용 Scheduled Events를 사용](./windows/scheduled-events.md)해보십시오. 

영향을 주지 않고 재부팅 없는 업데이트를 비롯한 모든 유지 관리 활동을 보다 효과적으로 제어하려면 유지 관리 제어 기능을 사용하면 됩니다. [Azure Dedicated Hosts](./dedicated-hosts.md) 또는 [격리된 VM](../security/fundamentals/isolation-choices.md)을 사용해야 합니다. 유지 관리 제어는 모든 플랫폼 업데이트를 건너뛰고 35일 롤링 기간 내에 사용자가 원하는 시간에 업데이트를 적용할 수 있는 옵션을 제공합니다. 자세한 내용은 [유지 관리 제어 및 Azure CLI를 사용하여 업데이트 제어](maintenance-control.md)를 참조 하세요.


### <a name="live-migration"></a>실시간 마이그레이션

실시간 마이그레이션은 다시 부팅이 필요하지 않으며 VM에 대한 메모리를 보존하는 작업입니다. 일시 중지 또는 중지가 일반적으로 5초 이하로 발생합니다. G, M, N, H 시리즈를 제외하고 모든 IaaS(infrastructure as a service) VM은 실시간 마이그레이션에 적합합니다 적합한 VM은 Azure 집합에 배포된 IaaS VM의 90%를 초과합니다. 

> [!NOTE]
> 다시 부팅할 필요가 없는 실시간 마이그레이션 작업에 대한 알림은 Azure Portal에서 수신되지 않습니다. 다시 부팅할 필요가 없는 실시간 마이그레이션 목록을 보려면 [예약된 이벤트를 쿼리](./windows/scheduled-events.md#query-for-events)합니다.

Azure 플랫폼은 다음과 같은 시나리오에서 실시간 마이그레이션을 시작합니다.
- 계획된 유지 보수
- 하드웨어 오류
- 할당 최적화

일부 계획된 유지 관리 시나리오는 실시간 마이그레이션을 사용하며, 실시간 마이그레이션 작업이 시작되는 시기는 Scheduled Events를 사용하여 미리 알 수 있습니다.

Azure Machine Learning 알고리즘이 임박한 하드웨어 오류를 예측하거나 VM 할당을 최적화하려는 경우 실시간 마이그레이션을 사용하여 VM을 이동할 수도 있습니다. 성능이 저하된 하드웨어 인스턴스를 감지하는 예측 모델링에 대한 자세한 내용은 [예측 기계 학습 및 실시간 마이그레이션을 사용하여 Azure VM 복원력 향상](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)을 참조하세요. 실시간 마이그레이션 알림은 Azure Portal에서 모니터 및 Service Health 로그 및 Scheduled Events에 나타납니다(해당 서비스를 사용하는 경우).



## <a name="maintenance-that-requires-a-reboot"></a>재부팅이 필요한 유지 관리

드물긴 하지만 계획된 유지 관리를 위해 VM을 다시 부팅해야 하는 경우 미리 알림이 제공됩니다. 계획된 유지 관리에는 셀프 서비스 단계와 예약된 유지 관리 단계라는 두 가지 단계가 있습니다.

일반적으로 4주 동안 지속되는 *셀프 서비스 단계* 에서는 사용자가 VM에 대한 유지 관리를 시작합니다. 셀프 서비스의 일부로 각 VM을 쿼리하여 해당 상태와 마지막 유지 관리 요청의 결과를 확인할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 VM이 이미 업데이트된 노드에 다시 배포됩니다. VM이 다시 부팅되기 때문에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다.

셀프 서비스 유지 관리 중에 오류가 발생하면 작업이 중지되고 VM이 업데이트되지 않으며 셀프 서비스 유지 관리를 다시 시도할 수 있는 옵션이 제공됩니다. 

셀프 서비스 단계가 끝나면 예약된 유지 관리 단계가 시작됩니다. 이 단계 중에도 유지 관리 단계를 계속 쿼리할 수 있지만 직접 유지 관리를 시작할 수는 없습니다.

재부팅이 필요한 유지 관리를 관리하는 방법에 대한 자세한 내용은 Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용하여 **계획된 유지 관리 알림 처리** 를 참조하세요. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>예약된 유지 관리 중 가용성 고려 사항 

예약된 유지 관리 단계까지 기다리기로 결정한 경우 VM의 고가용성을 유지 관리하기 위해 고려해야 할 사항이 몇 가지 있습니다. 

#### <a name="paired-regions"></a>쌍을 이루는 지역

Azure 지역은 동일한 지역 주변 내에서 다른 지역과 쌍을 이루고, 함께 지역 쌍을 만듭니다. 예약된 유지 관리 단계 중에 Azure는 지역 쌍의 단일 지역에 있는 VM만 업데이트합니다. 예를 들어 미국 중북부에 있는 VM을 업데이트하는 동안 Azure는 미국 중남부의 VM을 동시에 업데이트하지 않습니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](../availability-zones/cross-region-replication-azure.md)을 참조하세요.

#### <a name="availability-zones"></a>가용성 영역

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 

가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

각 인프라 업데이트는 단일 지역 내에서 영역별로 영역을 롤아웃합니다. 하지만 영역 1에서 배포를 진행하고 영역 2에서 다른 배포를 동시에 진행할 수 있습니다. 일부 배포는 직렬화되지 않습니다. 하지만 단일 배포는 한 번에 하나의 영역만 롤아웃하여 위험을 줄입니다.

#### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

**유연한** 오케스트레이션 모드의 가상 머신 확장 집합은 Azure 컴퓨팅 리소스로, 균일한 오케스트레이션 모드에서 가상 머신 확장 집합의 확장성을 가용성 집합의 지역 가용성 보장과 결합할 수 있습니다.

유연한 오케스트레이션을 사용하면 인스턴스가 여러 영역에 분산되는지 아니면 단일 지역 내의 장애 도메인에 분산되는지를 선택할 수 있습니다. 

#### <a name="availability-sets-and-uniform-scale-sets"></a>가용성 집합 및 균일한 확장 집합

Azure VM에서 워크로드를 배포할 때 애플리케이션에 고가용성을 제공하기 위해 가용성 세트 내에 VM을 만들 수 있습니다. 가용성 집합을 사용하면 재부팅이 필요한 중단 또는 유지 관리 이벤트 중 최소 하나 이상의 VM을 사용할 수 있도록 보장할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 업데이트 도메인에 걸쳐 분산됩니다. 예약된 유지 관리 중에는 한 번에 하나의 업데이트 도메인만 업데이트됩니다. 업데이트 도메인이 반드시 순차적으로 업데이트되는 것은 아닙니다. 

**균일한** 오케스트레이션 모드의 가상 머신 *확장 집합은* 동일한 VM 집합을 단일 리소스로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. 확장 집합은 가용성 집합의 VM과 같은 UD에 걸쳐 자동으로 배포됩니다. 가용성 집합과 마찬가지로, 균일한 확장 집합을 사용하는 경우 예약된 유지 관리 중에 지정된 시간에 하나의 UD만 업데이트됩니다.

VM의 고가용성 설정에 대한 자세한 내용은 [Windows용 VM의 가용성 관리](./availability.md) 또는 [Linux](./availability.md)용 해당 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계 

[Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용하여 계획된 유지 관리를 관리할 수 있습니다.
