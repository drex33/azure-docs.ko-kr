---
title: 인증용 id 개체 Id 찾기-FHIR 용 Azure API
description: 이 문서에서는 FHIR 용 Azure API에 대 한 인증을 구성 하는 데 필요한 id 개체 Id를 찾는 방법을 설명 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: zxue
ms.openlocfilehash: 96dfc3b10a1a4a9b4f12e8d9decb5d01446385f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436261"
---
# <a name="find-identity-object-ids-for-authentication-configuration-for-azure-api-for-fhir"></a>FHIR 용 Azure API의 인증 구성에 대 한 id 개체 Id 찾기

이 문서에서는 데이터 평면에 [외부 또는 보조 Active Directory 테 넌 트를 사용](configure-local-rbac.md) 하도록 FHIR 용 Azure API를 구성할 때 필요한 Id 개체 id를 찾는 방법에 대해 알아봅니다.

## <a name="find-user-object-id"></a>사용자 개체 ID 찾기

사용자 이름을 가진 사용자가 있는 경우 `myuser@contoso.com` `ObjectId` 다음 PowerShell 명령을 사용 하 여 사용자를 찾을 수 있습니다.

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

또는 Azure CLI를 사용할 수 있습니다.

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>서비스 사용자 개체 ID 찾기

[서비스 클라이언트 앱](register-service-azure-ad-client-app.md) 을 등록 하 고이 서비스 클라이언트에서 FHIR 용 Azure API에 액세스 하도록 허용 하려면 다음 PowerShell 명령을 사용 하 여 클라이언트 서비스 사용자의 개체 ID를 찾을 수 있습니다.

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

여기서 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 는 서비스 클라이언트 응용 프로그램 ID입니다. 또는 서비스 클라이언트의를 사용할 수 있습니다 `DisplayName` .

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Azure CLI 사용 하는 경우 다음을 사용할 수 있습니다.

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>보안 그룹 개체 ID 찾기

보안 그룹의 개체 ID를 찾으려면 다음 PowerShell 명령을 사용할 수 있습니다.

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
여기서 `mygroup` 는 관심이 있는 그룹의 이름입니다.

Azure CLI 사용 하는 경우 다음을 사용할 수 있습니다.

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 외부 또는 보조 Azure Active Directory 테 넌 트를 사용 하도록 fhir 용 Azure API를 구성 하는 데 필요한 id 개체 id를 찾는 방법에 대해 알아보았습니다. 다음에는 개체 Id를 사용 하 여 로컬 RBAC 설정을 구성 하는 방법에 대해 자세히 알아봅니다.
 
>[!div class="nextstepaction"]
>[로컬 RBAC 설정 구성](configure-local-rbac.md)
