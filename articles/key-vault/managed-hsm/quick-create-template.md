---
title: Azure 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 관리형 HSM 만들기
description: Resource Manager 템플릿을 사용하여 Azure에서 Azure Key Vault 관리형 HSM을 만드는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: da540d63c78e9029addf098b76ec24773e63a782
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131071"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용하여 Key Vault 관리형 HSM 만들기

관리형 HSM은 **FIPS 140-2 수준 3** 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다.  

이 빠른 시작에서는 Resource Manager 템플릿을 배포하여 관리형 HSM을 만드는 프로세스에 중점을 둡니다.  [Resource Manager 템플릿](../../azure-resource-manager/templates/overview.md)은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. Resource Manager 템플릿 개발에 대한 자세한 내용은 [Resource Manager 설명서](../../azure-resource-manager/index.yml) 및 [템플릿 참조](/azure/templates/microsoft.keyvault/allversions)에서 확인할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 있어야 합니다.

- Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
- Azure CLI 버전 2.12.0 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="create-a-manage-hsm"></a>관리 HSM 만들기

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/managed-hsm-create/)에서 나온 것입니다.

템플릿에 정의된 Azure 리소스는 다음과 같습니다.

* **Microsoft.KeyVault/managedHSMs**: Azure Key Vault 관리형 HSM을 만듭니다.

Azure Key Vault 템플릿 샘플을 더 보려면 [여기](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)에서 확인할 수 있습니다.

템플릿에는 계정과 연결된 개체 ID가 필요합니다. 이를 찾으려면 Azure CLI [az ad user show](/cli/azure/ad/user#az_ad_user_show) 명령을 사용하여 이메일 주소를 `--id` 매개 변수에 전달합니다. `--query` 매개 변수를 사용하는 경우에만 출력을 개체 ID로 제한할 수 있습니다.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

테넌트 ID가 필요할 수도 있습니다. 이를 찾으려면 Azure CLI [az ad user show](/cli/azure/account#az_account_show) 명령을 사용합니다. `--query` 매개 변수를 사용하는 경우에만 출력을 테넌트 ID로 제한할 수 있습니다.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 키 자격 증명 모음 및 비밀이 생성됩니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2F%2Fmaster%2Fquickstarts%2Fmicrosoft.keyvault%2Fmanaged-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. 다음 값을 선택하거나 입력합니다.

    지정되지 않은 경우에는 기본 값을 사용하여 키 자격 증명 모음과 비밀을 만듭니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인** 을 클릭합니다.
    - **위치**: 위치를 선택합니다. 예: **미국 중부**
    - **managedHSMName**: 관리형 HSM의 이름을 입력합니다.
    - **테넌트 ID**: 템플릿 함수는 테넌트 ID를 자동으로 검색하므로 기본값을 변경하지 마세요.  값이 없는 경우 [필수 구성 요소](#prerequisites)에서 검색한 테넌트 ID를 입력합니다.
    * **initialAdminObjectIds**: [필수 구성 요소](#prerequisites)에서 검색한 개체 ID를 입력합니다.

3. **구매** 를 선택합니다. 키 자격 증명 모음이 성공적으로 배포되면 알림을 받게 됩니다.

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 관리형 HSM을 만들었습니다. 이 관리형 HSM은 활성화될 때까지 완전하게 작동하지 않습니다. HSM을 활성화하는 방법에 대한 자세한 내용은 [관리형 HSM 활성화](quick-create-cli.md#activate-your-managed-hsm)를 참조하세요.

- [관리형 HSM 개요](overview.md) 읽기
- [관리형 HSM에서 키 관리](key-management.md)에 대해 알아보기
- [관리형 HSM 모범 사례](best-practices.md) 검토
