---
title: 클라이언트 인증서 인증을 사용한 API Management 백엔드 보호
titleSuffix: Azure API Management
description: Azure API Management에서 클라이언트 인증서 인증을 사용하여 클라이언트 인증서를 관리하고 백엔드 서비스를 보호하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00d00c8dc5419b524eb71172adfb5271905d668f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057787"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management에서 클라이언트 인증서 인증을 사용한 백엔드 서비스 보호

API Management를 사용하면 클라이언트 인증서를 사용하여 API의 백엔드 서비스에 대한 액세스를 보호할 수 있습니다. 이 가이드에서는 Azure Portal을 이용하여 Azure API Management 서비스 인스턴스의 인증서를 관리하는 방법을 보여줍니다. 또한 인증서를 사용하여 백엔드 서비스에 액세스하도록 API를 구성하는 방법을 설명합니다.

[API Management REST API](/rest/api/apimanagement/2021-01-01-preview/certificate)를 사용하여 API Management 인증서를 관리할 수도 있습니다.

## <a name="certificate-options"></a>인증서 옵션

API Management에서는 백엔드 서비스에 대한 액세스를 보호하는 데 사용되는 인증서를 관리하는 두 가지 옵션을 제공합니다.

* [Azure Key Vault](../key-vault/general/overview.md)에서 관리되는 인증서 참조 
* API Management에 직접 인증서 파일 추가

API Management 보안을 높이기 위해 키 자격 증명 모음 인증서를 사용하는 것이 좋습니다.

