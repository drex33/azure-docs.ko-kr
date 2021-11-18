---
title: Azure 응용 프로그램에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여
description: 이 문서에서는 Azure 응용 프로그램에서 Azure AD로 리소스를 SignalR 하는 권한 부여 요청에 대 한 정보를 제공 합니다
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 2ad3fd5a6a4aa3d002c75558e798e6deedf043df
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716928"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Azure 응용 프로그램에서 Azure AD를 사용 하 여 리소스를 SignalR 하는 요청 권한 부여

azure SignalR Service는 azure [응용 프로그램](../active-directory/develop/app-objects-and-service-principals.md)에서 요청을 승인 하는 Azure Active Directory (azure AD)를 지원 합니다.

이 문서에서는 Azure 응용 프로그램에서 SignalR 리소스에 대 한 요청에 권한을 부여 하도록 SignalR 리소스 및 코드를 구성 하는 방법을 보여 줍니다.

## <a name="register-an-application"></a>애플리케이션 등록

첫 번째 단계는 Azure 응용 프로그램을 등록 하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에서를 검색 하 고 선택 **Azure Active Directory**
2. **관리** 섹션에서 **앱 등록** 을 선택 합니다.
3. **새 등록** 을 클릭합니다.

    ![응용 프로그램 등록 스크린샷](./media/authenticate/register-an-application.png)

4. 애플리케이션의 표시 **이름** 을 입력합니다.
5. 등록 **을 클릭 하 여 등록을 확인** 합니다.

응용 프로그램을 등록 하면 개요 페이지에서 **응용 프로그램 (클라이언트) id** 및 **디렉터리 (테 넌 트) id** 를 찾을 수 있습니다. 이러한 Guid는 다음 단계에서 유용할 수 있습니다.

![응용 프로그램의 스크린샷](./media/authenticate/application-overview.png)

응용 프로그램을 등록 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [빠른 시작: Microsoft ID 플랫폼을 사용 하 여 응용 프로그램을 등록](../active-directory/develop/quickstart-register-app.md)합니다.


## <a name="add-credentials"></a>자격 증명 추가

인증서와 클라이언트 암호(문자열)를 모두 자격 증명으로 기밀 클라이언트 앱 등록에 추가할 수 있습니다.

### <a name="client-secret"></a>클라이언트 암호

응용 프로그램은 토큰을 요청할 때 해당 id를 증명 하기 위해 클라이언트 암호가 필요 합니다. 클라이언트 암호를 만들려면 다음 단계를 수행 합니다.

1. **관리** 섹션에서 **인증서 & 암호** 를 선택 합니다.
1. **클라이언트 암호** 탭에서 **새 클라이언트 암호** 를 클릭 합니다.
![클라이언트 암호 만들기 스크린샷](./media/authenticate/new-client-secret.png)
1. 클라이언트 암호에 대 한 **설명을** 입력 하 고 **만료 시간** 을 선택 합니다.
1. **클라이언트 암호** 의 값을 복사 하 여 안전한 위치에 붙여넣습니다. 
    > [!NOTE]
    > 비밀은 한 번만 표시 됩니다.

### <a name="certificate"></a>인증서

클라이언트 암호를 만드는 대신 인증서를 업로드할 수도 있습니다.

![인증 업로드 스크린샷](./media/authenticate/upload-certificate.png)

자격 증명을 추가 하는 방법에 대 한 자세한 내용은을 참조 하세요.

