---
title: 빠른 시작 - Azure Key Vault에서 비밀 설정 및 검색
description: Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색하는 방법을 보여주는 빠른 시작
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli, mode-api
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 88b32cfed91ccc630e955c6c37aec8fa3f4845be
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133067371"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Key Vault에서 Key Vault를 만듭니다. Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 살펴보세요. Azure CLI는 명령 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 작업을 완료하면 비밀을 저장할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

이 빠른 시작에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

자격 증명 모음에 비밀을 추가하려면 몇 가지 추가 단계만 수행하면 됩니다. 이 암호는 애플리케이션에서 사용할 수 있습니다. 암호는 **ExamplePassword** 로 지정되며 **hVFkk965BuUv** 값을 저장합니다.

아래의 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 명령을 사용하여 **hVFkk965BuUv** 값을 저장하는 **ExamplePassword** 라는 Key Vault에서 비밀을 만듭니다.

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault에서 비밀 검색

이제 해당 URI를 사용하여 Azure Key Vault에 추가한 이 암호를 참조할 수 있습니다. 현재 버전을 가져오려면 **`https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword`** 를 사용합니다.

비밀에 포함된 값을 일반 텍스트로 보려면 Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) 명령을 사용합니다.

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

지금까지 Key Vault를 만들고 비밀을 저장하고, 검색했습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 비밀을 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Key Vault에서 여러 줄 비밀을 저장](multiline-secrets.md)하는 방법을 알아봅니다.
- [Azure CLI az keyvault 명령](/cli/azure/keyvault)에 대한 참조를 참조하세요.
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