* 키 자격 증명 모음에 저장된 인증서는 서비스에서 재사용할 수 있습니다.
* 키 자격 증명 모음에 저장된 인증서에 세분화된 [액세스 정책](../key-vault/general/security-features.md#privileged-access)을 적용할 수 있습니다.
* 키 자격 증명 모음에서 업데이트한 인증서는 API Management에서 자동으로 회전됩니다. 키 자격 증명 모음에서 업데이트 후 API Management에 있는 인증서는 4시간 이내에 업데이트됩니다. Azure Portal 또는 Management REST API를 사용하여 인증서를 수동으로 새로 고칠 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.
* 클라이언트 인증서 인증에 대해 백엔드 서비스를 구성해야 합니다. Azure App Service에서 인증서 인증을 구성하려면 [이 문서][to configure certificate authentication in Azure WebSites refer to this article]를 참조하세요. 
* Azure Key Vault에서 관리를 위해 인증서 및 암호에 액세스하거나 API Management 서비스에 업로드해야 합니다. 인증서는 **PFX** 형식이어야 합니다. 자체 서명된 인증서도 사용할 수 있습니다.

### <a name="prerequisites-for-key-vault-integration"></a>키 자격 증명 모음 통합을 위한 사전 요구 사항

1. 키 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.
1. API Management 인스턴스에서 시스템 또는 사용자가 할당한 [관리 ID](api-management-howto-use-managed-service-identity.md)를 사용하도록 설정합니다.
1. 자격 증명 모음에서 인증서를 가져오고 나열할 수 있는 권한을 통해 관리 ID에 [키 자격 증명 모음 액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 할당합니다. 정책을 추가하려면 다음을 수행합니다.
    1. 포털에서 키 자격 증명 모음으로 이동합니다.
    1. **설정 > 액세스 정책 > + 액세스 정책 추가** 를 차례로 선택합니다.
    1. **인증서 사용 권한** 을 선택한 다음, **가져오기** 및 **목록** 을 선택합니다.
    1. **보안 주체 선택** 에서 관리 ID의 리소스 이름을 선택합니다. 시스템이 할당한 ID를 사용하는 경우 보안 주체는 API Management 인스턴스의 이름입니다.
1. 키 자격 증명 모음에 인증서를 만들거나 가져옵니다. [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)을 참조하세요.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>키 자격 증명 모음 인증서 추가

[키 자격 증명 모음 통합을 위한 사전 요구 사항](#prerequisites-for-key-vault-integration)을 참조하세요.

> [!CAUTION]
> API Management에서 키 자격 증명 모음 인증서를 사용하는 경우 키 자격 증명 모음 액세스에 사용하는 인증서, 키 자격 증명 모음 또는 관리 ID를 삭제하지 않도록 주의합니다.

API Management에 키 자격 증명 모음 인증서를 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **보안** 에서 **인증서** 를 선택합니다.
1. **인증서** >  **+ 추가** 를 선택합니다.
1. **ID** 에서 원하는 이름을 입력합니다.
1. **인증서** 에서 **키 자격 증명 모음** 을 선택합니다.
1. 키 자격 증명 모음 인증서의 식별자를 입력하거나, 키 자격 증명 모음에서 인증서를 선택하려면 **선택** 을 선택합니다.
    > [!IMPORTANT]
    > 키 자격 증명 모음 인증서 식별자를 직접 입력할 때 버전 정보를 포함해서는 안 됩니다. 이를 포함할 경우 키 자격 증명 모음을 업데이트한 후에 API Management에서 인증서가 자동으로 회전되지 않습니다.
1. **클라이언트 ID** 에서 시스템 또는 기존 사용자가 할당한 관리 ID를 선택합니다. [API Management 서비스에서 관리 ID를 추가 또는 수정](api-management-howto-use-managed-service-identity.md)하는 방법에 대해 알아봅니다.
    > [!NOTE]
    > ID에는 키 자격 증명 모음에서 인증서를 가져오고 나열할 수 있는 권한이 필요합니다. 키 자격 증명 모음에 대한 액세스를 아직 구성하지 않은 경우 API Management는 필수적인 권한을 통해 ID를 자동으로 구성할 수 있도록 메시지를 표시합니다.
1. **추가** 를 선택합니다.



    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="키 자격 증명 모음 인증서 추가":::
    
1. **저장** 을 선택합니다.

## <a name="upload-a-certificate"></a>인증서 업로드

API Management에 클라이언트 인증서를 업로드하려면 다음을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **보안** 에서 **인증서** 를 선택합니다.
1. **인증서** >  **+ 추가** 를 선택합니다.
1. **ID** 에서 원하는 이름을 입력합니다.
1. **인증서** 에서 **사용자 지정** 을 선택합니다.
1. 인증서 .pfx 파일을 찾아 선택하고 암호를 입력합니다.
1. **추가** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="클라이언트 인증서 업로드":::


1. **저장** 을 선택합니다.

인증서를 업로드하면 **인증서** 창에 표시됩니다. 인증서가 많은 경우 [게이트웨이 인증](#configure-an-api-to-use-client-certificate-for-gateway-authentication)에 클라이언트 인증서를 사용하도록 API를 구성하려면 원하는 인증서의 지문을 기록해 둡니다.

> [!NOTE]
> 자체 서명된 인증서 등을 사용할 때 인증서 체인 유효성 검사를 해제하려면 이 문서 뒷부분에 나오는 [자체 서명된 인증서](#self-signed-certificates)에 설명된 단계를 수행합니다.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>게이트웨이 인증에 클라이언트 인증서를 사용하도록 API 구성

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **API** 에서 **API** 를 선택합니다.
1. 목록에서 API를 선택합니다. 
1. **디자인** 탭에서 **백엔드** 섹션에 있는 편집기 아이콘을 선택합니다.
1. **게이트웨이 자격 증명** 에서 **클라이언트 인증서** 를 선택하고 드롭다운 목록에서 인증서를 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="게이트웨이 인증에 클라이언트 인증서 사용":::

> [!CAUTION]
> 변경한 내용은 즉시 적용되며 해당 API의 작업 호출은 인증서를 사용하여 백엔드 서버에서 인증됩니다.

> [!TIP]
> API의 백 엔드 서비스에 대한 게이트웨이 인증을 위해 지정된 인증서는 해당 API의 정책에 포함되며 정책 편집기에서 볼 수 있습니다.

## <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명된 인증서를 사용하는 경우 API Management와 백엔드 시스템의 통신을 위해 인증서 체인 유효성 검사를 비활성화해야 합니다. 그렇지 않으면 500 오류 코드를 반환합니다. 이를 구성하기 위해 [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend)(새로운 백엔드) 또는 [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend)(기존 백엔드) PowerShell cmdlet을 사용하고 `-SkipCertificateChainValidation` 매개 변수를 `True`로 설정할 수 있습니다.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>클라이언트 인증서 삭제

인증서를 삭제하려면 해당 인증서를 선택한 다음, 컨텍스트 메뉴( **...** )에서 **삭제** 를 선택합니다.

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="인증서 삭제":::

> [!IMPORTANT]
> 인증서가 어떠한 정책에서 참조되고 있다면, 경고 화면이 표시됩니다. 인증서를 삭제하려면 해당 인증서를 사용하도록 구성된 정책에서 인증서를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

* [API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법](api-management-howto-mutual-certificates-for-clients.md)
* [API Management의 정책](api-management-howto-policies.md)에 대해 알아보기


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md
