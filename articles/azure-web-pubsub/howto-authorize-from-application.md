---
title: Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여
description: 이 문서에서는 Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한을 부여하는 방법을 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 7bca3e4840cf78b497f5907e780a267221dd1b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482957"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여

Azure Web PubSub Service는 Azure 애플리케이션의 요청에 권한을 부여하는 azure AD(Azure Active Directory)를 [지원합니다.](../active-directory/develop/app-objects-and-service-principals.md) 

이 문서에서는 Azure 애플리케이션에서 Web PubSub 리소스에 대한 요청에 권한을 부여하도록 Web PubSub 리소스 및 코드를 구성하는 방법을 보여 줍니다.

## <a name="register-an-application"></a>애플리케이션 등록

첫 번째 단계는 Azure 애플리케이션을 등록하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Active Directory** 검색하여 선택합니다.
2. **관리** 섹션에서 **앱 등록** 선택합니다.
3. **새 등록** 을 클릭합니다.

    ![애플리케이션 등록 스크린샷](./media/aad-authorization/register-an-application.png)

4. 애플리케이션의 표시 **이름** 을 입력합니다.
5. **등록을 클릭하여 등록을** 확인합니다.

애플리케이션이 등록되면 개요 페이지에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID를** 찾을 수 있습니다. 이러한GUID는 다음 단계에서 유용할 수 있습니다.

![애플리케이션의 스크린샷](./media/aad-authorization/application-overview.png)

애플리케이션 등록에 대한 자세한 내용은 다음을 참조하세요.
- [빠른 시작: Microsoft ID 플랫폼 애플리케이션을 등록합니다.](../active-directory/develop/quickstart-register-app.md)

## <a name="add-credentials"></a>자격 증명 추가

인증서와 클라이언트 암호(문자열)를 모두 자격 증명으로 기밀 클라이언트 앱 등록에 추가할 수 있습니다.

### <a name="client-secret"></a>클라이언트 암호

애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 비밀을 만들려면 다음 단계를 수행합니다.

1. **관리** 섹션에서 **인증서 & 비밀을 선택합니다.**
1. 클라이언트 **비밀** 탭에서 **새 클라이언트 암호** 를 클릭합니다.
![클라이언트 암호 만들기 스크린샷](./media/aad-authorization/new-client-secret.png)
1. 클라이언트 비밀에 대한 **설명을** 입력하고 **만료 시간** 를 선택합니다.
1. **클라이언트 암호의** 값을 복사한 다음 안전한 위치에 붙여넣습니다. 
    > [!NOTE]
    > 비밀은 한 번만 표시됩니다.
### <a name="certificate"></a>인증서

클라이언트 비밀을 만드는 대신 인증을 업로드할 수도 있습니다.

![인증 업로드 스크린샷](./media/aad-authorization/upload-certificate.png)

자격 증명을 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [자격 증명 추가](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal 역할 할당 추가

이 샘플에서는 Web `Web PubSub Service Owner` PubSub 리소스를 통해 서비스 주체(애플리케이션)에 역할을 할당하는 방법을 보여줍니다. 

> [!Note]
> 역할은 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯한 모든 범위에 할당할 수 있습니다. 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해를 참조하세요.](../role-based-access-control/scope-overview.md)
1. [Azure Portal](https://portal.azure.com/)Web PubSub 리소스로 이동합니다.

1. **Access Control(IAM)를** 클릭하여 Azure Web PubSub에 대한 액세스 제어 설정을 표시합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷은 Web PubSub 리소스에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

   ![액세스 제어 스크린샷](./media/aad-authorization/access-control.png)

1. **추가 > 역할 할당 추가를** 클릭합니다.

1. **역할** 탭에서 를 `Web PubSub App Server` 선택합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가 스크린샷](./media/aad-authorization/add-role-assignment.png)

1. **구성원** 탭의 **에 대한 액세스 할당** 섹션에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

1. **멤버 선택을** 클릭합니다.

3. 역할을 할당할 애플리케이션을 검색하여 선택합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

4. **다음** 을 클릭합니다.

   ![서비스 주체에 역할을 할당하는 스크린샷](./media/aad-authorization/assign-role-to-service-principals.png)

5. **검토 + 할당을** 클릭하여 변경 내용을 확인합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.
Azure 역할 할당을 할당하고 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-server"></a>서버 구성

환경 변수에서 ID 및 자격 증명을 구성하는 것이 좋습니다.

| 변수  | Description |
|------|------
| `AZURE_TENANT_ID` | Azure Active Directory 테넌트(디렉터리) ID입니다. |
| `AZURE_CLIENT_ID` | 테넌트에서 앱 등록의 클라이언트(애플리케이션) ID입니다. |
| `AZURE_CLIENT_SECRET` | 앱 등록을 위해 생성된 클라이언트 암호입니다. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | 앱 등록을 인증할 수 있는 PEM 또는 PFX 형식의 인증서 및 프라이빗 키 쌍에 대한 경로입니다. |
| `AZURE_USERNAME`  | Azure Active Directory 사용자 계정의 사용자 이름(upn이라고도 함)입니다. |
| `AZURE_PASSWORD`  | Azure Active Directory 사용자 계정의 암호입니다. MFA를 사용하도록 설정된 계정은 지원하지 않습니다. |

이렇게 하면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 또는 [EnvironmentCredential을](/dotnet/api/azure.identity.environmentcredential) 사용하여 Web PubSub 엔드포인트를 구성할 수 있습니다.

### <a name="sample-codes"></a>샘플 코드

다음은 C#에 대한 샘플 코드입니다. 지원되는 다른 언어는 JavaScript/Python/Java를 참조하세요.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new DefaultAzureCredential());
```

작동 방식을 알아보려면 `DefaultAzureCredential` [DefaultAzureCredential 클래스 를 참조하세요.](/dotnet/api/azure.identity.defaultazurecredential)

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new EnvironmentCredential());
```

원하는 경우 [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) 또는 [ClientCertificateCredential을](/dotnet/api/azure.identity.clientcertificatecredential) 직접 사용할 수도 있습니다.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```
```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```

Azure AD 권한 부여를 만드는 방법에 대한 자세한 내용은 `TokenCredential` 다음 문서를 참조하세요.

- [DefaultAzureCredential 클래스](/dotnet/api/azure.identity.defaultazurecredential)
- [ClientSecretCredential 생성자](/dotnet/api/azure.identity.clientsecretcredential.-ctor)
- [ClientCertificateCredential 생성자](/dotnet/api/azure.identity.clientcertificatecredential.-ctor)

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [Web PubSub용 Azure AD 개요](concept-azure-ad-authorization.md)
- [관리 ID에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여](howto-authorize-from-managed-identity.md)