---
title: Config Server 및 서비스 레지스트리에 액세스
titleSuffix: Azure Spring Cloud
description: Azure Active Directory 역할 기반 액세스 제어를 사용하여 Config Server 및 서비스 레지스트리 엔드포인트에 액세스하는 방법입니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 7c792164f30de2c8c1d2614a54aaf79bebfbd873
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567449"
---
# <a name="access-config-server-and-service-registry"></a>Config Server 및 서비스 레지스트리에 액세스

이 문서에서는 Azure AD(Azure Active Directory) RBAC(역할 기반 액세스 제어)를 사용하여 Azure Spring Cloud에서 관리하는 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리에 액세스하는 방법을 설명합니다.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD 사용자/그룹, MSI 또는 서비스 주체에 역할 할당

[management-group | subscription | resource-group | resource] 범위에서 [user | group | service-principal | managed-identity]에 [azure-spring-cloud-data-reader](../role-based-access-control/built-in-roles.md#azure-spring-cloud-data-reader) 역할을 할당합니다.

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="access-config-server-and-service-registry-endpoints"></a>Config Server 및 서비스 레지스트리 엔드포인트에 액세스

Azure Spring Cloud Data Reader 역할이 할당된 후 고객은 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리 엔드포인트에 액세스할 수 있습니다. 다음 절차를 사용합니다.

1. 액세스 토큰을 가져옵니다. Azure AD 사용자에게 Azure Spring Cloud Data Reader 역할이 할당된 후 고객은 다음 명령을 사용하여 사용자, 서비스 주체 또는 관리 ID로 Azure CLI에 로그인하여 액세스 토큰을 가져올 수 있습니다. 자세한 내용은 [Azure CLI 인증](/cli/azure/authenticate-azure-cli)을 참조하세요.

    ```azurecli
    az login
    az account get-access-token
    ```

2. 엔드포인트를 작성합니다. Azure Spring Cloud에서 관리되는 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리의 기본 엔드포인트를 지원합니다. 자세한 내용은 [프로덕션 준비 엔드포인트](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)를 참조하세요. 고객은 엔드포인트에 액세스하여 Azure Spring Cloud에서 관리되는 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리의 전체 목록을 가져올 수도 있습니다.

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'*

>[!NOTE]
> Azure 중국을 사용하는 경우 `*.azuremicroservices.io`를 `*.microservices.azure.cn`으로 바꾸고 [자세히 알아보세요](/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. 액세스 토큰을 사용하여 구성된 엔드포인트에 액세스합니다. 액세스 토큰을 헤더에 배치하여 권한 부여를 제공합니다.`--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`  "GET" 메서드만 지원됩니다.

    예를 들어 *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health '* 와 같은 엔드포인트에 액세스하여 eureka의 상태를 확인합니다.

    응답이 *401 인증되지 않음* 인 경우 역할이 성공적으로 할당되었는지 확인합니다.  역할이 적용되거나 액세스 토큰이 만료되지 않았는지 확인하는 데 몇 분 정도가 걸립니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI 인증](/cli/azure/authenticate-azure-cli)
* [프로덕션 준비 엔드포인트](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [역할 및 권한 만들기](how-to-permissions.md)
