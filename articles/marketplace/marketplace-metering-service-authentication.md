---
title: Azure Marketplace에서 지원 되는 Marketplace 계량 서비스 인증 전략
description: Azure Marketplace에서 지원 되는 계량 서비스 인증 전략입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 1670552bbbe4b9032befc118947df46e480f1faf
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071098"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace 계량 서비스 인증 전략

Marketplace 계량 서비스는 두 가지 인증 전략을 지원합니다.

* [Azure AD 보안 토큰](../active-directory/develop/access-tokens.md)
* [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 

Marketplace 계량 서비스를 사용하여 사용자 지정 미터를 안전하게 제출하기 위해 다양한 인증 전략을 사용하는 시기와 방법을 설명합니다.

## <a name="using-the-azure-ad-security-token"></a>Azure AD 보안 토큰 사용

적용 가능한 제품 유형은 관리되는 애플리케이션 계획 유형을 사용하는 트랜잭션 가능한 SaaS 및 Azure 애플리케이션입니다.  

미리 정의된 고정 Azure AD 애플리케이션 ID를 사용하여 사용자 지정 미터를 제출하여 인증합니다.

SaaS 제품의 경우 유일하게 사용할 수 있는 옵션입니다. [SaaS 애플리케이션 등록](partner-center-portal/pc-saas-registration.md)에 설명된 대로 SaaS 제품을 게시하기 위한 필수 단계입니다.

관리되는 애플리케이션 계획이 있는 Azure 애플리케이션의 경우 다음과 같은 경우에 이 전략을 사용하는 것을 고려해야 합니다.

* 백 엔드 서비스와 통신하는 메커니즘이 이미 있으며 이 메커니즘을 확장하여 중앙 서비스에서 사용자 지정 미터를 내보냅니다.
* 복잡한 사용자 지정 미터 논리를 사용할 수 있습니다.  관리되는 애플리케이션 리소스 대신 중앙 위치에서 이 논리를 실행합니다.

애플리케이션을 등록한 후에는 프로그래밍 방식으로 Azure AD 보안 토큰을 요청할 수 있습니다. 게시자는 이 토큰을 사용하고, 이를 확인하는 요청을 수행해야 합니다.

이러한 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](../active-directory/develop/access-tokens.md)을 참조하세요.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD 앱에 따라 토큰 가져오기

#### <a name="http-method"></a>HTTP 메서드

**POST**

#### <a name="request-url"></a>*요청 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 매개 변수*

|  **매개 변수 이름** |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 등록된 Azure AD 애플리케이션의 테넌트 ID입니다.   |
| | | |

#### <a name="request-header"></a>*요청 헤더*

|  **헤더 이름**    |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다.  |
| | | |

#### <a name="request-body"></a>*요청 본문*

|  **속성 이름**  |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 권한 부여 유형입니다. `client_credentials`을 사용하세요. |
|  `Client_id`        |   True         | Azure AD 앱과 연결된 클라이언트/앱 식별자입니다.|
|  `client_secret`    |   True         | Azure AD 앱과 연결된 암호입니다.  |
|  `Resource`         |   True         | 토큰이 요청된 대상 리소스입니다. `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`을 사용합니다. |
| | | |

#### <a name="response"></a>*응답*

|  **이름**    |  **형식**  |  **설명**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | 요청이 성공했습니다.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

샘플 응답 토큰:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Azure 관리 ID 토큰 사용

적용 가능한 제품 유형은 관리되는 애플리케이션 계획 유형을 사용하는 Azure 애플리케이션입니다.

이 접근 방식을 사용하면 배포된 리소스 ID를 인증하여 사용자 지정 미터 사용량 이벤트를 보낼 수 있습니다.  사용량을 내보내는 코드를 배포 경계 내에 포함할 수 있습니다.

>[!Note]
>게시자는 사용량을 내보내는 리소스가 잠겨 있는지 확인해야 하므로 변조되지 않습니다.

관리되는 애플리케이션은 Virtual Machines에서 Azure Functions까지 다른 유형의 리소스를 포함할 수 있습니다.  여러 서비스에 대해 관리 ID를 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID를 사용하는 방법](../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)을 참조하세요.

예를 들어 다음 단계에 따라 Windows VM을 사용하여 인증합니다.

1. 다음 방법 중 하나를 사용하여 관리 ID를 구성했는지 확인합니다.
    * [Azure Portal UI](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Azure SDK](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. 시스템 ID, VM에 대한 RDP를 사용하여 Marketplace 계량 서비스 애플리케이션 ID(`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`)에 대한 액세스 토큰을 가져오고, PowerShell 콘솔을 열고 아래 명령을 실행합니다.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 현재 리소스 그룹 'ManagedBy' 속성에서 관리 앱 ID를 가져옵니다.

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace 계량 서비스는 `resourceID`에 대한 사용량(관리되는 애플리케이션의 경우 `resourceUsageId`)을 보고해야 합니다.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. [Marketplace 계량 서비스 API](./marketplace-metering-service-apis.md)를 사용하여 사용량을 내보냅니다.

## <a name="next-steps"></a>다음 단계

* [Azure 애플리케이션 제품 만들기](azure-app-offer-setup.md)
* [SaaS 제품 계획](plan-saas-offer.md)
