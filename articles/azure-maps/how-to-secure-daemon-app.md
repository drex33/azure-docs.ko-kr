---
title: Microsoft Azure Maps에서 디먼 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: 이 문서에서는 백그라운드 프로세스, 타이머 및 작업과 같은 디먼 애플리케이션을 Microsoft Azure Maps의 신뢰할 수 있는 보안 환경에 호스트하는 방법을 설명합니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
custom.ms: subject-rbac-steps
ms.openlocfilehash: e64645f7bdcbfb40cbee0fd29d1df2464a5d4f3e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450859"
---
# <a name="secure-a-daemon-application"></a>디먼 애플리케이션 보호

이 문서에서는 Microsoft Azure Maps의 신뢰할 수 있는 보안 환경에 디먼 애플리케이션을 호스트하는 방법을 설명합니다.

다음은 디먼 애플리케이션의 예입니다.

- Azure 웹 작업
- Azure 함수 앱
- Windows 서비스
- 실행 중인 안정적인 백그라운드 서비스

## <a name="view-azure-maps-authentication-details"></a>Azure Maps 인증 세부 정보 보기

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>프로덕션 애플리케이션의 경우 Azure AD 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 구현하는 것이 좋습니다. Azure AD 개념에 관한 개요는 [Azure Maps 인증](azure-maps-authentication.md)을 참조하세요.

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>시나리오: Azure Key Vault을 사용하여 공유 키 인증

공유 키 인증을 사용하는 애플리케이션은 키를 보안 저장소에 저장해야 합니다. 이 시나리오에서는 애플리케이션 키를 Azure Key Vault에 비밀로 안전하게 저장하는 방법을 설명합니다. 공유 키를 애플리케이션 구성에 저장하는 대신, 애플리케이션은 공유 키를 Azure Key Vault 비밀로 검색할 수 있습니다. 키를 간단하게 다시 생성할 수 있도록 애플리케이션에서 한 번에 하나의 키를 사용하는 것이 좋습니다. 그러면 애플리케이션이 키 하나로 현재 연결을 계속 유지하면서도 사용되지 않는 키를 다시 생성하고 다시 생성된 키를 Azure Key Vault에 배포할 수 있습니다. Azure Key Vault를 구성하는 방법을 알아보려면 [Azure Key Vault 개발자 가이드](../key-vault/general/developers-guide.md)를 참조하세요.

>[!IMPORTANT]
>이 시나리오에서는 Azure Key Vault를 통해 Azure Active Directory에 간접적으로 액세스합니다. 그러나 Azure AD 인증을 직접 사용하는 것이 좋습니다. Azure AD를 직접 사용하면 공유 키 인증을 사용하고 Key Vault를 설정하는 복잡한 과정과 작업을 수행할 필요가 없습니다.

다음 단계에서는 이 프로세스를 간략하게 설명합니다.

