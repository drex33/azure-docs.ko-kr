---
title: '빠른 시작: PowerShell/Azure CLI를 사용하여 Purview 계정 만들기'
description: 이 빠른 시작에서는 Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정을 만드는 방법을 설명합니다.
author: hophanms
ms.author: hophan
ms.date: 10/28/2021
ms.topic: quickstart
ms.service: purview
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: 0adba8cad7baca8188982f10aca8b8b591864ee8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055539"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기

이 빠른 시작에서는 Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정을 만듭니다. [Purview용 PowerShell 참조](/powershell/module/az.purview/)를 사용할 수 있지만 이 문서에서는 PowerShell을 사용하여 계정을 만드는 데 필요한 모든 단계를 설명합니다.

Azure Purview는 데이터 환경을 관리하고 제어하는 데 도움이 되는 데이터 거버넌스 서비스입니다. 온-프레미스, 다중 클라우드 및 SaaS(Software as a Service) 원본에서 데이터에 연결하면 Purview에서 최신 정보 맵을 만듭니다. 중요한 데이터를 식별 및 분류하고 엔드투엔드 계보를 제공합니다. 데이터 소비자는 조직 전체에서 데이터를 검색할 수 있으며, 데이터 관리자는 데이터의 올바른 사용을 감사, 보호 및 보장할 수 있습니다.

Purview에 대한 자세한 내용은 [개요 페이지를 참조](overview.md)하세요. Purview를 조직 전체에 배포하는 방법에 대한 자세한 내용은 [배포 모범 사례를 참조](deployment-best-practices.md)하세요.

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>PowerShell 설치

 클라이언트 머신에 Azure PowerShell 또는 Azure CLI를 설치하여 템플릿을 배포합니다. [명령줄 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>Azure Purview 계정 만들기

1. Azure 자격 증명으로 로그인

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Connect-AzAccount
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az login
    ```

    ---

1. Azure 구독이 여러 개 있으면 사용할 구독을 선택합니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```

    ---

1. Purview 계정에 대한 리소스 그룹을 만듭니다. 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name myResourceGroup -Location 'East US'
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```

    ---

1. Purview 계정 만들기 또는 배포

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    [New-AzPurviewAccount](/powershell/module/az.purview/new-azpurviewaccount) cmdlet을 사용하여 Purview 계정을 만듭니다.

    ```azurepowershell
    New-AzPurviewAccount -Name yourPurviewAccountName -ResourceGroupName myResourceGroup -Location eastus -IdentityType SystemAssigned -SkuCapacity 4 -SkuName Standard -PublicNetworkAccess
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    1. `purviewtemplate.json`과 같은 Purview 템플릿 파일을 만듭니다. `name`, `location` 및 `capacity`(`4`또는 `16`)를 업데이트할 수 있습니다.

        ```json
        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "name": "<yourPurviewAccountName>",
              "type": "Microsoft.Purview/accounts",
              "apiVersion": "2020-12-01-preview",
              "location": "EastUs",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "networkAcls": {
                  "defaultAction": "Allow"
                }
              },
              "dependsOn": [],
              "sku": {
                "name": "Standard",
                "capacity": "4"
              },
              "tags": {}
            }
          ],
          "outputs": {}
        }
        ```

    1. Purview 템플릿 배포

        이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

        ```azurecli
        az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
        ```

    ---

1. 배포 명령에서 결과를 반환합니다. `ProvisioningState`를 찾아서 배포에 성공했는지 확인합니다.

1. 사용자 계정 대신 서비스 주체를 사용하여 Azure Purview 계정을 배포한 경우 Azure CLI 아래 명령도 실행해야 합니다.

    ```azurecli
    az purview account add-root-collection-admin --account-name --resource-group [--object-id]
    ```

    이 명령은 Azure Purview 계정의 루트 컬렉션에 대한 사용자 계정 [컬렉션 관리자](catalog-permissions.md#roles) 권한을 부여합니다. 이렇게 하면 사용자가 Purview 스튜디오에 액세스하고 다른 사용자에 대한 권한을 추가할 수 있습니다. Azure Purview의 권한에 대한 자세한 내용은 [사용 권한 가이드](catalog-permissions.md)를 참조하세요. 컬렉션에 대한 자세한 내용은 [컬렉션 문서 관리](how-to-create-and-manage-collections.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Purview 계정을 만드는 방법을 알아보았습니다.

Purview Studio를 탐색하고, 컬렉션을 만들고, Purview에 대해 액세스 권한을 부여하는 방법을 알아보려면 다음 문서를 참조하세요.

* [Purview Studio를 사용하는 방법](use-purview-studio.md)
* [Azure Purview 계정에 사용자 추가](catalog-permissions.md)
* [컬렉션 만들기](quickstart-create-collection.md)
