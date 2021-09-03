---
title: Azure ID 리소스를 Azure 독일을 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure ID 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 41f8019d8732560754be4757845f22f436cfcf70
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122535440"
---
# <a name="migrate-identity-resources-to-global-azure"></a>ID 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure ID 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

ID/테넌트에 대한 지침은 Azure 전용 고객을 대상으로 합니다. Azure 및 Microsoft 365(또는 기타 Microsoft 제품)에 대한 일반 Azure AD(Azure Active Directory) 테넌트를 사용하는 경우 ID 마이그레이션이 복잡하므로 이 마이그레이션 지침을 사용하기 전에 먼저 계정 관리자에게 문의해야 합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure 독일의 Azure AD는 글로벌 Azure의 Azure AD와 별개입니다. 현재 Azure 독일에서 글로벌 Azure로 Azure AD 사용자를 이동할 수 없습니다.

Azure가 환경에 따라 자동으로 접미사를 추가하기 때문에 Azure 독일과 글로벌 Azure의 기본 테넌트 이름은 항상 다릅니다. 예를 들어 글로벌 Azure의 **contoso** 테넌트 구성원의 사용자 이름은 **user1\@contoso.microsoftazure.com** 입니다. Azure 독일에서는 **user1\@contoso.microsoftazure.de** 입니다.

Azure AD에서 사용자 지정 도메인 이름(예: **contoso.com**)을 사용하는 경우 Azure에 도메인 이름을 등록해야 합니다. 사용자 지정 도메인 이름은 한 번에 *하나의* 클라우드 환경에서만 정의할 수 있습니다. 도메인이 Azure Active Directory의 *모든* 인스턴스에 이미 등록되어 있으면 도메인 유효성 검사가 실패합니다. 예를 들어 Azure 독일에 있는 사용자 **user1\@contoso.com** 은 글로벌 Azure에서도 같은 이름으로 동시에 존재할 수 없습니다. **contoso.com** 에 대한 등록이 실패합니다.

일부 사용자는 이미 새 환경에 있고 일부 사용자는 여전히 이전 환경에 있는 "소프트" 마이그레이션에는 서로 다른 클라우드 환경에 대해 서로 다른 로그인 이름이 필요합니다.

이 문서에서는 가능한 각 마이그레이션 시나리오를 다루지 않습니다. 권장 사항은 예를 들어 사용자를 프로비저닝하는 방법, 다른 사용자 이름 또는 UserPrincipalNames를 사용하기 위한 옵션 및 기타 종속성에 따라 다릅니다. 그러나 현재 환경에서 사용자 및 그룹의 인벤토리를 작성하는 데 도움이 되는 몇 가지 힌트를 컴파일했습니다.

Azure AD와 관련된 모든 cmdlet 목록을 가져오려면 다음을 실행합니다.

```powershell
Get-Help Get-AzureAD*
```

### <a name="inventory-users"></a>인벤토리 사용자

Azure AD 인스턴스에 있는 모든 사용자 및 그룹의 개요를 보려면 다음을 수행합니다.

```powershell
Get-AzureADUser -All $true
```

사용하도록 설정된 계정만 나열하려면 다음 필터를 추가합니다.

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true}
```

잊어버린 경우에 대비하여 모든 특성의 전체 덤프를 만들려면 다음을 수행합니다.

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | Format-List *
```

사용자를 다시 만드는 데 필요한 속성을 선택하려면 다음을 수행합니다.

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname
```

목록을 Excel로 내보내려면 이 목록 끝에 있는 **Export-Csv** cmdlet을 사용합니다. 전체 내보내기는 다음 예와 같이 표시될 수 있습니다.

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname | Export-Csv -Path c:\temp\alluserUTF8.csv -Delimiter ";" -Encoding UTF8
```

> [!NOTE]
> 암호는 마이그레이션할 수 없습니다. 대신 시나리오에 따라 새 암호를 할당하거나 셀프 서비스 메커니즘을 사용해야 합니다.
>
>또한 환경에 따라 **Extensions**, **DirectReport** 또는 **LicenseDetail** 값과 같은 다른 정보를 수집해야 할 수도 있습니다.

필요에 따라 CSV 파일 형식을 지정합니다. 그런 다음 [CSV에서 데이터 가져오기](/powershell/azure/active-directory/importing-data)에 설명된 단계에 따라 새 환경에서 사용자를 다시 만듭니다.

### <a name="inventory-groups"></a>인벤토리 그룹

그룹 구성원을 문서화하려면 다음을 수행합니다.

