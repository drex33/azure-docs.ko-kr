---
title: 'Azure AD Connect: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서에는 Azure AD Connect 및 Azure AD Sync의 모든 릴리스가 나열되어 있습니다.
author: billmath
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/21/2021
ms.subservice: hybrid
ms.author: rodejo
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d58e688133af9f587e3b2c6d40d9f9cf07e5a60
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500890"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: 버전 릴리스 내역
Azure AD(Azure Active Directory) 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

이 문서는 릴리스된 버전을 추적하고 최신 버전에 변경 내용을 이해할 수 있도록 도와줍니다.

## <a name="looking-for-the-latest-versions"></a>최신 버전을 찾고 있나요?

AADConnect 서버를 지원되는 모든 버전에서 최신 버전으로 업그레이드할 수 있습니다.

 - **Windows Server 2016 이상** 을 사용하는 경우 **Azure AD Connect V2.0** 을 사용해야 합니다. [이 링크](https://www.microsoft.com/en-us/download/details.aspx?id=47594)를 사용하여 최신 버전의 Azure AD Connect 2.0을 다운로드할 수 있습니다. 최신 V2.0 릴리스에 대한 릴리스 정보는 [여기](reference-connect-version-history.md#20280)에 있습니다.
 - **이전 버전의 Windows Server** 를 계속 사용하는 경우 **Azure AD Connect V1.6** 을 사용해야 합니다. [이 링크](https://www.microsoft.com/download/details.aspx?id=103336)를 사용하여 최신 버전의 Azure AD Connect V1을 다운로드할 수 있습니다. 최신 V1.6 릴리스에 대한 릴리스 정보는 [여기](reference-connect-version-history.md#16160)에 있습니다.
 - Microsoft는 앞으로 V1.x 버전에 대한 중요한 변경 사항만 적용하며, V1.x 릴리스에서 V2.0에 대한 일부 기능 및 수정 사항을 찾을 수 없으므로 가능한 한 빨리 V2.0 버전으로 업그레이드해야 합니다. 특히 V2 엔드포인트에 대한 지원은 더 이상 V1.x 버전에서 사용할 수 없습니다. 그룹을 50,000명 넘는 구성원과 동기화해야 하는 경우 Azure AD Connect V2.0으로 업그레이드해야 합니다.

이 테이블은 관련 항목 목록입니다.

항목 |  세부 정보
--------- | --------- |
Azure AD Connect에서 업그레이드하는 단계 | Azure AD Connect 릴리스를 [이전 버전에서 최신 버전으로 업그레이드](how-to-upgrade-previous-version.md) 하는 다른 방법입니다.
필요한 사용 권한 | 업데이트를 적용하는 데 필요한 사용 권한은 [계정 및 사용 권한](reference-connect-accounts-permissions.md#upgrade)을 참조하세요.


>[!IMPORTANT]
> **2022년 8월 31일부터 모든 1.x 버전의 Azure AD(Azure Active Directory) Connect는 더 이상 지원되지 않을 SQL Server 2012 구성 요소를 포함하므로 사용이 중단됩니다.** 해당 날짜까지 최신 버전의 Azure AD Connect(2.x 버전)로 업그레이드하거나 [Azure AD 클라우드 동기화를 평가하고 전환](../cloud-sync/what-is-cloud-sync.md)합니다.
> 
> 최적의 지원 환경을 받으려면 최신 버전의 Azure AD Connect를 실행하고 있는지 확인해야 합니다. 
> 
> 사용 중지된 버전의 Azure AD Connect를 실행하면 예기치 않게 작동이 중지될 수 있으며 최신 보안 수정 사항, 성능 개선 사항, 문제 해결 및 진단 도구, 서비스 개선 사항이 없을 수 있습니다. 또한 지원이 필요한 경우 조직에 필요한 수준의 서비스를 제공하지 못할 수도 있습니다.
> 
> 이 문서로 이동하여 V2.0에서 변경된 사항 및 이 변경 사항이 사용자에게 미치는 영향 등 [Azure Active Directory Connect V2.0](whatis-azure-ad-connect-v2.md)에 대해 자세히 알아봅니다.
>
> Azure AD Connect를 최신 버전으로 업그레이드하는 방법에 대한 자세한 내용은 [이 문서](./how-to-upgrade-previous-version.md)를 참조하세요.
>
> 사용 중지된 버전 관련 버전 기록 정보는 [Azure AD Connect 버전 릴리스 기록 보관](reference-connect-version-history-archive.md)을 참조하세요.

>[!NOTE]
>새로운 버전의 Azure AD Connect를 릴리스하는 과정은 서비스 작업 기능을 보장하기 위해 몇 가지 품질 관리 단계가 필요한 프로세스이며, 이 프로세스를 거치는 동안 새 릴리스의 버전 번호와 릴리스 상태가 최신 상태를 반영하도록 업데이트됩니다.
일부 버전의 Azure AD Connect는 자동 업그레이드에 사용할 수 있습니다. 릴리스 상태는 릴리스가 자동 업그레이드 또는 다운로드에만 사용할 수 있는지 여부를 나타냅니다. 자동 업그레이드가 Azure AD Connect 서버에서 활성화된 경우 해당 서버는 자동으로 자동 업그레이드용으로 릴리스되는 최신 버전의 Azure AD Connect로 업그레이드됩니다. 일부 Azure AD Connect 구성을 자동 업그레이드에 사용할 수 있습니다. 

>자동 업그레이드의 사용을 명시하기 위해 중요한 업데이트 및 중요한 픽스를 모두 푸시하는 것이 좋습니다. 모든 버전에서 중요한 보안 문제 관련 픽스를 요구하거나 포함하지 않기 때문에 최신 버전이 아닐 수도 있습니다(여러 경우 중 하나의 예시에 불과함). 중요한 문제는 일반적으로 자동 업그레이드를 통해 제공되는 새 버전으로 해결됩니다. 해당 문제가 없으면 자동 업그레이드를 사용하여 푸시된 업데이트가 없으며, 일반적으로 최신 자동 업그레이드 버전을 사용한다면 정상입니다.
그러나 모든 최신 기능 및 업데이트를 원하는 경우 이 페이지를 확인하고 적절하게 설치하는 것이 가장 좋은 방법입니다. 

>[자동 업그레이드](how-to-connect-install-automatic-upgrade.md)에 대한 자세한 내용은 이 링크 참조


## <a name="16160"></a>1.6.16.0
>[!NOTE] 
>Azure AD Connect에 대한 업데이트 릴리스입니다. 이 버전은 고객이 이전 버전의 Windows Server를 실행하는 데 사용되며 현재는 Windows Server 2016 이상으로 서버를 업그레이드할 수 없습니다. 이 버전을 사용하여 Azure AD Connect V2.0 서버를 업데이트할 수 없습니다. 이 릴리스는 Windows Server 2016 이상에서 지원되지 않습니다. 이 릴리스에는 SQL Server 2012 구성 요소가 포함되며 2022년 8월 31일에 사용 중지됩니다. 이 날짜 이전에 Server OS 및 AADConnect 버전을 업그레이드해야 합니다.
>V2 엔드포인트에 대한 지원은 더 이상 V1.x 버전에서 사용할 수 없습니다. 그룹을 50,000명 넘는 구성원과 동기화해야 하는 경우 Azure AD Connect V2.0으로 업그레이드해야 합니다.

### <a name="release-status"></a>릴리스 상태
2021/10/13: 자동 업그레이드 및 다운로드용으로 릴리스되었습니다.

### <a name="bug-fixes"></a>버그 수정
- Autoupgrade 프로세스가 이전 Windows OS 버전 2008 또는 2008 R2를 실행하는 AADConnect 서버를 업그레이드하려고 했지만 실패하는 버그가 수정되었습니다. 이러한 버전의 Windows Server는 더 이상 지원되지 않습니다. 이 릴리스에서는 Windows Server 2012 이상 버전을 실행하는 머신에서만 자동 업그레이드를 시도합니다.
- 특정 조건에서 액세스 위반 예외로 인해 서버가 충돌하는 문제를 해결했습니다.

## <a name="20280"></a>2.0.28.0

>[!NOTE] 
> Azure AD Connect 유지 관리 업데이트 릴리스입니다. 이 릴리스에는 Windows Server 2016 이상이 필요합니다.

### <a name="release-status"></a>릴리스 상태
2021/9/30: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="bug-fixes"></a>버그 수정
 - 마법사의 그룹 쓰기 저장 사용 권한 페이지에서 PowerShell 스크립트에 대한 다운로드 단추를 제거하고 마법사 페이지에서 텍스트를 변경하여 PowerShell 스크립트를 찾을 수 있는 온라인 문서에 연결하는 자세한 정보 링크를 포함시켰습니다.

 - 레지스트리 키가 없기 때문에 서버의 .NET 버전이 4.6보다 큰 경우 마법사가 설치를 잘못 차단하는 버그를 수정했습니다. 이러한 레지스트리 키는 필요하지 않으며 의도적으로 false로 설정된 경우에만 설치를 차단해야 합니다.

- 동기화 단계를 초기화하는 동안 가상 개체가 발견되는 경우 오류가 발생하는 버그를 수정했습니다. 이렇게 하면 동기화 단계가 차단되거나 임시 개체가 제거됩니다. 이제 가상 개체는 무시됩니다.
참고: 가상 개체는 존재하지 않거나 아직 표시되지 않은 개체에 대한 자리 표시자입니다. 예를 들어 원본 개체에 없는 대상 개체에 대한 참조가 있는 경우 대상 개체를 가상 개체로 만듭니다.

### <a name="functional-changes"></a>기능 변경 내용

 - 사용자가 포함 목록에서 개체 및 특성을 사용 중인 경우에도 선택을 취소할 수 있도록 하는 변경이 수행되었습니다. 이를 차단하는 대신 이제 경고를 제공합니다. 

## <a name="16142"></a>1.6.14.2
>[!NOTE] 
>Azure AD Connect에 대한 업데이트 릴리스입니다. 이 버전은 고객이 이전 버전의 Windows Server를 실행하는 데 사용되며 현재는 Windows Server 2016 이상으로 서버를 업그레이드할 수 없습니다. 이 버전을 사용하여 Azure AD Connect V2.0 서버를 업데이트할 수 없습니다.
>이 버전을 다운로드할 수 있는 경우 적격한 테넌트를 자동으로 업그레이드하기 시작합니다. 자동 업그레이드를 완료하는 데 몇 주가 걸립니다.
>V2 엔드포인트에 대한 지원은 더 이상 V1.x 버전에서 사용할 수 없습니다. 그룹을 50,000명 넘는 구성원과 동기화해야 하는 경우 Azure AD Connect V2.0으로 업그레이드해야 합니다.

### <a name="release-status"></a>릴리스 상태
2021/9/21: 자동 업그레이드 및 다운로드용으로 릴리스되었습니다.

### <a name="functional-changes"></a>기능 변경 내용
 - MIM 커넥터(1.1.1610.0)의 최신 버전을 추가했습니다. 추가 정보는 [MiM 커넥터의 릴리스 기록 페이지](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)에서 찾을 수 있습니다.
 - Azure AD Connect에서 소프트 일치 기능을 비활성화하는 구성 옵션을 추가했습니다. 고객이 클라우드 전용 계정을 사용해야 하는 경우를 제외하고 소프트 일치를 사용하지 않도록 설정하는 것이 좋습니다. 이 [문서](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant)에서는 소프트 일치를 비활성화하는 방법을 보여 줍니다.

### <a name="bug-fixes"></a>버그 수정
 - 이전 버전에서 업그레이드한 후 DesktopSSO 설정이 유지되지 않는 버그를 수정했습니다.
 - Set-ADSync\*Permission cmdlet이 실패하는 버그를 수정했습니다.

## <a name="20251"></a>2.0.25.1

>[!NOTE] 
> Azure AD Connect에 대한 핫픽스 업데이트 릴리스입니다. 이 릴리스에는 Windows Server 2016 이상이 필요하며 Azure AD Connect 버전 2.0에 있는 보안 문제 및 기타 버그 수정 사항을 수정합니다.

### <a name="release-status"></a>릴리스 상태
2021/9/14: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="bug-fixes"></a>버그 수정

 - 따옴표가 붙지 않은 경로를 사용하여 Azure AD Connect 서비스를 가리키는 보안 문제가 해결되었습니다. 이 경로는 이제 따옴표가 붙은 경로입니다.
 - 기존 AD 커넥터 계정을 사용할 때 쓰기 저장을 사용하여 가져오기 구성 문제를 해결합니다.
 - 잘못된 상속 형식으로 인해 1.6에서 중단된 Set-ADSyncExchangeHybridPermissions 및 기타 관련 cmdlet의 문제를 해결했습니다.
 - TLS 버전을 설정하기 위해 이전 릴리스에 게시한 cmdlet에는 키를 덮어쓰고 키에 있던 값을 제거하는 문제가 발생했습니다. 새 키가 아직 없는 경우에만 새 키를 만들어 이 문제를 해결했습니다. 또한 사용자에게 TLS 레지스트리 변경 내용이 Azure AD Connect 전용이 아니라 동일한 서버의 다른 애플리케이션에 영향을 줄 수 있음을 알릴 수 있도록 경고가 추가되었습니다.
 - Windows Server 2016 이상이 필요하도록 V2.0의 자동 업그레이드를 적용하는 검사가 추가되었습니다.
 - Set-ADSyncBasicReadPermissions cmdlet에서 '디렉터리 변경 내용 복제' 권한을 추가했습니다.
 - UseExistingDatabase 및 가져오기 구성은 충돌하는 구성 설정을 포함할 수 있으므로 함께 사용되지 않도록 변경했습니다.
 - 애플리케이션 관리자 역할이 있는 사용자가 앱 프록시 서비스 구성을 변경할 수 있도록 변경했습니다.
 - Import/Export 설정의 레이블에서 '(미리 보기)' 레이블을 제거했습니다. 지금은 이 기능이 일반 공급 상태입니다.
 - 아직 "회사 관리자"인 일부 레이블을 변경합니다. 이제 "전역 관리자" 역할 이름을 사용합니다.
 - AAD 서비스 주체에 클레임 변환 규칙을 추가하는 AAD Kerberos PowerShell cmdlet "\*-AADKerberosServer"를 새로 만들었습니다.

### <a name="functional-changes"></a>기능 변경 내용
 - MIM 커넥터(1.1.1610.0)의 최신 버전을 추가했습니다. 추가 정보는 [MiM 커넥터의 릴리스 기록 페이지](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#1116100-september-2021)에서 찾을 수 있습니다.
 - Azure AD Connect에서 소프트 일치 기능을 비활성화하는 구성 옵션을 추가했습니다. 고객이 클라우드 전용 계정을 사용해야 하는 경우를 제외하고 소프트 일치를 사용하지 않도록 설정하는 것이 좋습니다. 이 [문서](/powershell/module/msonline/set-msoldirsyncfeature#example-2--block-soft-matching-for-the-tenant)에서는 소프트 일치를 비활성화하는 방법을 보여 줍니다.

## <a name="20100"></a>2.0.10.0

### <a name="release-status"></a>릴리스 상태
2021년 8월 19일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

>[!NOTE] 
>Azure AD Connect에 대한 핫픽스 업데이트 릴리스입니다. 이 릴리스에는 Windows Server 2016 이상이 필요합니다. 이 핫픽스는 Azure AD Connect 버전 1.6뿐만 아니라 버전 2.0에도 나타나는 문제를 해결합니다. 이전 Windows Server에서 Azure AD Connect 실행하는 경우 [1.6.13.0](#16130) 빌드를 대신 설치해야 합니다.

### <a name="release-status"></a>릴리스 상태
2021년 8월 19일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="known-issues"></a>알려진 문제
 - 특정 상황에서는 이 버전에 대한 설치 관리자에서 TLS 1.2를 사용할 수 없다는 오류를 표시하고 설치를 중지합니다. 이는 TLS 1.2에 대한 레지스트리 설정을 확인하는 코드의 오류로 인한 것입니다. 이는 향후 릴리스에서 해결될 예정입니다. 이 문제가 발생하는 고객은 "[Azure AD Connect에 대한 TLS 1.2 적용](reference-connect-tls-enforcement.md)" 문서에서 찾을 수 있는 TLS 1.2를 활성화하는 지침을 따라야 합니다.

### <a name="bug-fixes"></a>버그 수정

 - 도메인 이름을 바꾸면 이벤트 로그에서 “지정된 캐스트가 유효하지 않음”을 나타내는 오류와 함께 암호 해시 동기화가 실패하는 버그를 수정했습니다. 이는 이전 빌드에서 발생한 버그가 재발한 것입니다.

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>Azure AD Connect에 대한 핫픽스 업데이트 릴리스입니다. 이 릴리스는 Windows Server 2012 또는 2012 R2가 있는 서버에서 Azure AD Connect를 실행하는 고객을 위한 것입니다.

2021년 8월 19일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="bug-fixes"></a>버그 수정

 - 도메인 이름을 바꾸면 이벤트 로그에서 “지정된 캐스트가 유효하지 않음”을 나타내는 오류와 함께 암호 해시 동기화가 실패하는 버그를 수정했습니다. 이는 이전 빌드에서 발생한 버그가 재발한 것입니다.

### <a name="functional-changes"></a>기능 변경 내용
이 릴리스에는 기능 변경 내용이 없습니다.

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>릴리스 상태
2021년 8월 17일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="bug-fixes"></a>버그 수정
>[!NOTE] 
>Azure AD Connect에 대한 핫픽스 업데이트 릴리스입니다. 이 릴리스에는 Windows Server 2016 이상이 필요합니다. 이 릴리스는 버전 2.0.8.0에 있는 문제를 해결합니다. 이 문제는 Azure AD Connect 버전 1.6에서는 나타나지 않습니다.

 - 많은 수의 암호 해시 동기화 트랜잭션을 동기화할 때 이벤트 로그 항목 길이가 암호 해시 동기화 이벤트 항목에 허용되는 최대 길이를 초과하는 버그를 수정했습니다. 이제 긴 로그 항목을 여러 항목으로 분할합니다.

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>Azure AD Connect 보안 업데이트 릴리스입니다. 이 릴리스에는 Windows Server 2016 이상이 필요합니다. 이전 버전의 Windows Server를 사용하는 경우 [버전 1.6.11.3](#16113)을 사용하세요.
>이 릴리스는 [이 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949)에 설명된 대로 취약성을 해결합니다. 이 취약성에 대한 자세한 내용은 CVE를 참조하세요.
>[이 링크](https://www.microsoft.com/en-us/download/details.aspx?id=47594)를 사용하여 최신 버전의 Azure AD Connect 2.0을 다운로드할 수 있습니다.

### <a name="release-status"></a>릴리스 상태
2021년 8월 10일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다. 

### <a name="functional-changes"></a>기능 변경 내용
이 릴리스에는 기능 변경 내용이 없습니다.

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>Azure AD Connect 보안 업데이트 릴리스입니다. 이 버전은 고객이 이전 버전의 Windows Server를 실행하는 데 사용되며 현재는 Windows Server 2016 이상으로 서버를 업그레이드할 수 없습니다. 이 버전을 사용하여 Azure AD Connect V2.0 서버를 업데이트할 수 없습니다.
>이 릴리스는 [이 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949)에 설명된 대로 취약성을 해결합니다. 이 취약성에 대한 자세한 내용은 CVE를 참조하세요.
>[이 링크](https://www.microsoft.com/download/details.aspx?id=103336)를 사용하여 최신 버전의 Azure AD Connect 1.6을 다운로드할 수 있습니다.

### <a name="release-status"></a>릴리스 상태
2021년 8월 10일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.

### <a name="functional-changes"></a>기능 변경 내용
이 릴리스에는 기능 변경 내용이 없습니다.

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>Azure AD Connect의 주 릴리스입니다. 자세한 내용은 [Azure Active Directory V2.0 문서](whatis-azure-ad-connect-v2.md)를 참조하세요.

### <a name="release-status"></a>릴리스 상태
2021년 7월 20일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없습니다.
### <a name="functional-changes"></a>기능 변경 내용
 - SQL Server의 LocalDB 구성 요소를 SQL 2019로 업그레이드했습니다. 
 - 이 릴리스에는 SQL Server 2019의 요구 사항으로 인해 Windows Server 2016 이상 버전이 필요합니다. Azure AD Connect 서버에서 Windows Server의 현재 위치 업그레이드는 지원되지 않으므로 [스윙 마이그레이션](how-to-upgrade-previous-version.md#swing-migration)을 사용해야 할 수 있습니다.
 -    이 릴리스에서는 TLS 1.2 사용을 적용합니다. TLS 1.2에 대해 Windows Server를 사용하도록 설정한 경우 AADConnect는 이 프로토콜을 사용합니다. 서버에서 TLS 1.2를 사용하도록 설정하지 않은 경우 AADConnect를 설치하려고 할 때 오류 메시지가 표시되며 TLS 1.2를 사용하도록 설정할 때까지 설치가 계속되지 않습니다. 새로운 "Set-ADSyncToolsTls12" cmdlet을 사용하여 서버에서 TLS 1.2를 사용하도록 설정할 수 있습니다.
 -    이 릴리스에서는 Azure AD Connect를 설치할 때 "하이브리드 ID 관리자" 사용자 역할의 사용자를 사용하여 인증을 받을 수 있습니다. 이를 위한 전역 관리자 역할은 더 이상 필요하지 않습니다.
 - SQL Server 2019에 대한 필수 조건으로 Visual C++ 런타임 라이브러리를 버전 14로 업그레이드했습니다.    
 -    이 릴리스는 인증에 MSAL 라이브러리를 사용하며, 2022년에 사용 중단될 이전 ADAL 라이브러리를 제거했습니다.    
 -    Windows 보안 지침에 따라 AdminSDHolders에 대한 사용 권한을 더 이상 적용하지 않습니다. ADSyncConfig.psm1 모듈에서 "SkipAdminSdHolders" 매개 변수를 "IncludeAdminSdHolders"로 변경했습니다.
 -    암호가 변경되었는지 여부에 관계 없이 만료된 암호가 "만료되지 않은" 상태일 때 암호가 다시 평가됩니다. 사용자의 경우 암호를 "다음 로그온 할 때 반드시 암호 변경"으로 설정하고 이 플래그를 선택 취소하면(암호 "만료 취소"), "만료되지 않음" 상태와 암호 해시가 Azure AD와 동기화되고 사용자가 Azure AD에서 로그인을 시도할 때 만료되지 않은 암호를 사용할 수 있습니다.
Active Directory의 만료된 암호를 Azure Active Directory와 동기화하려면 Azure AD Connect에서 [임시 암호 동기화](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon) 기능을 사용 하세요. 이 기능을 사용하려면 암호 쓰기 저장을 사용하도록 설정해야 하므로 사용하는 암호는 Active Directory에도 다시 기록됩니다.
 - Windows Server에서 TLS 1.2 설정을 사용하도록 설정 하거나 검색하기 위해 ADSyncTools 모듈에 두 개의 새 cmdlet을 추가했습니다. 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
이러한 cmdlet을 사용하여 TLS 1.2 사용 상태를 검색하거나 필요에 따라 설정할 수 있습니다. 설치 또는 AADConnect가 성공 하려면 서버에서 TLS 1.2를 사용하도록 설정해야 합니다.

 -    몇 가지 새롭고 향상된 cmdlet으로 ADSyncTools를 개선했습니다. [ADSyncTools 문서](reference-connect-adsynctools.md)에는 이러한 cmdlet에 대한 자세한 내용이 나와 있습니다. 
  다음 cmdlet이 추가되거나 업데이트되었습니다. 
    - Clear-ADSyncToolsMsDsConsistencyGuid    
    - ConvertFrom-ADSyncToolsAadDistinguishedName    
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName    
    - ConvertTo-ADSyncToolsCloudAnchor    
    - ConvertTo-ADSyncToolsImmutableID    
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects    
    - Export-ADSyncToolsRunHistory    
    - Get-ADSyncToolsAadObject    
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects    
    - Import-ADSyncToolsRunHistory    
    - Remove-ADSyncToolsAadObject    
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport    
    - Trace-ADSyncToolsLdapQuery    
-    이제 가져오기 및 내보내기에 V2 엔드포인트를 사용하고 Get-ADSyncAADConnectorExportApiVersion cmdlet의 문제를 해결했습니다. v2 엔드포인트에 대한 자세한 내용은 [Azure AD Connect 동기화 v2 엔드포인트 문서](how-to-connect-sync-endpoint-api-v2.md)를 참조하세요.
-    온-프레미스 AD에서 Azure AD로 동기화하기 위해 다음과 같은 새 사용자 속성을 추가했습니다.    
    - employeeType    
    - employeeHireDate    
-    이 릴리스를 사용하려면 Windows Server에 PowerShell 버전 5.0 이상이 설치되어 있어야 합니다. 이 버전은 Windows Server 2016 이상 버전에 포함되어 있습니다.    
-    새 V2 엔드포인트를 사용하여 그룹 동기화 구성원 자격 제한을 250k로 늘렸습니다.
-    일반 LDAP 커넥터 및 일반 SQL 커넥터를 최신 버전으로 업데이트했습니다. 이러한 커넥터에 대한 자세한 내용은 다음에서 참조하세요.
    - [일반 LDAP 커넥터 참조 설명서](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [일반 SQL 커넥터 참조 설명서](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-    M365 관리 센터에서 이제 Azure AD에 대한 내보내기 작업이 있을 때마다 AADConnect 클라이언트 버전을 보고합니다. 이렇게 하면 M365 관리 센터는 항상 최신 AADConnect 클라이언트 버전을 보유하게 되며 오래된 버전을 사용하는 경우 감지할 수 있습니다.

### <a name="bug-fixes"></a>버그 수정
- 화면 판독기가 '자세한 정보' 링크의 잘못된 역할을 표시하는 접근성 버그를 수정했습니다.
-    우선 순위 값이 매우 높은(즉, 387163089) 동기화 규칙 때문에 업그레이드가 실패하게 되는 버그를 수정했습니다. 'mms_UpdateSyncRulePrecedence'를 업데이트하여 이 우선 순위 값을 늘리기 전에 정수로 캐스팅했습니다.
-    그룹 쓰기 저장 구성을 가져오는 경우 동기화 계정에서 그룹 쓰기 저장 권한이 설정되지 않는 버그를 수정했습니다. 이제 가져온 구성에서 그룹 쓰기 저장을 사용하도록 설정하는 경우 그룹 쓰기 저장 권한을 설정합니다.
-    설치 오류를 해결하기 위해 Azure AD Connect Health 에이전트 버전을 3.1.110.0으로 업데이트했습니다.
-    디렉터리 확장 특성이 구성된 내보낸 구성의 비기본 특성에 문제가 있습니다. 이러한 구성을 새 서버/설치로 가져올 때 특성 포함 목록이 디렉터리 확장 구성 단계에 의해 재정의되기 때문에 가져오기 후에 동기화 서비스 관리자에서 기본 및 디렉터리 확장 특성만 선택됩니다(비기본 특성은 설치에 포함되어 있지 않으므로 가져온 동기화 규칙이 적용되려면 동기화 서비스 관리자에서 수동으로 다시 사용하도록 설정해야 함). 이제 특성 포함 목록의 기존 특성을 유지하도록 디렉터리 확장을 구성하기 전에 AAD Connector를 새로 고칩니다.
-    페이지 머리글의 글꼴 두께가 "가늘게"로 설정되는 접근성 문제를 수정했습니다. 이제 글꼴 두께가 페이지 제목에 대해 "굵게"로 설정되며 모든 페이지의 머리글에 적용됩니다.
-    AD cmdlet에서 모호성을 방지하기 위해 ADSyncSingleObjectSync.ps1의 함수 Get-AdObject의 이름이 Get-AdDirectoryObject로 변경되었습니다.
-    SQL 함수 'mms_CheckSynchronizationRuleHasUniquePrecedence'는 다른 커넥터의 아웃바운드 동기화 규칙에서 중복 우선 순위를 허용합니다. 중복 규칙 우선 순위를 허용하는 조건을 제거했습니다.
-    특성 흐름 데이터가 null인 경우(삭제 작업 내보내기 시) 단일 개체 동기화 cmdlet이 실패하는 버그를 수정했습니다.    
-    ADSync 부트스트랩 서비스를 시작할 수 없기 때문에 설치가 실패하는 버그를 수정했습니다. 이제 부트스트랩 서비스를 시작하기 전에 동기화 서비스 계정을 로컬 기본 제공 사용자 그룹에 추가합니다.
-    AAD Connect 마법사의 활성 탭이 고대비 테마에서 올바른 색을 표시하지 않는 접근성 문제를 수정했습니다. 표준 색 코드 구성의 조건 누락으로 인해 선택한 색 코드가 덮어쓰여집니다.
-    사용자가 UI와 PowerShell을 사용하여 동기화 규칙에 사용되는 개체 및 특성의 선택을 취소할 수 있는 문제를 해결했습니다. 이제 모든 동기화 규칙에서 사용되는 특성 또는 개체를 선택 취소하려고 하면 오류 메시지가 표시됩니다.
-    이전 버전의 Azure AD Connect에서 스크립트를 실행할 때 이전 버전과의 호환성 문제를 확인하고 해결하도록 "설정 코드 마이그레이션"을 업데이트했습니다.    
-    PHS가 불완전한 개체를 조회하려고 할 때 원래 암호를 가져오는 데 사용된 DC를 확인하는 데 동일한 알고리즘을 사용하지 않는 버그를 수정했습니다. 특히 선호가 설정된 DC 정보를 무시합니다. 불완전한 개체 조회는 두 인스턴스에서 모두 동일한 논리를 사용하여 DC를 찾아야 합니다.
-    AAD Connect 클라이언트 ID를 기준으로 Microsoft Graph을 직접 호출하는 권한 문제로 인해 AADConnect가 Microsoft Graph를 사용하여 애플리케이션 프록시 항목을 읽을 수 없는 버그를 수정했습니다. 이 문제를 해결하기 위해 Microsoft Graph에 대한 종속성을 제거하고 앱 프록시 애플리케이션 개체 작업을 위해 AAD PowerShell을 대신 사용했습니다.
-    'Out to AD - Group SOAInAAD Exchange' 동기화 규칙에서 쓰기 저장 구성원 제한을 제거했습니다.    
-    커넥터 계정 사용 권한을 변경할 때 개체가 마지막 델타 가져오기 이후에 변경되지 않은 범위에 속할 경우 델타 가져오기를 통해 가져올 수 없는 버그를 수정했습니다. 이제 사용자에게 문제를 알리는 경고가 표시됩니다.
-    화면 판독기가 라디오 단추 위치를 읽지 못하는 접근성 문제를 수정했습니다. 라디오 단추 접근성 텍스트 필드에 위치 텍스트를 추가했습니다.
-    패스스루 인증 에이전트 번들을 업데이트했습니다. 이전 번들에 US Gov의 HIP 자사 애플리케이션에 대한 올바른 회신 URL이 없습니다.    
-    기본적으로 DirSyncWebServices API V2를 사용하고 기존 데이터베이스를 사용하는 AADConnect 버전 1.6.X.X를 새로 설치한 후에 AAD Connector 내보내기 시 'stopped-extension-dll-exception'이 나타나는 버그를 수정했습니다.    이전에는 업그레이드를 위해 내보내기 버전을 v2로 설정하기만 했지만 이제는 새로 설치도 가능하도록 변경했습니다.
-    "ADSyncPrep.psm1" 모듈은 더 이상 사용되지 않으며 설치에서 제거됩니다.

### <a name="known-issues"></a>알려진 문제
-    AADConnect 마법사는 "동기화 설정 가져오기" 옵션을 "미리 보기"로 표시하지만 이 기능은 일반 공급됩니다.
-    일부 Active Directory Connector는 설정 마이그레이션 스크립트의 출력을 사용하여 제품을 설치할 때와는 다른 순서로 설치할 수 있습니다. 
-    Azure AD Connect 마법사의 사용자 로그인 옵션 페이지에 "회사 관리자"가 표시됩니다. 이 용어는 더 이상 사용되지 않으며 "전역 관리자"로 바꾸어야 합니다.
-    로그인 옵션이 PingFederate를 사용하도록 구성된 경우 "설정 내보내기" 옵션이 중단됩니다.
-    이제 하이브리드 ID 관리자 역할을 사용하여 Azure AD Connect를 배포할 수 있지만, 셀프 서비스 암호 재설정을 구성하는 경우에도 전역 관리자 역할을 사용하는 사용자가 필요합니다.
-    원래 AADConnect 구성과는 다른 테넌트에 연결하도록 배포하는 동안 AADConnect 구성을 가져오는 경우 디렉터리 확장 특성이 올바르게 구성되지 않습니다.

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> 이제 Azure AD Connect 동기화 V2 엔드포인트 API를 다음 Azure 환경에서 사용할 수 있습니다.
> - Azure Commercial
> - Azure 중국 클라우드
> - Azure 미국 정부 클라우드
> - 이 릴리스는 Azure 독일 클라우드에서 사용할 수 없습니다.

### <a name="release-status"></a>릴리스 상태
2021년 3월 31일: 다운로드 전용으로 릴리스되며 자동 업그레이드에 사용할 수 없음

### <a name="bug-fixes"></a>버그 수정
- 이 릴리스는 버전 1.6.2.4에서 해당 릴리스로 업그레이드한 후 Azure AD Connect Health 기능이 올바르게 등록되지 않았고 작동하지 않았던 버그를 수정합니다. 빌드 1.6.2.4를 배포한 고객은 해당 빌드와 함께 Azure AD Connect 서버를 업데이트하여 주세요. 그러면 Health 기능이 올바르게 등록될 것입니다. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> 2021년 3월 30일 업데이트: 해당 빌드에서 문제가 발견되었습니다. 빌드를 설치하고 나면 Health 서비스가 등록되지 않습니다. 해당 빌드를 설치하지 않는 것이 좋습니다. 핫픽스를 곧 릴리스할 예정입니다.
> 빌드를 이미 설치한 경우 [이 문서](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync)에 표시된 대로 cmdlet을 사용하여 수동으로 상태 서비스를 등록할 수 있습니다.

>[!NOTE]
> - 이 릴리스는 다운로드 전용으로 사용 가능할 것입니다.
> - 동기화 규칙 변경으로 인해 이 릴리스로 업그레이드하려면 전체 동기화가 필요합니다.
> - 이 릴리스는 AADConnect 서버를 새 V2 엔드포인트로 기본 설정합니다.

### <a name="release-status"></a>릴리스 상태
2021년 3월 19일: 다운로드용으로 릴리스되며 자동 업그레이드에 사용할 수 없음

### <a name="functional-changes"></a>기능 변경 내용

 - 쓰기 저장된 그룹의 구성원을 5만 명으로 제한하는 기본 동기화 규칙을 업데이트했습니다.
   - Azure Active Directory(Out to AAD - 그룹 쓰기 저장 구성원 제한) 그룹에 대한 그룹 쓰기 저장(Out to AD - 그룹 쓰기 저장 구성원 제한) 및 그룹 동기화에서 구성원 수를 제한하기 위한 새 기본 동기화 규칙을 추가했습니다.
   - 'Out to AD - Group SOAInAAD - Exchange' 규칙에 구성원 특성을 추가하여 쓰기 저장된 그룹의 구성원을 5만 명으로 제한합니다.
 - 그룹 쓰기 저장 v2를 지원하기 위해 동기화 규칙을 업데이트함 - "In from AAD - Group SOAInAAD" 규칙이 복제되고 AADConnect가 업그레이드된 경우
     - 업데이트된 규칙이 기본적으로 사용하지 않도록 설정되므로 targetWritebackType은 null입니다.
     - AADConnect는 모든 클라우드 그룹(쓰기 저장을 사용하도록 설정된 Azure Active Directory 보안 그룹 포함)을 배포 목록으로 쓰기 저장합니다.
   -"Out to AD-Group SOAInAAD" 규칙이 복제되고 AADConnect가 업그레이드된 경우.
     - 업데이트된 규칙은 기본적으로 사용하지 않도록 설정됩니다. 그러나 추가된 새 동기화 규칙 "Out to AD - Group SOAInAAD - Exchange"는 사용하도록 설정됩니다.
     - 복제된 사용자 지정 동기화 규칙의 우선 순위에 따라 AADConnect는 Mail과 Exchange 특성을 전달합니다.
     - 복제된 사용자 지정 동기화 규칙에서 Mail 및 Exchange 특성을 전달하지 않으면 새 Exchange 동기화 규칙에서 해당 특성을 추가합니다.
     - 그룹 쓰기 저장 V2는 현재 프라이빗 미리 보기로 제공되며 공개적으로 사용할 수 없습니다.
 - [선택적 암호 해시 동기화](./how-to-connect-selective-password-hash-synchronization.md)를 위해 추가된 지원
 - 새 [단일 개체 동기화 cmdlet](./how-to-connect-single-object-sync.md)을 추가했습니다. cmdlet을 사용하여 Azure AD Connect 동기화 구성 문제를 해결할 수 있습니다. 
 -  Azure AD Connect는 이제 서비스를 구성하기 위한 하이브리드 ID 관리자 역할을 지원합니다.
 - 3\.1.83.0으로 업데이트된 AADConnectHealth 에이전트
 - 몇 가지 새롭고 향상된 cmdlet이 포함된 [ADSyncTools PowerShell 모듈](./reference-connect-adsynctools.md)의 새 버전입니다. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - 토큰 획득 실패에 관한 오류 로깅이 업데이트되었습니다.
 - 연결된 정보에 관한 자세한 정보를 제공하기 위해 구성 페이지에서 ' 자세한 정보' 링크를 업데이트했습니다.
 - 이전 동기화 UI의 CS 검색 페이지에서 명시적 열을 제거하였습니다.
 - 이전 단계에서 자격 증명이 아직 제공되지 않은 경우 사용자에게 자격 증명을 묻는 프롬프트를 표시하거나 ADSyncConfig 모듈을 사용하여 고유한 권한을 구성하기 위해 그룹 쓰기 저장 흐름에 추가 UI가 추가되었습니다.
 - DC에서 ADSync 서비스 계정을 위한 MSA를 자동 생성합니다. 
 - 기존 cmdlet에서 Azure Active Directory DirSync 기능 그룹 쓰기 저장 V2를 설정하고 가져오기 위해 추가된 기능:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - AWS API 버전을 읽기 위해 추가된 2개의 cmdlet
    - Get-ADSyncAADConnectorImportAPIVersion - AWS API 버전 가져오기
    - Get-ADSyncAADConnectorExportAPIVersion - AWS API 버전을 내보내기

 - 이제 동기화 규칙에 관한 변경 내용을 추적하여 서비스의 변경 내용 문제를 해결할 수 있습니다. "Get-ADSyncRuleAudit" cmdlet은 추적된 변경 내용을 검색합니다.
 - ADSyncAdmin 그룹의 사용자가 AD DS 커넥터 계정을 변경할 수 있도록 [ADSyncConfig PowerShell 모듈](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module)의 Add-ADSyncADDSConnectorAccount cmdlet을 업데이트했습니다. 

### <a name="bug-fixes"></a>버그 수정
 - 흰색 배경에 관한 명도 요구 사항을 충족하기 위해 비활성화된 전경색을 업데이트했습니다. 명도 요구 사항을 충족하기 위해 비활성화된 페이지를 선택할 때 전경 텍스트 색을 흰색으로 설정하기 위한 탐색 트리의 조건이 추가되었습니다.
 - Set-ADSyncPasswordHashSyncPermissions cmdlet의 세분성 확대 - 선택적 "ADobjectDN" 매개 변수를 포함하도록 PHS 권한 스크립트(Set-ADSyncPasswordHashSyncPermissions)를 업데이트했습니다. 
 - 접근성 버그 수정. 이제 화면 읽기 프로그램은 포리스트 목록을 "**포리스트 목록**" 대신 "**포리스트 목록**"으로 포함하는 UX 요소를 설명합니다.
 - Azure AD Connect 마법사의 일부 항목 관련하여 업데이트된 화면 읽기 프로그램 출력입니다. 대비 요구 사항을 충족하도록 업데이트된 단추 가리키기 색입니다. 대비 요구 사항을 충족하도록 업데이트된 Synchronization Service Manager 제목 색입니다.
 - 사용자 지정 확장 특성이 있는 내보낸 구성에서 AADConnect를 설치하는 문제를 해결했습니다. 동기화 규칙을 적용하는 동안 대상 스키마의 확장 특성을 위한 확인을 건너뛰는 조건을 추가했습니다.
 - 그룹 쓰기 저장 기능을 사용하도록 설정한 경우 설치 시 적절한 권한이 추가됩니다.
 - 가져올 때 중복된 기본 동기화 규칙 우선 순위 수정
 - 상태 포털을 통해 복구된 충돌하는 개체에 V2 API 델타 가져오기 작업을 수행하는 동안 준비 오류가 발생된 문제를 해결했습니다.
 - 동기화 엔진에서 CS 개체가 일관되지 않은 링크 상태를 갖도록 하는 문제를 해결했습니다.
 - Get-ADSyncConnectorStatistics 출력에 가져오기 카운터를 추가했습니다.
 - Pass2 마법사를 진행하는 동안 일부 비정상 상황에서 연결할 수 없는 도메인 선택이 취소되는(이전에 선택) 문제를 해결했습니다.
 - 사용자 지정 규칙에 중복된 우선 순위가 있는 경우 정책 가져오기 및 내보내기가 실패하도록 수정 
 - 도메인 선택 논리에서 버그를 수정했습니다.
 - mS-DS-ConsistencyGuid를 원본 앵커로 사용하고 In from AD-Group Join 규칙을 복제한 경우 빌드 1.5.18.0을 포함하는 문제를 해결했습니다.
 - 새 AADConnect 설치는 클라우드에 저장된 내보내기 삭제 임계값을 하나 사용할 수 있고, 다른 것이 전달되지 않은 경우 이를 사용합니다.
 - AADConnect가 하이브리드 조인 디바이스의 AD displayName 변경 내용을 읽지 않는 문제를 해결했습니다.

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>릴리스 상태
2020년 7월 29일: 다운로드용으로 릴리스되었습니다.

### <a name="functional-changes"></a>기능 변경 내용
버그 수정 릴리스입니다. 이 릴리스에는 기능 변경 내용이 없습니다.

### <a name="fixed-issues"></a>해결된 문제

- AZUREADSSOACC 컴퓨터 계정이 이미 "Active Directory"에 있는 경우 관리자가 "Seamless Single Sign-on"을 사용하도록 설정할 수 없는 문제를 해결했습니다.
- 상태 포털을 통해 복구된 충돌하는 개체에 V2 API 델타 가져오기 작업을 수행하는 동안 준비 오류가 발생된 문제를 해결했습니다.
- 사용하지 않도록 설정된 사용자 지정 규칙을 사용하도록 설정한 상태로 가져온 경우 가져오기/내보내기 구성의 문제를 해결했습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
