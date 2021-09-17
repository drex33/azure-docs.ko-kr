---
title: 유연한 서버 CLI 오류 Azure Database for PostgreSQL 문제 해결
description: 이 항목에서는 PostgreSQL 유연한 서버를 사용할 때 Azure CLI 일반적인 문제를 해결하는 지침을 제공합니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: dd44e0bf0ffd7ae70cdb2b715561517d5b92a049
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441261"
---
# <a name="troubleshoot-azure-database-for-postgresql-flexible-server-cli-errors"></a>유연한 서버 CLI 오류 Azure Database for PostgreSQL 문제 해결

이 문서는 PostgreSQL 유연한 서버를 사용할 때 Azure CLI 관련된 일반적인 문제를 해결하는 데 도움이 됩니다.

## <a name="command-not-found"></a>명령을 찾을 수 없습니다.

 **명령의 철자가 잘못되거나 시스템에서 인식되지 않는다는** 오류 메시지가 표시됩니다. 이는 클라이언트 컴퓨터의 CLI 버전이 최신 버전이 아닐 수 있음을 의미할 수 있습니다. ```az upgrade```를 실행하여 최신 버전으로 업그레이드합니다. CLI 버전을 업그레이드하면 API 변경으로 인한 명령 비호환성 문제를 해결하는 데 도움이 될 수 있습니다.
 
## <a name="debug-deployment-failures"></a>배포 오류 디버그 
현재 Azure CLI 디버그 로깅 켜기를 지원하지 않지만 아래 단계에 따라 디버그 로깅을 검색할 수 있습니다.

>[!NOTE]
> - ```examplegroup```및 ```exampledeployment``` 를 데이터베이스 서버의 올바른 리소스 그룹 및 배포 이름으로 대체합니다. 
> - 리소스 그룹의 배포 페이지에서 배포 이름을 볼 수 있습니다. [배포 이름을 찾는 방법을](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal) 참조하세요.


1. 리소스 그룹의 배포를 나열하여 PostgreSQL 서버 배포를 식별합니다. 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. PostgreSQL 서버 배포의 요청 콘텐츠 얻기 
    ```azurecli

        az deployment operation group list \
          --name exampledeployment \
          -g examplegroup \
          --query [].properties.request
    ```
3. 응답 콘텐츠 검사 
    ```azurecli
    az deployment operation group list \
      --name exampledeployment \
      -g examplegroup \
      --query [].properties.response
    ```

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 완화 방법 |
| ---------- | ---------- | 
|MissingSubscriptionRegistration|리소스 공급자에 구독을 등록합니다. 명령을 ```az provider register --namespace Microsoft.DBPostgreSQL``` 실행하여 문제를 해결합니다.|
|InternalServerError| 서버에 대한 활동 로그를 확인하여 자세한 정보가 있는지 확인합니다. ```az monitor activity-log list --correlation-id <enter correlation-id>``` 명령을 실행합니다. 몇 분 후에 동일한 CLI 명령을 사용해 볼 수 있습니다. 문제가 지속되면 [보고하거나](https://github.com/Azure/azure-cli/issues) Microsoft 지원에 문의하세요.|
|ResourceNotFound| 참조되는 리소스를 찾을 수 없습니다.  리소스 속성을 확인하거나 리소스가 삭제되었는지 확인하거나 리소스가 다른 구독인지 확인할 수 있습니다. |
|LocationNotAvailableForResourceType| - Azure 지역에서 Azure Database for Postgres 유연한 서버의 [가용성을 확인합니다.](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) <br>- Azure DB for PostgreSQL 리소스 유형이 구독에 등록되었는지 확인합니다. |
|ResourceGroupBeingDeleted| 리소스 그룹이 삭제되고 있습니다. 삭제가 완료될 때까지 기다립니다.|
|PasswordTooLong| 제공된 암호가 너무 깁니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|PasswordNotComplex| 제공된 암호가 충분히 복잡하지 않습니다.  8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|PasswordTooShort| 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|SubscriptionNotFound| 요청된 구독을 찾을 수 없습니다. ```az account list all```를 실행하여 현재 구독을 모두 확인합니다.|
|InvalidParameterValue| 매개 변수에 잘못된 값이 지정되었습니다. CLI [참조 문서를](/cli/azure/postgres/flexible-server?view=azure-cli-latest&preserve-view=true) 확인하여 인수에 대해 지원되는 올바른 값이 무엇인지 확인합니다.|
|InvalidLocation| 잘못된 위치가 지정되었습니다. Azure [지역에서](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) Azure Database for Postgres 유연한 서버의 가용성 확인 |
|InvalidServerName|잘못된 서버 이름을 식별했습니다. 심각도 이름을 확인합니다. [az mysql flexible-server list](/cli/azure/mysql/flexible-server?view=azure-cli-latest#az_mysql_flexible_server_list&preserve-view=true) 명령을 실행하여 사용 가능한 모든 유연한 서버 목록을 확인합니다.|
|InvalidResourceIdSegment| Azure Resource Manager 템플릿에서 구문 오류가 식별되었습니다. JSON 포맷터 도구를 사용하여 JSON의 유효성을 검사하여 구문 오류를 식별합니다.|
|InvalidUserName| 유효한 사용자 이름을 입력합니다. 관리 사용자 이름은 azure_superuser, azure_pg_admin, 관리자, 관리자, 루트, 게스트 또는 공용일 수 없습니다. pg_로 시작할 수 없습니다.|
|BlockedUserName| 관리 사용자 이름은 azure_superuser, azure_pg_admin, 관리자, 관리자, 루트, 게스트 또는 공용일 수 없습니다. pg_로 시작할 수 없습니다. 관리자 이름에 이러한 패턴을 사용하지 마십시오.|

## <a name="next-steps"></a>다음 단계

- 여전히 문제가 발생하는 경우 [문제를 보고하세요.](https://github.com/Azure/azure-cli/issues) 
- 질문이 있는 경우 Stack Overflow 페이지를 https://aka.ms/azcli/questions 방문하세요. 
- 이 설문 조사를 통해 어떻게 하고 있는지 https://aka.ms/azureclihats 알려주세요. 