```powershell
Get-AzureADGroup
```

각 그룹의 구성원 목록을 가져오려면 다음을 수행합니다.

```powershell
Get-AzureADGroup | ForEach-Object {$_.DisplayName; Get-AzureADGroupMember -ObjectId $_.ObjectId}
```

### <a name="inventory-service-principals-and-applications"></a>인벤토리 서비스 주체 및 애플리케이션

모든 서비스 주체 및 애플리케이션을 다시 만들어야 하지만 서비스 주체 및 애플리케이션 상태를 문서화하는 것이 좋습니다. 다음 cmdlet을 사용하여 모든 서비스 주체의 광범위한 목록을 가져올 수 있습니다.

```powershell
Get-AzureADServicePrincipal |Format-List *
```

```powershell
Get-AzureADApplication |Format-List *
```

`Get-AzureADServicePrincipal*` 또는 `Get-AzureADApplication*`으로 시작하는 다른 cmdlet을 사용하여 자세한 정보를 얻을 수 있습니다. 

### <a name="inventory-directory-roles"></a>인벤토리 디렉터리 역할

현재 역할 할당을 문서화하려면 다음을 수행합니다.

```powershell
Get-AzureADDirectoryRole
```

각 역할을 살펴보고 해당 역할과 연결된 사용자 또는 애플리케이션을 찾습니다.

```powershell
Get-AzureADDirectoryRole | ForEach-Object {$_.DisplayName; Get-AzureADDirectoryRoleMember -ObjectId
$_.ObjectId | Format-Table}
```
추가 정보는 다음 항목을 참조하세요.

- [하이브리드 ID 솔루션](../active-directory/hybrid/whatis-hybrid-identity.md)에 대해 알아봅니다.
- 블로그 게시물 [여러 클라우드와 함께 Azure AD Connect 사용](/archive/blogs/ralfwi/using-adconnect-with-multiple-clouds)을 읽고 다양한 클라우드 환경과 동기화하는 방법에 대해 알아봅니다.
- [Azure Active Directory](../active-directory/index.yml)에 대해 자세히 알아봅니다.
- [사용자 지정 도메인 이름](../active-directory/fundamentals/add-custom-domain.md)을 참조하세요.
- [CSV에서 Azure AD로 데이터 가져오기](/powershell/azure/active-directory/importing-data) 방법을 알아봅니다.

## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect는 온-프레미스 Active Directory 인스턴스와 Azure AD(Azure Active Directory) 간에 ID 데이터를 동기화하는 도구입니다. 현재 버전의 Azure AD Connect는 Azure 독일 및 글로벌 Azure 모두에서 작동합니다. Azure AD Connect는 한 번에 하나의 Azure AD 인스턴스에만 동기화할 수 있습니다. Azure 독일 및 글로벌 Azure에 동시에 동기화하려면 다음 옵션을 고려합니다.

- Azure AD Connect의 두 번째 인스턴스에 추가 서버를 사용합니다. 동일한 서버에 Azure AD Connect의 여러 인스턴스를 가질 수 없습니다.
- 사용자의 새 로그인 이름을 정의합니다. 로그인 이름의 도메인 부분( **\@** 뒤)은 환경마다 달라야 합니다.
- 역방향(Azure AD에서 온-프레미스 Active Directory)으로 동기화할 때에도 명확한 "신뢰할 수 있는 원본"을 정의합니다.

이미 Azure AD Connect를 사용하여 Azure 독일과 동기화하고 있는 경우 수동으로 만든 사용자를 마이그레이션해야 합니다. 다음 PowerShell cmdlet은 Azure AD Connect를 사용하여 동기화되지 않은 모든 사용자를 나열합니다.

```powershell
Get-AzureADUser -All $true |Where-Object {$_.DirSyncEnabled -ne "True"}
```

추가 정보는 다음 항목을 참조하세요.

- [Azure AD Connect](../active-directory/hybrid/reference-connect-dirsync-deprecated.md)에 대해 자세히 알아봅니다.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

새 환경에서 사용자를 다시 만들고 Azure AD Multi-Factor Authentication 인스턴스를 재정의해야 합니다. 

다단계 인증이 사용하도록 설정되거나 적용되는 사용자 계정 목록을 얻으려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.
1. **사용자** > **모든 사용자** > **Multi-Factor Authentication** 을 선택합니다.
1. 다단계 인증 서비스 페이지로 리디렉션되면 적절한 필터를 설정하여 사용자 목록을 가져옵니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)