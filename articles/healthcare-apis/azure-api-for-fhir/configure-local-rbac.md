---
title: Azure API for FHIR 대한 로컬 RBAC(로컬 역할 기반 액세스 제어) 구성
description: 이 문서에서는 데이터 평면에 외부 Azure AD 테넌트 사용을 Azure API for FHIR 구성하는 방법을 설명합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: zxue
ms.openlocfilehash: 1b081f230f562a333bbc014fb4583cc37a556d50
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435057"
---
# <a name="configure-local-rbac-for-fhir"></a>FHIR에 대한 로컬 RBAC 구성 

이 문서에서는 데이터 평면 액세스를 관리하기 위해 외부의 보조 Azure Active Directory 테넌트에서 사용하도록 Azure API for FHIR 구성하는 방법을 설명합니다. 구독과 연결된 Azure Active Directory 테넌트를 사용할 수 없는 경우에만 이 모드를 사용합니다.

> [!NOTE]
> FHIR 서비스 데이터 평면이 구독과 연결된 기본 Azure Active Directory 테넌트 사용을 구성한 경우 [Azure RBAC를 사용하여 데이터 평면 역할을 할당합니다.](configure-azure-rbac.md)

## <a name="add-service-principal"></a>서비스 주체 추가

로컬 RBAC를 사용하면 외부 Azure Active Directory 테넌트에서 FHIR 서버를 사용할 수 있습니다. 로컬 RBAC 시스템에서 이 테넌트에서 그룹 멤버 자격을 확인할 수 있도록 하려면 Azure API for FHIR 테넌트에서 서비스 주체가 있어야 합니다. 이 서비스 주체는 Azure API for FHIR 배포한 구독에 연결된 테넌트에서 자동으로 생성되지만 테넌트에서 연결된 구독이 없는 경우 테넌트 관리자는 다음 명령 중 하나를 사용하여 이 서비스 주체를 만들어야 합니다.

`Az`PowerShell 모듈 사용:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

또는 `AzureAd` PowerShell 모듈을 사용할 수 있습니다.

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

또는 Azure CLI 사용할 수 있습니다.

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>로컬 RBAC 구성

**인증** 블레이드에서 외부 또는 보조 Azure Active Directory 테넌트 사용을 Azure API for FHIR 구성할 수 있습니다.

![로컬 RBAC 할당](media/rbac/local-rbac-guids.png).

기관 상자에 유효한 Azure Active Directory 테넌트 를 입력합니다. 테넌트 유효성이 검사되면 **허용된 개체 ID** 상자를 활성화하고 ID 개체 ID 목록을 입력할 수 있습니다. 이러한 ID는 다음의 ID 개체 ID일 수 있습니다.

* Azure Active Directory 사용자입니다.
* Azure Active Directory 서비스 주체.
* Azure Active Directory 보안 그룹.

자세한 내용은 ID 개체 [ID를 찾는](find-identity-object-ids.md) 방법에 대한 문서를 읽어보세요.

필요한 개체 ID를 입력한 후 **저장을** 클릭하고 할당된 사용자, 서비스 주체 또는 그룹을 사용하여 데이터 평면에 액세스하기 전에 변경 내용이 저장될 때까지 기다립니다.

## <a name="caching-behavior"></a>캐싱 동작

Azure API for FHIR 최대 5분 동안 결정을 캐시합니다. 허용된 개체의 목록에 추가하여 사용자에게 FHIR 서버에 대한 액세스 권한을 부여하거나 목록에서 제거하는 경우 권한 변경 내용이 전파되는 데 최대 5분이 소요될 것으로 예상해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 외부(보조) Azure Active Directory 테넌트를 사용하여 FHIR 데이터 평면 액세스를 할당하는 방법을 배웠습니다. 다음으로 Azure API for FHIR 대한 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
