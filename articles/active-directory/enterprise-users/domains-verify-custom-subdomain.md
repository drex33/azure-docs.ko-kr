---
title: PowerShell 및 Graph를 사용하여 하위 도메인 인증 유형 변경 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 루트 도메인 설정에서 상속된 기본 하위 도메인 인증 설정을 변경합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 11/05/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8a0ab89e8437edec176e7033665b627df6cd493
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866491"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Azure Active Directory에서 하위 도메인 인증 유형 변경

루트 도메인이 Azure AD(Azure Active Directory)에 추가된 후 Azure AD 조직의 해당 루트에 추가된 모든 후속 하위 도메인은 루트 도메인에서 인증 설정을 자동으로 상속합니다. 그러나 루트 도메인 설정과 별개로 도메인 인증 설정을 관리하려는 경우에는 이제 Microsoft Graph API를 사용할 수 있습니다. 예를 들어, contoso.com과 같은 페더레이션된 루트 도메인이 있는 경우 이 문서를 통해 페더레이션되는 것이 아니라 관리형 child.contoso.com과 같은 하위 도메인을 확인할 수 있습니다.

Azure AD 포털에서 부모 도메인이 페더레이션되고 관리자가 **사용자 지정 도메인 이름** 페이지에서 관리형 하위 도메인을 확인하려고 하면 “하나 이상의 속성에 잘못된 값이 포함되어 있습니다.”라는 이유로 ‘도메인을 추가하지 못함’ 오류가 표시됩니다. Microsoft 365 관리 센터에서 이 하위 도메인을 추가하려고 하면 비슷한 오류가 표시됩니다. 오류에 관한 자세한 내용은 [자식 도메인이 Office 365, Azure 또는 Intune의 부모 도메인 변경 내용을 상속하지 않음](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)을 참조하세요.

## <a name="how-to-verify-a-custom-subdomain"></a>사용자 지정 하위 도메인을 확인하는 방법

하위 도메인은 기본적으로 루트 도메인의 인증 유형을 상속하기 때문에 인증 유형을 원하는 유형으로 설정할 수 있도록 Microsoft Graph를 사용하여 Azure AD의 루트 도메인으로 하위 도메인의 수준을 올려야 합니다.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>하위 도메인 추가 및 해당 인증 유형 보기

1. PowerShell을 사용하여 루트 도메인의 기본 인증 유형이 있는 새 하위 도메인을 추가합니다. Azure AD 및 Microsoft 365 관리 센터는 아직 이 작업을 지원하지 않습니다.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. 다음 예를 사용하여 도메인을 가져옵니다(GET). 해당 도메인은 루트 도메인이 아니므로 루트 도메인 인증 유형을 상속합니다. 고유한 테넌트 ID를 사용하면 명령 및 결과가 다음과 같이 표시될 수 있습니다.

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-microsoft-graph-api-to-make-this-a-root-domain"></a>Microsoft Graph API를 사용하여 이를 루트 도메인으로 만들기

다음 명령을 사용하여 하위 도메인의 수준을 올립니다.

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>하위 도메인 인증 유형 변경

1. 다음 명령을 사용하여 하위 도메인 인증 유형을 변경합니다.

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Microsoft Graph API에서 GET을 통해 이제 하위 도메인 인증 유형이 관리되는지 확인합니다.

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>다음 단계

- [사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [도메인 이름 관리](domains-manage.md)
- [Microsoft Graph API를 통해 사용자 지정 도메인 이름 ForceDelete](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)