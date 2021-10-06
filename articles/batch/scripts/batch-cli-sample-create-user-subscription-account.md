---
title: Azure CLI 스크립트 예제 - Batch 계정 만들기 - 사용자 구독 | Microsoft Docs
description: 사용자 구독 모드에서 Azure Batch 계정을 만드는 방법을 알아봅니다. 이 계정은 컴퓨팅 노드를 구독에 할당합니다.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: batch, azure cli 샘플, azure cli 예제, azure cli 코드 샘플
ms.openlocfilehash: 087ba199787e36dccba58f37dd1ebb83c715d729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658662"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 예제: 사용자 구독 모드에서 Batch 계정 만들기

이 스크립트는 사용자 구독 모드에서 Batch 계정을 만듭니다. 구독에 컴퓨팅 노드를 할당하는 계정은 Azure Active Directory 토큰을 통해 인증되어야 합니다. 할당된 컴퓨팅 노드는 구독의 vCPU(코어) 할당량에 집계됩니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.20 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.  

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az role assignment create](/cli/azure/role) | 사용자, 그룹 또는 서비스 사용자에 대한 새 역할 할당을 만듭니다. |
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | 키 자격 증명 모음을 만듭니다. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | 지정된 주요 자격 증명 모음의 보안 정책을 업데이트합니다. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch 계정을 만듭니다.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 추가 CLI 상호 작용을 위해 지정된 Batch 계정에 대해 인증합니다.  |
| [az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
