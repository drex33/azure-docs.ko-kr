---
title: Azure AD 역할에 PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소 - Azure Active Directory
description: Azure Active Directory 역할에 PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소입니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef457948a28d9d07464b96bfaea4df6a6f2a8723
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536753"
---
# <a name="prerequisites-to-use-powershell-or-graph-explorer-for-azure-ad-roles"></a>Azure AD 역할에 PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소

PowerShell 또는 Graph 탐색기를 사용하여 Azure Active Directory(Azure AD) 역할을 관리하려면 필수 구성 요소가 있어야 합니다. 이 문서에서는 다양한 Azure AD 역할 기능에 필요한 PowerShell 및 Graph 탐색기 필수 구성 요소를 설명합니다.

## <a name="azuread-module"></a>AzureAD 모듈

PowerShell 명령을 사용하여 다음을 수행합니다.

- 역할 할당 나열
- 역할 할당 가능 그룹 만들기
- 관리 단위 관리

다음 모듈이 설치되어 있어야 합니다.

- [AzureAD](https://www.powershellgallery.com/packages/AzureAD) 버전 2.0.2.137 이상


#### <a name="check-azuread-version"></a>AzureAD 버전 확인

설치한 AzureAD 버전을 확인하려면 [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule)을 사용합니다.

```powershell
Get-InstalledModule -Name AzureAD
```

다음과 비슷한 내용이 출력됩니다.

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.137  AzureAD                             PSGallery            Azure Active Directory V2 General Availability M...
```

#### <a name="install-azuread"></a>AzureAD 설치

AzureAD가 설치되지 않은 경우 [Install-Module을](/powershell/module/powershellget/install-module) 사용하여 AzureAD를 설치합니다.

```powershell
Install-Module -Name AzureAD
```

#### <a name="update-azuread"></a>AzureAD 업데이트

AzureAD를 최신 버전으로 업데이트하려면 [Install-Module](/powershell/module/powershellget/install-module)을 다시 실행합니다.

```powershell
Install-Module -Name AzureAD
```

#### <a name="use-azuread"></a>AzureAD 사용

AzureAD를 사용하려면 다음 단계에 따라 현재 세션으로 가져와야 합니다.

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module)을 사용하여 AzureAD가 메모리에 로드되었는지 확인합니다.

    ```powershell
    Get-Module -Name AzureAD
    ```

1. 이전 단계에서 출력이 표시되지 않으면 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureAD를 가져옵니다. `-Force` 매개 변수는 로드된 모듈을 제거한 후 다시 가져옵니다.

    ```powershell
    Import-Module -Name AzureAD -Force
    ```

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module)을 다시 실행합니다.

    ```powershell
    Get-Module -Name AzureAD
    ```

    다음과 비슷한 내용이 출력됩니다.
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.137  AzureAD                             {Add-AzureADApplicationOwner, Add-AzureADDeviceRegisteredO...
    ```

## <a name="azureadpreview-module"></a>AzureADPreview 모듈

PowerShell 명령을 사용하여 다음을 수행합니다.

- 사용자 또는 그룹에 역할 할당
- 역할 할당 제거
- Privileged Identity Management를 사용하여 그룹에 역할 적격 그룹 만들기
- 사용자 지정 역할 만들기

다음 모듈이 설치되어 있어야 합니다.

- [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) 버전 2.0.2.138 이상


#### <a name="check-azureadpreview-version"></a>AzureADPreview 버전 확인

설치한 AzureADPreview 버전을 확인하려면 [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule)을 사용합니다.

```powershell
Get-InstalledModule -Name AzureADPreview
```

다음과 비슷한 내용이 출력됩니다.

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.138  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...
```

#### <a name="install-azureadpreview"></a>AzureADPreview 설치

AzureADPreview가 설치되지 않은 경우 [Install-Module](/powershell/module/powershellget/install-module)을 사용하여 AzureADPreview를 설치합니다.

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="update-azureadpreview"></a>AzureADPreview 업데이트

AzureADPreview를 최신 버전으로 업데이트하려면 [Install-Module](/powershell/module/powershellget/install-module)을 다시 실행합니다.

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="use-azureadpreview"></a>AzureADPreview 사용

AzureADPreview를 사용하려면 다음 단계에 따라 현재 세션으로 가져와야 합니다.

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module)을 사용하여 AzureADPreview가 메모리에 로드되는지 확인합니다.

    ```powershell
    Get-Module -Name AzureADPreview
    ```

1. 이전 단계에서 출력이 표시되지 않으면 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview를 가져옵니다. `-Force` 매개 변수는 로드된 모듈을 제거한 후 다시 가져옵니다.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module)을 다시 실행합니다.

    ```powershell
    Get-Module -Name AzureADPreview
    ```

    다음과 비슷한 내용이 출력됩니다.
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.138  AzureADPreview                      {Add-AzureADAdministrativeUnitMember, Add-AzureADApplicati...
    ```

## <a name="graph-explorer"></a>그래프 탐색기

[Microsoft Graph API](/graph/overview) 및 [Graph 탐색기](/graph/graph-explorer/graph-explorer-overview)를 사용하여 Azure AD 역할을 관리하려면 다음을 수행해야 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **Enterprise 애플리케이션** 을 선택합니다.

1. 애플리케이션 목록에서 **Graph 탐색기** 를 찾아 선택합니다.

1. **권한** 을 선택합니다.

1. **Graph 탐색기에 관리자 동의 부여** 를 선택합니다.

    ![“Graph 탐색기에 대한 관리자 동의 허용” 링크를 보여 주는 스크린샷.](./media/prerequisites/select-graph-explorer.png)

1. [Graph 탐색기 도구](https://aka.ms/ge)를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory PowerShell for Graph 설치](/powershell/azure/active-directory/install-adv2)
- [AzureAD 모듈 문서](/powershell/module/azuread/)
- [그래프 탐색기](/graph/graph-explorer/graph-explorer-overview)
