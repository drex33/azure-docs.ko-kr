---
title: 유연한 서버 CLI 오류 Azure Database for MySQL 문제 해결
description: 이 항목에서는 MySQL 유연한 서버를 사용 하는 경우 Azure CLI 관련 된 일반적인 문제를 해결 하는 지침을 제공 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: eae5f1fe6a44cedafb00e10c9995905b993ca7b9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441192"
---
# <a name="troubleshoot-azure-database-for-mysql-flexible-server-cli-errors"></a>유연한 서버 CLI 오류 Azure Database for MySQL 문제 해결
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서는 MySQL 유연한 서버를 사용 하는 경우 Azure CLI 관련 된 일반적인 문제를 해결 하는 데 도움이 됩니다.

## <a name="command-not-found"></a>명령을 찾을 수 없음

 메시지 **의 철자가 잘못 되었거나 시스템이 인식 하지** 못하는 경우 이는 클라이언트 컴퓨터의 CLI 버전이 최신 상태가 아닐 수 있음을 의미할 수 있습니다. 를 실행 ```az upgrade``` 하 여 최신 버전으로 업그레이드 합니다. CLI 버전을 업그레이드 하면 API 변경으로 인해 명령과의 비호환 문제를 해결 하는 데 도움이 될 수 있습니다.

 
## <a name="debug-deployment-failures"></a>배포 실패 디버그 
현재 Azure CLI는 디버그 로깅 켜기를 지원 하지 않지만 아래 단계를 수행 하 여 디버그 로깅을 검색할 수 있습니다.

>[!NOTE]
> - ```examplegroup```및를 ```exampledeployment``` 데이터베이스 서버에 대 한 올바른 리소스 그룹 및 배포 이름으로 바꿉니다. 
> - 리소스 그룹의 배포 페이지에서 배포 이름을 확인할 수 있습니다. [배포 이름을 찾는 방법을](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal)참조 하세요.

1. MySQL 서버 배포를 식별 하기 위해 리소스 그룹의 배포 나열 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. MySQL Server 배포의 요청 콘텐츠 가져오기 
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
|MissingSubscriptionRegistration|리소스 공급자에 구독을 등록합니다. 명령을 실행 ```az provider register --namespace Microsoft.DBMySQL``` 하 여 문제를 해결 합니다.|
|InternalServerError| 서버에 대 한 활동 로그를 확인 하 여 추가 정보가 있는지 확인 하십시오. ```az monitor activity-log list --correlation-id <enter correlation-id>``` 명령을 실행합니다. 몇 분 후에 동일한 CLI 명령을 사용해 볼 수 있습니다. 문제가 지속 되 면 Microsoft 지원에 [문의 하거나 Microsoft](https://github.com/Azure/azure-cli/issues) 지원에 문의 하세요.|
|ResourceNotFound| 참조 중인 리소스를 찾을 수 없습니다.  리소스 속성을 확인 하거나 리소스가 삭제 되었는지 확인 하거나 리소스가 다른 구독 인지 여부를 확인할 수 있습니다. |
|Locationnotto의 Forresourcetype| - [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)에서 Azure Database for MySQL 유연한 서버 가용성을 확인 하세요. <br>-MySQL 용 Azure DB 리소스 형식이 구독에 등록 되어 있는지 확인 합니다. |
|ResourceGroupBeingDeleted| 리소스 그룹을 삭제 하 고 있습니다. 삭제가 완료될 때까지 기다립니다.|
|PasswordTooLong| 제공 된 암호가 너무 깁니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|PasswordNotComplex| 제공 된 암호는 충분히 복잡 하지 않습니다.  8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|PasswordTooShort| 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).|
|SubscriptionNotFound| 요청 된 구독을 찾을 수 없습니다. ```az account list all```를 실행 하 여 현재 구독을 모두 표시 합니다.|
|InvalidParameterValue| 매개 변수에 잘못 된 값이 지정 된 경우 [CLI 참조 문서](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true) 를 확인 하 여 인수에 대해 지원 되는 올바른 값을 확인 합니다.|
|InvalidLocation| 잘못 된 위치가 지정 되었습니다. [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=mysql) 에서 Azure Database for MySQL 유연한 서버 가용성 확인 |
|InvalidServerName| 잘못 된 서버 이름을 식별 했습니다. 서버 이름을 확인 하십시오. [Az mysql 신축 서버 목록](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true#az_mysql_flexible_server_list) 명령을 실행 하 여 사용 가능한 유연한 서버 목록을 모두 표시 합니다. |
|InvalidResourceIdSegment| Azure Resource Manager 템플릿에서 구문 오류가 식별 되었습니다. Json 포맷터 도구를 사용 하 여 JSON의 유효성을 검사 하 여 구문 오류를 식별 합니다.|
|InvalidUserName| 올바른 사용자 이름을 입력 하십시오. 관리자 사용자 이름은 azure_superuser, azure_pg_admin, 관리자, 관리자, 루트, 게스트 또는 공용 일 수 없습니다. pg_로 시작할 수 없습니다.|
|BlockedUserName| 관리자 사용자 이름은 azure_superuser, azure_pg_admin, 관리자, 관리자, 루트, 게스트 또는 공용 일 수 없습니다. pg_로 시작할 수 없습니다. 관리자 이름에는 이러한 패턴을 사용 하지 마십시오.|

## <a name="next-steps"></a>다음 단계

- 여전히 문제가 발생 하는 경우 문제를 [보고](https://github.com/Azure/azure-cli/issues)하세요. 
- 질문이 있는 경우 Stack Overflow 페이지를 방문 하세요 https://aka.ms/azcli/questions . 
- 이 간단한 설문 조사로 무엇을 하 고 있는지 알려주세요 https://aka.ms/azureclihats . 