- [자격 증명 추가](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal에 역할 할당 추가

이 샘플에서는 `SignalR App Server` SignalR 리소스를 통해 서비스 주체 (응용 프로그램)에 역할을 할당 하는 방법을 보여 줍니다. 

> [!Note]
> 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯 한 모든 범위에 역할을 할당할 수 있습니다. 범위에 대해 자세히 알아보려면 [AZURE RBAC의 범위 이해](../role-based-access-control/scope-overview.md) 를 참조 하세요.

1. [Azure Portal](https://portal.azure.com/)에서 SignalR 리소스로 이동 합니다.

1. **Access Control (IAM)** 을 클릭 하 여 Azure SignalR에 대 한 액세스 제어 설정을 표시 합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷에서는 SignalR 리소스에 대 한 액세스 제어 (IAM) 페이지의 예를 보여 줍니다.

   ![액세스 제어 스크린샷](./media/authenticate/access-control.png)

1. **추가 > 역할 할당 추가** 를 클릭 합니다.

1. **역할** 탭에서을 선택 `SignalR App Server` 합니다.

1. **다음** 을 클릭합니다.
    
   ![역할 할당 추가의 스크린샷](./media/authenticate/add-role-assignment.png)

1. **구성원** 탭의 **액세스 할당** 섹션에서 **사용자, 그룹 또는 서비스 주체** 를 선택 합니다.

1. **멤버 선택** 을 클릭 합니다.

3. 역할을 할당 하려는 응용 프로그램을 검색 하 고 선택 합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

4. **다음** 을 클릭합니다.
    
   ![서비스 사용자에 게 역할을 할당 하는 스크린샷](./media/authenticate/assign-role-to-service-principals.png)

5. **검토 + 할당** 을 클릭 하 여 변경 내용을 확인 합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.

Azure 역할 할당을 할당 하 고 관리 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>앱 구성

### <a name="app-server"></a>앱 서버

모범 사례는 환경 변수에서 id 및 자격 증명을 구성 하는 것입니다.

| 변수  | 설명 |
|------|------
| `AZURE_TENANT_ID` | Azure Active Directory 테 넌 트 (디렉터리) ID입니다. |
| `AZURE_CLIENT_ID` | 테 넌 트에서 앱 등록의 클라이언트 (응용 프로그램) ID입니다. |
| `AZURE_CLIENT_SECRET` | 앱 등록을 위해 생성 된 클라이언트 암호입니다. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | 앱 등록을 인증할 수 있는 PEM 또는 PFX 형식의 인증서 및 개인 키 쌍에 대 한 경로입니다. |
| `AZURE_USERNAME`  | Azure Active Directory 사용자 계정의 사용자 이름 (upn)입니다. |
| `AZURE_PASSWORD`  | Azure Active Directory 사용자 계정의 암호입니다. 참고이는 MFA를 사용 하는 계정을 지원 하지 않습니다. |

이렇게 하면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 또는 환경 [자격 증명](/dotnet/api/azure.identity.environmentcredential) 을 사용 하 여 SignalR 끝점을 구성할 수 있습니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

또는 `EnvironmentalCredential` 직접를 사용 합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

가 작동 하는 방법을 알아보려면 `DefaultAzureCredential` [DefaultAzureCredential 클래스](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)를 참조 하세요.

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>여러 끝점을 사용 하는 동안 다른 자격 증명을 사용 합니다.

몇 가지 이유로 끝점 마다 다른 자격 증명을 사용 하는 것이 좋습니다.

이 시나리오에서는 [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) 또는 [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential)를 사용할 수 있습니다.

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

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR 바인딩

> [!WARNING]
> SignalR 트리거 바인딩은 id 기반 연결을 지원 하지 않으며 여전히 연결 문자열이 필요 합니다.

Azure Functions SignalR 바인딩은 포털 또는 로컬에서 [응용 프로그램 설정을](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 사용 [`local.settings.json`](../azure-functions/functions-develop-local.md#local-settings-file) 하 여 SignalR 리소스에 액세스할 수 있도록 Azure 응용 프로그램 id를 구성 합니다.

먼저 SignalR 서비스의 서비스 URI를 지정 해야 합니다 .이 서비스는 키가 `serviceUri` **연결 이름 접두사** (기본값)로 시작 `AzureSignalRConnectionString` 하 고 구분 기호 ( `__` Azure Portal 및 로컬. s s i s `:` 파일)로 시작 합니다. 바인딩 속성을 사용 하 여 연결 이름을 사용자 지정할 수 있습니다 [`ConnectionStringSetting`](../azure-functions/functions-bindings-signalr-service.md) . 읽기를 계속 하 여 샘플을 찾습니다.

그런 다음 [미리 정의 된 환경 변수](#configure-identity-in-pre-defined-environment-variables) 또는 [SignalR 지정 된 변수](#configure-identity-in-signalr-specified-variables)에서 Azure 응용 프로그램 id를 구성 하도록 선택 합니다.

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>미리 정의 된 환경 변수에서 id 구성

미리 정의 된 환경 변수 목록은 [환경 변수](/dotnet/api/overview/azure/identity-readme#environment-variables) 를 참조 하세요. 여러 서비스가 동일한 Azure 응용 프로그램 id에 종속 된 SignalR를 포함 하 여 각 서비스에 대 한 id를 구성할 필요가 없는 경우에 권장 됩니다. 이러한 환경 변수는 다른 서비스의 설정에 따라 다른 서비스에서 사용 될 수도 있습니다.

예를 들어 클라이언트 암호 자격 증명을 사용 하려면 파일에서 다음과 같이 구성 `local.settings.json` 합니다.
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
Azure Portal에서 다음과 같이 설정을 추가 합니다.
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>SignalR 지정 변수에서 id 구성

SignalR 지정 된 변수는 키와 동일한 키 접두사를 공유 합니다 `serviceUri` . 사용할 수 있는 변수 목록은 다음과 같습니다.
* clientId
* clientSecret
* tenantId

클라이언트 암호 자격 증명을 사용 하는 샘플은 다음과 같습니다.

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

Azure Portal 다음과 같이 설정을 추가합니다.
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [SignalR용 Azure AD 개요](signalr-concept-authorize-azure-active-directory.md)
- [관리 ID에서 Azure AD를 사용하여 SignalR 리소스에 대한 요청 권한 부여](signalr-howto-authorize-managed-identity.md)