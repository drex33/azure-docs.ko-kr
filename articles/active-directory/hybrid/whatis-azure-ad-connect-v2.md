---
title: Azure AD Connect v2.0이란? | Microsoft 문서
description: Azure AD Connect의 차기 버전에 대해 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management, has-adal-ref
ms.openlocfilehash: 06b982a43c5155ce5a111bafbac41862363ec07d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355508"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Azure AD Connect v2.0 소개 

Azure AD Connect는 몇 년 전에 출시되었습니다.  그 이후에 Azure AD Connect에서 사용하는 구성 요소 중 일부는 사용 중단되고 최신 버전으로 업데이트될 것으로 예정되었습니다.  이러한 모든 구성 요소를 개별적으로 업데이트하려고 하면 시간과 플랜이 필요합니다.  

이를 해결하기 위해 이러한 많은 최신 구성 요소를 새로운 단일 릴리스로 번들화하여 한 번만 업데이트할 수 있게 하려고 합니다. 이 릴리스는 Azure AD Connect v2.0입니다.  이 버전은 최신 기본 구성 요소를 사용하여 빌드된 새 버전의 소프트웨어로, 하이브리드 ID 목표를 달성하는 데 사용되는 버전과 일치합니다. 

## <a name="what-are-the-major-changes"></a>주요 변경 내용 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 LocalDB 

이전 버전의 Azure AD Connect에는 SQL Server 2012 LocalDB가 함께 제공되었습니다. v 2.0에는 향상된 안정성과 성능을 제공하고 몇 가지 보안 관련 버그 수정이 포함된 SQL Server 2019 LocalDB가 함께 제공됩니다. SQL Server 2012는 2022년 7월까지 연장된 지원에 해당되지 않습니다. 자세한 내용은 [Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019)를 참조하세요.

### <a name="msal-authentication-library"></a>MSAL 인증 라이브러리 

이전 버전의 Azure AD Connect에는 ADAL 인증 라이브러리가 함께 제공됩니다. 이 라이브러리는 2022년 6월부터 사용 중단됩니다. V2.0 릴리스에는 최신 MSAL 라이브러리가 함께 제공됩니다. 자세한 내용은 [MSAL 라이브러리 개요](../../active-directory/develop/msal-overview.md)를 참조하세요.

### <a name="visual-c-redist-14"></a>Visual C++ Redist 14 

SQL Server 2019에는 Visual C++ Redist 14 런타임이 필요하므로 이 버전을 사용하도록 C++ 런타임 라이브러리를 업데이트할 예정입니다. 이 업데이트는 Azure AD Connect v2.0 패키지와 함께 설치되므로 C++ 런타임 업데이트를 위해 어떤 작업도 수행할 필요가 없습니다. 

### <a name="tls-12"></a>TLS 1.2 

TLS 1.0 및 TLS 1.1은 안전하지 않은 것으로 간주되며 Microsoft에서 더 이상 지원되지 않는 프로토콜입니다. 이 Azure AD Connect 릴리스는 TLS 1.2만 지원합니다. Azure AD Connect V2.0에 지원되는 모든 버전의 Windows Server는 이미 TLS 1.2로 기본 설정되어 있습니다. 서버에서 TLS 1.2를 지원하지 않는 경우 Azure AD Connect v2.0을 배포하기 전에 이 프로토콜을 사용하도록 설정해야 합니다. 자세한 내용은 [Azure AD Connect에 대한 TLS 1.2 적용](reference-connect-tls-enforcement.md)을 참조하세요.

### <a name="all-binaries-signed-with-sha2"></a>SHA2로 서명된 모든 이진 파일 

일부 구성 요소에는 SHA1으로 서명된 이진 파일이 있습니다. 다운로드 가능한 이진 파일용 SHA1을 더 이상 지원하지 않으며 모든 이진 파일은 SHA2 서명으로 업그레이드했습니다. 업데이트가 Microsoft에서 직접 제공되고 배달 중에 변조되지 않았음을 확인하기 위해 디지털 서명이 사용됩니다. SHA-1 알고리즘의 약점 때문에 업계 표준에 맞게 Windows 업데이트의 서명을 보다 안전한 SHA-2 알고리즘을 사용하도록 변경했습니다."  

