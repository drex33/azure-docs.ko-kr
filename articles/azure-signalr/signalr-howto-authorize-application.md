---
title: Azure 애플리케이션에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여
description: 이 문서에서는 Azure 애플리케이션에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한을 부여하는 방법을 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 8297a715d66206bbad2721faaca89c3616744ce4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483059"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Azure 애플리케이션에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여

Azure SignalR Service Azure AD(Azure Active Directory)가 [Azure 애플리케이션의](../active-directory/develop/app-objects-and-service-principals.md)요청에 권한을 부여할 수 있습니다.

이 문서에서는 Azure 애플리케이션에서 SignalR 리소스에 대한 요청에 권한을 부여하도록 SignalR 리소스 및 코드를 구성하는 방법을 보여 줍니다.

## <a name="register-an-application"></a>애플리케이션 등록

첫 번째 단계는 Azure 애플리케이션을 등록하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Active Directory** 검색하여 선택합니다.
2. **관리** 섹션에서 **앱 등록** 선택합니다.
3. **새 등록** 을 클릭합니다.

    ![애플리케이션 등록 스크린샷](./media/authenticate/register-an-application.png)

4. 애플리케이션의 표시 **이름** 을 입력합니다.
5. **등록을 클릭하여 등록을** 확인합니다.

애플리케이션이 등록되면 개요 페이지에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID를** 찾을 수 있습니다. 이러한GUID는 다음 단계에서 유용할 수 있습니다.

![애플리케이션의 스크린샷](./media/authenticate/application-overview.png)

애플리케이션 등록에 대한 자세한 내용은 다음을 참조하세요.
- [빠른 시작: Microsoft ID 플랫폼 애플리케이션을 등록합니다.](../active-directory/develop/quickstart-register-app.md)


## <a name="add-credentials"></a>자격 증명 추가

인증서와 클라이언트 암호(문자열)를 모두 자격 증명으로 기밀 클라이언트 앱 등록에 추가할 수 있습니다.

### <a name="client-secret"></a>클라이언트 암호

애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 비밀을 만들려면 다음 단계를 수행합니다.

1. **관리** 섹션에서 **인증서 & 비밀을 선택합니다.**
1. 클라이언트 **비밀** 탭에서 **새 클라이언트 암호** 를 클릭합니다.
![클라이언트 암호 만들기 스크린샷](./media/authenticate/new-client-secret.png)
1. 클라이언트 비밀에 대한 **설명을** 입력하고 **만료 시간** 를 선택합니다.
1. **클라이언트 암호의** 값을 복사한 다음 안전한 위치에 붙여넣습니다. 
    > [!NOTE]
    > 비밀은 한 번만 표시됩니다.

### <a name="certificate"></a>인증서

클라이언트 비밀을 만드는 대신 인증을 업로드할 수도 있습니다.

![인증 업로드 스크린샷](./media/authenticate/upload-certificate.png)

자격 증명을 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [자격 증명 추가](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal 역할 할당 추가

이 샘플에서는 `SignalR App Server` SignalR 리소스를 통해 서비스 주체(애플리케이션)에 역할을 할당하는 방법을 보여줍니다. 

> [!Note]
> 역할은 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯한 모든 범위에 할당할 수 있습니다. 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해를 참조하세요.](../role-based-access-control/scope-overview.md)

