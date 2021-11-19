---
title: 온라인 끝점 배포 (미리 보기) 문제 해결
titleSuffix: Azure Machine Learning
description: 온라인 끝점을 사용 하 여 몇 가지 일반적인 배포 및 점수 매기기 오류를 해결 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: a14921ccbceaed4f60532fd5ba4602e5a813d18d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808150"
---
# <a name="troubleshooting-online-endpoints-deployment-and-scoring-preview"></a>온라인 끝점 배포 및 점수 매기기 (미리 보기) 문제 해결

Azure Machine Learning 온라인 끝점 (미리 보기)의 배포 및 점수 매기기에서 일반적인 문제를 해결 하는 방법에 대해 알아봅니다.

이 문서는 문제 해결에 접근하는 방식으로 구성됩니다.

1. 클라우드에 배포하기 전에 [로컬 배포](#deploy-locally)를 사용하여 모델을 로컬에서 테스트하고 디버그합니다.
1. [컨테이너 로그](#get-container-logs)를 사용하여 문제를 디버그합니다.
1. 발생할 수 있는 [일반적인 배포 오류](#common-deployment-errors)와 이를 해결하는 방법을 이해합니다.

[HTTP 상태 코드](#http-status-codes) 섹션에서는 REST 요청으로 엔드포인트를 채점할 때 호출 및 예측 오류가 HTTP 상태 코드에 매핑하는 방법을 설명합니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Azure CLI](/cli/azure/install-azure-cli)
* [CLI(v2) 설치, 설정 및 사용(미리 보기)](how-to-configure-cli.md)

## <a name="deploy-locally"></a>로컬로 배포

로컬 배포는 로컬 Docker 환경에 모델을 배포합니다. 로컬 배포는 클라우드에 배포하기 전에 테스트 및 디버그하는 데 유용합니다.

> [!TIP]
> Visual Studio Code를 사용 하 여 끝점을 로컬로 테스트 하 고 디버그할 수 있습니다. 자세한 내용은 [Visual Studio Code에서 로컬로 온라인 끝점 디버그](how-to-debug-managed-online-endpoints-visual-studio-code.md)를 참조 하세요.

로컬 배포는 로컬 엔드포인트의 생성, 업데이트 및 삭제를 지원합니다. 또한 엔드포인트에서 로그를 호출하고 얻을 수 있습니다. 로컬 배포를 사용하려면 `--local`을 적절한 CLI 명령에 추가합니다.

```azurecli
az ml online-deployment create --endpoint-name <endpoint-name> -n <deployment-name> -f <spec_file.yaml> --local
```

로컬 배포의 일부로 다음 단계가 수행됩니다.

- Docker는 새 컨테이너 이미지를 빌드하거나 로컬 Docker 캐시에서 기존 이미지를 풀합니다. 기존 이미지가 사양 파일의 환경 부분과 일치하는 경우 해당 이미지가 사용됩니다.
- Docker는 모델 및 코드 파일과 같은 탑재된 로컬 아티팩트로 새 컨테이너를 시작합니다.

자세한 내용은 [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점에서 로컬 배포](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)를 참조하세요.

## <a name="get-container-logs"></a>컨테이너 로그 가져오기

모델이 배포된 VM에 직접 액세스할 수는 없습니다. 그러나 VM에서 실행되는 일부 컨테이너에서 로그를 가져올 수 있습니다. 정보의 양은 배포의 프로비전 상태에 따라 달라집니다. 지정된 컨테이너가 실행되고 있으면 해당 콘솔 출력이 표시되고, 그렇지 않으면 나중에 다시 시도하라는 메시지가 표시됩니다.

컨테이너에서 로그 출력을 보려면 다음 CLI 명령을 사용합니다.

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

또는

```azurecli
    az ml online-deployment get-logs --endpoint-name <endpoint-name> --name <deployment-name> --lines 100
```

`az configure`를 통해 이러한 매개 변수를 아직 설정하지 않은 경우 위의 명령에 `--resource-group` 및 `--workspace-name`을 추가합니다.

이러한 매개 변수를 설정하는 방법에 대한 정보를 보고 현재 값이 이미 설정된 경우 다음을 실행합니다.

```azurecli
az ml online-deployment get-logs -h
```

기본적으로 로그는 유추 서버에서 풀됩니다. 로그에는 'score.py' 코드의 print/log 문을 포함하는 유추 서버의 콘솔 로그가 포함되어 있습니다.

> [!NOTE]
> Python 로깅을 사용하는 경우 메시지가 로그에 게시되도록 하려면 올바른 로깅 수준 순서를 사용해야 합니다. 예를 들면 INFO입니다.


`–-container storage-initializer`를 전달하여 스토리지 이니셜라이저 컨테이너에서 로그를 가져올 수도 있습니다. 이러한 로그에는 코드 및 모델 데이터가 컨테이너에 성공적으로 다운로드되었는지 여부에 대한 정보가 포함됩니다.

자세한 정보를 보려면 명령에 `--help` 및/또는 `--debug`를 추가합니다. 문제 해결에 도움이 되도록 `x-ms-client-request-id` 헤더를 포함합니다.

## <a name="common-deployment-errors"></a>일반적인 배포 오류

다음은 배포 작업 상태의 일부로 보고되는 일반적인 배포 오류 목록입니다.

* [OutOfQuota](#error-outofquota)
* [OutOfCapacity](#error-outofcapacity)
* [BadArgument](#error-badargument)
* [ResourceNotReady](#error-resourcenotready)
* [ResourceNotFound](#error-resourcenotfound)
* [OperationCancelled](#error-operationcancelled)
* [InternalServerError](#error-internalservererror)

### <a name="error-outofquota"></a>오류: OutOfQuota

다음은 Azure 서비스를 사용 하는 경우 할당량이 부족 하 게 될 수 있는 일반적인 리소스 목록입니다.

* [CPU](#cpu-quota)
* [역할 할당](#role-assignment-quota)
* [엔드포인트](#endpoint-quota)
* [Kubernetes](#kubernetes-quota)
* [기타](#other-quota)

#### <a name="cpu-quota"></a>CPU 할당량

모델을 배포하기 전에 충분한 컴퓨팅 할당량이 있어야 합니다. 이 할당량은 구독, 작업 영역, SKU 및 지역별로 사용할 수 있는 가상 코어의 양을 정의합니다. 각 배포는 사용 가능한 할당량에서 차감하고 SKU 유형에 따라 삭제 후 다시 추가합니다.

이를 완화할 수 있는 한 가지 방법은 사용되지 않은 배포를 삭제할 수 있는지 확인하는 것입니다. 또는 [할당량 증가 요청](how-to-manage-quotas.md#request-quota-increases)을 제출할 수 있습니다.

#### <a name="role-assignment-quota"></a>역할 할당 할당량

이 구독에서 사용 하지 않는 역할 할당을 삭제 하십시오. Access Control 메뉴에서 Azure Portal의 모든 역할 할당을 확인할 수 있습니다.

#### <a name="endpoint-quota"></a>끝점 할당량

이 구독에서 사용 되지 않는 끝점을 삭제 하십시오.

#### <a name="kubernetes-quota"></a>Kubernetes 할당량

요청한 CPU 또는 메모리를 만족할 수 없습니다. 요청 또는 클러스터를 조정 하세요.

#### <a name="other-quota"></a>기타 할당량

배포의 일부로 제공된 `score.py`를 실행하기 위해 Azure는 `score.py`에 필요한 모든 리소스를 포함하는 컨테이너를 만들고 해당 컨테이너에서 채점 스크립트를 실행합니다.

컨테이너를 시작할 수 없는 경우 점수 매기기가 발생 하지 않을 수 있습니다. 컨테이너가 지원할 수 있는 것 보다 많은 리소스를 요청 하 고 있을 수 있습니다 `instance_type` . 그렇다면 온라인 배포의 `instance_type`을 업데이트하는 것이 좋습니다.

오류에 대한 정확한 원인을 보려면 다음을 실행합니다. 

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

### <a name="error-outofcapacity"></a>오류: OutOfCapacity

Azure Machine Learning 용량 부족으로 인해 지정된 VM 크기를 프로비저닝하지 못했습니다. 나중에 다시 시도하거나 다른 지역에 배포해 보세요.

### <a name="error-badargument"></a>오류: BadArgument

다음은이 오류를 발생 시킬 수 있는 이유에 대 한 목록입니다.

* [리소스 요청이 제한 보다 큽니다.](#resource-requests-greater-than-limits)
* [리소스를 다운로드할 수 없습니다.](#unable-to-download-resources)

#### <a name="resource-requests-greater-than-limits"></a>리소스 요청이 제한 보다 큼

리소스에 대 한 요청은 한도 보다 작거나 같아야 합니다. 제한을 설정 하지 않으면 계산을 Azure Machine Learning 작업 영역에 연결할 때 기본값이 설정 됩니다. Azure Portal 또는 명령을 사용 하 여 제한을 확인할 수 있습니다 `az ml compute show` .

#### <a name="unable-to-download-resources"></a>리소스를 다운로드할 수 없습니다.

계산 리소스를 프로 비전 한 후 배포를 만드는 동안 Azure는 ACR (작업 영역 개인 Azure Container Registry)에서 사용자 컨테이너 이미지를 끌어와 작업 영역 저장소 계정의 사용자 컨테이너에 사용자 모델 및 코드 아티팩트를 탑재 하려고 합니다.

먼저 ACR 액세스에 문제가 있는지 확인 합니다.

Blob을 풀하기 위해 Azure는 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 스토리지 계정에 액세스합니다.

  - SystemAssigned를 사용하여 연결된 엔드포인트를 만든 경우 Azure RBAC(역할 기반 액세스 제어) 권한이 자동으로 부여되며 추가 권한이 필요하지 않습니다.

  - UserAssigned를 사용하여 연결된 엔드포인트를 만든 경우 사용자의 관리 ID에는 작업 영역 스토리지 계정에 대한 스토리지 Blob 데이터 읽기 권한이 있어야 합니다.

이 과정에서 작업이 실패 한 단계에 따라 몇 가지 문제를 실행할 수 있습니다.

* [사용자 컨테이너 이미지를 다운로드할 수 없습니다.](#unable-to-download-user-container-image)
* [사용자 모델 또는 코드 아티팩트를 다운로드할 수 없음](#unable-to-download-user-model-or-code-artifacts)

이러한 오류에 대한 자세한 내용을 얻으려면 다음을 실행합니다.

```azurecli
az ml online-deployment get-logs -n <endpoint-name> --deployment <deployment-name> --l 100
``` 

#### <a name="unable-to-download-user-container-image"></a>사용자 컨테이너 이미지를 다운로드할 수 없음

사용자 컨테이너를 찾을 수 없는 것일 수 있습니다.

작업 영역 ACR에서 컨테이너 이미지를 사용할 수 있는지 확인합니다.

예를 들어 이미지가 `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest`인 경우 `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table`을 사용하여 리포지토리를 확인합니다.

#### <a name="unable-to-download-user-model-or-code-artifacts"></a>사용자 모델 또는 코드 아티팩트 다운로드할 수 없음

사용자 모델 또는 코드 아티팩트 를 찾을 수 없는 것일 수 있습니다.

모델 및 코드 아티팩트가 배포와 동일한 작업 영역에 등록되어 있는지 확인합니다. `show` 명령을 사용하여 작업 영역에서 모델 또는 코드 아티팩트에 대한 세부 정보를 표시합니다. 

- 예를 들면 다음과 같습니다. 
  
  ```azurecli
  az ml model show --name <model-name>
  az ml code show --name <code-name> --version <version>
  ```
 
  작업 영역 스토리지 계정에 Blob이 있는지 확인할 수도 있습니다.

- 예를 들어 Blob이 인 경우 `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl` 이 명령을 사용하여 Blob이 있는지 확인할 수 있습니다.

  `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

### <a name="error-resourcenotready"></a>오류: ResourceNotReady

배포의 일부로 제공된 `score.py`를 실행하기 위해 Azure는 `score.py`에 필요한 모든 리소스를 포함하는 컨테이너를 만들고 해당 컨테이너에서 채점 스크립트를 실행합니다. 이 시나리오의 오류는 실행 시 이 컨테이너가 충돌한다는 것입니다. 즉, 채점이 발생할 수 없습니다. 이 오류는 다음과 같은 경우에 발생합니다.

- `score.py`에 오류가 있습니다. `get-logs`를 사용하여 일반적인 문제를 진단합니다.
    - 가져온 패키지이지만 conda 환경에 없는 패키지입니다.
    - 구문 오류입니다.
    - `init()`메서드의 오류입니다.
- `get-logs`가 로그를 생성하지 않는 경우 일반적으로 컨테이너를 시작하지 못했는지를 의미합니다. 이 문제를 디버그하려면 대신 [로컬로 배포해](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/how-to-troubleshoot-online-endpoints.md#deploy-locally) 보세요.
- 준비 또는 활동성 프로브가 올바르게 설정되지 않았습니다.
- 종속성 누락 등 컨테이너의 환경 설정에 오류가 있습니다.

### <a name="error-resourcenotfound"></a>오류: ResourceNotFound

이 오류는 Azure Resource Manager 필요한 리소스를 찾을 수 없을 때 발생합니다. 예를 들어 스토리지 계정이 참조되었지만 지정된 경로에서 찾을 수 없는 경우 이 오류가 발생합니다. 정확한 경로 또는 이름의 철자로 제공되었을 수 있는 리소스를 다시 확인해야 합니다.

이 오류 코드에 대한 자세한 내용은 [리소스를 찾을 수 없음 오류 해결](../azure-resource-manager/troubleshooting/error-not-found.md)을 참조하세요. 

### <a name="error-operationcancelled"></a>오류: OperationCancelled

Azure 작업은 특정 우선 순위 수준을 가지며 가장 높은 수준에서 가장 낮은 수준으로 실행됩니다. 이 오류는 우선 순위가 높은 다른 작업에 의해 작업이 재정의된 경우에 발생합니다. 작업을 다시 시도하면 취소 없이 작업을 수행할 수 있습니다.

### <a name="error-internalservererror"></a>오류: InternalServerError

안정적이고 안정적인 서비스를 제공하기 위해 최선을 다하지만 계획대로 진행되지 않는 경우도 있습니다. 이 오류가 발생하면 어떤 것이 우리 쪽에 맞지 않아서 수정해야 한다는 의미입니다. 모든 관련 정보와 함께 [고객 지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출하면 문제를 해결하겠습니다. 

## <a name="autoscaling-issues"></a>자동 조정 문제

자동 크기 조정에 문제가 있는 경우 Azure 자동 크기 [조정 문제 해결을](../azure-monitor/autoscale/autoscale-troubleshoot.md)참조하세요.

## <a name="bandwidth-limit-issues"></a>대역폭 제한 문제

관리되는 온라인 엔드포인트에는 각 엔드포인트에 대한 대역폭 제한이 있습니다. 여기에서 [Azure Machine Learning 리소스에 대한 할당량 관리 및 증가에서](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview) 제한 구성을 찾을 수 있습니다. 대역폭 사용량이 제한을 초과하면 요청이 지연됩니다. 대역폭 지연을 모니터링하려면:

- 메트릭 "네트워크 바이트"를 사용하여 현재 대역폭 사용량을 파악합니다. 자세한 내용은 [관리되는 온라인 엔드포인트 모니터링을 참조하세요.](how-to-monitor-online-endpoints.md)
- 대역폭 제한이 적용되는 경우 두 개의 응답 트레일러가 반환됩니다. 
    - `ms-azureml-bandwidth-request-delay-ms`: 요청 스트림 전송에 걸린 지연 시간(밀리초)입니다.
    - `ms-azureml-bandwidth-response-delay-ms`: 응답 스트림 전송에 걸린 지연 시간(밀리초)입니다.

## <a name="http-status-codes"></a>HTTP 상태 코드

REST 요청을 사용하여 온라인 엔드포인트에 액세스하는 경우 반환된 상태 코드는 HTTP 상태 코드에 대한 표준을 [준수합니다.](https://aka.ms/http-status-codes) 다음은 엔드포인트 호출 및 예측 오류가 HTTP 상태 코드에 매핑되는 방법에 대한 세부 정보입니다.

| 상태 코드| 이유 구문 |  이 코드가 반환되는 이유 |
| --- | --- | --- |
| 200 | 확인 | 대기 시간 범위 내에서 모델이 성공적으로 실행되었습니다. |
| 401 | 권한 없음 | 점수와 같은 요청된 작업을 수행할 권한이 없거나 토큰이 만료되었습니다. |
| 404 | 찾을 수 없음 | URL이 올바르지 않습니다. |
| 408 | 요청 시간 초과 | 모델 실행이 모델 배포 구성의 `request_settings`에 있는 `request_timeout_ms`에 제공된 제한 시간보다 오래 걸렸습니다.|
| 424 | 모델 오류 | 모델 컨테이너가 200이 아닌 응답을 반환하면 Azure는 424를 반환합니다. 응답 헤더를 확인하고 `ms-azureml-model-error-statuscode` `ms-azureml-model-error-reason` 자세한 내용을 확인합니다. |
| 429 | 속도 제한 | 엔드포인트에 초당 100개가 넘는 요청을 보내려고 했습니다. |
| 429 | 보류 중인 요청이 너무 많음 | 모델이 처리할 수 있는 것보다 더 많은 요청을 받고 있습니다. 언제든지 2 `max_concurrent_requests_per_instance`  *  `instance_count` * 요청을 허용합니다. 추가 요청이 거부됩니다. `request_settings` 및 `scale_settings` 아래의 모델 배포 구성에서 이러한 설정을 확인할 수 있습니다. 자동 크기 조정을 사용하는 경우 모델은 시스템을 스케일 업할 수 있는 것보다 더 빠르게 요청을 받고 있습니다. 자동 크기 조정을 사용하면 [지수 백오프](https://aka.ms/exponential-backoff)를 사용하여 요청을 다시 보낼 수 있습니다. 이렇게 하면 시스템이 조정할 시간을 확보할 수 있습니다. |
| 500 | 내부 서버 오류 | Azure ML 프로비전된 인프라가 실패합니다. |

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)
- [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)

