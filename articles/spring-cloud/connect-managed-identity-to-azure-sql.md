---
title: 관리 ID를 사용하여 Azure SQL을 Azure Spring Cloud 앱에 연결
description: 관리 ID를 설정하여 Azure SQL을 Azure Spring Cloud 앱에 연결합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 1a87a2f0d52895b97efdbe79ba3a53082efeccb2
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529466"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>관리 ID를 사용하여 Azure SQL Database를 Azure Spring Cloud 앱에 연결

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 Azure Spring Cloud 앱에 대한 관리 ID를 만들고 이를 사용하여 Azure SQL Database에 액세스하는 방법을 보여 줍니다.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)는 클라우드용으로 빌드된 지능적이고 스케일링 가능한 관계형 데이터베이스 서비스입니다. 성능과 내구성을 최적화하는 AI 기반 기능과 자동화된 기능을 사용하여 항상 최신 상태를 유지합니다. 서버리스 컴퓨팅 및 Hyperscale 스토리지 옵션은 주문형 리소스를 자동으로 스케일링하므로 스토리지 크기나 리소스 관리에 대해 걱정하지 않고 새 애플리케이션을 빌드하는 데 집중할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Spring Data JPA자습서](/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server)에 따라 Azure SQL Database를 프로비저닝하고 로컬에서 Java 앱과 함께 사용
* [Azure Spring Cloud 시스템 할당 관리 ID 자습서](./how-to-enable-system-assigned-managed-identity.md)에 따라 MI를 사용하는 Azure Spring Cloud 앱을 프로비저닝

## <a name="grant-permission-to-the-managed-identity"></a>관리 ID에 사용 권한 부여

SQL 서버에 연결하고 다음 SQL 쿼리를 실행:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

이 `<MIName>`은 `<service instance name>/apps/<app name>` 규칙(예: `myspringcloud/apps/sqldemo`)을 따릅니다. Azure CLI를 사용하여 MIName을 쿼리할 수도 있습니다.

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>관리 ID를 사용하도록 Java 앱 구성

*src/main/resources/application.properties* 파일을 열고 다음 줄 끝에 `Authentication=ActiveDirectoryMSI;`를 추가합니다. $AZ_DATABASE_NAME 변수에 대해 올바른 값을 사용해야 합니다.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Azure Spring Cloud에 앱 빌드 및 배포

앱을 다시 빌드하고 이를 필수 구성 요소의 두 번째 글머리 기호 지점에서 프로비저닝된 Azure Spring Cloud 앱에 배포합니다. 이제 JPA를 사용하여 Azure Spring Cloud의 Azure SQL Database에서 데이터를 저장하고 검색하며 관리 ID로 인증된 Spring Boot 애플리케이션이 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud에서 관리 ID를 사용하여 스토리지 Blob에 액세스하는 방법](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [시스템이 할당한 관리 ID를 Azure Spring Cloud 애플리케이션에 사용하도록 설정하는 방법](./how-to-enable-system-assigned-managed-identity.md)
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증](./github-actions-key-vault.md)