사용자 측에서 필요한 작업은 없습니다. 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Windows Server 2012 및 Windows Server 2012 R2는 더 이상 지원되지 않음 

SQL Server 2019에는 서버 운영 체제로 Windows Server 2016 이상 버전이 필요합니다. AAD Connect v2는 SQL Server 2019 구성 요소를 포함하므로 더 이상 이전 Windows 서버 버전을 지원할 수 없습니다.  

이 버전은 이전 Windows 서버 버전에 설치할 수 없습니다. Azure AD Connect 서버를 Windows Server 운영 체제의 최신 버전인 Windows server 2019로 업그레이드하는 것이 좋습니다. 

이 [문서](/windows-server/get-started-19/install-upgrade-migrate-19)에서는 이전 Windows Server 버전에서 Windows Server 2019로 업그레이드하는 방법을 설명합니다. 

### <a name="powershell-50"></a>PowerShell 5.0 

이 Azure AD Connect 릴리스에는 PowerShell 5.0이 필요한 몇 가지 cmdlet이 포함되어 있으므로 이 요구 사항은 Azure AD Connect의 새로운 필수 구성 요소입니다.  

PowerShell 필수 조건에 대한 자세한 내용은 [여기](/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements#windows-powershell-50)에서 찾을 수 있습니다.

 >[!NOTE]
 >PowerShell 5는 이미 Windows Server 2016의 일부이므로 최근 Windows Server 버전을 사용하는 한, 별도로 수행해야 할 작업은 없을 것입니다. 

## <a name="what-else-do-i-need-to-know"></a>그 밖에 알아야 할 사항은 무엇인가요? 


**이 업그레이드가 중요한 이유는 무엇인가요?** </br>
내년에는 현재 Azure AD Connect 서버 설치의 몇 가지 구성 요소에 대한 지원이 중단될 예정입니다. 지원되지 않는 제품을 사용하는 경우 지원 팀이 조직에서 요구하는 지원 환경을 제공하는 것이 더 어려워집니다. 따라서 모든 고객은 최대한 빨리 이 최신 버전으로 업그레이드하는 것이 좋습니다. 

이 업그레이드는 Azure AD Connect에 대한 필수 구성 요소를 업데이트해야 하고 이러한 필수 구성 요소의 최신 버전에 대한 서버 업데이트를 계획하고 실제로 업데이트하기 위한 추가 시간이 필요할 수 있으므로 특히 중요합니다. 

**알아두어야 할 새로운 기능이 있나요?** </br>
아니요 - 이 릴리스에는 새로운 기능이 포함되어 있지 않습니다. 이 릴리스에는 Azure AD Connect의 일부 기본 구성 요소의 업데이트만 포함되어 있습니다. 

**이전 버전에서 V2.0으로 업그레이드할 수 있나요?** </br>
예 – 이전 버전의 Azure AD Connect에서 Azure AD Connect v2.0로 업그레이드하는 것은 지원됩니다. [이 문서](how-to-upgrade-previous-version.md)의 지침에 따라 가장 적합한 업그레이드 전략을 결정하세요. 

**현재 서버의 구성을 내보내고 Azure AD Connect V2.0으로 가져올 수 있나요?** </br>
예, 가능합니다. 특히 새 운영 체제 버전으로 업그레이드하는 경우 이 방법이 Azure AD Connect V2.0으로 마이그레이션하는 좋은 방법입니다. 가져오기/내보내기 구성 기능 및 이 기능을 사용하는 방법에 대한 자세한 내용은 이 [문서](how-to-connect-import-export-config.md)에서 확인할 수 있습니다. 

**Azure AD Connect에 대한 자동 업그레이드를 사용하도록 설정했습니다. 이 새 버전을 자동으로 받게 되나요?** </br> 아니요 – Azure AD Connect V2.0은 현재 자동 업그레이드에 사용할 수 없습니다. 

**아직 업그레이드할 준비가 되지 않았습니다. 여유 기간이 얼마나 되나요?** </br>
가능하면 빨리 Azure AD Connect V2.0으로 업그레이드해야 합니다. **__모든 Azure AD Connect V1 버전은 2022년 8월 31일에 사용이 중단됩니다.__** 당분간 이전 버전의 Azure AD Connect을 계속 지원할 예정이지만 Azure AD Connect의 일부 구성 요소의 지원이 중단될 경우 적절한 지원 환경을 제공하기 어려울 수 있습니다. ADAL 및 TLS1.0/1.1 서비스는 사용 중단된 후에 예기치 않게 작동 중단될 수 있으므로 이러한 서비스의 경우 이 업그레이드가 특히 중요합니다. 

**외부 SQL 데이터베이스를 사용하며 SQL 2012 LocalDb를 사용하지 않고 있습니다. 그래도 업그레이드해야 하나요?** </br>
예, TLS1.0/1.1 및 ADAL 사용 중단 때문에 SQL Server 2012를 사용하지 않더라도 지원을 받으려면 여전히 업그레이드가 필요합니다. SQL Server 2012는 Azure AD Connect V2.0에서 외부 SQL 데이터베이스로 계속 사용될 수 있습니다. Azure AD Connect V2.0의 SQL 2019 드라이버는 SQL Server 2012와 호환됩니다.

**Azure AD Connect 인스턴스를 V2.0으로 업그레이드한 후 SQL 2012 구성 요소가 자동으로 제거되나요?** </br>
아니요, SQL 2019로 업그레이드해도 서버에서 SQL 2012 구성 요소가 제거되지 않습니다. 이러한 구성 요소가 더 이상 필요하지 않으면 [SQL Server 제거 지침](/sql/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup)을 따라야 합니다.

**업그레이드하지 않으면 어떻게 되나요?** </br>
사용 중지될 구성 요소 중 하나가 실제로 사용 중단될 때까지는 어떤 영향도 나타나지 않습니다. Azure AD Connect는 계속 작동합니다. 

TLS 1.0/1.1은 2022년 1월부터 더 이상 지원되지 않을 예정이며 서비스가 예기치 않게 작동 중지될 수 있으므로 해당 날짜부터 이러한 프로토콜을 사용하지 않는지 확인해야 합니다. TLS 1.2에 맞게 서버를 수동으로 구성할 수 있지만 Azure AD Connect를 V2.0으로 업그레이드할 필요는 없습니다. 

2022년 6월부터 ADAL은 지원되지 않을 예정입니다. ADAL이 지원되지 않는 경우에는 예기치 않게 인증이 중지되고 이로 인해 Azure AD Connect 서버가 제대로 작동하지 않을 수 있습니다. 2022년 6월 이전에 Azure AD Connect를 V2.0으로 업그레이드할 것을 강력히 권장합니다. 현재 Azure AD Connect 버전을 사용하여 지원되는 인증 라이브러리로 업그레이드할 수 없습니다. 

**2.0으로 업그레이드한 후 ADSync PowerShell cmdlet이 작동하지 않나요?** </br>
이것은 알려진 문제입니다.  이 문제를 해결하려면 버전 2.0을 설치하거나 이 버전으로 업그레이드한 후 PowerShell 세션을 다시 시작하고 모듈을 다시 가져옵니다.  다음 지침에 따라 모듈을 가져옵니다.
 
 1.  관리자 권한으로 Windows PowerShell을 엽니다.
 2.  다음을 입력하거나 복사하여 붙여넣습니다. 
    ``` powershell
              Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
    ```
 

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>Azure AD Connect V2.0 사용을 위한 라이선스 요구 사항

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Azure AD Connect Health 사용을 위한 라이선스 요구 사항
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)

이 문서에서는 이전 Windows Server 버전에서 Windows Server 2019로 업그레이드하는 방법을 설명합니다.
