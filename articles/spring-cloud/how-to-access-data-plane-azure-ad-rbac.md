---
title: Config Server 및 서비스 레지스트리에 액세스
titleSuffix: Azure Spring Cloud
description: Azure Active Directory 역할 기반 액세스 제어를 사용하여 Config Server 및 서비스 레지스트리 엔드포인트에 액세스하는 방법입니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: ce5905b43d37faf3b1a6965ae04efb9f8f73c2ff
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835883"
---
# <a name="access-config-server-and-service-registry"></a>Config Server 및 서비스 레지스트리에 액세스

이 문서에서는 Azure AD(Azure Active Directory) RBAC(역할 기반 액세스 제어)를 사용하여 Azure Spring Cloud에서 관리하는 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리에 액세스하는 방법을 설명합니다.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD 사용자/그룹, MSI 또는 서비스 주체에 역할 할당

[management-group | subscription | resource-group | resource] 범위에서 [user | group | service-principal | managed-identity]에 역할을 할당합니다.

| 역할 이름                                       | Description                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Azure Spring Cloud 구성 서버 읽기 권한자         | Azure Spring Cloud Config Server에 대한 읽기 권한 허용                       |
| Azure Spring Cloud Config Server 기여자    | Azure Spring Cloud Config Server에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다.    |
| Azure Spring Cloud Service Registry 읽기 권한자      | Azure Spring Cloud Service Registry에 대한 읽기 액세스를 허용합니다.                    |
| Azure Spring Cloud Service Registry 기여자 | Azure Spring Cloud Service Registry에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. |

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="access-config-server-and-service-registry-endpoints"></a>Config Server 및 서비스 레지스트리 엔드포인트에 액세스

역할이 할당된 후 담당자는 다음 절차를 사용하여 Spring Cloud Config Server 및 Spring Cloud Service Registry 엔드포인트에 액세스할 수 있습니다.

1. 액세스 토큰을 가져옵니다. Azure AD 사용자에게 역할이 할당되면 다음 명령을 사용하여 사용자, 서비스 주체 또는 관리 ID로 Azure CLI에 로그인하여 액세스 토큰을 얻을 수 있습니다. 자세한 내용은 [Azure CLI 인증](/cli/azure/authenticate-azure-cli)을 참조하세요.

    ```azurecli
    az login
    az account get-access-token
    ```

1. 엔드포인트를 작성합니다. Azure Spring Cloud에서 관리되는 Spring Cloud Config Server 및 Spring Cloud 서비스 레지스트리의 기본 엔드포인트를 지원합니다.

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > Azure 중국을 사용하는 경우 `*.azuremicroservices.io`를 `*.microservices.azure.cn`으로 바꿉니다. 자세한 내용은 [Azure 중국 개발자 가이드](/azure/china/resources-developer-guide)의 [Azure에서 엔드포인트 확인](/azure/china/resources-developer-guide#check-endpoints-in-azure) 섹션을 참조하세요.

1. 액세스 토큰을 사용하여 구성된 엔드포인트에 액세스합니다. 액세스 토큰을 헤더에 배치하여 권한 부여를 제공합니다.`--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`

    예를 들면 다음과 같습니다.

    a. Config Server의 상태를 보려면 *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health '* 와 같은 엔드포인트에 액세스합니다.

    b. *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps '* 와 같은 엔드포인트에 액세스하여 Spring Cloud Service Registry(여기서는 Eureka)에 등록된 앱을 확인합니다.

    응답이 *401 인증되지 않음* 인 경우 역할이 성공적으로 할당되었는지 확인합니다.  역할이 적용되거나 액세스 토큰이 만료되지 않았는지 확인하는 데 몇 분 정도가 걸립니다.

작동기 엔드포인트에 대한 자세한 내용은 [프로덕션 준비 엔드포인트](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)를 참조하세요.

Eureka 엔드포인트는 [Eureka-REST-operations](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)를 참조하세요.

Config Server 엔드포인트 및 자세한 경로 정보는 [ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) 및 [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java)를 참조하세요.

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>Azure Spring Cloud에서 관리하는 Spring Cloud Config Server 및 Service Registry에 Spring Boot 앱 등록

역할이 할당되면 Azure AD 토큰 인증을 사용하여 Azure Spring Cloud에서 관리하는 Spring Cloud Config Server 및 Service Registry에 Spring Boot 앱을 등록할 수 있습니다. Config Server와 Service Registry는 모두 인증을 위한 전달자 토큰을 삽입하기 위해 [사용자 지정 REST 템플릿](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template)을 지원합니다.

자세한 내용은 샘플 [Azure Spring Cloud 관리형 Config Server 액세스](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client) 및 [Azure Spring Cloud 관리형 Spring Cloud Service Registry 액세스](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)를 참조하세요. 다음 섹션에서는 이러한 샘플의 몇 가지 중요한 세부 정보를 설명합니다.

**In *AccessTokenManager.java*:**

`AccessTokenManager`는 Azure AD에서 액세스 토큰을 가져오는 일을 담당합니다. *application.properties* 파일에서 서비스 주체의 로그인 정보를 구성하고 `ApplicationTokenCredentials`를 초기화하여 토큰을 가져옵니다. 두 샘플에서 이 파일을 찾을 수 있습니다.

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

**In *CustomConfigServiceBootstrapConfiguration.java*:**

`CustomConfigServiceBootstrapConfiguration`은 Config Server에 대한 사용자 지정 REST 템플릿을 구현하고 Azure AD의 토큰을 `Authorization` 헤더로 삽입합니다. 이 파일은 [Config Server 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client)에서 찾을 수 있습니다.

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

**In *CustomRestTemplateTransportClientFactories.java*:**

이전 두 클래스는 Spring Cloud Service Registry에 대한 사용자 지정 REST 템플릿 구현을 위한 것입니다. `intercept` 부분은 위의 Config Server와 동일합니다. 메시지 변환기에 `factory.mappingJacksonHttpMessageConverter()`를 추가해야 합니다. 이 파일은 [Spring Cloud Service Registry 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)에서 찾을 수 있습니다.

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

Kubernetes 클러스터에서 애플리케이션을 실행하는 경우 액세스를 위해 IP 주소를 사용하여 Spring Cloud Service Registry를 등록하는 것이 좋습니다.

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>다음 단계

* [Azure CLI 인증](/cli/azure/authenticate-azure-cli)
* [프로덕션 준비 엔드포인트](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [역할 및 권한 만들기](how-to-permissions.md)
