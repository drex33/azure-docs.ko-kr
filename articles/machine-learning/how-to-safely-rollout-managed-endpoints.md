---
title: 온라인 엔드포인트에 대한 안전한 롤아웃
titleSuffix: Azure Machine Learning
description: 최신 버전의 ML 모델을 중단 없이 롤아웃합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 7f82c65a2aba8057ab3f7cbc6729b83ed597e12b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564806"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)

프로덕션에 배포 된 기존 모델이 있고 새 버전의 모델을 배포 하려고 합니다. 새 ML 모델을 중단 없이 롤아웃하는 방법은 무엇인가요? 파란색-녹색 배포가 좋은 방법입니다. 이 배포는 변경 사항을 완전히 배포하기 전에 소수의 사용자/요청 하위 집합에 롤아웃하는 방식으로 프로덕션에 웹 서비스의 새 버전을 도입하는 방법입니다. 이 문서에서는 온라인 엔드포인트를 사용하고 있다고 가정합닏. 자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

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

* 기존 온라인 끝점 및 배포. 이 문서에서는 [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)의 설명대로 배포를 진행한다고 가정합니다.

* 환경 변수 $ENDPOINT _NAME를 아직 설정 하지 않은 경우 지금 수행 합니다.

   :::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (권장) 샘플 리포지토리를 복제하고 리포지토리의 `cli/` 디렉터리로 전환합니다. 

   ```azurecli
   git clone https://github.com/Azure/azureml-examples
   cd azureml-examples/cli
   ```

이 자습서의 명령은 `deploy-safe-rollout-online-endpoints.sh` 파일에 있고 YAML 구성 파일은 하위 디렉터리 `endpoints/online/managed/sample/`에 있습니다.

## <a name="confirm-your-existing-deployment-is-created"></a>기존 배포가 생성되었는지 확인

다음을 실행 하 여 기존 끝점 및 배포의 상태를 볼 수 있습니다. 

```azurecli
az ml online-endpoint show --name $ENDPOINT_NAME 

az ml online-deployment show --name blue --endpoint $ENDPOINT_NAME 
```

`$ENDPOINT_NAME`이라고 식별된 엔드포인트 및 `blue`라는 배포가 표시됩니다. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>더 많은 트래픽을 처리하도록 기존 배포 스케일링

[관리 되는 온라인 끝점 (미리 보기)을 사용 하 여 machine learning 모델 배포 및 점수 매기기](how-to-deploy-managed-online-endpoints.md)에 설명 된 배포에서을 `instance_count` `1` 배포 yaml 파일의 값으로 설정 합니다. 다음 명령을 사용 하 여 규모를 확장할 수 있습니다 `update` .

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!Note]
> 위의 명령에서를 사용 하 여 `--set` 배포 구성을 재정의 합니다. 또는 yaml 파일을 업데이트 하 고 `update` 입력을 사용 하 여 명령에 입력으로 전달할 수 있습니다 `--file` .

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>새 모델을 배포하지만 아직 트래픽은 보내지 않음

이라는 새 배포를 만듭니다 `green` . 

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="create_green" :::

녹색에는 명시적으로 트래픽을 할당 하지 않았으므로 0 개의 트래픽이 할당 됩니다. 다음 명령을 사용 하 여 확인할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="get_traffic" :::

### <a name="test-the-new-deployment"></a>새 배포 테스트

`green`에는 0%의 트래픽이 할당 되지만 이름을 지정 하 여이를 직접 호출할 수 있습니다 `--deployment` .

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green" :::

REST 클라이언트를 사용하여 트래픽 규칙을 거치지 않고 배포를 직접 호출하려면 다음 HTTP 헤더를 설정합니다. `azureml-model-deployment: <deployment-name>` 아래 코드 조각에서는 `curl` 를 사용 하 여 배포를 직접 호출 합니다. 코드 조각은 Unix/WSL 환경에서 작동 해야 합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="test_green_using_curl" :::

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>적은 비율의 라이브 트래픽으로 새 배포 테스트

배포를 테스트 한 후 `green` 에는 적은 비율의 트래픽을 할당 합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

이제 `green` 배포는 요청의 10%를 받습니다. 

## <a name="send-all-traffic-to-your-new-deployment"></a>새 배포에 모든 트래픽 보내기

`green` 배포가 완전히 만족스러우면 모든 트래픽을 여기로 전환합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>이전 배포 제거

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>엔드포인트 및 배포 삭제

배포를 사용 하지 않는 경우 다음을 사용 하 여 삭제 해야 합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::


## <a name="next-steps"></a>다음 단계
- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [스튜디오에서 관리형 온라인 엔드포인트(미리 보기) 만들기 및 사용](how-to-use-managed-online-endpoint-studio.md)
- [관리 되는 온라인 끝점 및 관리 id (미리 보기)를 사용 하 여 Azure 리소스에 액세스](how-to-access-resources-from-endpoints-managed-identities.md)
- [관리형 온라인 엔드포인트 모니터링(미리 보기)](how-to-monitor-online-endpoints.md)
- [Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증가](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
- [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 대한 비용 보기](how-to-view-online-endpoints-costs.md)
- [관리형 온라인 엔드포인트 SKU 목록(미리 보기)](reference-managed-online-endpoints-vm-sku-list.md)
- [관리형 온라인 엔드포인트 배포 및 채점(미리 보기) 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)
- [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)
