---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Key Vault 만들기
description: Azure CLI를 사용하여 Azure Key Vault를 만드는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: mode-api
ms.openlocfilehash: 41406ca37978ee5873e016b2d3adf3bb22222526
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048097"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 키 자격 증명 모음 만들기

Azure Key Vault는 [키](../keys/index.yml), [비밀](../secrets/index.yml) 및 [인증서](../certificates/index.yml)에 대한 보안 저장소를 제공하는 클라우드 서비스입니다. Key Vault에 대한 자세한 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요. 키 자격 증명 모음에 저장할 수 있는 항목에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-certificates.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 빠른 시작에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고 삭제했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](overview.md) 참조
- [Azure Key Vault 보안 개요](security-features.md)를 검토하세요.
- [Azure CLI az keyvault 명령](/cli/azure/keyvault)에 대한 참조를 참조하세요.
