---
title: Azure Batch의 비용 분석 가져오기 및 예산 설정
description: Batch 워크로드를 실행하는 데 사용되는 기본 컴퓨팅 리소스 및 소프트웨어 라이선스의 비용 분석을 수행하고 예산을 설정하며 비용을 절감하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 0e4c0b6235537d1e5c36e7adf997516d0f966d76
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532573"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Azure Batch의 비용 분석 가져오기 및 예산 설정

본 토픽을 통해 Azure Batch에 관련될 수 있는 비용, Batch 풀 또는 계정에 대한 예산을 설정하는 방법, Batch 워크로드에 대한 비용을 절감하는 방법을 이해할 수 있습니다.

## <a name="understand-costs-associated-with-batch-resources"></a>Batch 리소스와 관련된 비용 이해

Batch 워크로드를 실행하는 데 사용되는 기본 컴퓨팅 리소스 및 소프트웨어 라이선스에 대한 요금이 있을 수 있지만 Azure Batch 사용 자체에 대한 비용은 없습니다. 비용은 주로 풀의 VM(가상 머신), VM에서의 데이터 전송 또는 클라우드에 저장된 입력 또는 출력 데이터로 인해 발생합니다.

### <a name="virtual-machines"></a>가상 머신

가상 머신은 Batch 처리에 사용되는 가장 중요한 리소스입니다. Batch의 VM 사용 비용은 유형, 수량 및 사용 기간을 기준으로 계산됩니다. VM 청구 옵션에는 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [예약](../cost-management-billing/reservations/save-compute-costs-reservations.md)(선불) 등이 있습니다. 두 결제 옵션의 이점은 컴퓨팅 워크로드에 따라 달라지며, 청구서에 다르게 적용됩니다.

