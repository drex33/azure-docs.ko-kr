---
title: 비용 관리 및 최적화
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 기계 학습 모델을 빌드할 때 비용을 최적화하는 팁을 알아봅니다.
author: luisquintanilla
ms.author: luquinta
ms.custom: subject-cost-optimization
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 47f10b542bd2c4eb7965e6dfc25349661ca3c3d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805252"
---
# <a name="manage-and-optimize-azure-machine-learning-costs"></a>Azure Machine Learning 비용 관리 및 최적화

기계 학습 모델을 학습하고 Azure Machine Learning에 배포할 때 비용을 관리하고 최적화하는 방법을 알아봅니다.

다음 팁을 사용하여 컴퓨팅 리소스 비용을 관리하고 최적화할 수 있습니다.

- 자동 크기 조정을 위한 학습 클러스터 구성
- 구독 및 작업 영역에 대한 할당량 설정
- 학습 실행에 대한 종료 정책 설정
- 우선 순위가 낮은 VM(가상 머신) 사용
- 컴퓨팅 인스턴스가 자동으로 종료 및 시작하도록 예약
- Azure Reserved VM Instance 사용
- 로컬에서 학습
- 학습 병렬화
- 데이터 보존 및 삭제 정책 설정
- 동일한 지역에 리소스 배포

비용 계획 및 모니터링에 대한 자세한 내용은 [Azure Machine Learning에 대한 비용 관리 계획](concept-plan-manage-cost.md)을 참조하세요.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning 컴퓨팅 클러스터(AmlCompute) 사용

지속적으로 변화하는 데이터를 통해 정확한 모델을 유지하기 위해 빠르고 효율적인 모델 학습 및 재학습을 수행해야 합니다. 단, 지속적인 학습에는 비용이 발생합니다. 특히나 GPU의 딥 러닝 모델에 대해 그렇습니다. 

Azure Machine Learning 사용자는 AmlCompute라고도 하는 관리되는 Azure Machine Learning 컴퓨팅 클러스터를 사용할 수 있습니다. AmlCompute는 다양한 GPU 및 CPU 옵션을 지원합니다. AmlCompute는 Azure Machine Learning에 의해 사용자 구독을 대신하여 내부적으로 호스팅됩니다. Azure IaaS 클라우드 규모에서 동일한 엔터프라이즈급 보안, 규정 준수 및 거버넌스를 제공합니다.

이러한 컴퓨팅 풀이 Azure의 IaaS 인프라 내에 있으므로 인프라의 나머지 부분과 동일한 보안 및 규정 준수 요구 사항을 사용하여 학습을 배포, 확장 및 관리할 수 있습니다.  이러한 배포는 구독에서 발생하고 거버넌스 규칙을 준수합니다. [Azure Machine Learning 컴퓨팅](how-to-create-attach-compute-cluster.md)에 대해 자세히 알아보세요.

## <a name="configure-training-clusters-for-autoscaling"></a>자동 크기 조정을 위한 학습 클러스터 구성

워크로드 요구 사항에 따라 클러스터 크기를 자동으로 조정하면 필요한 기능만 사용하도록 비용을 줄일 수 있습니다.

AmlCompute 클러스터는 워크로드에 따라 동적으로 크기를 조정하도록 설계되었습니다. 클러스터는 구성하는 최대 노드 수까지 확장할 수 있습니다. 각 실행이 완료되면 클러스터에서 노드가 해제되고 구성된 최소 노드 수로 확장됩니다.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

또한 규모를 축소하기 전에 노드가 유휴 상태로 유지되는 시간을 구성할 수 있습니다. 기본적으로 축소 전의 유휴 시간은 120초로 설정됩니다.

+ 반복적 실험을 수행하는 경우 비용을 절약하기 위해 이 시간을 줄입니다.
+ 매우 반복적인 개발/테스트 실험을 수행하는 경우 학습 스크립트나 환경에 대한 각 변경 후 일정한 확장 및 축소에 대해 비용을 지불하지 않도록 시간을 늘려야 할 수 있습니다.

