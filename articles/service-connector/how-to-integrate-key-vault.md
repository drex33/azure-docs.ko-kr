---
title: 서비스 커넥터와 Azure Key Vault 통합
description: Service Connector를 Azure Key Vault 애플리케이션에 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b46f9c0080fb13f178ec1dca96fb49316c2f617
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052887"
---
# <a name="integrate-azure-key-vault-with-service-connector"></a>서비스 커넥터와 Azure Key Vault 통합

이 페이지에는 Service Connector를 사용하여 지원되는 인증 유형 및 클라이언트 유형의 Azure Key Vault 표시됩니다. Service Connector를 사용하지 않고도 다른 프로그래밍 언어로 Azure Key Vault 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 얻을 수 있는 기본 환경 변수 이름 및 값(또는 Spring Boot 구성)도 표시됩니다. [Service Connector 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원되는 컴퓨팅 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 ID | Secret/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net | | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java | | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java - Spring Boot | | | | ![예 아이콘](./media/green-check.png) |
| Node.js | | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Python | | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 애플리케이션 속성

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**시스템 할당 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC 범위 | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault 엔드포인트 | `https://{yourKeyVault}.vault.azure.net/` |

**사용자가 할당한 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC 범위 | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault 엔드포인트 | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | 클라이언트 ID | `{yourClientID}` |

Service Principal

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC 범위 | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault 엔드포인트 | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | 클라이언트 ID | `{yourClientID}` |
| AZURE_KEYVAULT_CLIENTSECRET | 클라이언트 암호 | `{yourClientSecret}` |
| AZURE_KEYVAULT_TENANTID | 테넌트 ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

Service Principal
| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| azure.keyvault.uri | Key Vault 엔드포인트 URL | `"https://{yourKeyVaultName}.vault.azure.net/"` |
| azure.keyvault.client-id | 클라이언트 ID | `{yourClientID}` |
| azure.keyvault.client-key | 클라이언트 암호 | `{yourClientSecret}` |
| azure.keyvault.tenant-id |  테넌트 ID | `{yourTenantID}` |
| azure.keyvault.scope | Azure RBAC 범위 | `https://management.azure.com/.default` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
