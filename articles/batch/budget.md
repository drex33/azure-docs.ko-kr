---
title: Azure Batch의 비용 분석 가져오기 및 예산 설정
description: Batch 워크로드를 실행하는 데 사용되는 기본 컴퓨팅 리소스 및 소프트웨어 라이선스의 비용 분석을 수행하고 예산을 설정하며 비용을 절감하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: a590d8687c51b1693494c11d95de720f7f2c7eb0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538852"
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

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>비용 분석 보기 및 풀에 대한 예산 만들기

Azure Portal에서 Batch 풀 또는 Batch 계정에 대한 예산 및 지출 경고를 만들 수 있습니다. 예산 및 경고는 지출 경고가 지연되고 예산을 약간 초과할 수 있지만 초과 지출의 위험을 관련자에게 알리는 데 유용합니다.

> [!NOTE]
> 이 예제의 풀은 **가상 머신 구성** 을 사용합니다. 이 구성은 대부분의 풀에 권장되며 Virtual Machines 가격 책정 구조에 따라 요금이 부과됩니다. **Cloud Services 구성** 을 사용하는 풀은 Cloud Services 가격 책정 체계에 따라 요금이 청구됩니다.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Batch 풀에 대한 비용 분석 보기

1. Azure Portal에서 **Cost Management + Billing** 을 입력하거나 선택합니다.
1. **청구 범위** 섹션에서 구독을 선택합니다.
1. **Cost Management** 에서 **비용 분석** 을 선택합니다.
1. **필터 추가** 를 선택합니다. 첫 번째 드롭다운에서 **리소스** 를 선택합니다.
1. 두 번째 드롭다운 목록에서 Batch 풀을 선택합니다. 풀을 선택하면 여기에 소개된 예제와 비슷하게 풀에 대한 비용 분석이 표시됩니다.
    ![Azure Portal 풀의 비용 분석을 보여 주는 스크린샷](./media/batch-budget/pool-cost-analysis.png)

비용 분석 결과는 이 비용에 영향을 주는 리소스뿐만 아니라 풀의 비용도 보여 줍니다. 이 예에서는 풀에 사용된 VM이 가장 비용이 많이 드는 리소스입니다.

### <a name="create-a-budget-for-a-batch-pool"></a>Batch 풀에 대한 예산 만들기

1. **비용 분석** 페이지에서 **예산: 없음** 을 선택합니다.
1. **새 예산 만들기** 를 선택합니다.
1. 나타나는 창을 사용하여 풀의 구체적인 예산을 구성합니다. 자세한 내용은 [자습서: Azure 예산 세우기 및 관리](../cost-management-billing/costs/tutorial-acm-create-budgets.md)를 참조하세요.

## <a name="minimize-costs-associated-with-azure-batch"></a>Azure Batch 관련된 비용 최소화

시나리오에 따라 비용을 최대한 줄일 수 있습니다. 해당 전략 중 하나 이상을 사용하여 워크로드의 효율성을 극대화하고 잠재적인 비용을 절감하는 것이 좋습니다.

### <a name="use-low-priority-virtual-machines"></a>우선 순위가 낮은 가상 머신 사용

[우선 순위가 낮은 VM](batch-low-pri-vms.md)은 Azure에서 여분의 컴퓨팅 용량을 활용하여 Batch 워크로드의 비용을 줄입니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Batch는 이러한 남는 용량을 사용하여 워크로드를 실행할 수 있습니다. 전용 VM 대신 우선 순위가 낮은 VM을 사용하면 비용을 크게 절감할 수 있습니다.

### <a name="use-ephemeral-os-disks"></a>사용 후 삭제 OS 디스크 사용

Virtual Machine 구성 풀은 [관리 디스크와](create-pool-ephemeral-os-disk.md)관련된 추가 비용을 방지하기 위해 VM 캐시 또는 임시 SSD에 OS 디스크를 만드는 임시 OS 디스크를 사용할 수 있습니다.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>가상 머신 인스턴스에 대한 구매 예약

Batch를 장기간 사용하려는 경우 워크로드에 [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)를 사용하여 VM 비용을 절감할 수 있습니다. 예약 요금은 종량제 요금보다 훨씬 저렴합니다. 예약 없이 사용되는 가상 머신 인스턴스는 종량제 요금으로 청구됩니다. 예약을 구매하면 예약 할인이 적용됩니다.

### <a name="use-automatic-scaling"></a>자동 스케일링 사용

[자동 크기 조정](batch-automatic-scaling.md)은 현재 작업의 요구에 따라 Batch 풀의 VM 수를 동적으로 조정합니다. 자동 스케일링은 작업 수명에 따라 풀을 스케일링하여 수행할 작업이 있는 경우에만 VM이 스케일 업되고 사용되도록 합니다. 작업이 완료되거나 작업이 없으면 VM이 자동으로 스케일 다운되어 컴퓨팅 리소스가 절약됩니다. 스케일링을 사용하면 필요한 리소스만 사용하여 Batch 솔루션의 전체 비용을 낮출 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md)에 대해 자세히 알아보기
- [Batch에서 우선 순위가 낮은 VM을 사용](batch-low-pri-vms.md)하는 방법에 대해 알아보세요.