[AmlCompute SDK 클래스](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), [AmlCompute CLI](/cli/azure/ml(v1)/computetarget/create#az_ml_v1__computetarget_create_amlcompute)와 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)를 사용하여 Azure Portal에서 변화하는 워크로드 요구 사항에 대해 AmlCompute 클러스터를 구성할 수 있습니다.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>리소스에 대한 할당량 설정

AmlCompute는 [할당량(또는 제한) 구성](how-to-manage-quotas.md#azure-machine-learning-compute)과 함께 제공됩니다. 이 할당량은 VM 제품군(예: Dv2 시리즈, NCv3 시리즈)에 따라 다르며 각 구독에 대한 지역별 차이가 있습니다. 구독은 작은 기본값으로 시작되지만, 이 설정을 사용하여 구독에서 분리할 수 있는 Amlcompute 리소스의 양을 제어할 수 있습니다. 

또한 구독 내의 각 작업 영역에 대해 [VM 제품군별 작업 영역 수준 할당량](how-to-manage-quotas.md#workspace-level-quotas)을 구성합니다. 이렇게 하면 각 작업 영역에서 잠재적으로 발생할 수 있는 비용을 더 세부적으로 제어하고 특정 VM 제품군을 제한할 수 있습니다. 

작업 영역 수준에서 할당량을 설정하려면 [Azure Portal](https://portal.azure.com)에서 시작합니다.  구독에서 아무 작업 영역이나 선택하고 왼쪽 창에서 **사용량 + 할당량** 을 선택합니다. 그런 다음, **할당량 구성** 탭을 선택하여 할당량을 확인합니다. 할당량을 설정하려면 구독 범위에서 권한이 필요합니다. 이는 여러 작업 영역에 영향을 주는 설정이기 때문입니다.

## <a name="set-run-autotermination-policies"></a>자동 종료 정책 실행 설정 

경우에 따라 학습 실행을 구성하여 기간을 제한하거나 조기에 종료해야 합니다. 예를 들어 Azure Machine Learning의 기본 제공 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용하는 경우입니다.

다음은 몇 가지 옵션입니다.
* RunConfiguration에서 `max_run_duration_seconds`라는 매개 변수를 정의하여 사용자가 선택하는 컴퓨팅(로컬 또는 원격 클라우드 컴퓨팅)에서 실행이 확장될 수 있는 최대 기간을 제어합니다.
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md#early-termination)의 경우 Bandit 정책, 중간 중지 정책 또는 잘림 선택 정책에서 조기 종료 정책을 정의합니다. 하이퍼 매개 변수 스윕을 추가로 제어하려면 `max_total_runs` 또는 `max_duration_minutes`와 같은 매개 변수를 사용합니다.
* [자동화된 Machine Learning](how-to-configure-auto-train.md#exit)의 경우 `enable_early_stopping` 플래그를 사용하여 비슷한 종료 정책을 설정합니다. 또한 `iteration_timeout_minutes` 및 `experiment_timeout_minutes`와 같은 속성을 사용하여 실행의 최대 기간 또는 전체 실험을 제어합니다.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 우선 순위가 낮은 VM 사용

Azure를 통해 가상 머신 확장 집합, Batch 및 Machine Learning 서비스에서 활용되지 않는 과도한 용량을 우선 순위가 낮은 VM으로 사용할 수 있습니다. 이러한 할당은 선점이 가능하지만, 전용 VM에 비해 저렴한 가격으로 제공됩니다. 일반적으로 Batch 워크로드에 우선 순위가 낮은 VM을 사용하는 것이 좋습니다. 다시 전송(Batch 추론의 경우) 또는 다시 시작(검사점을 사용한 딥 러닝 학습)을 통해 중단을 복구할 수 있는 경우에도 이를 사용해야 합니다.

우선 순위가 낮은 VM의 개별 할당량은 VM 제품군별 전용 할당량 값과 별개입니다. [AmlCompute 할당량에 대해 자세히](how-to-manage-quotas.md) 알아보세요.

 우선 순위가 낮은 VM은 대화형 Notebook 환경을 지원해야 하기 때문에 컴퓨팅 인스턴스에 대해 작동하지 않습니다.

## <a name="schedule-compute-instances"></a>컴퓨팅 인스턴스 예약

[컴퓨팅 인스턴스](concept-compute-instance.md)를 만들 때 VM은 작업에 사용할 수 있도록 유지됩니다.  컴퓨팅 인스턴스(미리 보기)를 자동으로 시작 및 중지하도록 [일정을 설정](how-to-create-manage-compute-instance.md#schedule)하여 사용하지 않을 때 비용을 절감합니다.

## <a name="use-reserved-instances"></a>예약 인스턴스 사용

컴퓨팅 리소스에 대한 비용을 절감하는 또 다른 방법은 Azure Reserved VM Instance입니다. 이 제품을 통해 1년 또는 3년 조건으로 약정하게 됩니다. 이러한 할인 범위는 종량제 가격의 최대 72%이며 월간 Azure 청구서에 직접 적용됩니다.

Azure Machine Learning 컴퓨팅은 기본적으로 예약 인스턴스를 지원합니다. 1년 또는 3년 예약 인스턴스를 구매하는 경우 Azure Machine Learning 관리 컴퓨팅에 대한 할인이 자동으로 적용됩니다.

## <a name="train-locally"></a>로컬에서 학습

로컬 컴퓨터에서 실행할 수 있을 만큼 작은 학습 작업을 프로토타이핑하고 실행하는 경우 로컬로 학습하는 것이 좋습니다. Python SDK를 사용하여 컴퓨팅 대상을 `local`로 설정하면 스크립트가 로컬로 실행됩니다. 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md#select-a-compute-target)을 참조하세요.

Visual Studio Code는 기계 학습 애플리케이션을 개발하기 위한 모든 기능을 갖춘 환경을 제공합니다. Azure Machine Learning 시각적 개체 Visual Studio Code 확장 및 Docker를 사용하여 로컬에서 실행하고 디버그할 수 있습니다. 자세한 내용은 [Visual Studio Code를 사용하여 대화형 디버깅](how-to-debug-visual-studio-code.md)을 참조하세요.

## <a name="parallelize-training"></a>학습 병렬화

비용 및 성능을 최적화하는 주요 방법 중 하나는 Azure Machine Learning의 병렬 실행 단계를 통해 워크로드를 병렬화하는 것입니다. 이 단계를 사용하면 많은 작은 노드를 사용하여 작업을 병렬로 실행할 수 있으므로 가로로 크기를 조정합니다. 병렬화에 대한 오버헤드가 있습니다. 달성할 수 있는 워크로드 및 병렬화 정도에 따라 이는 옵션이 될 수도 있고 그렇지 않을 수도 있습니다. 자세한 내용은 [ParallelRunStep](xref:azureml.contrib.pipeline.steps.ParallelRunStep) 설명서를 참조하세요.

## <a name="set-data-retention--deletion-policies"></a>데이터 보존 및 삭제 정책 설정

파이프라인이 실행될 때마다 각 단계에서 중간 데이터 세트가 생성됩니다. 시간이 지남에 따라 이러한 중간 데이터 세트는 스토리지 계정에서 공간을 차지합니다. 수명 주기 내내 데이터를 관리하여 데이터 세트를 보관하고 삭제하는 정책을 설정하는 것이 좋습니다. 자세한 내용은 [Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](../storage/blobs/lifecycle-management-overview.md)를 참조하세요.

## <a name="deploy-resources-to-the-same-region"></a>동일한 지역에 리소스 배포

다른 지역에 있는 컴퓨팅은 네트워크 대기 시간 및 데이터 전송 비용 증가를 경험할 수 있습니다. Azure 네트워크 비용은 Azure 데이터 센터의 아웃바운드 대역폭에서 발생합니다. 네트워크 비용을 줄이려면 해당 지역의 모든 리소스를 배포합니다. 데이터와 동일한 지역에서 Azure Machine Learning 작업 영역 및 종속 리소스를 프로비전하면 비용을 절감하고 성능을 향상시킬 수 있습니다.

ExpressRoute를 사용하는 하이브리드 클라우드 시나리오의 경우 네트워크 비용과 대기 시간을 최적화하기 위해 모든 리소스를 Azure로 이동하는 것이 더 비용 효율적일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Machine Learning의 비용 관리 계획](concept-plan-manage-cost.md)
- [조직 규모에서 Azure Machine Learning에 대한 예산, 비용 및 할당량 관리](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)