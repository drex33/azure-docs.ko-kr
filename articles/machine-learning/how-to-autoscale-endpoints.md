---
title: 관리되는 온라인 엔드포인트 자동 크기 조정
titleSuffix: Azure Machine Learning
description: 관리형 엔드포인트를 확장하는 방법을 알아봅니다. 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능을 가져옵니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: f979651909a2484f6bcdf7b0953c91874bea7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520658"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>관리되는 온라인 엔드포인트 자동 크기 조정(미리 보기)

자동 크기 조정은 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스를 자동으로 실행합니다. [관리형 엔드포인트는](concept-endpoints.md) Azure Monitor 자동 크기 조정 기능과의 통합을 통해 자동 크기 조정을 지원합니다.

Azure Monitor 자동 조정은 다양한 규칙 집합을 지원합니다. 메트릭 기반 크기 조정(예: CPU 사용률 >70%), 일정 기반 크기 조정(예: 최대 업무 시간에 대한 크기 조정 규칙) 또는 조합을 구성할 수 있습니다. 자세한 내용은 [Microsoft Azure 자동 크기 조정 개요를](/azure-monitor/autoscale/autoscale-overview.md)참조하세요.

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="필요에 따라 인스턴스를 추가/제거하는 자동 크기 조정 다이어그램":::

현재 Azure CLI, REST, ARM 또는 브라우저 기반 Azure Portal 사용하여 자동 조정을 관리할 수 있습니다. Python SDK와 같은 다른 Azure ML SDK는 시간이 지남에 따라 지원을 추가합니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소 

* 배포된 엔드포인트입니다. [관리형 온라인 엔드포인트(미리 보기)를 사용하여 기계 학습 모델을 배포하고 점수](how-to-deploy-managed-online-endpoints.md)매기기 

## <a name="define-an-autoscale-profile"></a>자동 크기 조정 프로필 정의

엔드포인트에 대해 자동 크기 조정을 사용하도록 설정하려면 먼저 자동 크기 조정 프로필을 정의합니다. 이 프로필은 기본, 최소, 최대 확장 집합 용량을 정의합니다. 다음 예제에서는 기본 및 최소 용량을 두 개의 VM 인스턴스로 설정하고 최대 용량을 5로 설정합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 조각은 엔드포인트 및 배포 이름을 설정합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

다음으로 배포 및 엔드포인트의 Azure Resource Manager ID를 얻습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

