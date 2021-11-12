---
title: 리소스 및 할당량 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 리소스 할당량 및 제한과 할당량 증가를 요청하는 방법에 대해 자세히 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: SimranArora904
ms.author: siarora
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 809f87ea7d8c4bdf3c6f21fe742ef5a5f2b595d9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335635"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증가

Azure는 사기로 인한 예산 초과를 방지하고, Azure 용량 제약 조건을 준수하기 위해 한도 및 할당량을 사용합니다. 프로덕션 워크로드의 크기를 조정할 때 이러한 한도를 고려하세요. 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> + [Azure Machine Learning](overview-what-is-azure-machine-learning.md)과 관련된 Azure 리소스에 대한 기본 한도
> + 작업 영역 수준 할당량 만들기
> + 할당량 및 한도 보기
> + 할당량 증가 다시 요청

할당량 관리와 함께 [Azure Machine Learning에 대한 비용을 계획 및 관리](concept-plan-manage-cost.md)하는 방법 또는 [Azure Machine Learning의 서비스 한도](resource-limits-quotas-capacity.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="special-considerations"></a>특별 고려 사항

+ 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 [Azure 지원에 할당량 증가를 문의](#request-quota-increases)하세요.

+ 할당량은 Azure Machine Learning을 포함하여 구독의 모든 서비스에 공유됩니다. 용량을 평가할 때 모든 서비스의 사용량을 계산합니다.
 
  Azure Machine Learning 컴퓨팅은 예외입니다. 이것은 핵심 컴퓨팅 할당량과 별도의 할당량을 갖습니다. 

+ 기본 한도는 평가판, 종량제, VM(가상 머신) 시리즈(예: Dv2, F 및 G)와 같은 범주 유형에 따라 다릅니다.

## <a name="default-resource-quotas"></a>기본 리소스 할당량

이 섹션에서는 다음 리소스의 기본 및 최대 할당량 한도에 대해 알아봅니다.

+ Azure Machine Learning 자산
  + Azure Machine Learning 컴퓨팅
  + Azure Machine Learning 파이프라인
+ 가상 머신
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> 한도는 변경될 수 있습니다. 최신 정보는 [Azure Machine Learning의 서비스 한도](resource-limits-quotas-capacity.md)를 참조하세요.



### <a name="azure-machine-learning-assets"></a>Azure Machine Learning 자산
자산에 대한 다음 한도는 작업 영역별 기준으로 적용됩니다. 

| **리소스** | **최대 한도** |
| --- | --- |
| 데이터 세트 | 1천만 |
| 실행 | 1천만 |
| 모델 | 1천만|
| Artifacts | 1천만 |

또한 최대 **런타임** 은 30일이고 **실행별로 기록되는 메트릭** 의 최대 개수는 100만입니다.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
[Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed)는 구독에서 지역별로 허용되는 고유 컴퓨팅 리소스 수는 물론 각 VM 제품군 및 누적 합계 코어 수로 분할되는 코어 수에 대해 기본 할당량 한도를 갖습니다. 이 할당량은 Azure Machine Learning의 관리되는 컴퓨팅 리소스에만 적용되므로 이전 섹션에 나열된 VM 코어 할당량과 별개입니다.

여러 VM 제품군 코어 할당량, 이 섹션의 총 구독 코어 할당량 및 리소스에 대해 한도를 늘리기 위해 [할당량 증가를 요청](#request-quota-increases)합니다.

사용 가능한 리소스:
+ **지역별 전용 코어** 는 구독 제안 유형에 따라 24~300의 기본 한도를 갖습니다. 각 VM 제품군에 대해 구독별 전용 코어 수를 늘릴 수 있습니다. NCv2, NCv3 또는 ND 시리즈와 같은 특별한 VM 제품군은 기본 코어 수 0으로 시작합니다.

+ **지역별 낮은 우선순위 코어** 는 구독 제안 유형에 따라 100~3,000의 기본 한도를 갖습니다. 구독당 우선 순위가 낮은 코어 수는 확장 가능하며 VM 제품군별로 값이 다릅니다.

+ **지역별 클러스터** 는 기본 한도가 200입니다. 이러한 클러스터는 학습 클러스터와 컴퓨팅 인스턴스 사이에 공유됩니다. (계산 인스턴스는 할당량 목적의 단일 노드 클러스터로 고려됩니다.)

> [!TIP]
> 할당량 증가를 요청할 VM 제품군에 대해 자세히 알아보려면 [Azure에서 가상 머신 크기](../virtual-machines/sizes.md)를 확인합니다. 예를 들어 GPU VM 제품군은 해당 제품군 이름이 “N”으로 시작합니다(예: NCv3 시리즈).

다음 표에서는 플랫폼의 추가 한도를 보여줍니다. 예외를 요청하려면 **기술** 지원 티켓을 통해 AzureML 제품 팀에 연락하세요.

| **리소스 또는 작업** | **최대 한도** |
| --- | --- |
| 리소스 그룹당 작업 영역 | 800 |
| 통신을 사용하지 않는 풀로 설정된 단일 Azure Machine Learning Compute(AmlCompute) **클러스터** 의 노드 | 100개 노드, 하지만 최대 65000개 노드까지 구성 가능 |
| Azure Machine Learning Compute(AmlCompute) 클러스터에서 **실행** 되는 단일 병렬 실행 단계의 노드 | 100개 노드, 하지만 클러스터가 위 내용에 따라 확장하도록 설정된 경우 최대 65000개 노드까지 구성 가능 |
| 통신 사용 풀로 설정된 단일 Azure Machine Learning Compute(AmlCompute) **클러스터** 의 노드 | 300개 노드, 하지만 최대 4000개 노드까지 구성 가능 |
| RDMA 지원 VM 제품군에서 통신 사용 풀로 설정된 단일 Azure Machine Learning Compute(AmlCompute) **클러스터** 의 노드 | 100개 노드 |
| Azure Machine Learning Compute(AmlCompute) 클러스터에서 **실행** 되는 단일 MPI의 노드 | 100개 노드, 하지만 최대 300개 노드까지 증가 가능 |
| 노드당 GPU MPI 프로세스 | 1-4 |
| 노드당 GPU 작업자 | 1-4 |
| 작업 수명 | 21일<sup>1</sup> |
| 우선 순위가 낮은 노드의 작업 수명 | 7일<sup>2</sup> |
| 노드당 매개 변수 서버 | 1 |

<sup>1</sup> 최대 수명은 실행 시작 시간과 종료 시간 사이의 기간입니다. 완료된 실행은 무기한 유지됩니다. 최대 수명 내에 완료되지 않은 실행에 대한 데이터는 액세스할 수 없습니다.

<sup>2</sup> 용량 제약 조건이 있을 때마다 우선 순위가 낮은 노드의 작업이 선점될 수 있습니다. 작업에서 검사점을 구현하는 것이 좋습니다.

### <a name="azure-machine-learning-managed-online-endpoints-preview"></a>Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)
[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Azure Machine Learning 관리되는 온라인 엔드포인트에는 다음 표에 설명된 제한이 있습니다. 

엔드포인트에 대한 현재 사용량을 확인하려면 [메트릭을 확인합니다.](how-to-monitor-online-endpoints.md#view-metrics) Azure Machine Learning 제품 팀에서 예외를 요청하려면 기술 지원 티켓을 여세요.

| **리소스** | **제한** |
| --- | --- |
| 엔드포인트 이름| 엔드포인트 이름은 다음과 같아야 합니다. <li> 문자로 시작 <li> 3~32자 길이  <li> 문자 및 숫자로만 구성 <sup>1</sup> |
| 배포 이름| 배포 이름은 다음과 같아야 합니다. <li> 문자로 시작 <li> 3~32자 길이  <li>  문자 및 숫자로만 구성 <sup>1</sup> |
| 구독당 엔드포인트 수 | 50 |
| 구독당 배포 수 | 200 |
| 엔드포인트당 배포 수 | 20 |
| 배포당 인스턴스 수 | 20 |
| 엔드포인트 수준에서 최대 요청 시간 초과  | 90 초 |
| 모든 배포에 대 한 끝점 수준에서 초당 총 요청 수  | 500 <sup>2</sup> |
| 모든 배포에 대 한 끝점 수준 초당 총 연결 수  | 500 <sup>2</sup> |
| 모든 배포에 대해 끝점 수준에서 활성화 되는 총 연결  | 500 <sup>2</sup> |
| 모든 배포에 대 한 끝점 수준의 총 대역폭  | 5MBPS <sup>2</sup> |

<sup>1</sup> ,와 같은 단일 파선 `my-endpoint-name` 은 끝점 및 배포 이름에 허용 됩니다.

<sup>2</sup> 제한 증가를 요청 하는 경우 필요할 수 있는 관련 제한 증가를 계산 해야 합니다. 예를 들어 초당 요청에 대 한 제한 증가를 요청 하는 경우 필요한 연결 및 대역폭 제한을 계산 하 고 이러한 한도를 동일한 요청에 증가 시킬 수 있습니다.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인
[Azure Machine Learning 파이프라인](concept-ml-pipelines.md)은 다음 한도를 갖습니다.

| **리소스** | **제한** |
| --- | --- |
| 파이프라인의 단계 | 30,000 |
| 리소스 그룹당 작업 영역 | 800 |

### <a name="virtual-machines"></a>가상 머신
각 Azure 구독은 모든 서비스에서 가상 머신 수에 대한 한도를 갖습니다. 가상 머신 코어는 지역별 총 한도 및 크기 시리즈당 지역별 한도를 갖습니다. 두 한도는 개별적으로 적용됩니다.

미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이러한 구독은 30개의 A1 VM 또는 30개의 D1 VM을 배포하도록 허용됩니다. 또는 총 30개 코어를 초과하지 않는 두 구독의 조합도 허용됩니다.

다음 표에 표시된 값을 넘도록 가상 머신의 한도를 늘릴 수 없습니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

자세한 내용은 [Container Instances 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)를 참조하세요.

### <a name="storage"></a>Storage
Azure Storage는 구독당 지역별로 250개의 스토리지 계정으로 제한됩니다. 이러한 한도에는 표준 및 Premium 스토리지 계정이 모두 포함됩니다.

한도를 늘리려면 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)을 통해 요청하세요. Azure Storage 팀이 해당 사례를 검토하고 지역별로 최대 250까지 스토리지 계정을 승인할 수 있습니다.


## <a name="workspace-level-quotas"></a>작업 영역 수준 할당량

작업 영역 수준 할당량을 사용하여 동일한 구독의 여러 [작업 영역](concept-workspace.md) 간에 Azure Machine Learning 컴퓨팅 대상 할당을 관리할 수 있습니다.

기본적으로 모든 작업 영역은 VM 제품군의 구독 수준 할당량과 동일한 할당량을 공유합니다. 하지만 구독의 작업 영역에 개별 VM 제품군에 대해 최대 할당량을 설정할 수 있습니다. 이렇게 하여 용량을 공유하고 리소스 경합 문제를 방지할 수 있습니다.

1. 구독의 작업 영역으로 이동합니다.
1. 왼쪽 창에서 **사용량 + 할당량** 을 선택합니다.
1. **할당량 구성** 탭을 선택하여 할당량을 확인합니다.
1. VM 제품군을 확장합니다.
1. VM 제품군 아래에 나열된 작업 영역에 할당량 한도를 설정합니다.

구독 수준 할당량보다 높은 값 또는 음수 값은 설정할 수 없습니다.

[![Azure Machine Learning 작업 영역 수준 할당량을 보여주는 스크린샷입니다.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 작업 영역 수준에서 할당량을 설정하려면 구독 수준 권한이 필요합니다.

## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기

가상 머신, 스토리지 또는 네트워크와 같은 여러 Azure 리소스에 대해 할당량을 보려면 Azure Portal을 사용합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택한 후 **일반** 범주 아래에서 **구독** 을 선택합니다.

2. 구독 목록에서 할당량을 찾으려는 구독을 선택합니다.

3. **사용량 + 할당량** 을 선택하여 현재 할당량 한도 및 사용량을 확인합니다. 필터를 사용하여 공급자 및 위치를 선택합니다. 

다른 Azure 할당량과 별도로 구독에서 Azure Machine Learning 컴퓨팅 할당량을 관리합니다. 

1. Azure Portal에서 **Azure Machine Learning** 작업 영역으로 이동합니다.

2. 왼쪽 창의 **지원 + 문제 해결** 섹션에서 **사용량 + 할당량** 을 선택하여 현재 할당량 한도 및 사용량을 확인합니다.

3. 구독을 선택하여 할당량 한도를 확인합니다. 원하는 지역으로 필터링합니다.

4. 이제 구독 수준 보기 또는 작업 영역 수준 보기로 전환할 수 있습니다.

## <a name="request-quota-increases"></a>할당량 증가 요청

기본 한도 이상으로 한도 또는 할당량을 높이려면 비용 없이 [온라인 고객 지원 요청을 개설](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)합니다.

위 표에 표시된 최댓값 이상으로 한도를 늘릴 수 없습니다. 최대 한도가 없으면 해당 리소스의 한도를 조정할 수 없습니다.

할당량 증가를 요청하는 경우 원하는 서비스를 선택합니다. 예를 들어 Azure Machine Learning, Container Instances 또는 스토리지를 선택합니다. Azure Machine Learning 컴퓨팅의 경우 이전 단계에서 할당량을 확인할 때 **할당량 요청** 단추를 선택할 수 있습니다.

> [!NOTE]
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)은 한도 또는 할당량 증가 자격이 없습니다. 평가판 구독을 사용 중이면 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 평가판을 종량제로 업그레이드](../cost-management-billing/manage/upgrade-azure-subscription.md) 및 [Azure 체험 계정 FAQ](https://azure.microsoft.com/free/free-account-faq)를 참조하세요.

## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning 비용 계획 및 관리](concept-plan-manage-cost.md)
+ [Azure Machine Learning의 서비스 한도](resource-limits-quotas-capacity.md)
+ [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](./how-to-troubleshoot-online-endpoints.md)