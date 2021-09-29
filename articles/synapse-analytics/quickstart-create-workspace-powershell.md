---
title: '빠른 시작: Azure PowerShell을 사용하여 Synapse 작업 영역 만들기'
description: 이 가이드의 단계에 따라 Azure PowerShell을 사용하여 Azure Synapse 작업 영역을 만듭니다.
services: synapse-analytics
author: rothja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee02f650ad20eb9f6673875823090ea3d242b7cd
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093006"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure Synapse 작업 영역 만들기

Azure PowerShell은 PowerShell에서 직접 Azure 리소스를 관리하기 위한 cmdlet 세트입니다. 브라우저에서 Azure Cloud Shell과 함께 사용할 수 있습니다. macOS, Linux 또는 Windows에도 설치할 수 있습니다.

이 빠른 시작에서는 Azure PowerShell을 사용하여 Synapse 작업 영역을 만드는 방법에 대해 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Data Lake Storage Gen2 스토리지 계정](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > 선택한 ADLS Gen2 계정을 Azure Synapse 작업 영역에서 읽고 쓸 수 있어야 합니다. 기본 스토리지 계정으로 연결하는 모든 스토리지 계정의 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account) 페이지에서 설명한 대로 스토리지 계정을 만들 때 **계층 구조 네임스페이스** 를 사용하도록 설정해야 합니다.

Cloud Shell을 사용하도록 선택하는 경우 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.

### <a name="install-the-azure-powershell-module-locally"></a>로컬로 Azure PowerShell 모듈 설치

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

Azure PowerShell을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 로그인](/powershell/azure/authenticate-azureps)을 참조하세요.

### <a name="install-the-azure-synapse-powershell-module"></a>Azure Synapse PowerShell 모듈 설치

> [!IMPORTANT]
> **Az.Synapse** PowerShell 모듈은 미리 보기에 있는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Synapse 작업 영역 만들기

1. Azure Synapse 작업 영역에 대한 리소스를 만드는 데 필요한 환경 변수를 정의합니다.

   |        변수 이름        |                                                 설명                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | 기존 ADLS Gen2 스토리지 계정에 대한 이름입니다.                                                           |
   | StorageAccountResourceGroup | 기존 ADLS Gen2 스토리지 계정 리소스 그룹의 이름입니다.                                             |
   | FileShareName               | 기존 스토리지 파일 시스템의 이름입니다.                                                                  |
   | SynapseResourceGroup        | Azure Synapse 리소스 그룹의 새 이름을 선택합니다.                                                    |
   | 지역                      | [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/#overview) 중 하나를 선택합니다. |
   | SynapseWorkspaceName        | 새 Azure Synapse 작업 영역의 고유한 이름을 선택합니다.                                                  |
   | SqlUser                     | 새 사용자 이름에 대한 값을 선택합니다.                                                                          |
   | SqlPassword                 | 보안 암호를 선택합니다.                                                                                   |
   | ClientIP                    | PowerShell이 실행되는 시스템의 공용 IP 주소입니다.                                             |
   |                             |                                                                                                             |

1. Azure Synapse 작업 영역의 컨테이너로 리소스 그룹을 만듭니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Azure Synapse 작업 영역을 만듭니다.

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Azure Synapse 작업 영역에 대한 웹 및 개발 URL을 가져옵니다.

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.dev
   ```

1. 머신에서 Azure Synapse 작업 영역에 액세스할 수 있도록 허용하는 방화벽 규칙을 만듭니다.

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. 환경 변수 `WorkspaceWeb`에 저장된 Azure Synapse 작업 영역 웹 URL 주소를 열어 작업 영역에 액세스합니다.

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse 작업 영역 웹](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure Synapse 작업 영역을 삭제하려면 다음 단계를 수행합니다.

> [!WARNING]
> Azure Synapse 작업 영역을 삭제하면 포함된 SQL 풀 및 작업 영역 메타데이터 데이터베이스에 저장된 분석 엔진과 데이터가 제거됩니다. 더 이상 SQL 또는 Apache Spark 엔드포인트에 연결할 수 없게 됩니다. 모든 코드 아티팩트(쿼리, Notebook, 작업 정의 및 파이프라인)가 삭제됩니다. 작업 영역을 삭제해도 작업 영역에 연결된 Data Lake Store Gen2의 데이터에는 영향을 주지 **않습니다**.

이 문서에서 만든 Azure Synapse 작업 영역이 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>다음 단계

다음으로 [SQL 풀 만들기](quickstart-create-sql-pool-studio.md) 또는 [Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-studio.md)를 수행하여 데이터를 분석하고 살펴볼 수 있습니다.