다음 조각은 자동 크기 조정 프로필을 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> 자세한 내용은 자동 크기 조정에 [대한 참조 페이지를 참조하세요.](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Machine Learning Studio에서](https://ml.azure.com)작업 영역을 선택한 다음, 페이지 왼쪽에서 __엔드포인트를__ 선택합니다. 엔드포인트가 나열되면 구성하려는 엔드포인트를 선택합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="포털의 엔드포인트 배포 항목 스크린샷.":::

엔드포인트에 대한 __세부 정보__ 탭에서 __자동 크기 조정 구성을__ 선택합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="엔드포인트 세부 정보의 자동 크기 조정 구성 링크 스크린샷.":::

__리소스 크기를 조정하는 방법 선택에서__ 사용자 지정 자동 __크기 조정을__ 선택하여 구성을 시작합니다. 기본 크기 조정 조건의 경우 다음 값을 사용합니다.

* 크기 __조정 모드를__ __메트릭 에 따라 크기 조정으로__ 설정합니다.
* __최소__ 를 __2로__ 설정합니다.
* __최대값을__ __5로__ 설정합니다.
* __기본값을__ __2로__ 설정합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="사용자 지정 자동 크기 조정 선택을 보여주는 스크린샷.":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>메트릭을 사용하여 스케일 아웃하는 규칙 만들기

일반적인 스케일 아웃 규칙은 평균 CPU 부하가 높을 때 VM 인스턴스 수를 늘리는 규칙입니다. 다음 예제에서는 CPU가 평균 5분 동안 70%를 초과하는 부하를 평균하는 경우 두 개의 노드(최대값까지)를 더 할당합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

규칙은 `my-scale-settings` 프로필의 일부입니다(프로필의 `autoscale-name` 와 `name` 일치). 인수 값은 `condition` "VM 인스턴스 간의 평균 CPU 사용량이 5분 동안 70%를 초과할 때 규칙이 트리거되어야 합니다."라고 말합니다. 해당 조건이 충족되면 두 개의 VM 인스턴스가 더 할당됩니다. 

> [!NOTE]
> CLI 구문에 대한 자세한 내용은 를 [`az monitor autoscale`](/cli/azure/monitor/autoscale) 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

__규칙__ 섹션에서 __규칙 추가__ 를 선택합니다. __크기 조정 규칙__ 페이지가 표시됩니다. 이 페이지의 필드를 채우려면 다음 정보를 사용합니다.

* __메트릭 이름을__ __CPU 사용률__ 로 설정합니다.
* __연산자를__ __보다 큽진__ 값으로 설정하고 __메트릭 임계값을__ __70으로__ 설정합니다.
* __기간(분)을__ 5로 설정합니다. 시간 __세분성 통계는__ __평균으로__ 둡니다.
* __작업을__ __개수 증가로__ 설정하고 __인스턴스 수를__ __2로__ 설정합니다.

마지막으로 __추가__ 단추를 선택하여 규칙을 만듭니다.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="5분 동안 70% CPU를 >스케일 아웃 규칙을 보여주는 스크린샷.":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>메트릭을 사용하여 규모를 축소하는 규칙 만들기

부하가 적으면 규칙의 크기 조정을 통해 VM 인스턴스 수를 줄일 수 있습니다. 다음 예제에서는 CPU 로드가 5분 동안 30% 미만인 경우 단일 노드를 최소 2까지 해제합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

__규칙__ 섹션에서 __규칙 추가__ 를 선택합니다. __크기 조정 규칙__ 페이지가 표시됩니다. 이 페이지의 필드를 채우려면 다음 정보를 사용합니다.

* __메트릭 이름을__ __CPU 사용률__ 로 설정합니다.
* __연산자를__ __보다 작은__ 값으로 설정하고 __메트릭 임계값을__ __30으로__ 설정합니다.
* __기간(분)을__ __5로__ 설정합니다.
* __작업을__ __개수 줄이기 로__ 설정하고 __인스턴스 수를__ __1로__ 설정합니다.

마지막으로 __추가__ 단추를 선택하여 규칙을 만듭니다.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="규모 축소 규칙을 보여주는 스크린샷":::

규모 확장 및 규모 축소 규칙이 둘 다 있는 경우 규칙은 다음 스크린샷과 유사하게 보입니다. 평균 CPU 부하가 5분 동안 70%를 초과하면 2개의 노드를 더 할당해야 하며 최대 5개까지 할당해야 한다고 지정했습니다. CPU 로드가 5분 동안 30% 미만인 경우 단일 노드를 최소 2까지 해제해야 합니다. 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="규칙을 포함한 자동 크기 조정 설정을 보여주는 스크린샷.":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>엔드포인트 메트릭을 기반으로 크기 조정 규칙 만들기

배포에 적용된 이전 규칙입니다. 이제 엔드포인트에 적용되는 규칙을 추가합니다. 이 예제에서 요청 대기 시간이 5분 동안 평균 70밀리초보다 큰 경우 다른 노드를 할당합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

페이지 아래쪽에서 __+ 크기 조정 조건 추가를__ 선택합니다.

__메트릭에 따라 크기 조정을__ 선택한 __다음, 규칙 추가를__ 선택합니다. __크기 조정 규칙__ 페이지가 표시됩니다. 이 페이지의 필드를 채우려면 다음 정보를 사용합니다.

* __메트릭 원본을__ __기타 리소스__ 로 설정합니다.
* __리소스 종류를__ __Machine Learning 온라인 엔드포인트로 설정합니다.__
* __리소스를__ 엔드포인트로 설정합니다.
* __메트릭 이름을__ __요청 대기 시간__ 로 설정합니다.
* __연산자를__ __보다 큽진__ 값으로 설정하고 __메트릭 임계값을__ __70으로__ 설정합니다.
* __기간(분)을__ __5로__ 설정합니다.
* __작업을__ __개수 증가로__ 설정하고 __인스턴스 수를__ 1로 설정합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="엔드포인트 메트릭 규칙을 보여주는 스크린샷.":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>일정에 따라 크기 조정 규칙 만들기

특정 요일 또는 특정 시간에만 적용되는 규칙을 만들 수도 있습니다. 이 예제에서 노드 수는 주말에 2로 설정됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

페이지 아래쪽에서 __+ 크기 조정 조건 추가를__ 선택합니다. 새 크기 조정 조건에서 다음 정보를 사용하여 필드를 채웁다.
 
* __특정 인스턴스 수로 크기 조정을__ 선택합니다.
* __인스턴스 수__ 를 __2__ 로 설정 합니다.
* __특정 날짜를 반복__ 하도록 __일정__ 을 설정 합니다.
* __토요일과__ __일요일__ __마다 반복__ 되도록 일정을 설정 합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/schedule-rules.png" lightbox="media/how-to-autoscale-endpoints/schedule-rules.png" alt-text="일정 기반 규칙을 보여 주는 스크린샷":::

---

## <a name="delete-resources"></a>리소스 삭제

배포를 사용 하지 않으려는 경우 삭제 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>다음 단계

Azure Monitor의 자동 크기 조정에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [자동 크기 조정 설정 이해](/azure-monitor/autoscale/autoscale-understand-settings)
- [일반적인 자동 크기 조정 패턴에 대한 개요](/azure-monitor/autoscale/autoscale-common-scale-patterns)
- [자동 크기 조정 모범 사례](/azure-monitor/autoscale/autoscale-best-practices)
- [Azure 자동 크기 조정 문제 해결](/azure-monitor/autoscale/autoscale-troubleshoot)
