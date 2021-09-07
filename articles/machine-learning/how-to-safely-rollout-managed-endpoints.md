---
title: 온라인 엔드포인트에 대한 안전한 롤아웃
titleSuffix: Azure Machine Learning
description: 최신 버전의 ML 모델을 중단 없이 롤아웃합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5fe647fba68150f45c450d71f208cf6a53dd0b98
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568101"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)

프로덕션에 기존 모델이 배포되어 있고 이 모델의 새 버전을 배포하려고 합니다. 새 ML 모델을 중단 없이 롤아웃하는 방법은 무엇인가요? 파란색-녹색 배포가 좋은 방법입니다. 이 배포는 변경 사항을 완전히 배포하기 전에 소수의 사용자/요청 하위 집합에 롤아웃하는 방식으로 프로덕션에 웹 서비스의 새 버전을 도입하는 방법입니다. 이 문서에서는 온라인 엔드포인트를 사용하고 있다고 가정합닏. 자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * 모델 버전 1을 제공하는 "파란색"이라는 새로운 온라인 엔드포인트 배포
> * 이 배포가 더 많은 요청을 처리할 수 있도록 스케일링
> * 라이브 트래픽을 허용하지 않는 "녹색"이라는 엔드포인트에 모델 버전 2 배포
> * 녹색 배포를 격리된 상태로 테스트 
> * 라이브 트래픽의 10%를 녹색 배포로 보내기
> * 모든 라이브 트래픽을 녹색 배포로 완전히 전환
> * 이제 사용되지 않는 v1 파란색 배포 삭제

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 기계 학습을 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure CLI 및 ML 확장을 설치하고 구성해야 합니다. 자세한 내용은 [CLI(v2)(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요. 

* Azure 리소스 그룹이 있고, 사용자(또는 사용하는 서비스 주체)에게 이에 대한 `Contributor` 액세스 권한이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 리소스 그룹이 있을 것입니다. 

* Azure Machine Learning 작업 영역이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 작업 영역이 있을 것입니다.

* Azure CLI의 기본값을 아직 설정하지 않은 경우 기본 설정을 저장해야 합니다. 값을 반복해서 전달하지 않으려면 다음을 실행합니다.

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* 기존 관리형 엔드포인트입니다. 이 문서에서는 [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)의 설명대로 배포를 진행한다고 가정합니다.

* 환경 변수 $ENDPOINT_NAME을 아직 설정하지 않은 경우 지금 설정합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (권장) 샘플 리포지토리를 복제하고 리포지토리의 `cli/` 디렉터리로 전환합니다. 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

이 자습서의 명령은 `deploy-declarative-safe-rollout-online-endpoints.sh` 파일에 있고 YAML 구성 파일은 하위 디렉터리 `endpoints/online/managed/canary-declarative-flow/`에 있습니다.

## <a name="confirm-your-existing-deployment-is-created"></a>기존 배포가 생성되었는지 확인

기존 배포의 상태는 다음을 실행하여 확인할 수 있습니다. 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

`$ENDPOINT_NAME`이라고 식별된 엔드포인트 및 `blue`라는 배포가 표시됩니다. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>더 많은 트래픽을 처리하도록 기존 배포 스케일링

[관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)에 설명된 배포에서 `instance_count`의 값을 `1`로 설정합니다. 더 많은 트래픽을 처리하기 위해 YAML 파일의 두 번째 버전(`2-scale-blue.yml`)은 값을 `2`로 변경합니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

배포를 다음과 같이 업데이트합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> YAML을 사용한 업데이트는 선언적입니다. 즉, YAML의 변경 내용이 기본 Azure Resource Manager 리소스(엔드포인트 및 배포)에 반영됩니다. 이 접근법을 사용하면 [GitOps](https://www.atlassian.com/git/tutorials/gitops)가 용이해집니다. 엔드포인트/배포에 대한 *모든* 변경 내용이 YAML을 거칩니다(`instance_count`도 해당). 부작용은 YAML에서 배포를 제거하고 파일을 사용하여 `az ml endpoint update`를 실행할 경우 해당 배포가 삭제된다는 것입니다. 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>새 모델을 배포하지만 아직 트래픽은 보내지 않음

새 모델을 배포하려면 구성 파일의 `deployments` 섹션에 새 섹션을 추가하되, `traffic` 섹션에 트래픽의 0%를 수신하도록 지정합니다. `3-create-green.yml` 파일에 다음 변경 내용이 통합됩니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

배포 업데이트: 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>새 배포 테스트

방금 생성된 `green` 배포에 구성이 0% 트래픽을 지정했습니다. 테스트하려면 `--deployment` 이름을 지정하여 직접 호출합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

REST 클라이언트를 사용하여 트래픽 규칙을 거치지 않고 배포를 직접 호출하려면 다음 HTTP 헤더를 설정합니다. `azureml-model-deployment: <deployment-name>`

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>적은 비율의 라이브 트래픽으로 새 배포 테스트

`green` 배포를 테스트한 후 `4-flight-green.yml` 파일은 구성 파일에서 `traffic` 구성을 수정하여 트래픽의 일정 비율을 제공하는 방법을 보여줍니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

강조 표시된 줄 외에 구성 파일은 변경되지 않습니다. 배포를 다음과 같이 업데이트합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

이제 `green` 배포는 요청의 10%를 받습니다. 

## <a name="send-all-traffic-to-your-new-deployment"></a>새 배포에 모든 트래픽 보내기

`green` 배포가 완전히 만족스러우면 모든 트래픽을 여기로 전환합니다. 다음 코드 조각은 구성 파일의 관련 코드만 보여주며, 그렇지 않은 경우 변경되지 않습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

배포를 업데이트합니다. 

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>이전 배포 제거

이전 `blue` 배포를 삭제하여 새 모델로의 교체를 완료합니다. 최종 구성 파일은 다음과 같습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

배포를 다음과 같이 업데이트합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>엔드포인트 및 배포 삭제

배포를 사용하지 않을 경우 다음을 사용하여 삭제해야 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>다음 단계
- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [자습서: 관리형 온라인 엔드포인트 및 시스템 관리 ID를 사용하여 Azure 리소스에 액세스(미리 보기)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [관리형 온라인 엔드포인트 모니터링(미리 보기)](how-to-monitor-online-endpoints.md)
- [Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증가](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리형 온라인 엔드포인트 SKU 목록(미리 보기)](reference-managed-online-endpoints-vm-sku-list.md)
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)
- [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)