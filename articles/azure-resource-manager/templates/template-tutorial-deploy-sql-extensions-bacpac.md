---
title: 템플릿을 사용하여 SQL BACPAC 파일 가져오기
description: Azure SQL Database 확장을 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 SQL BACPAC 파일을 가져오는 방법에 대해 알아봅니다.
author: mumian
ms.date: 09/30/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3310d3f33d7aa31a24a19bc7c43b59173ffd78d6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456047"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>자습서: ARM 템플릿을 사용하여 SQL BACPAC 파일 가져오기

Azure SQL Database 확장을 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 파일을 가져오는 방법에 대해 알아봅니다. 배포 아티팩트는 배포를 완료하는 데 필요한 기본 템플릿 파일 이외의 모든 파일입니다. BACPAC 파일은 아티팩트입니다.

이 자습서에서는 [논리 SQL 서버](../../azure-sql/database/logical-servers.md) 및 단일 데이터베이스를 배포하는 템플릿을 만들고 BACPAC 파일을 가져옵니다. ARM 템플릿을 사용하여 Azure 가상 머신 확장을 배포하는 방법에 대한 자세한 내용은 [자습서: ARM 템플릿을 사용하여 가상 머신 확장 배포](./template-tutorial-deploy-vm-extensions.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
>
> - BACPAC 파일을 준비합니다.
> - 빠른 시작 템플릿을 엽니다.
> - 템플릿을 편집합니다.
> - 템플릿을 배포합니다.
> - 배포를 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

- Resource Manager Tools 확장이 있는 Visual Studio Code. [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](./quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.
- 보안을 강화하려면 서버 관리자 계정에 대해 생성된 암호를 사용합니다. [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 PowerShell 또는 Bash에서 다음 명령을 실행할 수 있습니다.

    ```shell
    openssl rand -base64 32
    ```

    자세히 알아보려면 `man openssl rand`를 실행하여 수동 페이지를 엽니다.

    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: ARM 템플릿 배포에 Azure Key Vault 통합](./template-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-a-bacpac-file"></a>BACPAC 파일 준비

BACPAC 파일은 [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)에서 공유됩니다. 사용자 고유의 데이터베이스를 만들려면 [Azure SQL Database에서 BACPAC 파일로 데이터베이스 내보내기](../../azure-sql/database/database-export.md)를 참조하세요. 사용자 고유의 위치에 파일을 게시하기로 선택하는 경우 자습서의 뒷부분에서 템플릿을 업데이트해야 합니다.

ARM 템플릿을 사용하여 BACPAC 파일을 가져오려면 먼저 Azure Storage 계정에 BACPAC 파일을 저장해야 합니다. 다음 PowerShell 스크립트는 다음 단계를 수행하여 BACPAC 파일을 준비합니다.

- BACPAC 파일 다운로드
- Azure Storage 계정 만들기
- 스토리지 계정 Blob 컨테이너를 만듭니다.
- 컨테이너에 BACPAC 파일 업로드
- 스토리지 계정 키, Blob URL, 리소스 그룹 이름 및 위치를 표시합니다.

1. **사용해 보기** 를 선택하여 Cloud Shell을 엽니다. 그런 후, 다음 PowerShell 스크립트를 복사하여 셸 창에 붙여넣습니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "The project name and location are $projectName and $location"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 스토리지 계정 키, BACPAC 파일 URL, 프로젝트 이름 및 위치를 저장합니다. 이 자습서의 후반부에서 템플릿을 배포할 때 해당 값을 사용합니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

이 자습서에서 사용된 템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)에 저장됩니다.

1. Visual Studio Code에서 **파일** > **파일 열기** 를 차례로 선택합니다.
1. **파일 이름** 에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. **열기** 를 선택하여 파일을 엽니다.

    템플릿에는 다음과 같은 2개의 리소스가 정의되어 있습니다.

   - `Microsoft.Sql/servers`. [템플릿 참조](/azure/templates/microsoft.sql/servers)를 참조하세요.
   - `Microsoft.SQL.servers/databases`. [템플릿 참조](/azure/templates/microsoft.sql/servers/databases)를 참조하세요.

     템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.

1. **파일** > **다른 이름으로 저장** 을 선택하여 파일 복사본을 로컬 컴퓨터에 _azuredeploy.json_ 이라는 이름으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

1. `parameters` 섹션의 끝에 두 개의 매개 변수를 추가하여 스토리지 계정 키와 BACPAC URL을 설정합니다.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    `adminPassword` 속성의 닫는 중괄호(`}`) 뒤에 쉼표를 추가합니다. Visual Studio Code에서 JSON 파일의 형식을 지정하려면 Shift + Alt + F를 선택합니다.

1. 템플릿에 두 개의 리소스를 추가합니다.

    - SQL Database 확장이 BACPAC 파일을 가져올 수 있도록 허용하려면 Azure 서비스의 트래픽을 허용해야 합니다. SQL 서버가 배포되면 방화벽 규칙은 **Azure 서비스 및 리소스가 이 서버에 액세스하도록 허용** 설정을 켭니다.

      서버 정의 아래에 다음 방화벽 규칙을 추가합니다.

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2021-02-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        다음 예제에서는 업데이트된 템플릿을 보여줍니다.

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png" alt-text="방화벽 정의를 사용하는 템플릿.":::

    - 다음 JSON을 사용하여 데이터베이스 정의에 SQL Database 확장 리소스를 추가합니다.

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        다음 예제에서는 업데이트된 템플릿을 보여줍니다.

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png" alt-text="SQL Database 확장이 있는 템플릿.":::

        리소스 정의를 이해하려면 API 버전의 [SQL Database 확장 참조](/azure/templates/microsoft.sql/servers/databases/extensions)를 참조하세요. 다음은 중요한 요소입니다.

        - `dependsOn`: 데이터베이스가 생성된 후 확장 리소스를 만들어야 합니다.
        - `storageKeyType`: 사용할 스토리지 키 형식을 지정합니다. 값은 `StorageAccessKey` 또는 `SharedAccessKey`일 수 있습니다. 이 자습서에서는 `StorageAccessKey`를 사용합니다.
        - `storageKey`: BACPAC 파일이 저장된 스토리지 계정의 키를 지정합니다. 스토리지 키 형식이 `SharedAccessKey`이면 앞에 “?”가 있어야 합니다.
        - `storageUri`: 스토리지 계정에 저장된 BACPAC 파일의 URL을 지정합니다.
        - `administratorLogin`: SQL 관리자의 계정 이름입니다.
        - `administratorLoginPassword`: SQL 관리자의 암호입니다. 생성된 암호를 사용하려면 [필수 구성 요소](#prerequisites)를 참조하세요.

다음 예제에서는 완성된 템플릿을 보여줍니다.

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>템플릿 배포

BACPAC 파일을 준비할 때 사용된 프로젝트 이름과 위치를 사용합니다. 이렇게 하면 모든 리소스가 동일한 리소스 그룹에 배치되므로 리소스를 삭제할 때 유용합니다.

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.
1. 왼쪽 위 모서리에서 **PowerShell** 을 선택합니다.

    :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/cloud-shell-select.png" alt-text="PowerShell에서 Azure Cloud Shell을 열고 파일을 업로드합니다.":::

1. **파일 업로드/다운로드** 를 선택하고 _azuredeploy.json_ 파일을 업로드합니다.
1. 템플릿을 배포하려면 다음 스크립트를 복사하여 셸 창에 붙여넣습니다.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the SQL admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $storageAccountKey = Read-Host -Prompt "Enter the storage account key"
    $bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUser $adminUsername `
        -adminPassword $adminPassword `
        -TemplateFile "$HOME/azuredeploy.json" `
        -storageAccountKey $storageAccountKey `
        -bacpacUrl $bacpacUrl

    Write-Host "Press [ENTER] to continue ..."
    ```

## <a name="verify-the-deployment"></a>배포 확인

클라이언트 컴퓨터에서 서버에 액세스하려면 방화벽 규칙을 추가해야 합니다. NAT(네트워크 주소 변환)으로 인해 클라이언트의 IP 주소와 서버에 연결하는 데 사용되는 IP 주소가 다를 수 있습니다. 자세한 내용은 [IP 방화벽 규칙 만들기 및 관리](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)를 참조하세요.

예를 들어 **쿼리 편집기** 에 로그인하면 해당 IP 주소가 허용되지 않는다는 메시지가 표시됩니다. NAT으로 인해 주소가 클라이언트의 IP 주소와 다릅니다. 메시지의 링크를 선택하여 IP 주소에 대한 방화벽 규칙을 추가합니다. 완료되면 서버의 **방화벽 및 가상 네트워크** 설정에서 IP 주소를 삭제할 수 있습니다.

Azure Portal의 리소스 그룹에서 데이터베이스를 선택합니다. **쿼리 편집기(미리 보기)** 를 선택하고 관리자 자격 증명을 입력합니다. 데이터베이스로 가져온 두 개의 테이블이 표시됩니다.

![쿼리 편집기(미리 보기)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>리소스 정리

배포한 Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 리소스 그룹, 스토리지 계정, SQL 서버 및 SQL 데이터베이스가 삭제됩니다.

1. Azure Portal의 검색 상자에 **리소스 그룹** 을 입력합니다.
1. **이름으로 필터링** 필드에 리소스 그룹 이름을 입력합니다.
1. 해당 리소스 그룹 이름을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. 삭제를 확인하려면 리소스 그룹 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 서버 및 데이터베이스를 배포하고 BACPAC 파일을 가져왔습니다. 템플릿 배포 문제를 해결하는 방법에 대한 자세한 내용은

> [!div class="nextstepaction"]
> [ARM 템플릿 배포 문제 해결](./template-tutorial-troubleshoot.md)을 참조하세요.
