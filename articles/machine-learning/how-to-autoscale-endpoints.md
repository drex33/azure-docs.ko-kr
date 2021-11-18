---
title: 자동 크기 조정 관리 되는 온라인 끝점
titleSuffix: Azure Machine Learning
description: 관리 되는 끝점을 확장 하는 방법을 알아봅니다. 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능을 가져옵니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: 3826286dd322b8e2da2ab1df6a02bcb14ec92b65
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760906"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>관리 되는 온라인 끝점 자동 크기 조정 (미리 보기)

자동 크기 조정은 응용 프로그램의 부하를 처리 하기 위해 적절 한 양의 리소스를 자동으로 실행 합니다. [관리 끝점](concept-endpoints.md) 은 Azure Monitor 자동 크기 조정 기능과 통합 하 여 자동 크기 조정을 지원 합니다.

Azure Monitor 자동 크기 조정은 다양 한 규칙 집합을 지원 합니다. 메트릭 기반 크기 조정 (예: CPU 사용률 >70%), 일정 기반 크기 조정 (예: 최대 업무 시간에 대 한 크기 조정 규칙) 또는 조합을 구성할 수 있습니다. 자세한 내용은 [Microsoft Azure의 자동 크기 조정 개요](/azure/azure-monitor/autoscale/autoscale-overview)를 참조 하세요.

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="필요에 따라 인스턴스 자동 크기 조정 추가/제거에 대 한 다이어그램":::

현재 Azure CLI, REST, ARM 또는 브라우저 기반 Azure Portal를 사용 하 여 자동 크기 조정을 관리할 수 있습니다. Python SDK와 같은 다른 Azure ML sdk는 시간에 따라 지원을 추가 합니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항 

* 배포 된 끝점입니다. [관리 되는 온라인 끝점 (미리 보기)을 사용 하 여 machine learning 모델을 배포 하 고 점수를](how-to-deploy-managed-online-endpoints.md)매길. 

## <a name="define-an-autoscale-profile"></a>자동 크기 조정 프로필 정의

끝점에 자동 크기 조정을 사용 하도록 설정 하려면 먼저 자동 크기 조정 프로필을 정의 합니다. 이 프로필은 기본, 최소, 최대 확장 집합 용량을 정의합니다. 다음 예제에서는 기본 및 최소 용량을 두 개의 VM 인스턴스로 설정 하 고 최대 용량을 5로 설정 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 코드 조각은 끝점과 배포 이름을 설정 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

다음으로, 배포 및 끝점의 Azure Resource Manager ID를 가져옵니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