1. [Azure Portal](https://portal.azure.com/)SignalR 리소스로 이동합니다.

1. **Access Control(IAM)를** 클릭하여 Azure SignalR에 대한 액세스 제어 설정을 표시합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷은 SignalR 리소스에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

   ![액세스 제어 스크린샷](./media/authenticate/access-control.png)

1. **추가 > 역할 할당 추가를** 클릭합니다.

1. **역할** 탭에서 를 `SignalR App Server` 선택합니다.

1. **다음** 을 클릭합니다.
    
   ![역할 할당 추가 스크린샷](./media/authenticate/add-role-assignment.png)

1. **구성원** 탭의 **에 대한 액세스 할당** 섹션에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

1. **멤버 선택을** 클릭합니다.

3. 역할을 할당할 애플리케이션을 검색하여 선택합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

4. **다음** 을 클릭합니다.
    
   ![서비스 주체에 역할을 할당하는 스크린샷](./media/authenticate/assign-role-to-service-principals.png)

5. **검토 + 할당을** 클릭하여 변경 내용을 확인합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

Azure 역할 할당을 할당하고 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>앱 구성

### <a name="app-server"></a>앱 서버

환경 변수에서 ID 및 자격 증명을 구성하는 것이 가장 좋습니다.

| 변수  | Description |
|------|------
| `AZURE_TENANT_ID` | Azure Active Directory 테넌트(디렉터리) ID입니다. |
| `AZURE_CLIENT_ID` | 테넌트에서 앱 등록의 클라이언트(애플리케이션) ID입니다. |
| `AZURE_CLIENT_SECRET` | 앱 등록을 위해 생성된 클라이언트 암호입니다. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | 앱 등록을 인증할 수 있는 PEM 또는 PFX 형식의 인증서 및 프라이빗 키 쌍에 대한 경로입니다. |
| `AZURE_USERNAME`  | Azure Active Directory 사용자 계정의 사용자 이름(upn이라고도 함)입니다. |
| `AZURE_PASSWORD`  | Azure Active Directory 사용자 계정의 암호입니다. MFA를 사용하도록 설정된 계정은 지원하지 않습니다. |

이렇게 하면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 또는 [EnvironmentCredential을](/dotnet/api/azure.identity.environmentcredential) 사용하여 SignalR 엔드포인트를 구성할 수 있습니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

또는 `EnvironmentalCredential` 를 직접 사용.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

작동 방식을 알아보려면 `DefaultAzureCredential` [DefaultAzureCredential 클래스 를 참조하세요.](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>여러 엔드포인트를 사용하는 동안 다른 자격 증명을 사용합니다.

어떤 이유로 엔드포인트에 대해 서로 다른 자격 증명을 사용할 수 있습니다.

이 시나리오에서는 [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) 또는 [ClientCertificateCredential 을](/dotnet/api/azure.identity.clientcertificatecredential)사용할 수 있습니다.

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    var credential1 = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
    var credential2 = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");

    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), credential1),
        new ServiceEndpoint(new Uri("https://<resource2>.service.signalr.net"), credential2),
    };
});
```

### <a name="azure-functions-signalr-bindings"></a>SignalR 바인딩 Azure Functions

> [!WARNING]
> SignalR 트리거 바인딩은 아직 ID 기반 연결을 지원하지 않으며 연결 문자열이 여전히 필요합니다.

Azure Functions SignalR 바인딩은 포털 또는 로컬에서 [애플리케이션 설정을](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) 사용하여 [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) SignalR 리소스에 액세스하도록 Azure 애플리케이션 ID를 구성합니다.

먼저 키가 `serviceUri` 연결 이름 접두사(기본값:)로 시작하는 SignalR Service 서비스  `AzureSignalRConnectionString` URI와 구분 `__` 기호(Azure Portal 및 `:` local.settings.json 파일에서)를 지정해야 합니다. 연결 이름은 바인딩 속성 를 통해 사용자 지정할 수 [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service) 있습니다. 계속 읽어 샘플을 찾습니다.

그런 다음 [미리 정의된 환경 변수](#configure-identity-in-pre-defined-environment-variables) 또는 SignalR 지정 변수 에서 Azure 애플리케이션 ID를 구성하도록 [선택합니다.](#configure-identity-in-signalr-specified-variables)

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>미리 정의된 환경 변수에서 ID 구성

미리 정의된 [환경 변수](/dotnet/api/overview/azure/identity-readme#environment-variables) 목록은 환경 변수를 참조하세요. 각 서비스에 대한 ID를 구성할 필요가 없도록 동일한 Azure 애플리케이션 ID에 종속된 SignalR을 포함하는 여러 서비스가 있는 경우 권장됩니다. 이러한 환경 변수는 다른 서비스의 설정에 따라 다른 서비스에서도 사용할 수 있습니다.

예를 들어 클라이언트 비밀 자격 증명을 사용하려면 파일에서 다음과 같이 를 `local.settings.json` 구성합니다.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "AZURE_CLIENT_ID":"...",
    "AZURE_CLIENT_SECRET":"...",
    "AZURE_TENANT_ID":"..."
  }
}
```
Azure Portal 다음과 같이 설정을 추가합니다.
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>SignalR 지정된 변수에서 ID 구성

SignalR 지정된 변수는 키와 동일한 키 접두사 를 `serviceUri` 공유합니다. 사용할 수 있는 변수 목록은 다음과 같습니다.
* clientId
* clientSecret
* tenantId

클라이언트 비밀 자격 증명을 사용하는 샘플은 다음과 같습니다.

파일 `local.settings.json`에서:
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "<CONNECTION_NAME_PREFIX>:clientId": "...",
    "<CONNECTION_NAME_PREFIX>:clientSecret": "...",
    "<CONNECTION_NAME_PREFIX>:tenantId": "..."
  }
}
```

Azure Portal에서 다음과 같이 설정을 추가 합니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [SignalR 용 Azure AD 개요](signalr-concept-authorize-azure-active-directory.md)
- [관리 id에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여](signalr-howto-authorize-managed-identity.md)