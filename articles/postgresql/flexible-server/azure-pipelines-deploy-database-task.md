---
title: Azure Pipelines 작업 Azure Database for PostgreSQL 유연한 서버
description: Azure Pipelines에 사용할 수 있도록 Azure Database for PostgreSQL 유연한 서버 CLI 작업 사용
ms.topic: quickstart
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.service: postgresql
ms.date: 08/09/2021
ms.openlocfilehash: 2f26ac8b2bea54bc49398b46eae02131991aac1b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363710"
---
# <a name="azure-pipelines-task-for-azure-database-for-postgresql-flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에 대한 Azure Pipelines 작업

**Azure Pipelines** 를 사용하여 빌드가 성공할 때마다 Azure Database for PostgreSQL 유연한 서버에 데이터베이스 업데이트를 자동으로 배포할 수 있습니다.  Azure CLI 작업을 사용해서 데이터베이스에 대해 SQL 파일 또는 인라인 SQL 스크립트로 데이터베이스를 업데이트할 수 있습니다. 이 작업은 Linux, macOS 또는 Windows 운영 체제에서 실행되는 플랫폼 간 에이전트에서 실행될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- Azure 계정에 대한 [Azure Resource Manager 서비스 연결](/azure/devops/pipelines/library/connect-to-azure)
- Microsoft 호스팅 에이전트에 Azure CLI가 미리 설치되어 있어야 합니다. 하지만 프라이빗 에이전트를 사용하는 경우 빌드 및 릴리스 에이전트를 실행하는 컴퓨터에 [Azure CLI를 설치](/cli/azure/install-azure-cli)합니다. 에이전트가 Azure CLI가 설치된 컴퓨터에서 이미 실행되고 있으면 모든 관련 스테이지 변수가 업데이트되도록 에이전트를 다시 시작합니다.
- [Azure Portal](./quickstart-create-server-portal.md) 또는 [Azure CLI](./quickstart-create-server-cli.md)를 사용하여 Azure Database for PostgreSQL 유연한 서버 만들기


## <a name="use-sql-file"></a>SQL 파일 사용

다음 예제에서는 데이터베이스 인수를 전달하고 ```execute``` 명령어를 실행하는 방법을 보여줍니다.  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az postgres flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>인라인 SQL 스크립트 사용

다음 예제에서는 ```execute``` 명령어를 사용하여 인라인 SQL 스크립트를 실행하는 방법을 보여줍니다.

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription 
      az postgres flexible-server execute --name $(SERVERNAME) --admin-user $(DBUSER) \
      --admin-password '$(DBPASSWORD)'  --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>작업 입력

Azure Pipelines에서 Azure CLI 작업을 사용할 때 모든 작업 입력의 전체 목록을 볼 수 있습니다. 

| 매개 변수            | Description         | 
| :------------------- | :-------------------|
| azureSubscription| (필수) 배포에 대한 Azure Resource Manager 구독을 제공합니다. 이 매개 변수는 선택한 작업 버전이 0.*일 때만 표시됩니다. Azure CLI 작업 v1.0은 Azure Resource Manager 구독만 지원합니다. |
|scriptType| (필수) 스크립트 유형을 제공합니다. 지원되는 스크립트는 PowerShell, PowerShell Core, Bat, 셸 및 스크립트입니다. **Linux 에이전트** 에서 실행할 때 ```bash``` 또는 ```pscore``` 중 하나를 선택합니다. **Windows 에이전트** 를 실행할 때 ```batch```, ```ps``` 및 ```pscore``` 중 하나를 선택합니다. |
|sriptLocation| (필수) 실제 파일 경로와 같은 스크립트 경로를 제공하거나 스크립트 인라인을 제공하는 경우 ```Inline script```를 사용합니다. 기본값은 ```scriptPath```입니다. |
|scriptPath| (필수) 스크립트의 정규화된 경로(Windows 기반 에이전트를 사용하는 경우에는 .ps1, .bat 또는 .cmd, Linux 기반 에이전트를 사용하는 경우에는 <code>.ps1 </code> 또는 <code>.sh </code>) 또는 기본 작업 디렉터리에 상대적인 경로입니다. |
|inlineScript|(필수) 여기에서 스크립트를 인라인으로 작성할 수 있습니다. Windows 에이전트를 사용할 때는 PowerShell 또는 PowerShell Core 또는 일괄 처리 스크립팅을 사용하고, Linux 기반 에이전트를 사용할 때는 PowerShell Core 또는 셸 스크립팅을 사용합니다. 일괄 처리 파일의 경우 모든 Azure 명령 앞에 \"call\" 접두사를 사용합니다. 또한 인수를 사용해서 이 스크립트로 사전 정의된 및 사용자 정의 변수를 전달할 수 있습니다. <br/>PowerShell/PowerShellCore/셸 예제:``` az --version az account show``` <br/>일괄 처리 예제: ``` call az --version call az account show```. |
| 인수| (선택 사항) 스크립트에 전달되는 모든 인수를 제공합니다. 예를 들면 ```-SERVERNAME mydemoserver```입니다. |
|powerShellErrorActionPreference| (선택 사항) PowerShell/PowerShell Core 스크립트 위에서 <b>$ErrorActionPreference = 'VALUE'</b> 줄을 앞에 추가합니다. 기본값은 stop입니다. 지원되는 값은 stop, continue 및 silentlyContinue입니다. |
|addSpnToEnvironment|(선택 사항) 선택한 Azure 엔드포인트의 서비스 주체 ID 및 키를 스크립트의 실행 환경에 추가합니다. 스크립트에서 <b>$env:servicePrincipalId, $env:servicePrincipalKey 및 $env:tenantId</b> 변수를 사용할 수 있습니다. 이것은 Azure 엔드포인트에 서비스 주체 인증 체계가 있는 경우에만 적용됩니다. 기본값은 false입니다.|
|useGlobalConfig|(선택 사항) false이면 이 작업에 고유 개별 <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">Azure CLI 구성 디렉터리</a>가 사용됩니다. <b>병렬</b> 릴리스에서 Azure CLI 작업을 실행하는 데 사용될 수 있습니다. <br/>기본값: false</td>
|workingDirectory| (선택 사항) 스크립트가 실행되는 현재 작업 디렉터리입니다.  Empty는 $(System.DefaultWorkingDirectory)에 해당하는 리포지토리(빌드) 또는 아티팩트(릴리스)의 루트입니다. |
|failOnStandardError|(선택 사항) true이면 오류가 StandardError 스트림에 기록될 때 이 작업이 실패합니다. 표준 오류를 무시하고 종료 코드를 사용하여 상태를 확인하려면 확인란을 선책 취소합니다. 기본값은 false입니다.|
|powerShellIgnoreLASTEXITCODE| (선택 사항) false이면 스크립트 끝에 <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> 줄이 추가됩니다. 이렇게 하면 외부 명령의 마지막 종료 코드가 PowerShell의 종료 코드로 전파됩니다. 그렇지 않으면 줄이 스크립트 끝에 추가되지 않습니다. 기본값은 false입니다. |

CLI 작업 문제가 있으면 [빌드 및 릴리스 문제 해결 방법](/azure/devops/pipelines/troubleshooting/troubleshooting)을 참조하세요.

## <a name="next-steps"></a>다음 단계 
다음은 Azure Pipelines를 사용하여 배포하는 데 사용할 수 있는 몇 가지 관련 작업입니다.

- [Azure 리소스 그룹 배포](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
- [Azure 웹앱 배포](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment)