[가상 머신 구성](nodes-and-pools.md#virtual-machine-configuration)을 사용하여 만든 풀의 각 VM에는 Azure 관리 디스크를 사용하는 OS 디스크가 연결되어 있습니다. Azure 관리 디스크에는 추가 비용이 부과되며 다른 디스크 성능 계층에도 다른 비용이 부과됩니다.

### <a name="storage"></a>스토리지

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드(VM)에 애플리케이션을 배포하는 경우 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 또한 리소스 파일 및 기타 로그 데이터와 같은 입력 또는 출력 파일의 스토리지에 대한 요금도 청구됩니다.

일반적으로 Batch와 연결된 스토리지 데이터의 비용이 컴퓨팅 리소스 비용보다 훨씬 적습니다.

### <a name="networking-resources"></a>네트워킹 리소스

Batch 풀은 네트워킹 리소스를 사용하며, 그중 일부에는 관련 비용이 발생합니다. 특히 가상 머신 구성 풀의 경우 표준 부하 분산 장치가 사용되며, 고정 IP 주소를 사용해야 합니다. Batch에서 사용하는 부하 분산 장치는 사용자 구독 모드로 구성된 [계정](accounts.md#batch-accounts)에 표시되지만 Batch 서비스 모드의 계정에는 표시되지 않습니다.

표준 부하 분산 장치는 Batch 풀 VM에서 들어오고 나가는 모든 데이터에 요금을 부과합니다. 풀 노드(예: 가져오기 작업/노드 파일)에서 데이터를 검색하는 Batch API, 작업 애플리케이션 패키지, 리소스/출력 파일 및 컨테이너 이미지를 선택해도 요금이 발생합니다.

### <a name="additional-services"></a>서비스

Batch 솔루션과 함께 사용하는 서비스에 따라 추가 요금이 발생할 수 있습니다. 각 추가 서비스의 비용을 확인하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요. 관련 비용이 있을 수 있는 Batch와 함께 일반적으로 사용되는 서비스는 다음과 같습니다.

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- 그래픽 애플리케이션이 있는 VM

## <a name="view-cost-analysis-and-create-budgets"></a>비용 분석 보기 및 예산 만들기

[Azure Cost Management](/cost-management-billing/cost-management-billing-overview.md) 사용하면 지출을 계획, 분석 및 줄여 클라우드 투자를 최대화할 수 있습니다. Azure Batch 포함하여 모든 Azure 서비스에 대한 사용 비용을 사용할 수 있습니다. 일괄 처리 비용을 보고 필터링하고, 향후 비용을 예측하고, 해당 한도에 도달하면 경고를 사용하여 지출 한도를 설정할 수 있습니다.

Azure Portal에서 Batch 풀 또는 Batch 계정에 대한 예산 및 지출 경고를 만들 수 있습니다. 예산 및 경고는 지출 경고가 지연되고 예산을 약간 초과할 수 있지만 초과 지출의 위험을 관련자에게 알리는 데 유용합니다.

다음 스크린샷은 모든 Batch 계정과 연결된 누적 **비용만** 표시하도록 필터링된 구독에 대한 비용 분석 보기의 예를 보여줍니다. 낮은 차트는 선택한 기간의 총 비용을 소비된 서비스, 위치 및 미터별로 분류할 수 있는 방법을 보여 줍니다. 이는 예제이며 구독에 대해 볼 수 있는 비용을 반영하지는 않지만 Batch 풀 노드에 할당된 가상 머신에 대한 가장 큰 비용은 일반적입니다.

:::image type="content" source="media/batch-budget/subscription-cost-analysis.png" alt-text="구독의 모든 Batch 계정에 대한 Azure Portal 비용 분석을 보여주는 스크린샷":::

**리소스** 필터를 지정하여 추가 수준의 비용 분석 세부 정보를 얻을 수 있습니다. Batch 계정의 경우 이러한 값은 Batch 계정 이름과 풀 이름입니다. 이렇게 하면 특정 풀, 여러 풀 또는 하나 이상의 계정에 대한 비용을 볼 수 있습니다.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Batch 풀에 대한 비용 분석 보기

#### <a name="batch-service-pool-allocation-mode"></a>Batch 서비스 풀 할당 모드

Batch 서비스 풀 할당 모드로 만든 Batch 계정의 경우:

1. Azure Portal에서 **Cost Management + Billing** 을 입력하거나 선택합니다.
1. **청구 범위** 섹션에서 구독을 선택합니다.
1. **Cost Management** 에서 **비용 분석** 을 선택합니다.
1. **필터 추가** 를 선택합니다. 첫 번째 드롭다운에서 **리소스** 를 선택합니다.
1. 두 번째 드롭다운 목록에서 Batch 풀을 선택합니다. 풀을 선택하면 풀에 대한 비용 분석이 표시됩니다. 아래 스크린샷은 예제 데이터를 보여줍니다.
   :::image type="content" source="media/batch-budget/pool-cost-analysis.png" alt-text="Azure Portal Batch 풀의 비용 분석을 보여주는 스크린샷":::

비용 분석 결과는 이 비용에 영향을 주는 리소스뿐만 아니라 풀의 비용도 보여 줍니다. 이 예에서는 풀에 사용된 VM이 가장 비용이 많이 드는 리소스입니다.

> [!NOTE]
> 이 예제의 풀은 Virtual **Machine 구성을** 사용합니다. 이 구성은 [대부분의 풀에 권장되며](batch-pool-cloud-service-to-virtual-machine-configuration.md) Virtual Machines 가격 책정 구조에 따라 요금이 청구됩니다. **Cloud Services 구성** 을 사용하는 풀은 Cloud Services 가격 책정 체계에 따라 요금이 청구됩니다.

[태그를](../azure-resource-manager/management/tag-resources.md) Batch 계정과 연결하여 추가 비용 필터링에 태그를 사용할 수 있습니다. 예를 들어 태그를 사용하여 프로젝트, 사용자 또는 그룹 정보를 Batch 계정과 연결할 수 있습니다. 태그는 현재 Batch 풀과 연결될 수 없습니다.

#### <a name="user-subscription-pool-allocation-mode"></a>사용자 구독 풀 할당 모드

사용자 구독 풀 할당 모드로 만든 Batch 계정의 경우:

1. Azure Portal에서 **Cost Management + Billing** 을 입력하거나 선택합니다.
1. **청구 범위** 섹션에서 구독을 선택합니다.
1. **Cost Management** 에서 **비용 분석** 을 선택합니다.
1. **필터 추가** 를 선택합니다. 첫 번째 드롭다운에서 **태그를** 선택합니다.
1. 두 번째 드롭다운에서 **poolname 을** 선택합니다.
1. 세 번째 드롭다운에서 Batch 풀을 선택합니다. 풀을 선택하면 풀에 대한 비용 분석이 표시됩니다. 아래 스크린샷은 예제 데이터를 보여줍니다.
   :::image type="content" source="media/batch-budget/user-subscription-pool.png" alt-text="Azure Portal 사용자 구독 Batch 풀의 비용 분석을 보여주는 스크린샷":::

사용자 구독 Batch 계정의 모든 풀에 대한 비용 데이터를 보려면 두 번째 드롭다운에서 **batchaccountname을** 선택하고 세 번째 드롭다운에서 Batch 계정의 이름을 선택할 수 있습니다. 

> [!NOTE]
> 사용자 구독 Batch 계정에서 만든 풀은 **리소스** 필터 아래에 표시되지 않지만 서비스 이름 아래에서 "가상 머신"을 필터링할 때는 사용량이 계속 표시됩니다.

### <a name="create-a-budget-for-a-batch-pool"></a>Batch 풀에 대한 예산 만들기

60%, 80% 및 100%와 같은 다양한 예산 비율에 도달하면 예산을 만들고 비용 경고를 생성할 수 있습니다. 예산은 하나 이상의 필터를 지정할 수 있으므로 다양한 세분성에서 Batch 계정 비용을 모니터링하고 경고할 수 있습니다.

1. **비용 분석** 페이지에서 **예산: 없음** 을 선택합니다.
1. **새 예산 만들기** 를 선택합니다.
1. 나타나는 창을 사용하여 풀의 구체적인 예산을 구성합니다. 자세한 내용은 [자습서: Azure 예산 세우기 및 관리](../cost-management-billing/costs/tutorial-acm-create-budgets.md)를 참조하세요.

## <a name="minimize-costs-associated-with-azure-batch"></a>Azure Batch 관련된 비용 최소화

시나리오에 따라 비용을 최대한 줄일 수 있습니다. 해당 전략 중 하나 이상을 사용하여 워크로드의 효율성을 극대화하고 잠재적인 비용을 절감하는 것이 좋습니다.

### <a name="reduce-pool-node-use"></a>풀 노드 사용 줄이기

Batch 사용과 관련된 가장 큰 비용은 일반적으로 풀 노드에 할당된 가상 머신에서 발생하는 것입니다. Virtual Machine 구성 풀의 경우 VM OS 디스크에 사용되는 연결된 관리 디스크도 비용에 크게 영향을 줄 수 있습니다.

Batch 애플리케이션을 평가하여 풀 노드가 작업 태스크에서 잘 활용되고 있는지 또는 풀 노드가 예상 시간 이상 유휴 상태인지 확인합니다. 할당된 풀 노드 수를 줄이거나, 풀 노드 강화 속도를 줄이거나, 사용률을 높이기 위해 스케일 다운 속도를 높일 수 있습니다.

사용자 지정 모니터링 외에도 [Batch 메트릭은](batch-diagnostics.md#view-batch-metrics) 할당되었지만 유휴 상태인 노드를 식별하는 데 도움이 될 수 있습니다. Azure Portal Batch 모니터링 메트릭을 사용하여 볼 대부분의 풀 노드 상태에 대한 메트릭을 선택할 수 있습니다. 예를 들어 '유휴 노드 수' 및 '실행 중인 노드 수'를 확인하여 풀 노드가 얼마나 잘 활용되는지를 알 수 있습니다.

### <a name="ensure-pool-nodes-are-able-to-run-tasks"></a>풀 노드가 작업을 실행할 수 있는지 확인

풀에 대해 나열된 할당된 노드에는 일반적으로 비용이 발생하지만 풀 노드는 'unusable' 또는 'starttaskfailed'와 같은 작업을 실행할 수 없는 상태에 있을 수 있습니다. Batch API 또는 메트릭을 사용하여 이 범주의 VM을 모니터링하고 검색할 수 있습니다. 이러한 비정상 노드를 줄이거나 제거 하기 위해 이러한 상태에 대 한 이유를 확인 하 고 정정 작업을 수행할 수 있습니다.

### <a name="use-the-right-pool-node-vm-size"></a>올바른 풀 노드 VM 크기 사용

필요한 시간 내에 작업을 완료 하는 데 필요한 성능을 제공 하면서 Vm이 작업을 실행할 때 잘 활용 되도록 적절 한 VM 크기를 사용 하 고 있는지 확인 합니다. 풀 노드 Vm은 CPU 사용량이 낮은 경우와 같은 일부 상황에서 미달 사용할 수 있습니다. 저렴 한 가격으로 VM 크기를 선택 하 여 비용을 절약할 수 있습니다.

VM 사용률을 확인 하려면 태스크를 실행 하 여 성능 데이터를 볼 때 노드에 로그인 하거나 Application Insights와 같은 [모니터링 기능](monitoring-overview.md)을 사용 하 여 풀 노드에서 성능 데이터를 가져올 수 있습니다.

### <a name="use-pool-slots-to-reduce-node-requirements"></a>풀 슬롯을 사용 하 여 노드 요구 사항 줄이기

풀에 대해 여러 작업 슬롯을 지정할 수 있으므로 해당 태스크 수를 각 노드에서 병렬로 실행할 수 있습니다. 풀 작업 슬롯을 사용 하면 노드 크기를 더 크게 선택 하 고 노드에서 여러 작업을 병렬로 실행 하 여 풀에서 사용 되는 노드 수를 줄일 수 있습니다. 노드가 사용 미달 인 경우 슬롯을 사용 하 여 사용률을 늘릴 수 있습니다. 예를 들어 단일 스레드 작업 응용 프로그램의 경우 코어 당 슬롯 하나를 구성할 수 있습니다. 코어 보다 많은 슬롯을 사용할 수도 있습니다. 이는 한 가지 예를 들어 응용 프로그램에서 외부 서비스에 대 한 호출이 반환 될 때까지 대기 하는 경우에 적용할 수 있습니다.

[`taskSchedulingPolicy`](/rest/api/batchservice/pool/add#taskschedulingpolicy)로 설정 `pack` 하면 작업을 실행 하지 않는 노드를 더 쉽게 제거할 수 있도록 하 여 vm을 최대한 활용 하는 데 도움이 됩니다.

### <a name="use-low-priority-virtual-machines"></a>우선 순위가 낮은 가상 머신 사용

[우선 순위가 낮은 VM](batch-low-pri-vms.md)은 Azure에서 여분의 컴퓨팅 용량을 활용하여 Batch 워크로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Batch는 이러한 남는 용량을 사용하여 워크로드를 실행할 수 있습니다. 전용 VM 대신 우선 순위가 낮은 VM을 사용하면 비용을 크게 절감할 수 있습니다. 낮은 우선 순위 Vm은 할당할 수 있는 용량이 없을 수도 있고 선점 될 수도 있으므로 모든 워크 로드에 적합 하지 않습니다.

### <a name="use-ephemeral-os-disks"></a>사용 후 삭제 OS 디스크 사용

기본적으로 풀 노드는 관리 디스크를 사용 하 여 비용을 발생 시킵니다. 일부 VM 크기의 가상 머신 구성 풀은 관리 디스크와 관련 된 추가 비용을 방지 하기 위해 VM 캐시 또는 임시 SSD에서 OS 디스크를 만드는 사용 [후 삭제 os 디스크](create-pool-ephemeral-os-disk.md)를 사용할 수 있습니다.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>가상 머신 인스턴스에 대한 구매 예약

Batch를 장기간 사용하려는 경우 워크로드에 [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)를 사용하여 VM 비용을 절감할 수 있습니다. 예약 요금은 종량제 요금보다 훨씬 저렴합니다. 예약 없이 사용되는 가상 머신 인스턴스는 종량제 요금으로 청구됩니다. 예약을 구매하면 예약 할인이 적용됩니다. [Vm 인스턴스에](../virtual-machines/prepay-reserved-vm-instances.md)대해 1 년 또는 3 년 요금제로 커밋하면 [Batch 풀을 통해 사용](../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)되는 vm을 포함 하 여 vm 사용에 상당한 할인이 적용 됩니다.

예약 할인은 "사용-또는 분실" 이란 점에 유의 해야 합니다.  한 시간 동안 사용 되는 일치 하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실 됩니다. 사용 하지 않는 예약 시간은 전달 되지 않으므로 사용 되지 않는 경우 손실 됩니다. 일괄 처리 작업은 로드에 따라 할당 된 Vm의 수를 조정 하 고 부하가 없는 기간을 포함 하 여 다양 한 부하를 발생 시키는 경우가 많습니다. 따라서 예약 된 시간을 결정 해야 합니다. 배치 Vm이 예약 수량 아래로 확장 된 경우 예약 된 시간을 잃게 됩니다.

### <a name="use-automatic-scaling"></a>자동 스케일링 사용

[자동 크기 조정](batch-automatic-scaling.md)은 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 조정합니다. 자동 스케일링은 작업 수명에 따라 풀을 스케일링하여 수행할 작업이 있는 경우에만 VM이 스케일 업되고 사용되도록 합니다. 작업이 완료되거나 작업이 없으면 VM이 자동으로 스케일 다운되어 컴퓨팅 리소스가 절약됩니다. 스케일링을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management + 청구](../cost-management-billing/cost-management-billing-overview.md)에 대해 자세히 알아보세요.
- [Batch에서 우선 순위가 낮은 VM을 사용](batch-low-pri-vms.md)하는 방법에 대해 알아보세요.
