---
title: Azure CLI 스크립트 예제 - Batch 작업 실행 | Microsoft Docs
description: Azure CLI를 사용하여 Batch 작업을 만들고 작업에 일련의 작업을 추가하는 방법에 대해 알아봅니다. 이 문서에서는 작업 및 작업을 모니터링하는 방법도 보여 줍니다.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, batch 작업, monitor 작업, azure cli 샘플, azure cli 코드 샘플, azure cli 스크립트 샘플
ms.openlocfilehash: 293e517a528b6a1630ff8597f4fa17c87008a78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595183"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI 예제: Azure Batch로 작업 및 태스크 실행

이 스크립트는 Batch 작업을 만들고 일련의 태스크를 작업에 추가합니다. 또한 작업 및 태스크를 모니터링하는 방법도 보여 줍니다. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.20 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch 계정을 만듭니다. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 추가 CLI 상호 작용을 위해 지정된 Batch 계정에 대해 인증합니다.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | 컴퓨팅 노드 풀을 만듭니다.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Batch 작업을 만듭니다.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | 지정된 Batch 작업에 태스크를 추가합니다.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Batch 작업의 속성을 업데이트합니다.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | 지정된 Batch 작업의 세부 정보를 검색합니다.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | 지정된 Batch 작업에서 태스크의 세부 정보를 검색합니다.  |
| [az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