1. [Azure Key Vault를 만듭니다](../key-vault/general/quick-create-portal.md).
2. 앱 등록 또는 관리 ID를 만들어 [Azure AD 서비스 주체](../active-directory/fundamentals/service-accounts-principal.md)를 만듭니다. 생성된 주체는 Azure Key Vault에 대한 액세스를 담당합니다.
3. Azure Key 비밀 `get` 권한에 대한 액세스 권한을 서비스 주체에 할당합니다. 권한 설정에 대한 자세한 내용은 [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조하세요.
4. 비밀 `set` 권한에 대한 액세스 권한을 개발자에게 일시적으로 할당합니다.
5. Key Vault 비밀에서 공유 키를 설정하고, 비밀 ID를 디먼 애플리케이션의 구성으로 참조합니다.
6. 비밀 `set` 권한을 제거합니다.
7. Azure Key Vault에서 공유 비밀 키를 검색하려면 디먼 애플리케이션에서 Azure Active Directory 인증을 구현합니다.
8. 공유 키를 사용하여 Azure Maps REST API 요청을 만듭니다.
이제 디먼 애플리케이션은 Key Vault에서 공유 키를 검색할 수 있습니다.

> [!TIP]
> 앱이 Azure 환경에 호스트되는 경우 관리 ID를 사용하여 인증 비밀을 관리하는 비용과 복잡성을 줄이는 것이 좋습니다. 관리 ID를 설정하는 방법을 알아보려면 [자습서: 관리 ID를 사용하여 .NET에서 Key Vault를 Azure 웹 앱에 연결](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)을 참조하세요.

## <a name="scenario-azure-ad-role-based-access-control"></a>시나리오: Azure AD 역할 기반 액세스 제어

Azure Maps 계정이 만들어지면 Azure Maps `Client ID` 값이 Azure Portal 인증 정보 페이지에 표시됩니다. 이 값은 REST API 요청에 사용되는 계정을 나타냅니다. 이 값은 애플리케이션 구성에 저장되고, HTTP 요청을 만들기 전에 검색됩니다. 이 시나리오의 목표는 디먼 애플리케이션이 Azure AD에 인증하고 Azure Maps REST API를 호출할 수 있도록 하는 것입니다.

> [!TIP]
>관리 ID 구성 요소의 이점을 활용할 수 있도록 Azure Virtual Machines, Virtual Machine Scale Sets 또는 App Services에서 호스트하는 것이 좋습니다.

### <a name="host-a-daemon-on-azure-resources"></a>Azure 리소스에서 디먼 호스트

Azure 리소스에서 실행하는 경우 적은 비용으로 최소한의 자격 증명 관리 작업을 수행할 수 있도록 Azure 관리 ID를 구성할 수 있습니다.

관리 ID에 대한 애플리케이션 액세스를 사용하려면 [관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

관리 ID의 이점은 다음과 같습니다.

- Azure 시스템 관리형 X509 인증서 공개 키 암호화 인증
- 클라이언트 비밀 대신 X509 인증서를 사용하는 Azure AD 보안.
- Azure는 관리 ID 리소스와 연결된 모든 인증서를 관리하고 갱신합니다.
- 관리 ID를 사용하면 Azure Key Vault와 같은 보호된 비밀 저장소 서비스가 필요 없기 때문에 자격 증명 관리가 간소화됩니다.

### <a name="host-a-daemon-on-non-azure-resources"></a>Azure 이외의 리소스에서 디먼 호스트

비 Azure 환경에서 실행하는 경우 관리 ID를 사용할 수 없습니다. 따라서 디먼 애플리케이션의 Azure AD 애플리케이션 등록을 통해 서비스 주체를 구성해야 합니다.

#### <a name="create-new-application-registration"></a>새 애플리케이션 등록 만들기

애플리케이션 등록을 이미 만든 경우 [위임된 API 권한 할당](#assign-delegated-api-permissions)으로 이동합니다.

새 애플리케이션 등록을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 를 선택합니다.

3. 왼쪽 창의 **관리** 에서 **앱 등록** 을 선택합니다.

4. **+ 새 등록** 탭을 선택합니다.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="앱 등록을 살펴봅니다.":::

5. **이름** 을 입력하고 **계정 유형 지원** 을 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="앱 등록을 만듭니다.":::

6. **등록** 을 선택합니다.  

#### <a name="assign-delegated-api-permissions"></a>위임된 API 사용 할당

위임된 API 권한을 Azure Maps에 할당하려면 다음을 수행합니다.

1. 아직 로그인하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 를 선택합니다.

3. 왼쪽 창의 **관리** 에서 **앱 등록** 을 선택합니다.

4. 애플리케이션을 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="앱 등록을 선택합니다.":::

5. 왼쪽 창의 **관리** 에서 **API 권한** 을 선택합니다.

6. **사용 권한 추가** 를 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="앱 권한을 추가합니다.":::

7. **내 조직에서 사용하는 API** 탭을 선택합니다.

8. 검색 상자에 **Azure Maps** 를 입력합니다.

9. **Azure Maps** 를 선택합니다.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="앱 권한을 요청합니다.":::

10. **Azure Maps 액세스** 확인란을 선택합니다.

11. **권한 추가** 를 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="앱 API 권한을 선택합니다.":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>클라이언트 암호 만들기 또는 인증서 구성

애플리케이션에 서버 또는 애플리케이션 기반 인증을 구현할 때 다음 두 가지 옵션 중 하나를 선택할 수 있습니다.

- 공개 키 인증서를 업로드합니다.
- 클라이언트 암호를 만듭니다.

##### <a name="upload-a-public-key-certificate"></a>공개 키 인증서 업로드

공개 키 인증서를 업로드하려면 다음을 수행합니다.

1. 왼쪽 창의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.

2. **인증서 업로드** 를 선택합니다.
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="인증서를 업로드합니다.":::

3. 텍스트 상자 오른쪽에서 파일 아이콘을 선택합니다.

4. *.crt*, *.cer* 또는 *.pem* 파일을 선택한 다음, **추가** 를 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="인증서 파일을 업로드합니다.":::

##### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

클라이언트 암호를 만들려면 다음을 수행합니다.

1. 왼쪽 창의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.

2. **+ 새 클라이언트 암호** 를 선택합니다.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="새 클라이언트 암호":::

3. 클라이언트 암호에 대한 설명을 입력합니다.

4. **추가** 를 선택합니다.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="새 클라이언트 암호를 추가합니다.":::

5. 비밀을 복사하여 Azure Key Vault와 같은 서비스에 안전하게 저장합니다. 이 문서의 [관리 ID를 사용하여 토큰 요청](#request-a-token-with-managed-identity) 섹션에서 이 비밀을 사용하게 됩니다.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="클라이언트 암호를 복사합니다.":::

     >[!IMPORTANT]
     >인증서 또는 비밀을 안전하게 저장하려면 [Azure Key Vault 개발자 가이드](../key-vault/general/developers-guide.md)를 참조하세요. 이 비밀을 사용하여 Azure AD에서 토큰을 가져옵니다.

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>관리 ID를 사용하여 토큰 요청

호스팅 리소스의 관리 ID가 구성되면 Azure SDK 또는 REST API를 사용하여 Azure Maps 토큰을 획득할 수 있습니다. 액세스 토큰을 획득하는 방법을 알아보려면 [액세스 토큰 획득](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

### <a name="request-token-with-application-registration"></a>애플리케이션 등록을 사용하여 토큰 요청

앱을 등록하고 Azure Maps와 연결한 후에는 액세스 토큰을 요청해야 합니다.

액세스 토큰을 획득하려면 다음을 수행합니다.

1. 아직 로그인하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 를 선택합니다.

3. 왼쪽 창의 **관리** 에서 **앱 등록** 을 선택합니다.

4. 애플리케이션을 선택합니다.

5. [개요] 페이지가 표시됩니다. 애플리케이션(클라이언트) ID 및 디렉터리(테넌트) ID를 복사합니다.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="토큰 매개 변수를 복사합니다.":::

여기서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하여 토큰 요청을 만들지만, 다른 API 개발 환경을 사용할 수도 있습니다.

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. **요청 이름**(예: *POST 토큰 요청*)을 입력합니다.

4. **POST** HTTP 메서드를 선택합니다.

5. 주소 표시줄에 다음 URL을 입력합니다(를 `{Tenant-ID}` 디렉터리(테넌트) ID로 바꾸고, 를 `{Client-ID}` 애플리케이션(클라이언트) `{Client-Secret}` ID로, 를 클라이언트 암호로 바꿉다.

    ```http
    https://login.microsoftonline.com/{Tenant-ID}/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id={Client-ID}&client_secret={Client-Secret}%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. **보내기** 를 선택합니다.

7. 다음 JSON 응답이 표시됩니다.

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

인증 흐름에 대한 자세한 내용은 [Microsoft ID 플랫폼의 OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)을 참조하세요.

## <a name="next-steps"></a>다음 단계

보다 자세한 예제는 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Azure AD 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