다음 코드 조각은 자동 크기 조정 프로필을 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> 자세한 내용은 [자동 크기 조정에 대 한 참조 페이지](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true) 를 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Machine Learning studio](https://ml.azure.com)에서 작업 영역을 선택한 다음 페이지의 왼쪽에서 __끝점__ 을 선택 합니다. 끝점이 표시 되 면 구성할 항목을 선택 합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="포털에서 끝점 배포 항목의 스크린샷":::

끝점의 __세부 정보__ 탭에서 __자동 크기 조정 구성__ 을 선택 합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="끝점 정보의 자동 크기 조정 구성 링크의 스크린샷":::

__리소스의 크기를 조정 하는 방법 선택__ 에서 __사용자 지정 자동 크기 조정__ 을 선택 하 여 구성을 시작 합니다. 기본 크기 조정 조건의 경우 다음 값을 사용 합니다.

* __메트릭에 따라__ 크기 조정 __모드__ 를 설정 합니다.
* __최소값__ 을 __2__ 로 설정 합니다.
* __최대값__ 을 __5__ 로 설정 합니다.
* __기본값__ 을 __2__ 로 설정 합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="사용자 지정 자동 크기 조정 옵션을 보여 주는 스크린샷":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>메트릭을 사용 하 여 규모를 확장 하는 규칙 만들기

일반적인 확장 규칙은 평균 CPU 부하가 높을 때 VM 인스턴스 수를 늘리는 것입니다. 다음 예제에서는 CPU가 5 분 동안 70% 보다 큰 부하를 평균 하는 경우 노드를 두 개 이상 할당 합니다 (최대 크기).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

규칙은 프로필의 일부입니다 `my-scale-settings` ( `autoscale-name` `name` 프로필의와 일치). 인수의 값은 `condition` "VM 인스턴스 간의 평균 CPU 사용률이 5 분 동안 70%를 초과 하는 경우 규칙이 트리거해야 함을 의미 합니다." 조건을 충족 하는 경우 두 개 이상의 VM 인스턴스가 할당 됩니다. 

> [!NOTE]
> CLI 구문에 대 한 자세한 내용은을 참조 하십시오 [`az monitor autoscale`](/cli/azure/monitor/autoscale) .

# <a name="portal"></a>[포털](#tab/azure-portal)

__규칙__ 섹션에서 __규칙 추가__ 를 선택합니다. __크기 조정 규칙__ 페이지가 표시 됩니다. 다음 정보를 사용 하 여이 페이지의 필드를 채웁니다.

* __메트릭 이름__ 을 __CPU 사용률 백분율로__ 설정 합니다.
* __연산자__ 를 __보다 큼__ 으로 설정 하 고 __메트릭 임계값__ 을 __70__ 으로 설정 합니다.
* __기간 (분)__ 을 5로 설정 합니다. 시간 수준 __통계__ 를 __평균__ 으로 그대로 둡니다.
* __Count를 늘려__ __작업__ 을 설정 하 고 __인스턴스 수__ 를 __2__ 로 설정 합니다.

마지막으로 __추가__ 단추를 선택 하 여 규칙을 만듭니다.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="5 분 동안 70% CPU >스케일 아웃 규칙을 보여 주는 스크린샷":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>메트릭을 사용 하 여 크기를 조정 하는 규칙 만들기

부하가 적은 경우 규칙의 크기를 조정 하면 VM 인스턴스 수를 줄일 수 있습니다. 다음 예에서는 CPU 로드가 5 분 동안 30% 미만이 면 단일 노드를 최소 2로 해제 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

__규칙__ 섹션에서 __규칙 추가__ 를 선택합니다. __크기 조정 규칙__ 페이지가 표시 됩니다. 다음 정보를 사용 하 여이 페이지의 필드를 채웁니다.

* __메트릭 이름__ 을 __CPU 사용률 백분율로__ 설정 합니다.
* __연산자__ 를 __보다 작음__ 으로, __메트릭 임계값__ 을 __30__ 으로 설정 합니다.
* __기간 (분)__ 을 __5__ 로 설정 합니다.
* __작업__ 을 설정 하 __여 Count__ 를 줄이고 __인스턴스 수__ 를 __1__ 로 설정 합니다.

마지막으로 __추가__ 단추를 선택 하 여 규칙을 만듭니다.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="규모 확장 규칙을 보여 주는 스크린샷":::

규모 확장 및 규모 확장 규칙이 모두 있는 경우 규칙은 다음 스크린샷에 표시 됩니다. 평균 CPU 로드가 5 분 동안 70%를 초과 하는 경우 최대 5 개의 노드를 할당 해야 합니다. CPU 로드가 5 분 동안 30% 미만이 면 단일 노드를 최소 2로 해제 해야 합니다. 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="규칙을 포함 하는 자동 크기 조정 설정을 보여 주는 스크린샷":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>끝점 메트릭을 기반으로 크기 조정 규칙 만들기

배포에 적용 된 이전 규칙입니다. 이제 끝점에 적용 되는 규칙을 추가 합니다. 이 예제에서 요청 대기 시간이 5 분의 평균 70 밀리초를 초과 하는 경우 다른 노드를 할당 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

페이지 맨 아래에서 __+ 크기 조정 조건 추가__ 를 선택 합니다.

__메트릭 기반 크기 조정__ 을 선택한 다음 __규칙 추가__ 를 선택 합니다. __크기 조정 규칙__ 페이지가 표시 됩니다. 다음 정보를 사용 하 여이 페이지의 필드를 채웁니다.

* __메트릭 소스__ 를 __다른 리소스로__ 설정 합니다.
* __리소스 종류__ 를 __온라인 끝점 Machine Learning__ 설정 합니다.
* __리소스__ 를 끝점으로 설정 합니다.
* __메트릭 이름을__ __요청 대기 시간__ 으로 설정 합니다.
* __연산자__ 를 __보다 큼__ 으로 설정 하 고 __메트릭 임계값__ 을 __70__ 으로 설정 합니다.
* __기간 (분)__ 을 __5__ 로 설정 합니다.
* __Count를 늘려__ __작업__ 을 설정 하 고 __인스턴스 수__ 를 1로 설정 합니다.

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="끝점 메트릭 규칙을 보여 주는 스크린샷":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>일정에 따라 크기 조정 규칙 만들기

특정 일에만 또는 특정 시간에 적용 되는 규칙을 만들 수도 있습니다. 이 예제에서 노드 수는 주말에 2로 설정 됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[포털](#tab/azure-portal)

페이지 맨 아래에서 __+ 크기 조정 조건 추가__ 를 선택 합니다. 새 크기 조정 조건에서 다음 정보를 사용 하 여 필드를 채웁니다.
 
* __특정 인스턴스 수로 크기 조정을__ 선택 합니다.
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

- [자동 크기 조정 설정 이해](/azure/azure-monitor/autoscale/autoscale-understanding-settings)
- [일반적인 자동 크기 조정 패턴에 대한 개요](/azure/azure-monitor/autoscale/autoscale-common-scale-patterns)
- [자동 크기 조정 모범 사례](/azure/azure-monitor/autoscale/autoscale-best-practices)
- [Azure 자동 크기 조정 문제 해결](/azure/azure-monitor/autoscale/autoscale-troubleshoot)