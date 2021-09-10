---
title: Azure Spring Cloud 애플리케이션에 대해 시스템에서 할당한 관리 ID 사용
description: 애플리케이션에 대해 시스템이 할당한 관리 ID를 사용하도록 설정하는 방법.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6e600a4634bf2aff23301fe2eb1f9084e181a416
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537438"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>시스템이 할당한 관리 ID를 Azure Spring Cloud 애플리케이션에 사용하도록 설정하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure Spring Cloud 애플리케이션과 같은 Azure 리소스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

이 문서에서는 Azure Portal 및 CLI(버전 0.2.4부터 사용 가능)를 사용하여 Azure Spring Cloud 앱에 대해 시스템이 할당한 관리 ID를 사용하거나 사용하지 않도록 설정하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 리소스에 대한 관리 ID에 대해 잘 모르는 경우 [개요 섹션](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.
배포된 Azure Spring Cloud 인스턴스가 있어야 합니다. [Azure CLI를 사용하여 빠른 배포 시작](./quickstart.md)을 수행하세요.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

시스템이 할당한 ID를 사용하여 앱을 만들려면 애플리케이션에서 추가 속성을 설정해야 합니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

[Azure Portal](https://portal.azure.com/)에서 관리 ID를 설정하려면 먼저 앱을 만든 다음, 해당 기능을 사용하도록 설정합니다.

1. 평소처럼 포털에서 앱을 만듭니다. 포털에서 해당 앱으로 이동합니다.
2. 왼쪽 탐색 창에서 **설정** 그룹이 나올 때까지 아래로 스크롤합니다.
3. **ID** 를 선택합니다.
4. **시스템 할당** 탭에서 **상태** 를 *켜기* 로 바꿉니다. **저장** 을 선택합니다.

![Portal의 관리 ID](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

앱 생성 중에 또는 기존 앱에서 시스템이 할당한 관리 ID를 사용하도록 설정할 수 있습니다.

**앱 생성 중에 시스템이 할당한 관리 ID를 사용하도록 설정**

다음 예에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템이 할당한 관리 ID를 사용하여 이름이 *app_name* 인 앱을 만듭니다.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**기존 앱에서 시스템 할당 관리 ID 사용**

`az spring-cloud app identity assign` 명령을 사용하여 기존 앱에서 시스템 할당 ID를 사용하도록 설정합니다.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Azure 리소스 토큰 가져오기

앱은 자체 관리 ID를 사용하여 Azure Key Vault와 같이 Azure Active Directory로 보호되는 다른 리소스에 액세스하는 토큰을 가져올 수 있습니다. 이러한 토큰은 애플리케이션의 특정 사용자가 아닌 리소스에 액세스하는 애플리케이션을 나타냅니다.

[애플리케이션의 액세스를 허용하도록 대상 리소스를 구성](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)해야 할 수도 있습니다. 예를 들어 Key Vault에 액세스하는 토큰을 요청할 경우 애플리케이션의 ID를 포함하는 액세스 정책을 추가했는지 확인해야 합니다. 그렇지 않으면 토큰이 포함되어 있더라도 Key Vault 호출이 거부됩니다. Azure Active Directory 토큰을 지원하는 리소스에 대한 자세한 내용은 [Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

Azure Spring Cloud는 Azure 가상 머신과 토큰 획득을 위해 동일한 엔드포인트를 공유합니다. Java SDK 또는 스프링 부팅 시작을 사용하여 토큰을 획득하는 것이 좋습니다.  토큰 만료 및 HTTP 오류 처리와 같은 중요한 항목에 대한 다양한 코드 및 스크립트 예제는 [VM 토큰을 사용하는 방법](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

권장: Java SDK 또는 스프링 부팅 시작을 사용하여 토큰을 가져옵니다.  [다음 단계](#next-steps)에서 샘플을 참조하세요.

## <a name="disable-system-assigned-identity-from-an-app"></a>앱에서 시스템이 할당한 ID를 사용하지 않도록 설정

시스템이 할당한 ID를 제거하면 Azure AD에서도 삭제됩니다. 앱 리소스를 삭제하면 Azure AD에서도 시스템이 할당한 ID가 자동으로 제거됩니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

더 이상 필요하지 않은 시스템이 할당한 관리 ID를 앱에서 제거하려면 다음을 수행합니다.

1. Azure Spring Cloud 인스턴스를 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 원하는 Virtual Machine으로 이동하여 **ID** 를 선택합니다.
1. **시스템 할당됨**/**상태** 에서 **끄기** 를 선택한 다음, **저장** 을 선택합니다.

![관리 ID](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Azure CLI 사용

더 이상 필요하지 않은 시스템이 할당한 관리 ID를 앱에서 제거하려면 다음 명령을 사용합니다.

```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>다음 단계

* [스프링 부팅 시작에서 관리 ID를 사용하여 Azure Key Vault에 액세스](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Azure 리소스의 관리 ID에 대해 자세히 알아보기](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Java SDK에서 관리 ID를 사용하는 방법](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
