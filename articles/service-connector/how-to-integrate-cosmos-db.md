---
title: Service Connector와 Azure Cosmos DB 통합
description: Service Connector를 통해 Azure Cosmos DB를 애플리케이션에 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 8bdd4a23ac0736f2ff4ddf3095f0d77b2af066ce
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373868"
---
# <a name="integrate-azure-cosmos-db-with-service-connector"></a>Service Connector와 Azure Cosmos DB 통합

이 페이지에는 서비스 커넥터를 사용하는 Azure Cosmos DB의 지원되는 인증 유형 및 클라이언트 유형이 표시됩니다. Service Connector를 사용하지 않고도 다른 프로그래밍 언어로 Azure Cosmos DB에 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 얻을 수 있는 기본 환경 변수 이름 및 값(또는 Spring Boot 구성)도 표시됩니다. [Service Connector 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원되는 컴퓨팅 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 ID | Secret/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .NET | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java  | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java - Spring Boot | | | ![예 아이콘](./media/green-check.png) | |
| Node.js | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Go | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 애플리케이션 속성

### <a name="dotnet-java-nodejs-and-go"></a>Dotnet, Java, Nodejs 및 Go

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_COSMOS_CONNECTIONSTRING | Cosmos DB 연결 문자열의 Mango DB | `mongodb://{mango-db-admin-user}:{********}@{mango-db-server}.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@{mango-db-server}@` |

**시스템 할당 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 연결 문자열을 얻을 URL입니다. | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | 관리 ID 범위 | `https://management.azure.com/.default` |
| AZURE_COSMOS_RESOURCEENDPOINT | 리소스 엔드포인트| `https://{your-database-server}.documents.azure.com:443/` |

**사용자가 할당한 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 연결 문자열을 얻을 URL입니다. | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | 관리 ID 범위 | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | 클라이언트 비밀 ID | `{client-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | 구독 ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | 리소스 엔드포인트| `https://{your-database-server}.documents.azure.com:443/` |

Service Principal

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 연결 문자열을 얻을 URL입니다. | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | 관리 ID 범위 | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | 클라이언트 비밀 ID | `{client-id}` |
| AZURE_COSMOS_CLIENTSECRET | 클라이언트 비밀 비밀 | `{client-secret}` |
| AZURE_COSMOS_TENANTID | 테넌트 ID | `{tenant-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | 구독 ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | 리소스 엔드포인트| `https://{your-database-server}.documents.azure.com:443/` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
