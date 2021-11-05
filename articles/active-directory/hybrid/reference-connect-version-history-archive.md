---
title: 'Azure AD Connect: 버전 릴리스 기록 보관 | Microsoft Docs'
description: 이 문서에는 Azure AD Connect 및 Azure AD Sync의 보관된 모든 릴리스가 나열되어 있습니다.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: ea6235f113567c2bb7cd2801df10e5e7e304c57f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067007"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: 버전 릴리스 기록 보관

Azure AD(Azure Active Directory) 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

>[!NOTE] 
> 이 문서에는 보관된 모든 Azure AD 버전(1.5.42.0 이하)에 대한 버전 참조 정보가 포함되어 있습니다.  현재 릴리스는 [Azure AD Connect 버전 릴리스 기록](reference-connect-version-history.md)을 참조하세요.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>릴리스 상태
2020년 7월 10일: 다운로드용으로 릴리스되었습니다.

### <a name="functional-changes"></a>기능 변경 내용
이 릴리스에는 기존 Azure AD Connect 서버의 구성을 새 Azure AD Connect 서버를 설치하여 원본 서버의 복사본을 만들 때 사용할 수 있는 JSON 파일로 내보내는 기능의 퍼블릭 미리 보기가 포함되어 있습니다.

이 새로운 기능에 관한 자세한 설명은 [이 문서](./how-to-connect-import-export-config.md)에서 확인할 수 있습니다.

### <a name="fixed-issues"></a>해결된 문제
- 업그레이드하는 동안 현지화된 빌드의 로컬 DB 크기에 관한 잘못된 경고가 발생하는 버그를 수정했습니다.
- 계정 이름/도메인 이름 바꾸기를 위한 앱 이벤트에 잘못된 오류가 발생하는 버그를 수정했습니다.
- DC에서 Azure AD Connect 설치에 실패하고 "member not found"(구성원을 찾을 수 없습니다.) 오류가 발생하는 오류를 수정했습니다.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>릴리스 상태
2020년 5월 7일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 손자 컨테이너만 선택한 경우 마법사 UI에서 선택하지 않은 도메인이 잘못 선택되는 문제를 해결합니다.


>[!NOTE]
>이 버전에는 새 Azure AD Connect 동기화 V2 엔드포인트 API가 포함 됩니다.  새로운 V2 엔드포인트는 현재 공개 미리 보기 상태입니다.  새로운 V2 엔드포인트 API를 사용하려면 이 버전 이상이 필요합니다.  하지만 이 버전을 설치하는 것만으로는 V2 엔드포인트를 사용할 수 없습니다. V2 엔드포인트를 사용하도록 설정하지 않으면 V1 엔드포인트가 계속 사용됩니다.  사용하도록 설정하고 공개 미리 보기를 옵트인하려면 [Azure AD Connect 동기화 V2 엔드포인트 API(공개 미리 보기)](how-to-connect-sync-endpoint-api-v2.md)의 단계를 수행해야 합니다.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 23일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 빌드 1.5.20.0에서 발생한 MFA를 사용하는 테넌트 관리자가 DSSO를 사용하도록 설정할 수 없는 문제를 해결합니다.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 20일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 1.5.20.0 빌드에서 **AD에서 들어오기 – 그룹 조인** 규칙을 복제하고 **AD에서 들어오기 – 그룹 공통** 규칙을 복제하지 않는 경우 발생하는 문제를 해결합니다.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 9일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
- 이 핫픽스 빌드는 1.5.18.0 빌드에서 그룹 필터링 기능을 사용하도록 설정하고 mS-DS-ConsistencyGuid를 소스 앵커로 사용하는 경우 발생하는 문제를 해결합니다.
- 모든 Set-ADSync* Permissions cmdlet에 사용된 DSACLS 명령을 호출하면 다음 오류 중 하나가 발생하는 ADSyncConfig PowerShell 모듈의 문제가 해결되었습니다.
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> **AD에서 들어오기 – 그룹 조인** 동기화 규칙을 복제하고 **AD에서 들어오기 – 그룹 공통** 동기화 규칙을 복제하지 않는 상태에서 업그레이드하려는 경우에는 업그레이드의 일환으로 다음 단계를 완료하십시오.
> 1. 업그레이드 중에 **구성이 완료되면 동기화 프로세스를 시작합니다.** 옵션을 선택 취소합니다.
> 2. 복제된 조인 동기화 규칙을 편집하고 다음 두 가지 변환을 추가합니다.
>     - 직접 흐름 `objectGUID`를 `sourceAnchorBinary`로 설정합니다.
>     - 식 흐름 `ConvertToBase64([objectGUID])`를 `sourceAnchor`로 설정합니다.     
> 3. `Set-ADSyncScheduler -SyncCycleEnabled $true`를 사용하여 스케줄러를 사용하도록 설정합니다.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 2일: 다운로드용으로 릴리스됨

### <a name="functional-changes-adsyncautoupgrade"></a>기능 변경 ADSyncAutoUpgrade 

- 그룹 개체의 mS-DS-ConsistencyGuid 기능에 대한 지원이 추가되었습니다. 이를 통해 포리스트 간에 그룹을 이동하거나 AD의 그룹을 AD 그룹 objectID가 변경된(예: 재난 후에 AD 서버를 재 구축한 경우) Azure AD에 다시 연결할 수 있습니다. 자세한 내용은 [포리스트 간 그룹 이동](how-to-connect-migrate-groups.md)을 참조하세요.
- mS-DS-ConsistencyGuid 특성이 동기화된 모든 그룹에 자동으로 설정되어, 이 기능을 사용하도록 설정하기 위해 아무것도 수행할 필요가 없습니다. 
- Get-ADSyncRunProfile이 더 이상 사용되지 않기 때문에 제거되었습니다. 
- AD DS 커넥터 계정에 대해 엔터프라이즈 관리자 또는 도메인 관리자 계정을 사용하여 더 많은 컨텍스트를 제공하려고 할 때 보이는 경고가 변경되었습니다. 
- 기존 CSDelete.exe 도구가 제거된 커넥터 공간에서 개체를 제거하는 새 cmdlet이 추가되고 새 Remove-ADSyncCSObject cmdlet으로 대체되었습니다. Remove-ADSyncCSObject cmdlet은 CsObject를 입력으로 사용합니다. 이 개체는 Get-ADSyncCSObject cmdlet을 사용하여 검색할 수 있습니다.

>[!NOTE]
>이전 CSDelete.exe 도구가 제거되고 새로운 Remove-ADSyncCSObject cmdlet으로 대체되었습니다. 

### <a name="fixed-issues"></a>해결된 문제

- 기능을 사용하지 않도록 설정한 후 Azure AD Connect 마법사를 다시 실행하는 경우 그룹 쓰기 저장(writeback) 포리스트/OU 선택기에 있는 버그가 수정되었습니다. 
- 필요한 DCOM 레지스트리 값에 새 도움말 링크가 누락된 경우 표시되는 새 오류 페이지가 도입되었습니다. 정보는 로그 파일에도 기록됩니다. 
- 계정을 사용하려고 하기 전에 계정이 모든 서비스 복제본에 전파되지 않았기 때문에 디렉터리 확장 또는 PHS를 사용하도록 설정하면 실패할 수 있는, Azure Active Directory 동기화 계정 생성과 관련된 문제가 해결되었습니다. 
- 서로게이트 문자를 올바르게 처리하지 못하는 동기화 오류 압축 유틸리티의 버그가 수정되었습니다. 
- 서버를 스케줄러 일시 중단 상태로 두는 자동 업그레이드의 버그가 수정되었습니다. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>릴리스 상태
2019년 12월 9일: 다운로드용 릴리스입니다. 자동 업그레이드를 통해 사용할 수 없습니다.
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- Kerberos 해시의 패딩을 올바르게 처리하도록 Azure AD Domain Services에 대한 암호 해시 동기화가 업데이트되었습니다.  따라서 Azure AD에서 Azure AD Domain Services로 암호를 동기화하는 동안 성능이 향상됩니다.
- 인증 에이전트와 서비스 버스 간의 신뢰할 수 있는 세션을 위한 지원이 추가되었습니다.
- 인증 에이전트와 클라우드 서비스 간의 WebSocket 연결을 위한 DNS 캐시가 추가되었습니다.
- 에이전트 연결을 테스트하기 위해 클라우드에서 특정 에이전트를 대상으로 하는 기능이 추가되었습니다.

### <a name="fixed-issues"></a>해결된 문제
- 릴리스 1.4.18.0에는 DSSO용 PowerShell cmdlet이 ps를 실행하는 동안 제공된 관리자 자격 증명 대신 로그인 Windows 자격 증명을 사용하는 버그가 있었습니다. 이로 인해 Azure AD Connect 사용자 인터페이스를 통해 다중 포리스트에서 DSSO를 사용하도록 설정할 수 없었습니다. 
- Azure AD Connect 사용자 인터페이스를 통해 모든 포리스트에서 동시에 DSSO를 사용할 수 있도록 수정했습니다.

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>릴리스 상태
2019년 11월 8일: 다운로드용으로 릴리스되었습니다. 자동 업그레이드를 통해 사용할 수 없습니다.

>[!IMPORTANT]
>이번 Azure AD Connect 릴리스의 내부 스키마가 변경되어 MSOnline PowerShell을 사용하여 AD FS 트러스트 관계 구성 설정을 관리하는 경우에는 MSOnline PowerShell 모듈을 1.1.183.57 이상 버전으로 업데이트해야 합니다.

### <a name="fixed-issues"></a>해결된 문제

이 버전은 기존 하이브리드 Azure AD 조인 디바이스 관련 문제를 해결합니다. 이 릴리스에는 이 문제를 해결하는 새로운 디바이스 동기화 규칙이 포함되어 있습니다.
이렇게 규칙이 변경되어 Azure AD에서 사용되지 않는 디바이스가 삭제될 수 있습니다. 조건부 액세스 권한 부여 중에는 Azure AD에서 해당 디바이스 개체가 사용되지 않기 때문에 걱정할 필요가 없습니다. 일부 고객의 경우 규칙 변경을 통해 삭제되는 디바이스 수가 삭제 임계값을 초과할 수 있습니다. Azure AD에서 디바이스 개체를 삭제하는 것이 내보내기 삭제 임계값을 초과하는 것으로 보이면, 삭제가 진행되도록 허용하는 것이 좋습니다. [삭제 임계값을 초과하는 경우 삭제가 진행되도록 하는 방법](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>릴리스 상태
2019년 9월 28일: 테넌트를 선택하는 자동 업그레이드용으로 릴리스되었습니다. 다운로드할 수 없습니다.

이 버전은 이전 버전에서 1.4.18.0으로 자동 업그레이드된 일부 서버에서 SSPR(셀프 서비스 암호 재설정) 및 암호 쓰기 저장 관련 문제가 발생하는 버그를 수정합니다.

### <a name="fixed-issues"></a>해결된 문제

특정 상황에서 버전 1.4.18.0으로 자동 업그레이드된 서버가 업그레이드가 완료된 후 셀프 서비스 암호 재설정 및 암호 쓰기 저장이 다시 활성화되지 않았습니다. 이번 자동 업그레이드 릴리스는 이 문제를 해결하고 셀프 서비스 암호 재설정 및 암호 쓰기 저장을 다시 활성화합니다.

서로게이트 문자를 올바르게 처리하지 못하는 동기화 오류 압축 유틸리티의 버그가 수정되었습니다.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>이 버전의 Azure AD Connect로 업그레이드한 후 일부 고객에게 기존 하이브리드 Azure AD 조인 디바이스 관련 문제가 발생하는 인시던트를 조사하고 있습니다. 하이브리드 Azure AD 조인을 배포한 고객은 문제의 근본 원인이 완전히 파악되어 완화될 때까지 이 버전으로 업그레이드하는 것을 연기하는 것이 좋습니다. 가능한 빨리 자세한 정보를 제공하겠습니다.

>[!IMPORTANT]
>이 버전의 Azure AD Connect를 사용하면 일부 고객이 Windows 디바이스의 일부 또는 전부가 Azure AD에서 사라지는 것을 볼 수 있습니다. 조건부 액세스 권한 부여 중에는 Azure AD에서 해당 디바이스 ID가 사용되지 않기 때문에 걱정할 필요가 없습니다. 자세한 내용은 [Azure AD Connect 1.4.xx.x 디바이스 누락 이해](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)를 참조하세요.


### <a name="release-status"></a>릴리스 상태
2019년 9월 25일: 자동 업그레이드 전용으로 릴리스되었습니다.

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- 새로운 문제 해결 도구는 "사용자 동기화 안 됨", "그룹 동기화 안 됨" 또는 "그룹 구성원 동기화 안 됨" 시나리오에서 문제를 해결하는 데 유용합니다.
- Azure AD Connect 문제 해결 스크립트에서 국가별 클라우드에 관한 지원을 추가했습니다.
- MIIS_Service에 더 이상 사용되지 않는 WMI 엔드포인트가 이제 제거된 것을 고객에게 알려야 합니다. 모든 WMI 작업은 이제 PS cmdlet을 통해 수행됩니다.
- AZUREADSSOACC 개체에 제한된 위임을 다시 설정하여 보안을 강화했습니다.
- 동기화 규칙을 추가/편집하는 경우 커넥터 스키마에 있지만 커넥터에 추가되지 않은 규칙에 사용된 특성이 있으면 이 특성은 커넥터에 자동으로 추가됩니다. 규칙이 적용되는 개체 형식에 대해서도 마찬가지입니다. 커넥터에 추가된 항목이 있으면 다음 동기화 주기에 커넥터가 전체 가져오기로 표시됩니다.
- 새 Azure AD Connect 배포에서는 엔터프라이즈 또는 도메인 관리자를 커넥터 계정으로 사용하는 것이 더 이상 지원되지 않습니다. 엔터프라이즈 또는 도메인 관리자를 커넥터 계정으로 사용하는 현재 Azure AD Connect 배포는 이 릴리스의 영향을 받지 않습니다.
- 동기화 관리자에서 규칙 생성/편집/삭제 시 전체 동기화가 실행됩니다. 규칙 변경 시 팝업이 나타나서 전체 가져오기 또는 전체 동기화가 실행되는 경우 사용자에게 알립니다.
- '커넥터 > 속성 > 연결성' 페이지에 암호 오류를 완화하는 단계가 추가되었습니다.
- 커넥터 속성 페이지에 동기화 서비스 관리자에 대한 사용 중단 경고가 추가되었습니다. 이 경고는 Azure AD Connect 마법사를 통해 변경해야 한다고 사용자에게 알려줍니다.
- 사용자의 암호 정책과 관련된 문제에 대해 새 오류가 추가되었습니다.
- 도메인 및 OU 필터로 그룹 필터링이 잘못 구성되지 않도록 합니다. 입력한 그룹의 도메인/OU가 이미 필터링된 경우에는 그룹 필터링에 오류가 표시되고 문제가 해결될 때까지 사용자가 계속 진행하지 못하도록 합니다.
- Synchronization Service Manager UI에서 사용자가 Active Directory Domain Services 또는 Windows Azure Active Directory용 커넥터를 더 이상 만들 수 없습니다.
- Synchronization Service Manager에서 사용자 지정 UI 컨트롤에 대한 접근성을 수정했습니다.
- Azure AD Connect의 모든 로그인 메서드에 대해 페더레이션 관리 작업을 6개 사용할 수 있습니다.  (이전에는, "AD FS TLS/SSL 인증서 업데이트" 작업만 모든 로그인에 사용할 수 있었습니다.)
- 로그인 방법을 페더레이션에서 PHS 또는 PTA로 변경하면 모든 Azure AD 도메인 및 사용자가 관리 인증으로 변환된다는 경고가 추가되었습니다.
- "Azure AD 및 AD FS 트러스트 재설정" 작업에서 토큰 서명 인증서가 제거되고 해당 인증서를 업데이트하는 별도의 하위 작업이 추가되었습니다.
- AD FS 팜의 TLS 또는 토큰 서명 인증서를 업데이트 하는 하위 작업이 포함된 "인증서 관리"라는 페더레이션 관리 작업이 새로 추가되었습니다.
- "주 서버 지정"이라는 페더레이션 관리 하위 작업이 새로 추가되어, 관리자가 AD FS 팜에 대한 주 서버를 새로 지정할 수 있습니다.
- "서버 관리"라는 페더레이션 관리 작업이 새로 추가되었습니다. 여기에는 AD FS 서버 배포, 웹 애플리케이션 프록시 서버 배포, 주 서버 지정을 위한 하위 작업이 포함되어 있습니다.
- 현재 AD FS 설정을 표시하는 "페더레이션 구성 보기"라는 페더레이션 관리 작업이 새로 추가되었습니다.  (이 작업이 추가되면서, AD FS 설정이 "솔루션 검토" 페이지에서 제거되었습니다.)

### <a name="fixed-issues"></a>해결된 문제
- 해당 연락처 개체를 사용하는 사용자 개체에 자체 참조가 있는(예: 사용자가 자신의 관리자임) 시나리오의 동기화 오류 문제가 해결되었습니다.
- 이제 도움말 팝업이 키보드 포커스에 표시됩니다.
- 자동 업그레이드의 경우, 충돌하는 앱이 6시간 이상 실행되면 종료하고 업그레이드를 계속합니다.
- 디렉터리 확장을 선택할 때 고객이 선택할 수 있는 특성의 수가 개체당 100개로 제한됩니다. 이렇게 하면 내보내기 중에 오류가 발생하지 않습니다. Azure에서 개체당 최대 확장 특성이 최대 100개이기 때문입니다.
- 버그를 수정하여 AD 연결 스크립트의 안정성을 높였습니다.
- 버그를 수정하여 기존의 명명된 파이프 WCF 서비스를 통해 머신에 Azure AD Connect를 더욱 안정적으로 설치할 수 있습니다.
- 처음 설치할 때 ADSync 서비스를 시작할 수 없는 그룹 정책 관련 진단 및 문제 해결 기능이 향상되었습니다.
- Windows 컴퓨터의 표시 이름이 잘못 쓰여지는 버그를 수정했습니다.
- Windows 컴퓨터의 OS 유형이 잘못 쓰여지는 버그를 수정했습니다.
- Windows 10 이외의 컴퓨터가 예기치 않게 동기화되는 버그를 수정했습니다. 그에 따라 이전에 동기화되던 Windows 10 이외의 컴퓨터가 이제는 삭제됩니다. 이러한 변화는 어떤 기능에도 영향을 주지 않습니다. Windows 컴퓨터의 동기화는 하이브리드 Azure AD 도메인 가입에만 사용되고, 이 기능은 Windows-10 디바이스에 대해서만 작동하기 때문입니다.
- ADSync PowerShell 모듈에 몇 가지 새로운(내부) cmdlet이 추가되었습니다.

## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Azure AD Connect를 이전 버전에서 1.3.21.0으로 업그레이드하는 경우 알려진 문제가 있습니다. Azure AD Connect가 성공적으로 업그레이드되어도 Microsoft 365 포털에 업데이트된 버전이 반영되지 않는 문제입니다.
>
> 이 문제를 해결하려면 **AdSync** 모듈을 가져온 다음, Azure AD Connect 서버에서 `Set-ADSyncDirSyncConfiguration` PowerShell cmdlet을 실행해야 합니다.  다음 단계를 사용할 수 있습니다.
>
>1.    관리자 모드에서 PowerShell을 시작합니다.
>2.    `Import-Module "ADSync"`을 실행합니다.
>3.    `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`을 실행합니다.
 
### <a name="release-status"></a>릴리스 상태 

2019년 5월 14일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제 

- Microsoft Azure Active Directory Connect 빌드 1.3.20.0에 존재하는 권한 상승 취약성을 수정했습니다.  이 취약성은, 특정 조건에서, 공격자가 권한 있는 계정의 컨텍스트에서 PowerShell cmdlet 두 가지를 실행하여 권한 있는 작업을 수행할 수 있도록 합니다.  이 보안 업데이트는 해당 cmdlet을 사용할 수 없도록 설정하여 문제를 해결합니다. 자세한 내용은 [보안 업데이트](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)를 참조하세요.


## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>릴리스 상태 

2019년 4월 24일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 

- 도메인 새로 고침에 대한 지원 추가 
- Exchange 메일 공용 폴더 기능이 GA 상태임 
- 서비스 오류에 대한 마법사 오류 처리 기능 향상 
- 커넥터 속성 페이지의 Synchronization Service Manager UI에 경고 링크가 추가됨 
- 통합 그룹 쓰기 저장 기능이 이제 GA 상태임 
- DC에 LDAP 컨트롤이 없는 경우 SSPR 오류 메시지가 개선됨 
- 설치 중 DCOM 레지스트리 오류에 대한 진단이 추가됨  
- PHS RPC 오류에 대한 추적 기능 향상 
- 자식 도메인에서 EA 자격 증명 허용 
- 설치 중 데이터베이스 이름을 입력하도록 허용(기본 이름 ADSync)
- ADAL 3.19.8로 업그레이드하여 Ping에 대한 WS-Trust 수정을 선택하고 새 Azure 인스턴스에 대한 지원 추가 
- samAccountName, DomainNetbios 및 DomainFQDN이 클라우드로 흐르도록 그룹 동기화 규칙 수정 - 클레임에 필요 
- 기본 동기화 규칙 처리가 수정됨 – [여기](how-to-connect-fix-default-rules.md)에서 자세히 알아보기
- Windows 서비스로 실행되는 새 에이전트가 추가되었습니다. 이 에이전트의 이름은 “관리 에이전트”이며, Azure AD Connect 서버를 심층적으로 원격 진단하는 데 사용할 수 있기 때문에 고객이 지원 사례를 열 경우 Microsoft 엔지니어가 문제를 해결하는 데 유용합니다. 이 에이전트는 기본적으로 설치 및 사용하도록 설정되지 않습니다.  에이전트 설치 및 사용 방법에 대한 자세한 내용은 [Azure AD Connect 관리 에이전트란?](whatis-aadc-admin-agent.md)을 참조하세요. 
- EULA(최종 사용자 사용권 계약) 업데이트 
- AD FS를 로그인 유형으로 사용하는 배포에 대한 자동 업그레이드 지원이 추가되었습니다.  이로 인해 업그레이드 프로세스의 일부로 AD FS Azure AD 신뢰 당사자 트러스트를 업데이트해야 하는 요구 사항이 제거됩니다. 
- 트러스트 분석/업데이트 및 트러스트 재설정이라는 두 가지 옵션을 제공하는 Azure AD 트러스트 관리 작업이 추가되었습니다. 
- AD FS Azure AD 신뢰 당사자 트러스트 동작이 항상 -SupportMultipleDomain 스위치(트러스트 및 Azure AD 도메인 업데이트 포함)를 사용하도록 변경되었습니다. 
- 사전 설치된 인증서를 사용하는 옵션을 제거하여 .pfx 인증서가 필요하도록 새 AD FS 팜 설치 동작이 변경되었습니다.
- 새 AD FS 팜 워크플로 설치가 AD FS 1개와 WAP 서버 1개만 배포할 수 있도록 업데이트되었습니다.  모든 추가 서버는 초기 설치 후에 수행됩니다. 

### <a name="fixed-issues"></a>해결된 문제 

- ADSync 서비스에 대한 SQL 다시 연결 논리 수정 
- 빈 SQL AOA DB를 사용한 새로 설치가 가능하도록 수정 
- GWB 권한을 구체화하도록 PS 권한 스크립트 수정 
- LocalDB를 사용하여 VSS 오류 수정  
- 개체 유형이 범위 내에 있지 않은 경우 잘못된 오류 메시지 수정 
- 서버에 Azure AD PowerShell을 설치하면 Azure AD Connect와 어셈블리 충돌이 발생할 수 있는 문제가 수정되었습니다. 
- Synchronization Service Manager UI에서 커넥터 자격 증명이 업데이트되는 경우 스테이징 서버의 PHS 버그가 수정되었습니다. 
- 일부 메모리 누수 수정 
- 기타 자동 업그레이드 수정 
- 내보내기 및 확인되지 않은 가져오기 처리를 위한 기타 수정 사항 
- 도메인 및 OU 필터링에서 백슬래시 처리와 관련된 버그 수정 
- ADSync 서비스를 중지하는 데 2분이 넘게 걸리고 업그레이드 시 문제를 유발하는 문제를 수정했습니다. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>릴리스 상태

12/18/2018: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

이 빌드는 Azure AD Connect와 함께 제공되는 비표준 커넥터(예: 일반 LDAP 커넥터, 일반 SQL 커넥터)를 업데이트합니다. 적용 가능한 커넥터에 대한 자세한 정보는 [커넥터 버전 릴리스 기록](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)에서 1.1.911.0 버전을 참조하세요.


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>릴리스 상태
2018년 12월 11일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드를 사용하면 디바이스 쓰기 저장을 사용하도록 설정하는 경우 사용자는 RegisteredDevices 컨테이너의 대상 도메인을 지정된 포리스트 내에서 선택할 수 있습니다.  새 디바이스 옵션 기능(1.1.819.0 – 1.2.68.0)을 포함하는 이전 버전에서 RegisteredDevices 컨테이너 위치는 포리스트 루트로 제한되었으며 자식 도메인을 허용하지 않았습니다.  이 제한은 새 배포에서 자체적으로 매니페스트되었으며, 현재 위치 업그레이드에 영향을 주지 않았습니다.  

업데이트된 디바이스 옵션 기능을 포함하는 모든 빌드를 새 서버에 배포하고 디바이스 쓰기 저장을 사용하도록 설정한 경우 컨테이너 위치가 포리스트 루트에 있기를 원하지 않는 경우 컨테이너의 위치를 수동으로 지정해야 합니다.  이렇게 하려면 디바이스 쓰기 저장을 사용하지 않도록 설정하고 다시 사용하도록 설정하여 “쓰기 저장 포리스트” 페이지에서 컨테이너 위치를 지정할 수 있습니다.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>릴리스 상태 

2018년 11월 30일:  다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

이 핫픽스 빌드는 동기화 서버에 MSOnline PowerShell 갤러리 모듈이 독립적으로 존재하기 때문에 인증 오류가 발생할 수 있는 경우의 충돌을 해결합니다.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>릴리스 상태 

2018년 11월 19일:  다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

이 핫픽스 빌드는 Windows Server 2008/R2에서 ADDS 도메인 컨트롤러를 사용할 때 암호 쓰기 저장이 실패하는 이전 빌드의 회귀를 수정합니다.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>릴리스 상태 

2018/10/25: 다운로드용으로 릴리스되었습니다.


### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 


- 호스트된 음성 메일이 예상대로 작동하도록 특성 쓰기 저장 기능이 변경되었습니다.  특정 시나리오에서 Azure AD는 null 값을 사용하여 쓰기 저장하는 동안 msExchUcVoicemailSettings 특성을 덮어썼습니다.  이제 클라우드 값이 설정되지 않으면 Azure AD는 이 특성의 온-프레미스 값을 더 이상 지우지 않습니다.
- Azure AD 연결 문제를 조사하고 식별하는 진단 기능이 Azure AD Connect 마법사에 추가되었습니다. 이러한 진단 기능은 Test- AdSyncAzureServiceConnectivity Cmdlet을 사용하여 PowerShell을 통해 직접 실행할 수도 있습니다. 
- AD 연결 문제를 조사하고 식별하는 진단 기능이 Azure AD Connect 마법사에 추가되었습니다. 이러한 진단 기능은 ADConnectivityTools PowerShell 모듈의 Start-ConnectivityValidation 함수를 사용하여 PowerShell을 통해 직접 실행할 수도 있습니다.  자세한 내용은 [ADConnectivityTool PowerShell 모듈이란?](how-to-connect-adconnectivitytools.md)을 참조하세요.
- 하이브리드 Azure Active Directory 조인 및 디바이스 쓰기 저장에 대한 AD 스키마 버전 사전 검사가 추가되었습니다. 
- 디렉터리 확장 페이지 특성 검색이 대/소문자를 구분하지 않도록 변경되었습니다.
-   TLS 1.2에 대한 전체 지원이 추가되었습니다. 이 릴리스는 사용되지 않는 다른 모든 프로토콜을 지원하며, Azure AD Connect가 설치된 머신에서 오직 TLS 1.2만 사용됩니다.  자세한 내용은 [Azure AD Connect에 대한 TLS 1.2 적용](reference-connect-tls-enforcement.md)을 참조하세요.



### <a name="fixed-issues"></a>해결된 문제   

- SQL Always On이 사용되는 경우 Azure AD Connect 업그레이드가 실패하는 버그가 수정되었습니다. 
- 슬래시가 포함된 OU 이름을 올바르게 구문 분석하도록 버그가 수정되었습니다. 
- 준비 모드에서 새로 설치할 때 통과 인증이 사용되지 않는 문제가 수정되었습니다. 
- 문제 해결 도구를 실행할 때 PowerShell 모듈이 로드되지 않는 버그가 수정되었습니다. 
- 고객이 호스트 이름의 첫 번째 문자에 숫자 값을 사용할 수 없는 버그가 수정되었습니다. 
- Azure AD Connect가 잘못된 파티션 및 컨테이너 선택을 허용하는 버그가 수정되었습니다. 
- 데스크톱 SSO를 사용할 때 "잘못된 암호" 오류 메시지가 표시되는 오류가 수정되었습니다. 
- AD FS 신뢰 관리에 대한 다양한 버그 수정  
- 디바이스 쓰기 저장을 구성할 때 - msDs-DeviceContainer 개체 클래스를 검색하도록 스키마 검사 수정(WS2012 R2에 도입)


## <a name="118820"></a>1.1.882.0  

2018년 9월 7일: 다운로드용 릴리스이며, 자동 업그레이드를 위한 릴리스가 아님 

### <a name="fixed-issues"></a>해결된 문제  

SQL Always On 가용성이 ADSync DB에 대해 구성된 경우 Azure AD Connect 업그레이드가 실패합니다. 이 핫픽스를 사용하면 이 문제가 해결되고 업그레이드에 성공할 수 있습니다. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>릴리스 상태

8/21/2018: 자동 업그레이드 및 다운로드용으로 릴리스되었습니다. 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- Azure AD Connect의 Ping Federate 통합은 현재 일반 공급됩니다. [Ping Federate를 사용하여 Azure AD를 페더레이션하는 방법에 대해 자세히 알아보기](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect는 이제 업데이트가 수행될 때마다 AD FS에서 Azure AD 트러스트의 백업을 만들고, 필요한 경우 쉽게 복원할 수 있도록 별도 파일에 저장합니다. [Azure AD Connect의 새로운 기능 및 Azure AD 트러스트 관리에 대해 자세히 알아보세요](./how-to-connect-azure-ad-trust.md).
- 새 문제 해결 도구는 기본 전자 메일 주소 변경 및 전체 주소 목록에서 계정 숨기기와 관련된 문제를 해결하는 데 도움이 됩니다.
- Azure AD Connect는 최신 SQL Server 2012 Native Client를 포함하도록 업데이트되었습니다.
- "사용자 로그인 변경" 작업에서 사용자 로그인을 암호 해시 동기화 또는 통과 인증 태스크로 전환하면 기본적으로 Seamless Single Sign-On 확인란이 사용되도록 설정됩니다.
- Windows Server Essentials 2019에 대한 지원이 추가되었습니다.
- Azure AD Connect Health 에이전트가 최신 버전인 3.1.7.0으로 업데이트되었습니다.
- 업그레이드하는 동안, 설치 관리자가 기본 동기화 규칙에 대한 변경을 감지하면 수정된 규칙을 덮어쓰기 전에 관리자에게 경고가 표시됩니다. 따라서 사용자는 수정 작업을 수행하고 나중에 다시 시작할 수 있습니다. 이전 동작: 기본 제공 규칙을 수정한 다음, 수동으로 업그레이드한 경우 사용자에게 경고하지 않고 해당 규칙을 덮어쓰고, 사용자에게 알리지 않고 동기화 스케줄러가 사용되지 않도록 설정되었습니다. 새 동작: 사용자가 수정된 기본 제공 동기화 규칙을 덮어쓰기 전에 경고가 표시됩니다. 사용자는 업그레이드 프로세스를 중지한 후 나중에 수정 작업 후에 다시 시작하도록 선택할 수 있습니다.
- FIPS 준수 문제가 보다 잘 처리되며, FIPS 준수 환경에서 MD5 해시 생성을 위한 오류 메시지와 이 문제에 대한 해결 방법을 제공하는 설명서에 대한 링크가 제공됩니다.
- 이제 페더레이션에 대한 별도 하위 그룹 아래에 포함되는 마법사에서 페더레이션 작업을 개선하도록 UI가 업데이트되었습니다. 
- 모든 페더레이션 추가 작업은 이제 편리한 사용을 위해 단일 하위 메뉴 아래에 그룹화됩니다.
- 새로운 AD 권한 기능으로 새로 수정된 ADSyncConfig Posh 모듈(AdSyncConfig.psm1)이 이전 ADSyncPrep.psm1(곧 사용되지 않을 수 있음)에서 전환되었습니다.

### <a name="fixed-issues"></a>해결된 문제 

- .NET 4.7.2로 업그레이드한 후 Azure AD Connect 서버가 높은 CPU 사용량을 표시하는 버그가 수정되었습니다.
- 자동으로 해결된 SQL 교착 상태 문제에 대한 오류 메시지를 간헐적으로 생성하는 버그가 수정되었습니다.
- 동기화 규칙 편집기 및 Sync Service Manager에 대한 몇 가지 접근성 문제가 수정되었습니다.  
- Azure AD Connect가 레지스트리 설정 정보를 가져올 수 없는 버그가 수정되었습니다.
- 사용자가 마법사에서 앞/뒤로 이동할 때 문제를 생성하는 버그가 수정되었습니다.
- 마법사의 잘못된 다중 스레드 처리로 인한 오류를 발생하지 못하게 하는 버그가 수정되었습니다.
- 보안 그룹을 확인할 때 그룹 동기화 필터링 페이지에 LDAP 오류가 발생하는 경우 Azure AD Connect는 이제 예외를 고화질로 그대로 반환합니다.  조회 예외에 대한 근본 원인을 여전히 알 수 없으며 다른 버그에 의해 해결됩니다.
-  STK 및 NGC 키(WHfB의 User/Device 개체에 대한 ms-DS-KeyCredentialLink 특성)에 대한 권한이 올바르게 설정되지 않는 버그가 수정되었습니다.     
- 'Set-ADSyncRestrictedPermissions’가 제대로 호출되지 않는 버그가 수정되었습니다.
-  Azure ADConnect 설치 마법사의 그룹 쓰기 저장(writeback)에 대한 권한 부여 지원이 추가됩니다.
- 암호 해시 동기화에서 AD FS로 로그인 방법을 변경할 경우 암호 해시 동기화가 사용되지 않도록 설정되지 않았습니다.
- AD FS 구성에서 IPv6 주소에 대한 확인이 추가되었습니다.
- 기존 구성이 있음을 알리도록 알림 메시지가 업데이트되었습니다.
- 디바이스 쓰기 저장(writeback) 기능이 신뢰할 수 없는 포리스트에 있는 컨테이너를 검색하지 못했습니다. 이 기능은 더 나은 오류 메시지와 해당 설명서에 대한 링크를 제공하도록 업데이트되었습니다.
- OU를 선택 취소한 후 OU에 해당하는 동기화/쓰기 저장(writeback)을 선택 취소하면 동기화 오류가 생성됩니다. 좀 더 이해하기 쉬운 오류 메시지를 만들도록 변경되었습니다.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>릴리스 상태

2018년 5월 14일: 자동 업그레이드 및 다운로드용으로 릴리스되었습니다.

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

새로운 기능 및 향상 기능

- 이 릴리스에는 Azure AD Connect에서 PingFederate 통합에 대한 공개 미리 보기가 포함되었습니다. 이 릴리스에서는 고객이 PingFederate를 페더레이션 공급자로 활용할 수 있도록 Azure Active Directory 환경을 안정적으로 쉽게 구성합니다. 이 새로운 기능을 사용하는 방법에 대한 자세한 내용은 [온라인 설명서](plan-connect-user-signin.md#federation-with-pingfederate)를 참조하세요. 
- Azure AD Connect 마법사 문제 해결 유틸리티가 업데이트되었습니다. 여기서는 연결된 사서함 및 AD 동적 그룹과 같은 더 많은 오류 시나리오가 분석됩니다. 문제 해결 유틸리티에 대한 자세한 내용은 [여기](tshoot-connect-objectsync.md)를 참조하세요.
- 디바이스 쓰기 저장 구성은 이제 Azure AD Connect 마법사에서만 관리됩니다.
- SQL 연결 문제 및 다양한 다른 문제 해결 유틸리티 문제를 해결하는 데 사용할 수 있는 ADSyncTools.psm1이라는 새 PowerShell 모듈이 추가되었습니다. ADSyncTools 모듈에 대한 자세한 내용은 [여기](tshoot-connect-tshoot-sql-connectivity.md)를 참조하세요. 
- 새 "디바이스 옵션 구성" 추가 작업이 추가되었습니다. 이 작업을 사용하여 구성할 수 있는 두 가지 작업은 다음과 같습니다. 
  - **하이브리드 Azure AD 조인**: 사용자 환경에 온-프레미스 AD 공간이 있고 Azure Active Directory에서 제공하는 기능의 혜택을 활용하려는 경우 하이브리드 Azure AD 가입 디바이스를 구현할 수 있습니다. 이는 온-프레미스 Azure Active Directory 및 Azure Active Directory에 모두 가입되어 있는 디바이스입니다.
  - **디바이스 쓰기 저장**: 디바이스 쓰기 저장은 AD FS(2012 R2 이상) 보호 디바이스에 대한 디바이스에 따라 조건부 액세스를 사용하도록 설정하는 데 사용됩니다.

    >[!NOTE] 
    > - 동기화 사용자 지정 옵션에서 디바이스 쓰기 저장을 사용하도록 설정하는 옵션이 회색으로 표시됩니다. 
    > -  이 릴리스에서는 ADPrep용 PowerShell 모듈이 사용되지 않습니다.


### <a name="fixed-issues"></a>해결된 문제 

- 이 릴리스는 SQL Server Express 설치를 특히 여러 보안 취약점에 대한 수정 프로그램을 제공하는 SQL Server 2012 SP4로 업데이트합니다.  SQL Server 2012 SP4에 대한 자세한 내용은 [여기](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)를 참조하세요.
- 동기화 규칙 처리: 부모 동기화가 더 이상 적용되지 않는 경우 조인 조건이 없는 아웃바운드 조인 동기화 규칙이 적용 취소됩니다.
- Synchronization Service Manager UI 및 Sync Rules Editor에 여러 접근성 수정 프로그램이 적용되었습니다.
- Azure AD Connect 마법사: Azure AD Connect가 작업 그룹에 있을 때 AD 커넥터 계정 만드는 오류가 발생합니다.
- Azure AD Connect 마법사: Azure AD 로그인 페이지에서 AD 도메인과 확인된 Azure AD 도메인이 일치하지 않을 때마다 확인 확인란이 표시됩니다.
- 자동 업그레이드가 시도되면 자동 업그레이드 PowerShell에서 특정 상황의 자동 업그레이드 상태를 올바르게 설정하도록 수정되었습니다.
- Azure AD Connect 마법사: 이전에 누락된 정보를 캡처하도록 원격 분석이 업데이트되었습니다.
- Azure AD Connect 마법사: **사용자 로그인 변경** 작업을 사용하여 AD FS에서 통과 인증으로 전환하는 경우 다음 변경 내용이 적용되었습니다.
    - 페더레이션 도메인에서 관리되는 도메인으로 변환하기 전에 통과 인증 에이전트가 Azure AD Connect 서버에 설치되고, 통과 인증 기능이 활성화됩니다.
    - 사용자는 더 이상 페더레이션 사용자에서 관리되는 사용자로 변환되지 않습니다. 도메인만 변환됩니다.
- Azure AD Connect 마법사: 특수 문자를 지원하기 위해 사용자 UPN에 ' 특수 문자 정규식 업데이트가 있으면 AD FS 다중 도메인 정규식이 올바르지 않습니다.
- Azure AD Connect 마법사: 변경 내용이 없으면 "원본 앵커 속성 구성" 가상 메시지를 제거합니다. 
- Azure AD Connect 마법사: 이중 페더레이션 시나리오에 대해 AD FS가 지원됩니다.
- Azure AD Connect 마법사: 관리형 도메인을 페더레이션된 도메인으로 변환할 때 추가된 도메인에 AD FS 클레임이 업데이트되지 않습니다.
- Azure AD Connect 마법사: 설치된 패키지를 검색하는 동안 Dirsync/Azure AD Sync/Azure AD Connect 관련 부실 제품을 찾습니다. 이제는 부실 제품을 제거하려고 시도합니다.
- Azure AD Connect 마법사: 통과 인증 에이전트 설치에 실패하면 오류 메시지 매핑을 수정합니다.
- Azure AD Connect 마법사: 도메인 OU 필터링 페이지에서 "Configuration" 컨테이너가 제거되었습니다.
- 동기화 엔진 설치: 동기화 엔진 설치 msi에서 가끔씩 실패한 불필요한 레거시 논리가 제거되었습니다.
- Azure AD Connect 마법사: 암호 해시 동기화에 대한 선택적 기능 페이지의 팝업 도움말 텍스트가 수정되었습니다.
- 동기화 엔진 런타임: CS 개체에 가져온 삭제가 있고 동기화 규칙에서 해당 개체를 다시 프로비전하려고 하는 시나리오를 수정합니다.
- 동기화 엔진 런타임: 온라인 연결 문제 해결 가이드에 대한 도움말 링크를 가져오기 오류 이벤트 로그에 추가합니다.
- 동기화 엔진 런타임: 커넥터를 열거할 때 동기화 스케줄러의 메모리 사용량이 감소되었습니다.
- Azure AD Connect 마법사: AD 읽기 권한이 없는 사용자 지정 동기화 서비스 계정을 확인하는 문제를 해결합니다.
- Azure AD Connect 마법사: 도메인 및 OU 필터링 선택 항목의 로깅을 향상시킵니다.
- Azure AD Connect 마법사: AD FS에서 MFA 시나리오에 대해 만든 페더레이션 신뢰에 기본 클레임을 추가합니다.
- Azure AD Connect 마법사: AD FS 배포 WAP: 서버를 추가하면 새 인증서를 사용하지 못합니다.
- Azure AD Connect 마법사: onPremCredentials가 도메인에 대해 초기화되지 않으면 DSSO 예외가 발생합니다. 
- 활성 사용자 개체의 AD distinguishedName 특성을 우선적으로 전달합니다.
- 첫 번째 OOB 동기화 규칙의 우선 순위가 100 대신 99로 설정되는 형식적 버그가 수정되었습니다.



## <a name="117510"></a>1.1.751.0
상태 2018년 4월 12일: 다운로드 전용으로 릴리스되었습니다.

>[!NOTE]
>이 릴리스는 Azure AD Connect에 대한 핫픽스
### <a name="azure-ad-connect-sync"></a>Azure AD Connect 동기화
#### <a name="fixed-issues"></a>해결된 문제
경우에 따라 중국 테넌트에 대한 자동 Azure 인스턴스 검색이 실패하는 문제를 수정했습니다.  

### <a name="ad-fs-management"></a>AD FS 관리
#### <a name="fixed-issues"></a>해결된 문제

구성 다시 시도 논리에 "동일한 키가 있는 항목이 이미 추가되었습니다."라는 ArgumentException이 발생하는 문제가 있었습니다.  이로 인해 모든 다시 시도 작업이 실패하게 됩니다.

## <a name="117500"></a>1.1.750.0
상태 2018년 3월 22일: 자동 업그레이드 및 다운로드용으로 릴리스되었습니다.
>[!NOTE]
>이 새 버전으로 업그레이드가 완료되면 Azure AD 커넥터에 대한 전체 동기화 및 전체 가져오기와 AD 커넥터에 대한 전체 동기화를 자동으로 트리거합니다. Azure AD Connect 환경의 크기에 따라 시간이 걸릴 수 있으므로, 이 기능을 지원하기 위해 필요한 단계를 수행했는지 확인하거나, 수행하기 적합한 시기를 찾을 때까지 업그레이드를 보류해야 합니다.
>[!NOTE]
>"1.1.524.0 이후 빌드를 배포하는 일부 테넌트의 경우 자동 업그레이드 기능이 올바르지 않게 비활성화되었습니다. Azure AD Connect 인스턴스가 여전히 자동 업그레이드에 적합한지 확인하려면 다음 PowerShell cmdlet을 실행합니다. “Set-ADSyncAutoUpgrade -AutoupGradeState Enabled”

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>해결된 문제

* 자동 업그레이드 상태가 일시 중단됨으로 설정되어 있으면 Set-ADSyncAutoUpgrade cmdlet은 자동 업그레이드를 차단합니다. 이제 이 기능은 이후 빌드의 자동 업그레이드를 차단하지 않도록 변경되었습니다.
* **사용자 로그인** 페이지 옵션 "암호 동기화"를 "암호 해시 동기화"로 변경합니다.  Azure AD Connect는 이 정렬이 실제로 이뤄지도록 암호가 아닌 암호 해시를 동기화합니다.  자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](how-to-connect-password-hash-synchronization.md)을 참조하세요.

## <a name="117490"></a>1.1.749.0
상태: 고객을 선택하도록 릴리스되었습니다.

>[!NOTE]
>이 새 버전으로 업그레이드가 완료되면 Azure AD 커넥터에 대한 전체 동기화 및 전체 가져오기와 AD 커넥터에 대한 전체 동기화를 자동으로 트리거합니다. Azure AD Connect 환경의 크기에 따라 시간이 걸릴 수 있으므로, 이 기능을 지원하기 위해 필요한 단계를 수행했는지 확인하거나, 수행하기 적합한 시기를 찾을 때까지 업그레이드를 보류해야 합니다.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>해결된 문제
* 다음 페이지로 전환할 때 파티션 필터링 페이지에 대한 백그라운드 작업에서 타이밍 창 수정

* ConfigDB 사용자 지정 작업을 수행하는 동안 액세스 위반이 발생하는 버그가 수정되었습니다.

* SQL 연결 시간 제한에서 복구하도록 버그 수정됨

* SAN 와일드카드가 있는 인증서가 필수 구성 요소 검사에 실패하는 버그 수정

* Azure AD 커넥터를 내보내는 동안 miiserver.exe가 충돌하는 버그 수정됨

* 구성을 변경하기 위해 Azure AD Connect 마법사를 실행할 때 DC에서 잘못된 암호 시도가 기록되는 버그 수정


#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* GDPR(일반 데이터 보호 규정)에 대한 개인 정보 설정이 추가되었습니다.  자세한 내용은 [여기](reference-connect-user-privacy.md) 문서를 참조하세요.

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* 애플리케이션 원격 분석 - 관리자는 이 데이터 클래스를 설정/해제할 수 있습니다.

* Azure AD 상태 데이터 - 관리자는 상태 설정을 제어하는 상태 포털을 방문해야 합니다.
   서비스 정책이 변경되고 나면 에이전트에서 읽고 적용합니다.

* 페이지 초기화에 대한 디바이스 나중 쓰기 구성 작업 및 진행률 표시줄 추가됨

* ZIP 텍스트/HTML 보고서에서 HTML 보고서 및 전체 데이터 컬렉션으로 일반 진단 개선됨

* 자동 업그레이드 안정성 향상 및 서버의 상태를 확인할 수 있도록 추가 원격 분석 추가됨

* AD 커넥터 계정에서 권한 있는 계정에 사용할 수 있는 권한 제한

  * 새로 설치하는 경우 마법사는 MSOL 계정을 만든 후 MSOL 계정에 있는 권한 있는 계정이 갖는 권한을 제한합니다.

변경 내용은 다음 항목을 고려합니다.
1. Express 설치
2. 자동 생성 계정을 사용한 사용자 지정 설치
3. 설치 관리자 변경으로 Azure AD Connect의 새로 설치에 대한 SA 권한이 필요하지 않음

* 특정 개체에 대한 동기화 문제를 해결하기 위해 새로운 유틸리티가 추가되었습니다. Azure AD 연결 마법사 문제 해결 추가 작업의 ‘개체 동기화 문제 해결’ 옵션에서 사용할 수 있습니다. 현재 유틸리티는 다음 사항을 확인합니다.

  * 동기화된 사용자 개체와 Azure AD 테넌트의 사용자 계정 간의 UserPrincipalName이 일치하지 않습니다.
  * 도메인 필터링으로 인해 개체가 동기화에서 필터링된 경우
  * OU(조직 구성 단위) 필터링으로 인해 개체가 동기화에서 필터링된 경우

* 특정 사용자 계정에 대한 온-프레미스 Active Directory에 저장된 현재 암호 해시를 동기화하기 위해 새로운 유틸리티가 추가되었습니다.

유틸리티는 암호 변경이 필요하지 않습니다. Azure AD 연결 마법사 문제 해결 추가 작업의 ‘암호 해시 동기화 문제 해결’ 옵션에서 사용할 수 있습니다.






## <a name="116540"></a>1.1.654.0
상태: 2017년 12월 12일

>[!NOTE]
>이 릴리스는 Azure AD Connect에 대한 보안 관련 핫픽스
### <a name="azure-ad-connect"></a>Azure AD Connect
Azure AD Connect 버전 1.1.654.0 이상에서는 Azure AD Connect가 AD DS 계정을 만들 때 [AD DS 계정에 대한 액세스 잠금](#lock) 섹션에 설명된 권장 사용 권한 변경 내용이 자동으로 적용되도록 하는 개선 사항이 추가되었습니다. 

- Azure AD Connect를 설치할 때 설치 관리자는 기존 AD DS 계정을 제공하거나 Azure AD Connect에서 계정을 자동으로 만들도록 할 수 있습니다. 사용 권한 변경 내용은 설치 중에 Azure AD Connect에서 만드는 AD DS 계정에 자동으로 적용됩니다. 설치 관리자가 제공한 기존 AD DS 계정에는 변경 내용이 적용되지 않습니다.
- 이전 버전의 Azure AD Connect에서1.1.654.0 이상으로 업그레이드한 고객의 경우 업그레이드 전에 생성된 기존 AD DS 계정에 사용 권한 변경이 소급 적용되지 않습니다. 업그레이드 후 만든 새 AD DS 계정에만 적용됩니다. 이 문제는 Azure AD에 동기화할 새 AD 포리스트를 추가할 때 발생합니다.

>[!NOTE]
>이 릴리스에서는 서비스 계정이 설치 프로세스에서 만들어진 Azure AD Connect의 새 설치에 대한 취약성만 제거합니다. 기존 설치의 경우 또는 계정을 직접 제공하는 경우 이 취약성이 없는지 확인해야 합니다.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> AD DS 계정에 대한 액세스 잠금
온-프레미스 AD에서 다음 사용 권한 변경을 구현하여 AD DS 계정에 대한 액세스를 잠급니다.  

*   지정된 개체에서 상속을 사용하지 않도록 설정합니다.
*   특정 개체에서 SELF와 관련된 ACE를 제외하고 ACE를 모두 제거합니다. SELF의 경우 기본 사용 권한을 그대로 유지할 수 있습니다.
*   다음과 같은 특정 권한을 할당합니다.

Type     | 속성                          | 액세스 권한               | 적용 대상
---------|-------------------------------|----------------------|--------------|
Allow    | SYSTEM                        | 모든 권한         | 이 개체  |
Allow    | 엔터프라이즈 관리자             | 모든 권한         | 이 개체  |
Allow    | 도메인 관리자                 | 모든 권한         | 이 개체  |
Allow    | 관리자                | 모든 권한         | 이 개체  |
Allow    | 엔터프라이즈 도메인 컨트롤러 | 내용 보기        | 이 개체  |
Allow    | 엔터프라이즈 도메인 컨트롤러 | 모든 속성 읽기  | 이 개체  |
Allow    | 엔터프라이즈 도메인 컨트롤러 | 읽기 권한     | 이 개체  |
Allow    | 인증된 사용자           | 내용 보기        | 이 개체  |
Allow    | 인증된 사용자           | 모든 속성 읽기  | 이 개체  |
Allow    | 인증된 사용자           | 읽기 권한     | 이 개체  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>기존 서비스 계정을 강화하는 PowerShell 스크립트입니다.

PowerShell 스크립트를 사용하여 조직에서 제공하거나 이전 Azure AD Connect 설치에서 만든 기존 AD DS 계정에 이러한 설정을 적용하려면 위에 제공된 링크에서 스크립트를 다운로드합니다.

##### <a name="usage"></a>Usage:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = 사용 권한을 강화해야 하는 Active Directory 계정입니다.

**$Credential** = $ObjectDN 계정에 대한 사용 권한을 제한하는 데 필요한 권한을 포함하는 관리자 인증서입니다. 이러한 권한은 보통 엔터프라이즈 또는 도메인 관리자가 갖고 있습니다. 계정 조회 오류를 방지하려면 관리자 계정의 정규화된 도메인 이름을 사용합니다. 예: contoso.com\admin.

>[!NOTE] 
>$credential.UserName은 FQDN\username 형식이어야 합니다. 예: contoso.com\admin 
##### <a name="example"></a>예제:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>이 취약성을 이용하여 무단 액세스했나요?

이 취약성을 이용하여 Azure AD Connect 구성을 손상시켰는지 확인하려면 서비스 계정의 마지막 암호 재설정 날짜를 확인해야 합니다.  예기치 않은 타임스탬프가 있으면 해당 암호 재설정 이벤트를 이벤트 로그를 통해 더 자세히 조사해야 합니다.

자세한 내용은 [Microsoft 보안 공지 4056318](/security-updates/securityadvisories/2017/4056318)을 참조하세요.

## <a name="116490"></a>1.1.649.0
상태: 2017년 10월 27일

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>해결된 문제
* Azure AD Connect 및 Azure AD Connect 상태 에이전트(동기화용) 사이의 버전 호환성 문제가 해결되었습니다. 이 문제는 Azure AD Connect를 버전 1.1.647.0으로 현재 위치 업그레이드를 수행하려고 하며 현재 상태 에이전트 버전 3.0.127.0이 있는 고객에게 영향을 미칩니다. 업그레이드 후에 상태 에이전트는 더 이상 Azure AD Connect 동기화 서비스에 대한 상태 데이터를 Azure AD 상태 서비스로 전송할 수 없습니다. 이 수정 프로그램을 적용하면 상태 에이전트 버전 3.0.129.0이 Azure AD Connect의 현재 위치 업그레이드 동안 설치됩니다. 상태 에이전트 3.0.129.0은 Azure AD Connect 버전 1.1.649.0과 호환성 문제가 없습니다.


## <a name="116470"></a>1.1.647.0
상태: 2017년 10월 19일

> [!IMPORTANT]
> Azure AD Connect 버전 1.1.647.0과 Azure AD Connect Health Agent(동기화용) 버전 3.0.127.0 사이에 알려진 호환성 문제가 있습니다. 이 문제로 인해 Health Agent가 Azure AD Connect 동기화 서비스에 대한 상태 데이터(개체 동기화 오류 및 실행 기록 데이터 포함)를 Azure AD Health Service에 보낼 수 없습니다. Azure AD Connect 배포를 버전 1.1.647.0으로 수동으로 업그레이드하기 전에 Azure AD Connect 서버에 설치된 Azure AD Connect Health Agent의 현재 버전을 확인하십시오. 이를 수행하려면 *제어판 → 프로그램 추가/제거* 로 이동하여 *동기화용 Microsoft Azure AD Connect Health Agent* 애플리케이션을 찾으십시오. 해당 버전이 3.0.127.0인 경우 업그레이드하기 전에 다음 Azure AD Connect 버전을 사용할 수 있을 때까지 기다리는 것이 좋습니다. Health Agent 버전이 3.0.127.0이 아닌 경우 수동 전체 업그레이드를 수행해도 됩니다. 이 문제는 스윙 업그레이드 또는 Azure AD Connect를 새로 설치하는 고객에게는 영향을 미치지 않습니다.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>해결된 문제
* Azure AD Connect 마법사의 *사용자 로그인 변경* 작업 관련 문제가 해결되었습니다.

  * 이 문제는 암호 동기화를 **사용하는** 기존 Azure AD Connect 배포가 있고 사용자 로그인 방법을 *통과 인증* 으로 설정하려고 할 때 발생합니다. 변경 내용을 적용하기 전에 마법사에 "*Disable Password Synchronization*(암호 동기화 사용 안 함)" 프롬프트가 잘못 표시됩니다. 하지만 암호 동기화는 변경이 적용된 후에도 사용하는 상태로 유지됩니다. 이 문제가 해결되어, 마법사에 프롬프트가 더 이상 표시되지 않습니다.

  * 기본적으로 *사용자 로그인 변경* 작업을 사용하여 사용자 로그인 방법을 업데이트할 때 마법사는 암호 동기화를 비활성화하지 않습니다. 통과 인증 또는 페더레이션을 기본 사용자 로그인 방법으로 사용하도록 설정하더라도 암호 동기화를 유지하려는 고객에게 혼란을 주지 않기 위해서 입니다.

  * 사용자 로그인 방법을 업데이트한 후 암호 동기화를 사용 안 함으로 설정하려면 마법사에서 *Customize Synchronization Configuration*(동기화 구성 사용자 지정) 작업을 실행해야 합니다. *선택적 기능* 페이지로 이동하여 *암호 동기화* 옵션을 선택 취소하십시오.

  * 원활한 Single Sign-On을 사용/사용 안 함으로 설정하려는 경우에도 동일한 문제가 발생합니다. 특히, 암호 동기화를 사용하는 기존 Azure AD Connect 배포가 있고 사용자 로그인 방법이 *통과 인증* 으로 이미 설정되어 있는 경우 그렇습니다. *사용자 로그인 변경* 작업을 사용하여, 사용자 로그인 방법이 "통과 인증"으로 구성되어 있는 동안 *Enable Seamless Single Sign-On*(원활한 Single Sign-On 사용) 옵션에 대해 선택/선택 취소를 시도합니다. 변경 내용을 적용하기 전에 마법사에 "*Disable Password Synchronization*(암호 동기화 사용 안 함)" 프롬프트가 잘못 표시됩니다. 하지만 암호 동기화는 변경이 적용된 후에도 사용하는 상태로 유지됩니다. 이 문제가 해결되어, 마법사에 프롬프트가 더 이상 표시되지 않습니다.

* Azure AD Connect 마법사의 *사용자 로그인 변경* 작업 관련 문제가 해결되었습니다.

  * 이 문제는 암호 동기화를 **사용하지 않는** 기존 Azure AD Connect 배포가 있고 사용자 로그인 방법을 *통과 인증* 으로 설정하려고 할 때 발생합니다. 변경이 적용되면 마법사는 통과 인증과 암호 동기화를 모두 사용하도록 설정합니다. 이 문제가 해결되어 마법사에서 암호 동기화를 더 이상 사용하도록 설정하지 않습니다.

  * 이전에는 암호 동기화가 통과 인증을 사용하기 위한 필수 구성 요소였습니다. 사용자 로그인 방법을 *통과 인증* 으로 설정하면 마법사가 통과 인증과 암호 동기화를 모두 사용하도록 설정합니다. 최근 암호 동기화가 필수 구성 요소로 제거되었습니다. Azure AD Connect 버전 1.1.557.0의 일부로, 사용자 로그인 방법을 *통과 인증* 으로 설정할 때 암호 동기화를 사용하도록 설정하지 않게 Azure AD Connect가 변경되었습니다. 이 변경 사항은 Azure AD Connect 설치에만 적용되었습니다. 이 문제가 해결되어 동일한 변화가 *사용자 로그인 변경* 작업에도 적용됩니다.

  * 원활한 Single Sign-On을 사용/사용 안 함으로 설정하려는 경우에도 동일한 문제가 발생합니다. 특히, 암호 동기화를 사용하지 않는 기존 Azure AD Connect 배포가 있고 사용자 로그인 방법이 *통과 인증* 으로 이미 설정되어 있는 경우 그렇습니다. *사용자 로그인 변경* 작업을 사용하여, 사용자 로그인 방법이 "통과 인증"으로 구성되어 있는 동안 *Enable Seamless Single Sign-On*(원활한 Single Sign-On 사용) 옵션에 대해 선택/선택 취소를 시도합니다. 변경이 적용되면 마법사가 암호 동기화를 사용하도록 설정합니다. 이 문제가 해결되어 마법사에서 암호 동기화를 더 이상 사용하도록 설정하지 않습니다. 

* Azure AD Connect 업그레이드가 "*Unable to upgrade the Synchronization Service*(동기화 서비스를 업그레이드 할 수 없음)" 오류로 인해 실패하는 문제가 해결되었습니다. 또한 동기화 서비스가 더 이상 이벤트 오류 "*The service was unable to start because the version of the database is newer than the version of the binaries installed*(데이터베이스 버전이 설치된 바이너리 버전보다 최신이기 때문에 서비스를 시작할 수 없습니다.)"로 시작되지 않습니다. 이 문제는 업그레이드를 수행하는 관리자에게 Azure AD Connect에서 사용 중인 SQL 서버에 대한 sysadmin 권한이 없는 경우 발생합니다. 이 문제가 해결되어 Azure AD Connect에서 업그레이드 중 관리자는 ADSync 데이터베이스에 대한 db_owner 권한만 있으면 됩니다.

* [원활한 Single Sign-On](./how-to-connect-sso.md)을 사용하도록 설정한 고객에게 영향을 미치는 Azure AD Connect 업그레이드 문제가 해결되었습니다. Azure AD Connect가 업그레이드된 후, 원활한 Single Sign-On 기능이 사용하도록 설정되어 있고 완전히 작동하는 경우에도 Azure AD Connect 마법사에 사용 안 함으로 잘못 표시됩니다. 이 문제가 해결되어 해당 기능이 마법사에 사용하도록 설정된 것으로 제대로 표시됩니다.

* 원본 앵커와 관련된 변경 사항이 없는 경우에도 Azure AD Connect 마법사가 *구성 준비 완료* 페이지에 "*원본 앵커 구성*" 프롬프트를 표시하는 문제가 해결되었습니다.

* Azure AD Connect의 수동 전체 업그레이드를 수행할 때 해당 Azure AD 테넌트의 전역 관리자 자격 증명을 제공해야 합니다. 이전에는 전역 관리자 자격 증명이 다른 Azure AD 테넌트에 속해 있어도 업그레이드가 진행될 수 있었습니다. 업그레이드가 성공적으로 완료된 것처럼 보이지만 특정 구성이 업그레이드와 제대로 유지되지 않습니다. 이 변경으로 제공된 자격 증명이 Azure AD 테넌트와 일치하지 않으면 마법사가 업그레이드가 진행되는 것을 막습니다.

* 수동 업그레이드를 시작할 때 Azure AD Connect Health 서비스를 불필요하게 다시 시작한 중복 논리가 제거되었습니다.


#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* Microsoft Germany Cloud로 Azure AD Connect를 설정하는 데 필요한 단계를 간소화하는 논리가 추가되었습니다. 이전에는 이 문서의 설명대로 Azure AD Connect 서버의 특정 레지스트리 키를 Microsoft Germany Cloud에서 올바르게 작동하도록 업데이트해야 했습니다. 이제 Azure AD Connect에서 설치 중에 제공된 전역 관리자 자격 증명을 기반으로 테넌트가 Microsoft Germany Cloud에 있는지 자동으로 감지할 수 있습니다.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 동기화
> [!NOTE]
> 참고: 동기화 서비스에는 사용자 지정 스케줄러를 개발할 수 있는 WMI 인터페이스가 있습니다. 이 인터페이스는 현재 사용되지 않으며 향후 2018년 6월 30일 이후에 출시되는 Azure AD Connect 이후 버전에서 제거될 예정입니다. 동기화 일정을 사용자 지정하려는 고객은 [기본 제공 스케줄러](./how-to-connect-sync-feature-scheduler.md)를 사용해야 합니다.
#### <a name="fixed-issues"></a>해결된 문제
* Azure AD Connect 마법사가 온-프레미스 Active Directory의 변경 내용을 동기화하는 데 필요한 AD Connector 계정을 만들면 해당 계정에 PublicFolder 개체를 읽는 데 필요한 권한이 올바르게 할당되지 않습니다. 이 문제는 기본 설치와 사용자 지정 설치 모두에 영향을 줍니다. 이 변경으로 문제가 해결됩니다.

* Azure AD Connect 마법사 문제 해결 페이지가 Windows Server 2016에서 실행되는 관리자에게 제대로 렌더링되지 않는 문제가 해결되었습니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* Azure AD Connect 마법사 문제 해결 페이지를 사용하여 암호 동기화 문제를 해결할 때 문제 해결 페이지에 이제 도메인별 상태가 반환됩니다.

* 이전에는 암호 해시 동기화를 사용하도록 설정하려고 하면 AD Connector 계정에 온-프레미스 AD의 암호 해시를 동기화하는 데 필요한 권한이 있는지 여부를 Azure AD Connect에서 확인하지 않습니다. 이제 Azure AD Connect 마법사에서 확인하고 AD Connector 계정에 충분한 권한이 없으면 경고를 표시합니다.

### <a name="ad-fs-management"></a>AD FS 관리
#### <a name="fixed-issue"></a>해결된 문제
* [ms-DS-ConsistencyGuid를 원본 앵커](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 기능으로 사용하는 것과 관련된 문제가 해결되었습니다. 이 문제는 *AD FS로 페더레이션* 을 사용자 로그인 방법으로 구성한 고객에게 영향을 줍니다. 마법사에서 *원본 앵커 구성* 작업을 실행하면 Azure AD Connect가 immutableId의 원본 특성으로 *ms-DS-ConsistencyGuid를 사용하도록 전환합니다. 이 변경의 일환으로 Azure AD Connect가 AD FS의 ImmutableId에 대한 클레임 규칙을 업데이트하려고 시도합니다. 하지만 Azure AD Connect에 AD FS를 구성하는 데 필요한 관리자 자격 증명이 없어서 이 단계가 실패했습니다. 이 문제가 해결되어 이제 *원본 앵커 구성* 작업을 실행하면 AD FS에 대한 관리자 자격 증명을 입력하라는 메시지가 Azure AD Connect에 표시됩니다.



## <a name="116140"></a>1.1.614.0
상태: 2017년 9월 5일

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>알려진 문제
* Azure AD Connect 업그레이드가 "*Unable to upgrade the Synchronization Service*(동기화 서비스를 업그레이드 할 수 없음)" 오류로 인해 실패하는 알려진 문제가 있습니다. 또한 동기화 서비스가 더 이상 이벤트 오류 "*The service was unable to start because the version of the database is newer than the version of the binaries installed*(데이터베이스 버전이 설치된 바이너리 버전보다 최신이기 때문에 서비스를 시작할 수 없습니다.)"로 시작되지 않습니다. 이 문제는 업그레이드를 수행하는 관리자에게 Azure AD Connect에서 사용 중인 SQL 서버에 대한 sysadmin 권한이 없는 경우 발생합니다. Dbo 권한은 충분하지 않습니다.

* [원활한 Single Sign-On](how-to-connect-sso.md)을 설정한 고객에게 영향을 미치는 Azure AD Connect 업그레이드와 관련한 알려진 문제가 있습니다. Azure AD Connect를 업그레이드한 후 기능은 활성화된 상태로 남아 있는 경우라도 마법사에서는 비활성화된 것으로 표시됩니다. 이 문제의 해결책은 향후 릴리스에서 제공될 예정입니다. 이 디스플레이 문제에 대해 염려하는 고객은 마법사에서 원활한 Single Sign-On을 사용하도록 설정하면 수동으로 해결할 수 있습니다.

#### <a name="fixed-issues"></a>해결된 문제
* [소스 앵커로 ms-DS-ConsistencyGuid](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 기능을 활성화하는 동안 Azure AD Connect가 온-프레미스 AD FS에서 클레임 규칙을 업데이트하지 못하도록 하는 문제를 해결했습니다. 로그인 방법으로 구성된 AD FS가 있는 기존 Azure AD Connect 배포에 대한 기능을 설정하려고 하는 경우 문제가 발생합니다. AD FS에서 클레임 규칙을 업데이트하려고 하기 전에 마법사가 ADFS 자격 증명에 대해 묻지 않기 때문에 문제가 발생합니다.
* 온-프레미스 AD 포리스트에 NTLM을 사용하지 않도록 설정된 경우 Azure AD Connect의 설치 실패를 유발하는 문제가 해결되었습니다. 이 문제는 Azure AD Connect 마법사가 Kerberos 인증에 필요한 보안 컨텍스트를 만들 때 정규화된 자격 증명을 제공하지 않기 때문입니다. 이로 인해 Kerberos 인증에 실패하고 Azure AD Connect 마법사는 NTLM을 다시 사용하지 못합니다.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect 동기화
#### <a name="fixed-issues"></a>해결된 문제
* 태그 특성이 채워져 있지 않은 경우 새 동기화 규칙을 생성할 수 없는 문제가 해결되었습니다.
* Kerberos를 사용할 수 있는 경우라도 NTLM을 사용하여 암호 동기화를 위해 온-프레미스 AD에 Azure AD Connect를 연결할 수 없도록 하는 문제가 해결되었습니다. 이 문제는 온-프레미스 AD 토폴로지에 백업에서 복원된 도메인 컨트롤러가 하나 이상 있는 경우 발생합니다.
* 업그레이드 후 불필요하게 발생하는 전체 동기화 단계를 유발하는 문제가 해결되었습니다. 기본 제공 동기화 규칙에 변화가 있는 경우 일반적으로 업그레이드한 후 전체 동기화 단계를 실행해야 합니다. 이 문제는 줄 바꿈 문자가 있는 동기화 규칙 식이 발생하는 경우 변경 사항을 잘못 감지한 변경 검색 논리의 오류로 인한 것입니다. 줄 바꿈 문자는 가독성을 높이기 위해 동기화 규칙 식에 삽입됩니다.
* Azure AD Connect 서버가 자동 업그레이드 후 올바르게 작동하지 않을 수 있는 문제가 수정되었습니다. 이 문제는 1.1.443.0(또는 이전) 버전을 사용하는 Azure AD Connect 서버에 영향을 미칩니다. 문제에 대한 자세한 내용은 아티클 [자동 업그레이드 후 Azure AD Connect가 제대로 작동하지 않습니다](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)를 참조하세요.
* 오류가 발생한 경우 자동 업그레이드가 5분마다 다시 시도되는 문제가 해결되었습니다. 수정 후에는 오류가 발생하면 자동 업그레이드가 지수적 백오프로 다시 시도합니다.
* 암호 동기화 이벤트 611이 Windows 애플리케이션 이벤트 로그에 **오류** 가 아닌 **정보** 로 잘못 표시되는 문제가 해결되었습니다. 이벤트 611이 암호 동기화에 문제가 발생할 때마다 생성됩니다. 
* Azure AD Connect 마법사에서 그룹 쓰기 저장에 필요한 OU를 선택하지 않고 그룹 쓰기 저장 기능을 사용하도록 설정하는 문제가 해결되었습니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* 추가 작업에서 Azure AD Connect 마법사에 문제 해결 작업이 추가되었습니다. 고객은 이 작업을 활용하여 암호 동기화와 관련된 문제를 해결하고 일반 진단을 수집할 수 있습니다. 나중에 문제 해결 작업은 다른 디렉터리 동기화와 관련된 문제를 포함하도록 확장될 예정입니다.
* 이제 Azure AD Connect에서 **기존 데이터베이스 사용** 이라는 새 설치 모드를 지원합니다. 이 설치 모드를 사용하면 고객은 기존 ADSync 데이터베이스를 지정하는 Azure AD Connect를 설치할 수 있습니다. 이 기능에 대한 자세한 내용은 아티클 [기존 데이터베이스 사용](how-to-connect-install-existing-database.md)을 참조하세요.
* 향상된 보안을 위해 Azure AD Connect는 이제 디렉터리 동기화를 위해 기본적으로 TLS1.2를 사용하여 Azure AD에 연결합니다. 이전에 기본 방법은 TLS1.0이었습니다.
* Azure AD Connect 암호 동기화 에이전트가 시작되면 암호 동기화에 대해 잘 알려진 Azure AD 엔드포인트에 연결하려고 합니다. 연결에 성공하면 지역별 엔드포인트로 리디렉션됩니다. 이전에는 암호 동기화 에이전트가 다시 시작될 때까지 지역별 엔드포인트를 캐시합니다. 이제 에이전트는 지역별 엔드포인트와 관련된 연결 문제가 발생하는 경우 캐시를 지우고 잘 알려진 엔드포인트로 재시도합니다. 이 변경을 통해 캐시된 지역별 엔드포인트를 더 이상 사용할 수 없는 경우 암호 동기화를 다른 지역별 엔드포인트로 장애 조치(failover)할 수 있도록 보장합니다.
* 온-프레미스 AD 포리스트에서 변경 내용을 동기화하려면 AD DS 계정이 필요합니다. (i) 직접 AD DS 계정을 만들고 Azure AD Connect에 해당 자격 증명을 제공하거나 (ii) 엔터프라이즈 관리자 자격 증명을 제공하고 Azure AD Connect에서 AD DS 계정을 만들도록 할 수 있습니다. 이전은 (i)이 Azure AD Connect 마법사에서 기본 옵션입니다. 이제 (ii)가 기본 옵션입니다.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* Microsoft Azure Government 클라우드 및 Microsoft 클라우드 독일에 대한 지원이 추가되었습니다.

### <a name="ad-fs-management"></a>AD FS 관리
#### <a name="fixed-issues"></a>해결된 문제
* AD 준비 PowerShell 모듈에서 Initialize-ADSyncNGCKeysWriteBack cmdlet은 디바이스 등록 컨테이너에 ACL을 잘못 적용하였으므로 기존 권한만 상속합니다.  이는 동기화 서비스 계정에 올바른 권한이 있도록 업데이트되었습니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* Azure AD Connect 확인 ADFS 로그인 작업은 ADFS에서 토큰 검색뿐 아니라 Microsoft Online에 대한 로그인을 확인하도록 업데이트되었습니다.
* Azure AD Connect를 사용하여 새 ADFS 팜을 설정할 때, ADFS 자격 증명을 묻는 페이지가 이동되어 이제 ADFS 및 WAP 서버를 제공하라는 메시지가 사용자에게 표시되기 전에 발생합니다.  따라서 Azure AD Connect에서 지정된 계정에 올바른 권한이 있는지 확인할 수 있습니다.
* Azure AD Connect를 업그레이드하는 동안 ADFS Azure AD 트러스트가 업데이트에 실패하는 경우 더 이상 업그레이드할 수 없습니다.  이렇게 되면 사용자에게 적절한 경고 메시지가 표시되며, Azure AD Connect 추가 작업을 통해 트러스트를 다시 설정하도록 진행해야 합니다.

### <a name="seamless-single-sign-on"></a>Seamless Single Sign-On
#### <a name="fixed-issues"></a>해결된 문제
* [원활한 Single Sign-On](how-to-connect-sso.md) 활성화를 시도하는 경우 Azure AD Connect 마법사가 오류를 반환하도록 하는 문제가 해결되었습니다. 오류 메시지는 *“Microsoft Azure AD Connect 인증 에이전트 구성에 실패했습니다”* 입니다. 이 문제는 이 [아티클](how-to-connect-pta-upgrade-preview-authentication-agents.md)에서 설명된 단계에 따라 [통과 인증](how-to-connect-sso.md)을 위해 인증 에이전트의 미리 보기 버전을 수동으로 업그레이드한 기존 고객에게 영향을 미칩니다.


## <a name="115610"></a>1.1.561.0
상태: 2017년 7월 23일

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>해결된 문제

* 기본 제공 동기화 규칙 “Out to AD - User ImmutableId”를 삭제하는 문제가 해결되었습니다.

  * Azure AD Connect를 업그레이드하는 경우 또는 Azure AD Connect 마법사에서 *동기화 구성 업데이트* 를 작업 옵션을 사용하여 Azure AD Connect 동기화 구성을 업데이트하는 경우 문제가 발생합니다.

  * 이 동기화 규칙은 [소스 앵커 기능으로 ms-DS-ConsistencyGuid](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)를 사용하도록 설정한 고객에게 적용할 수 있습니다. 이 기능은 1.1.524.0 이후 버전에서 도입되었습니다. 동기화 규칙이 제거되면 Azure AD Connect에서는 더 이상 ObjectGuid 특성 값으로 온-프레미스 AD ms-DS-ConsistencyGuid 특성을 채울 수 없습니다. 새 사용자가 Azure AD에 프로비전되지 않도록 방지하지 않습니다.

  * 기능이 설정되어 있다면 업그레이드 또는 구성을 변경하는 동안 동기화 규칙이 제거되지 않도록 수정합니다. 또한 이 문제의 영향을 받는 기존 고객의 경우 이 버전의 Azure AD Connect로 업그레이드한 후에 동기화 규칙이 다시 추가되도록 수정합니다.

* 100보다 작은 우선 순위 값을 갖는 기본 제공 동기화 규칙을 발생시키는 문제를 해결했습니다.

  * 일반적으로 사용자 지정 동기화 규칙에 대한 우선 순위 값은 0	~99로 예약됩니다. 업그레이드하는 동안 기본 제공 동기화 규칙의 우선 순위 값은 동기화 규칙 변경 내용을 수용하도록 업데이트됩니다. 이 문제로 인해 기본 제공 동기화 규칙은 100보다 작은 우선 순위 값을 할당받을 수 있습니다.

  * 이를 해결하여 업그레이드하는 동안 문제가 발생하지 않도록 방지합니다. 그러나 문제의 영향을 받는 기존 고객의 경우 우선 순위 값을 복원하지 않습니다. 별도의 수정은 복원에 도움이 되도록 나중에 이뤄집니다.

* OU 기반 필터링을 사용하는 경우에도 Azure AD Connect 마법사에서 [도메인 및 OU 필터링 화면](how-to-connect-install-custom.md#domain-and-ou-filtering)이 *모든 도메인 및 OU 동기화* 옵션이 선택되었음을 표시하는 문제가 해결되었습니다.

*   *새로 고침* 단추를 클릭한 경우 Synchronization Service Manager에서 [디렉터리 파티션 구성 화면](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)이 오류를 반환하는 문제가 해결되었습니다. *"도메인을 새로 고치는 동안 오류가 발생했습니다: ‘System.Collections.ArrayList’ 형식의 개체를 ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject’ 형식으로 캐스트할 수 없습니다”* 라는 오류 메시지가 표시됩니다. 새 AD 도메인을 기존 AD 포리스트에 추가하고 새로 고침 단추를 사용하여 Azure AD Connect를 업데이트하려는 경우에 이 오류가 발생합니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* [자동 업그레이드 기능](how-to-connect-install-automatic-upgrade.md)은 다음과 같은 구성 사용하는 고객을 지원하도록 확장되었습니다.
  * 디바이스 쓰기 저장 기능을 사용하도록 설정했습니다.
  * 그룹 쓰기 저장 기능을 사용하도록 설정했습니다.
  * 설치가 Express 설정 또는 DirSync 업그레이드가 아닙니다.
  * 메타버스에 10만 개가 넘는 개체가 있습니다.
  * 둘 이상의 포리스트에 연결되어 있습니다. 빠른 설치는 하나의 포리스트에만 연결합니다.
  * AD Connector 계정이 더 이상 기본 MSOL_ 계정이 아닙니다.
  * 서버가 준비 모드로 설정되어 있습니다.
  * 사용자 쓰기 저장 기능을 사용하도록 설정했습니다.

  >[!NOTE]
  >자동 업그레이드 기능의 범위를 확장하면 Azure AD Connect 빌드 1.1.105.0 이상을 사용하는 고객에게 영향을 줍니다. Azure AD Connect 서버를 자동으로 업그레이드하지 않으려면 Azure AD Connect 서버에서 다음 cmdlet을 실행해야 합니다. `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` 자동 업그레이드를 활성화/비활성화하는 방법에 대한 자세한 내용은 [Azure AD Connect: 자동 업그레이드](how-to-connect-install-automatic-upgrade.md) 문서를 참조하세요.
## <a name="115580"></a>1.1.558.0
상태: 릴리스되지 않습니다. 이 빌드의 변경 내용은 1.1.561.0 버전에 포함됩니다.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>해결된 문제

* OU 기반 필터링 구성이 업데이트되는 경우 기본 제공 동기화 규칙 “Out to AD - User ImmutableId”를 삭제하는 문제가 해결되었습니다. 이 동기화 규칙은 [소스 앵커 기능으로 ms-DS-ConsistencyGuid](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)에 필요합니다.

* OU 기반 필터링을 사용하는 경우에도 Azure AD Connect 마법사에서 [도메인 및 OU 필터링 화면](how-to-connect-install-custom.md#domain-and-ou-filtering)이 *모든 도메인 및 OU 동기화* 옵션이 선택되었음을 표시하는 문제가 해결되었습니다.

*   *새로 고침* 단추를 클릭한 경우 Synchronization Service Manager에서 [디렉터리 파티션 구성 화면](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)이 오류를 반환하는 문제가 해결되었습니다. *"도메인을 새로 고치는 동안 오류가 발생했습니다: ‘System.Collections.ArrayList’ 형식의 개체를 ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject’ 형식으로 캐스트할 수 없습니다”* 라는 오류 메시지가 표시됩니다. 새 AD 도메인을 기존 AD 포리스트에 추가하고 새로 고침 단추를 사용하여 Azure AD Connect를 업데이트하려는 경우에 이 오류가 발생합니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* [자동 업그레이드 기능](how-to-connect-install-automatic-upgrade.md)은 다음과 같은 구성 사용하는 고객을 지원하도록 확장되었습니다.
  * 디바이스 쓰기 저장 기능을 사용하도록 설정했습니다.
  * 그룹 쓰기 저장 기능을 사용하도록 설정했습니다.
  * 설치가 Express 설정 또는 DirSync 업그레이드가 아닙니다.
  * 메타버스에 10만 개가 넘는 개체가 있습니다.
  * 둘 이상의 포리스트에 연결되어 있습니다. 빠른 설치는 하나의 포리스트에만 연결합니다.
  * AD Connector 계정이 더 이상 기본 MSOL_ 계정이 아닙니다.
  * 서버가 준비 모드로 설정되어 있습니다.
  * 사용자 쓰기 저장 기능을 사용하도록 설정했습니다.

  >[!NOTE]
  >자동 업그레이드 기능의 범위를 확장하면 Azure AD Connect 빌드 1.1.105.0 이상을 사용하는 고객에게 영향을 줍니다. Azure AD Connect 서버를 자동으로 업그레이드하지 않으려면 Azure AD Connect 서버에서 다음 cmdlet을 실행해야 합니다. `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` 자동 업그레이드를 활성화/비활성화하는 방법에 대한 자세한 내용은 [Azure AD Connect: 자동 업그레이드](how-to-connect-install-automatic-upgrade.md) 문서를 참조하세요.
## <a name="115570"></a>1.1.557.0
상태: 2017년 7월

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>해결된 문제
* 기존 서비스 연결 지점 개체에 구성된 확인된 도메인이 여전히 유효한 도메인인 경우에도 변경되도록 하는, Initialize-ADSyncDomainJoinedComputerSync cmdlet과 관련된 문제가 해결되었습니다. 이 문제는 서비스 연결 지점을 구성하는 데 사용할 수 있는 확인된 도메인이 Azure AD 테넌트에 둘 이상 있는 경우에 발생합니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* 이제 비밀번호 쓰기 저장이 Microsoft Azure Government 클라우드 및 Microsoft Cloud Germany에서 미리 보기로 제공됩니다. 다양한 서비스 인스턴스용 Azure AD Connect 지원에 대한 자세한 내용은 [Azure AD Connect: 인스턴스에 대한 특별 고려 사항](reference-connect-instances.md)을 참조하세요.

* 이제 Initialize-ADSyncDomainJoinedComputerSync cmdlet에 AzureADDomain이라는 새로운 선택적 매개 변수가 있습니다. 이 매개 변수를 사용하면 서비스 연결 지점을 구성하는 데 사용할 확인된 도메인을 지정할 수 있습니다.

### <a name="pass-through-authentication"></a>통과 인증

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* 통과 인증에 필요한 에이전트 이름이 *Microsoft Azure AD 애플리케이션 프록시 커넥터* 에서 *Microsoft Azure AD Connect 인증 에이전트* 로 변경되었습니다.

* 통과 인증을 사용하도록 설정하면 기본적으로 암호 해시 동기화가 더 이상 활성화되지 않습니다.


## <a name="115530"></a>1.1.553.0
상태: 2017년 6월

> [!IMPORTANT]
> 이 빌드에서는 스키마 및 동기화 규칙이 변경되었습니다. Azure AD Connect 동기화 서비스는 업그레이드 후에 전체 가져오기 및 전체 동기화 단계를 트리거합니다. 자세한 변경 내용은 다음과 같습니다. 업그레이드 후 전체 가져오기 및 전체 동기화 단계를 일시적으로 연기하려면 [업그레이드 후 전체 동기화를 연기하는 방법](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade) 문서를 참조하세요.
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect 동기화

#### <a name="known-issue"></a>알려진 문제
* Azure AD Connect 동기화와 함께 [OU 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)을 사용하는 고객에게 영향을 주는 문제가 있습니다. Azure AD Connect 마법사의 [도메인 및 OU 필터링 페이지](how-to-connect-install-custom.md#domain-and-ou-filtering)로 이동할 경우 다음과 같은 동작이 예상됩니다.
  * OU 기반 필터링이 사용되는 경우 **선택한 도메인 및 OU 동기화** 옵션이 선택됩니다.
  * 그렇지 않은 경우 **모든 도메인 및 OU 동기화** 옵션이 선택됩니다.

발생하는 문제는 마법사를 실행할 때 항상 **모든 도메인 및 OU 동기화** 가 선택된다는 것입니다.  이 문제는 OU 기반 필터링이 이전에 구성된 경우에도 발생합니다. Azure AD Connect 구성 변경 내용을 저장하기 전에 **선택한 도메인 및 OU 동기화 옵션이 선택되었는지** 확인하고 동기화해야 하는 모든 OU가 다시 활성화되었는지 확인합니다. 그렇지 않으면 OU 기반 필터링이 비활성화됩니다.

#### <a name="fixed-issues"></a>해결된 문제

* Azure AD 관리자가 온-프레미스 AD 권한 있는 사용자 계정의 암호를 다시 설정할 수 있도록 하는, 비밀번호 쓰기 저장과 관련된 문제가 해결되었습니다. 이 문제는 Azure AD Connect에 권한 있는 계정에 대한 암호 다시 설정 권한이 부여된 경우에 발생합니다. 이 버전의 Azure AD Connect에서는 Azure AD 관리자가 계정의 소유자가 아닌 경우 임의의 온-프레미스 AD 권한 있는 사용자 계정의 암호를 다시 설정할 수 없도록 하여 이 문제를 해결합니다. 자세한 내용은 [보안 권고 4033453](/security-updates/SecurityAdvisories/2017/4033453)을 참조하세요.

* Azure AD Connect가 온-프레미스 AD ms-DS-ConsistencyGuid 특성에 쓰기 저장하지 않는, [원본 앵커로서의 ms-DS-ConsistencyGuid](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 기능과 관련된 문제가 해결되었습니다. 이 문제는 여러 개의 온-프레미스 AD 포리스트가 Azure AD Connect에 추가되어 있고 *사용자 ID가 여러 디렉터리에 있습니다. 옵션* 이 선택된 경우에 발생합니다. 이러한 구성을 사용하면 결과로 생성된 동기화 규칙이 메타버스의 sourceAnchorBinary 특성을 채우지 않습니다. sourceAnchorBinary 특성은 ms-DS-ConsistencyGuid 특성에 대한 원본 특성으로 사용됩니다. 결과적으로 ms-DSConsistencyGuid 특성에 대한 쓰기 저장이 발생하지 않습니다. 이 문제를 해결하기 위해 메타버스의 sourceAnchorBinary 특성이 항상 채워지도록 다음 동기화 규칙이 업데이트되었습니다.
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* 이전에는 [원본 앵커로서의 ms-DS-ConsistencyGuid](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 기능이 사용되지 않는 경우에도 “Out to AD – User ImmutableId” 동기화 규칙이 Azure AD Connect에 추가되었습니다. 영향은 무해하며, ms-DS-ConsistencyGuid 특성의 쓰기 저장이 발생하지 않습니다. 혼동을 피하기 위해 기능이 사용되는 경우에만 동기화 규칙이 추가되도록 논리가 추가되었습니다.

* 암호 해시 동기화가 오류 이벤트 611로 실패하게 만드는 문제가 해결되었습니다. 이 문제는 온-프레미스 AD에서 하나 이상의 도메인 컨트롤러가 제거된 후에 발생합니다. 각 암호 동기화 주기가 끝나면 온-프레미스 AD에서 발급된 동기화 쿠키에 제거된 도메인 컨트롤러의 호출 ID와 USN(업데이트 시퀀스 번호) 값 0이 포함됩니다. 암호 동기화 관리자는 USN 값 0이 포함된 동기화 쿠키를 유지할 수 없으며 오류 이벤트 611로 실패합니다. 다음 동기화 주기 동안 암호 동기화 관리자는 USN 값 0을 포함하지 않는 마지막으로 유지된 동기화 쿠키를 다시 사용합니다. 이 경우 동일한 암호 변경 내용을 다시 동기화됩니다. 이 문제가 해결되어 암호 동기화 관리자가 동기화 쿠키를 올바르게 유지합니다.

* 이전에는 자동 업그레이드가 Set-ADSyncAutoUpgrade cmdlet을 사용하여 비활성화된 경우에도 자동 업그레이드 프로세스에서 계속 업그레이드를 정기적으로 확인하고, 다운로드한 설치 관리자를 통해 비활성 상태를 유지했습니다. 이 문제가 해결되어 자동 업그레이드 프로세스에서 더 이상 업그레이드를 정기적으로 확인하지 않습니다. 이 Azure AD Connect 버전용 업그레이드 설치 관리자를 한 번 실행하면 수정 버전이 자동으로 적용됩니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 이전에는 [원본 앵커로서의 ms-DS-ConsistencyGuid](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 기능을 새 배포에만 사용할 수 있었습니다. 이제 기존 배포에도 사용할 수 있습니다. 더 구체적으로 살펴보면 다음과 같습니다.
  * 기능에 액세스하려면 Azure AD Connect 마법사를 시작하고 *원본 앵커 업데이트* 옵션을 선택합니다.
  * 이 옵션은 objectGuid를 sourceAnchor 특성으로 사용하는 기존 배포에만 표시됩니다.
  * 옵션을 구성할 때 마법사는 온-프레미스 Active Directory의 ms-DS-ConsistencyGuid 특성 상태를 확인합니다. 특성이 디렉터리에 있는 사용자 개체에 구성되어 있지 않으면, 마법사는 ms-DS-ConsistencyGuid를 sourceAnchor 특성으로 사용합니다. 특성이 디렉터리에 있는 하나 이상의 사용자 개체에 구성되어 있으면, 마법사는 특성이 다른 애플리케이션에서 사용되고 있으며 sourceAnchor 특성으로 적합하지 않다고 결정하고 원본 앵커 변경이 진행되지 않도록 합니다. 기존 애플리케이션에서 특성을 사용하지 않는다고 확신하는 경우, 오류를 표시하지 않는 방법에 대한 정보를 얻기 위해 지원에 문의해야 합니다.

* 디바이스 개체의 **userCertificate** 특성과 관련해서, Azure AD Connect는 이제 [Windows 10 환경용 Azure AD에 도메인 가입 디바이스를 연결](../devices/hybrid-azuread-join-plan.md)하는 데 필요한 인증서 값을 찾은 다음 Azure AD에 동기화하기 전에 나머지 값을 필터링합니다. 이 동작을 사용하도록 설정하기 위해 기본 제공 동기화 규칙 "Out to AAD - Device Join SOAInAD"가 업데이트되었습니다.

* 이제 Azure AD Connect에서 Exchange Online **cloudPublicDelegates** 특성을 온-프레미스 AD **publicDelegates** 특성에 쓰기 저장하는 기능을 지원합니다. 이렇게 하면 Exchange Online 사서함에 온-프레미스 Exchange 사서함이 있는 사용자에 대한 SendOnBehalfTo 권한을 부여할 수 있는 시나리오가 가능합니다. 이 기능을 지원하기 위해 새로운 기본 제공 동기화 규칙 “Out to AD – User Exchange Hybrid PublicDelegates writeback”이 추가되었습니다. 이 동기화 규칙은 Exchange 하이브리드 기능이 사용되는 경우에만 Azure AD Connect에 추가됩니다.

* 이제 Azure AD Connect에서 Azure AD의 **altRecipient** 특성 동기화를 지원합니다. 이 변경 내용은 지원하기 위해 다음과 같은 기본 제공 동기화 규칙이 필수 특성 흐름을 포함하도록 업데이트되었습니다.
  * AD에서 들어오기 – 사용자 Exchange
  * Out to AAD – User ExchangeOnline

* 메타버스의 **cloudSOAExchMailbox** 특성은 지정된 사용자에게 Exchange Online 사서함에 있는지 여부를 나타냅니다. 해당 정의가 Equipment and Conference Room 사서함 등의 추가 Exchange Online RecipientDisplayTypes를 포함하도록 업데이트되었습니다. 이 변경 내용을 사용하도록 설정하기 위해 기본 제공 동기화 규칙 “In from AAD – User Exchange Hybrid” 아래에 있는 cloudSOAExchMailbox 특성의 정의가

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    에서 다음으로 업데이트되었습니다.

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* userCertificate 특성의 인증서 값을 처리할 동기화 규칙 식을 만들기 위해 다음과 같은 X509Certificate2 호환 함수 집합이 추가되었습니다.
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * 선택
  * CertKeyAlgorithmParams
  * CertHashString
  * Where
  * With

* 고객이 그룹 개체에 대한 sAMAccountName, domainNetBios 및 domainFQDN과 사용자 개체에 대한 distinguishedName을 전달하는 사용자 지정 동기화 규칙을 만들 수 있도록 스키마가 다음과 같이 변경되었습니다.

  * 다음 특성이 MV 스키마에 추가되었습니다.
    * 그룹: AccountName
    * 그룹: domainNetBios
    * 그룹: domainFQDN
    * 사용자: distinguishedName

  * 다음 특성이 Azure AD Connector 스키마에 추가되었습니다.
    * 그룹: OnPremisesSamAccountName
    * 그룹: NetBiosName
    * 그룹: DnsDomainName
    * 사용자: OnPremisesDistinguishedName

* 이제 ADSyncDomainJoinedComputerSync cmdlet 스크립트에 AzureEnvironment라는 새로운 선택적 매개 변수가 있습니다. 이 매개 변수는 해당 Azure Active Directory 테넌트가 호스트되는 지역을 지정하는 데 사용됩니다. 유효한 값은 다음과 같습니다.
  * AzureCloud(기본값)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* 동기화 규칙을 만드는 동안 링크 형식의 기본값으로 준비 대신 연결을 사용하도록 동기화 규칙 편집기를 업데이트합니다.

### <a name="ad-fs-management"></a>AD FS 관리

#### <a name="issues-fixed"></a>해결된 문제

* 다음 URL은 Azure AD에서 인증 중단 시 복원력을 향상하기 위해 도입된 새로운 WS-Federation 엔드포인트로, 온-프레미스 AD FS 신뢰 당사자 트러스트 구성에 추가됩니다.
  * https:\//ests.login.microsoftonline.com/login.srf
  * https:\//stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* AD FS에서 IssuerID에 대해 잘못된 클레임 값이 생성되도록 하는 문제가 해결되었습니다. 이 문제는 Azure AD 테넌트에 확인된 도메인이 여러 개 있고 IssuerID 클레임을 생성하는 데 사용된 userPrincipalName 특성의 도메인 접미사가 3 수준 이상의 깊이(예: johndoe@us.contoso.com)인 경우에 발생합니다. 클레임 규칙에 사용된 정규식을 업데이트하면 문제가 해결됩니다.

#### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
* 이전에는 Azure AD Connect에서 제공하는 ADFS 인증서 관리 기능을 Azure AD Connect로 관리되는 ADFS 팜에서만 사용할 수 있었습니다. 이제 Azure AD Connect를 사용하여 관리되지 않는 ADFS 팜에서 이 기능을 사용할 수 있습니다.

## <a name="115240"></a>1.1.524.0
릴리스 날짜: 2017년 5월

> [!IMPORTANT]
> 이 빌드에서는 스키마 및 동기화 규칙이 변경되었습니다. Azure AD Connect 동기화 서비스는 업그레이드 후에 전체 가져오기 및 전체 동기화 단계를 트리거합니다. 자세한 변경 내용은 다음과 같습니다.
>
>
**수정된 문제:**

Azure AD Connect 동기화

* 고객이 Set-ADSyncAutoUpgrade cmdlet을 사용하여 자동 업그레이드 기능을 사용하지 않도록 설정한 경우에도 Azure AD Connect 서버에서 이 업그레이드가 발생하는 문제를 해결했습니다. 이 수정 프로그램을 통해 서버의 자동 업그레이드 프로세스에서 정기적으로 업그레이드를 계속 확인하지만 다운로드한 설치 관리자는 자동 업그레이드 구성을 준수합니다.
* DirSync 전체 업그레이드 중에 Azure AD Connect는 Azure AD 커넥터에서 Azure AD와 동기화하는 데 사용할 Azure AD 서비스 계정을 만듭니다. 계정을 만든 후에는 Azure AD Connect는 이 계정을 사용하여 Azure AD를 인증합니다. 때로는 일시적인 문제로 인해 인증이 실패하고, 이로 인해 *"AAD Sync 구성 작업을 실행하는 동안 오류가 발생했습니다: AADSTS50034: 이 애플리케이션에 로그인하려면 xxx.onmicrosoft.com 디렉터리에 계정을 추가해야 합니다"* 라는 오류 메시지와 함께 DirSync 현재 위치 업그레이드가 실패합니다. Azure AD Connect는 이제 DirSync 업그레이드의 복원력을 향상시키기 위해 인증 단계를 다시 시도합니다.
* DirSync 전체 업그레이드가 성공하지만 디렉터리 동기화에 필요한 실행 프로필을 만들지 않는 443 빌드 문제가 있었습니다. 이 문제를 해결하는 논리가 Azure AD Connect의 이 빌드에 포함되었습니다. 고객이 이 빌드로 업그레이드할 때 Azure AD Connect에서 누락된 실행 프로필을 검색하고, 없는 경우 해당 프로필을 만듭니다.
* 6900 이벤트 ID 및 *"동일한 키가 있는 항목이 이미 추가되었습니다."* 라는 오류 메시지와 함께 암호 동기화 프로세스가 시작되지 않는 문제를 해결했습니다. AD 구성 파티션을 포함하도록 OU 필터링 구성을 업데이트하면 이 문제가 발생합니다. 이 문제를 해결하기 위해 암호 동기화 프로세스는 이제 AD 도메인 파티션의 암호 변경만 동기화합니다. 구성 파티션과 같은 비도메인 파티션은 건너뜁니다.
* Azure AD Connect는 기본 설치 중에 AD 커넥터에서 온-프레미스 AD와 통신하는 데 사용할 온-프레미스 AD DS 계정을 만듭니다. 이전에는 user-Account-Control 특성에 설정된 PASSWD_NOTREQD 플래그가 있는 계정을 만들어 이 계정에 임의의 암호를 설정했습니다. 이제 Azure AD Connect는 계정에 암호를 설정한 후에 PASSWD_NOTREQD 플래그를 명시적으로 제거합니다.
* mailNickname 특성이 온-프레미스 AD 스키마에 있지만 AD User 개체 클래스에 바인딩되지 않으면 *"애플리케이션 잠금을 획득하려고 하는 SQL 서버에서 교착 상태가 발생했습니다."* 라는 오류 메시지와 함께 DirSync 업그레이드가 실패하는 문제를 해결했습니다.
* 관리자가 Azure AD Connect 마법사를 사용하여 Azure AD Connect 동기화 구성을 업데이트할 때 디바이스 쓰기 저장 기능을 자동으로 비활성화하는 문제를 해결했습니다. 이 문제는 마법사에서 온-프레미스 AD의 기존 디바이스 쓰기 저장 구성에 대한 필수 조건 확인을 수행함으로써 발생하는 것이며, 이 확인은 실패합니다. 이제 디바이스 쓰기 저장을 사용하도록 이미 설정되어 있는지 확인하는 것을 건너뛰도록 했습니다.
* OU 필터링을 구성하려면 Azure AD Connect 마법사 또는 동기화 서비스 관리자를 사용하면 됩니다. 이전에는 Azure AD Connect 마법사를 사용하여 OU 필터링을 구성하는 경우 그 후에 만든 새 OU가 디렉터리 동기화에 포함되었습니다. 새 OU를 포함하지 않도록 하려면 동기화 서비스 관리자를 사용하여 OU 필터링을 구성해야 합니다. 이제는 Azure AD Connect 마법사를 사용하여 동일한 동작을 수행할 수 있습니다.
* Azure AD Connect에 필요한 저장 프로시저를 dbo 스키마 대신 설치 관리자 스키마에서 만드는 문제를 해결했습니다.
* Azure AD에서 반환한 TrackingId 특성이 Azure AD Connect 서버 이벤트 로그에서 생략되는 문제를 해결했습니다. Azure AD Connect에서 Azure AD로부터 리디렉션 메시지를 받고 제공된 엔드포인트에 연결할 수 없는 경우에 이 문제가 발생합니다. TrackingId는 지원 엔지니어가 문제를 해결하는 동안 서비스 쪽 로그와 상호 연결하는 데 사용됩니다.
* Azure AD Connect에서 Azure AD로부터 LargeObject 오류를 받으면 6941 EventID 및 *"프로비전된 개체가 너무 큽니다. 이 개체의 특성 값 수를 조정하십시오."* 라는 메시지가 포함된 이벤트를 생성합니다. 동시에 Azure AD Connect에서는 6900 EventID 및 *"Microsoft.Online.Coexistence.ProvisionRetryException: Windows Azure Active Directory 서비스와 통신할 수 없습니다"* 라는 메시지가 포함된 잘못된 이벤트도 생성합니다. 혼란을 최소화하기 위해 Azure AD Connect에서 LargeObject 오류를 받으면 더 이상 후자의 이벤트를 생성하지 않습니다.
* 일반 LDAP 커넥터의 구성을 업데이트하려고 할 때 동기화 서비스 관리자에서 응답하지 않는 문제를 해결했습니다.

**새 기능/향상된 기능:**

Azure AD Connect 동기화
* 동기화 규칙 변경 - 다음 동기화 규칙 변경 내용을 구현했습니다.
  * 특성에 15개 이상의 값이 있는 경우 **userCertificate** 및 **userSMIMECertificate** 특성을 내보내지 않도록 기본 동기화 규칙 집합을 업데이트했습니다.
  * **employeeID** 및 **msExchBypassModerationLink** AD 특성은 이제 기본 동기화 규칙 집합에 포함되어 있습니다.
  * **photo** AD 특성은 기본 동기화 규칙 집합에서 제거되었습니다.
  * **preferredDataLocation** 을 메타버스 스키마 및 Azure AD 커넥터 스키마에 추가했습니다. Azure AD에서 두 특성 중 하나를 업데이트하려는 고객은 사용자 지정 동기화 규칙을 구현하여 해당 특성을 업데이트할 수 있습니다. 
  * **userType** 을 메타버스 스키마 및 Azure AD 커넥터 스키마에 추가했습니다. Azure AD에서 두 특성 중 하나를 업데이트하려는 고객은 사용자 지정 동기화 규칙을 구현하여 해당 특성을 업데이트할 수 있습니다.

* Azure AD Connect는 이제 ConsistencyGuid 특성을 온-프레미스 AD 개체에 대한 원본 앵커 특성으로 사용하도록 자동으로 설정합니다. 또한 Azure AD Connect는 ConsistencyGuid 특성이 비어 있는 경우 이 특성을 objectGuid 특성 값으로 채웁니다. 이 기능은 새 배포에만 적용됩니다. 이 기능에 대한 자세한 내용은 [Azure AD Connect: 설계 개념 - ms-DS-ConsistencyGuid를 sourceAnchor로 사용](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 문서 섹션을 참조하세요.
* 암호 해시 동기화 관련 문제를 진단하는 데 도움이 되는 새 Invoke-ADSyncDiagnostics 문제 해결 cmdlet을 추가했습니다. cmdlet 사용 방법에 대한 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md) 아티클을 참조하세요.
* Azure AD Connect는 이제 온-프레미스 AD에서 Azure AD로 메일 사용 가능 공용 폴더 개체 동기화를 지원합니다. Azure AD Connect 마법사를 사용하여 [선택적 기능]에서 이 기능을 사용하도록 설정할 수 있습니다. 이 기능에 대한 자세한 내용은 [Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218)(온-프레미스 메일 사용이 가능한 공용 폴더에 대한 Office 365 디렉터리 기반 에지 차단 지원) 문서를 참조하세요.
* 온-프레미스 AD에서 동기화하려면 Azure AD Connect에 AD DS 계정이 필요합니다. 이전에 Express 모드를 사용하여 Azure AD Connect를 설치한 경우 엔터프라이즈 관리자 계정의 자격 증명을 제공할 수 있고 Azure AD Connect에서는 필요한 AD DS 계정을 만듭니다. 그러나 사용자 지정 설치하거나 기존 배포에 포리스트를 추가하는 경우에는 AD DS 계정을 대신 제공해야 합니다. 이제는 사용자 지정 설치 중에 엔터프라이즈 관리자 계정의 자격 증명을 제공하고 Azure AD Connect에서 필요한 AD DS 계정을 만들 수 있습니다.
* Azure AD Connect는 이제 SQL AOA를 지원합니다. Azure AD Connect를 설치하기 전에 SQL AOA를 사용하도록 설정해야 합니다. Azure AD Connect는 설치 중에 제공된 SQL 인스턴스에서 SQL AOA를 사용하도록 설정되었는지 여부를 검색합니다. SQL AOA를 사용하도록 설정된 경우 Azure AD Connect는 SQL AOA에서 동기 복제 또는 비동기 복제를 사용하도록 구성되어 있는지 확인합니다. 가용성 그룹 수신기를 설정할 때 RegisterAllProvidersIP 속성을 0으로 설정하는 것이 좋습니다. 이를 권장하는 것은 Azure AD Connect에서 현재 SQL Native Client를 사용하여 SQL에 연결하고, SQL Native Client에서는 MultiSubNetFailover 속성 사용을 지원하지 않기 때문입니다.
* Azure AD Connect 서버의 데이터베이스로 LocalDB를 사용하고 10GB 크기 제한에 도달하면 동기화 서비스가 더 이상 시작되지 않습니다. 이전에는 LocalDB에서 ShrinkDatabase 작업을 수행하여 동기화 서비스를 시작할 수 있을 만큼 충분한 DB 공간을 확보해야 했습니다. 그런 후에 더 많은 DB 공간을 확보할 수 있도록 동기화 서비스 관리자를 사용하여 실행 기록을 삭제했습니다. 이제는 Start-ADSyncPurgeRunHistory cmdlet을 사용하여 LocalDB의 실행 기록 데이터를 제거하여 DB 공간을 확보할 수 있습니다. 또한 이 cmdlet은 동기화 서비스가 실행되고 있지 않을 때 -offline 매개 변수를 지정하여 사용할 수 있는 오프라인 모드를 지원합니다. 참고: 오프라인 모드는 동기화 서비스가 실행되고 있지 않고 사용되는 데이터베이스가 LocalDB인 경우에만 사용할 수 있습니다.
* Azure AD Connect는 이제 필요한 스토리지 공간을 줄이기 위해 먼저 동기화 오류 세부 정보를 압축한 후에 LocalDB/SQL 데이터베이스에 스토리지합니다. 이전 버전의 Azure AD Connect에서 이 버전으로 업그레이드할 때 Azure AD Connect는 기존 동기화 오류 세부 정보를 한 번 압축합니다.
* 이전에는 OU 필터링 구성을 업데이트한 후에 디렉터리 동기화에서 기존 개체가 올바르게 포함되거나 제외되도록 전체 가져오기를 수동으로 실행해야 했습니다. 이제는 Azure AD Connect에서 다음 동기화 주기 중에 전체 가져오기를 자동으로 트리거합니다. 또한 전체 가져오기는 업데이트로 영향을 받는 AD 커넥터에만 적용됩니다. 참고: 향상된 이 기능은 Azure AD Connect 마법사를 사용하여 만든 OU 필터링 업데이트에만 적용되며, 동기화 서비스 관리자를 사용하여 만든 OU 필터링 업데이트에는 적용되지 않습니다.
* 이전에는 그룹 기반 필터링에서 Users, Groups 및 Contact 개체만 지원합니다. 이제는 그룹 기반 필터링에서 Computer 개체도 지원합니다.
* 이전에는 Azure AD Connect 동기화 스케줄러를 사용하여 커넥터 공간 데이터를 삭제할 수 있었습니다. 이제는 동기화 서비스 관리자에서 스케줄러를 사용할 수 있음을 감지하면 커넥터 공간 데이터의 삭제를 차단합니다. 또한 커넥터 공간 데이터가 삭제되면 고객에게 잠재적인 데이터 손실에 대해 알리는 경고가 반환됩니다.
* 이전에는 Azure AD Connect 마법사를 제대로 실행하기 위해 PowerShell 기록을 사용하지 않도록 설정해야 했습니다. 이 문제는 부분적으로 해결되었습니다. Azure AD Connect 마법사를 사용하여 동기화 구성을 관리하는 경우 PowerShell 기록을 사용하도록 설정할 수 있지만, Azure AD Connect 마법사를 사용하여 ADFS 구성을 관리하는 경우에는 PowerShell 기록을 사용하지 않도록 설정해야 합니다.



## <a name="114860"></a>1.1.486.0
릴리스 날짜: 2017년 4월

**수정된 문제:**
* Azure AD Connect가 지역화된 버전의 Windows Server에 제대로 설치되지 않는 문제를 해결했습니다.

## <a name="114840"></a>1.1.484.0
릴리스 날짜: 2017년 4월

**알려진 문제:**

* 다음 조건에 모두 해당하면 이 버전의 Azure AD Connect가 제대로 설치되지 않습니다.
   1. DirSync 전체 업그레이드 또는 Azure AD Connect 새로 설치를 수행하고 있습니다.
   2. 서버에서 기본 제공 관리자 그룹의 이름이 “Administrators”가 아닌 지역화된 버전의 Windows Server를 사용하고 있습니다.
   3. 자체적인 전체 SQL을 제공하는 대신 Azure AD Connect와 함께 설치된 기본 SQL Server 2012 Express LocalDB를 사용하고 있습니다.

**수정된 문제:**

Azure AD Connect 동기화
* 하나 이상의 커넥터에 해당 동기화 단계에 대한 실행 프로필이 없는 경우 동기화 스케줄러가 전체 동기화 단계를 건너뛰는 문제를 해결했습니다. 예를 들어 델타 가져오기 실행 프로필을 만들지 않고 Synchronization Service Manager를 사용하여 커넥터를 수동으로 추가했습니다. 이 수정을 통해 동기화 스케줄러는 다른 커넥터에 대한 델타 가져오기를 계속 실행할 수 있습니다.
* 실행 단계 중 하나에 문제가 발생할 때 동기화 서비스가 실행 프로필 처리를 즉시 중지하는 문제를 해결했습니다. 이 수정을 통해 동기화 서비스는 해당 실행 단계를 건너뛰고 나머지 단계를 계속 처리합니다. 예를 들어 여러 실행 단계가 포함된 AD Connector에 대한 델타 가져오기 실행 프로필이 있습니다(각 온-프레미스 AD 도메인에 하나씩). 동기화 서비스는 다른 AD 도메인 중 하나에 네트워크 연결 문제가 있는 경우에도 해당 AD 도메인에서 델타 가져오기를 실행합니다.
* 자동 업그레이드 중에 Azure AD Connector 업데이트를 건너뛰는 문제를 해결했습니다.
* 설치 중에 서버가 도메인 컨트롤러인지 여부를 Azure AD Connect가 잘못 확인하여 이로 인해 DirSync 업그레이드가 실패하는 문제를 해결했습니다.
* DirSync 전체 업그레이드에서 Azure AD Connector에 대한 실행 프로필을 만들지 않는 문제를 해결했습니다.
* 일반 LDAP 커넥터를 구성할 때 Synchronization Service Manager 사용자 인터페이스가 응답하지 않는 문제를 해결했습니다.

AD FS 관리
* AD FS 주 노드가 다른 서버로 이동된 경우 Azure AD Connect 마법사가 실패하는 문제를 해결했습니다.

데스크톱 SSO
* 새로 설치하는 동안 로그인 옵션으로 암호 동기화를 선택한 경우 [로그인] 화면에서 데스크톱 SSO를 사용하도록 설정할 수 없는 Azure AD Connect 마법사의 문제를 해결했습니다.

**새 기능/향상된 기능:**

Azure AD Connect 동기화
* 이제 Azure AD Connect 동기화에서 가상 서비스 계정, 관리 서비스 계정 및 그룹 관리 서비스 계정을 서비스 계정으로 사용할 수 있습니다. 이 내용은 Azure AD Connect만 새로 설치하는 경우에만 적용됩니다. Azure AD Connect를 설치할 경우:
    * 기본적으로 Azure AD Connect 마법사는 가상 서비스 계정을 만들고 이를 서비스 계정으로 사용합니다.
    * 도메인 컨트롤러에서 설치할 경우 Azure AD Connect는 도메인 사용자 계정을 만들고 이를 서비스 계정으로 대신 사용하는 이전 동작으로 대체됩니다.
    * 다음 중 하나를 제공하여 기본 동작을 재정의할 수 있습니다.
      * 그룹 관리 서비스 계정
      * 관리 서비스 계정
      * 도메인 사용자 계정
      * 로컬 사용자 계정
* 이전에는 커넥터 업데이트 또는 동기화 규칙 변경 내용이 포함된 Azure AD Connect의 새 빌드로 업그레이드할 경우 Azure AD Connect는 전체 동기화 주기를 트리거합니다. 이제 Azure AD Connect는 선택적으로 업데이트가 있는 커넥터에 대해서만 전체 가져오기 단계를 트리거하고 동기화 규칙 변경 내용이 있는 커넥터에 대해서만 전체 동기화 단계를 트리거합니다.
* 이전에는 내보내기 삭제 임계값은 동기화 스케줄러를 통해 트리거된 내보내기에만 적용됩니다. 이제 이 기능은 Synchronization Service Manager를 사용하여 고객이 수동으로 트리거한 내보내기를 포함하도록 확장됩니다.
* Azure AD 테넌트에는 테넌트에 대해 암호 동기화 기능을 사용할지 여부를 나타내는 서비스 구성이 있습니다. 이전에는 활성화된 스테이징 서비스가 있을 경우 Azure AD Connect에서 서비스 구성을 잘못 구성하기 쉽습니다. 이제 Azure AD Connect는 서비스 구성을 오직 활성 Azure AD Connect 서버와 일치하게 유지하려고 합니다.
* Azure AD Connect 마법사는 이제 온-프레미스 AD에서 AD 휴지통이 사용되지 않는 경우 경고를 감지하고 반환합니다.
* 이전에는 일괄 처리의 전체 개체 크기가 특정 임계값을 초과할 경우 Azure AD로 내보내기의 시간이 초과되고 내보내기에 실패합니다. 이제 동기화 서비스는 문제가 발생한 경우 별도의 더 작은 일괄 처리로 개체를 다시 보내려고 재시도합니다.
* 동기화 서비스 키 관리 애플리케이션이 Windows [시작] 메뉴에서 제거되었습니다. 암호화 키 관리는 miiskmu.exe를 사용하는 명령줄 인터페이스를 통해 계속 지원됩니다. 암호화 키 관리에 대한 자세한 내용은 [Abandoning the Azure AD Connect Sync encryption key](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key)(Azure AD Connect 동기화 암호화 키 중단) 문서를 참조하세요.
* 이전에는 Azure AD Connect 동기화 서비스 계정 암호를 변경하면 암호화 키를 제거하고 Azure AD Connect 동기화 서비스 계정 암호를 다시 초기화할 때까지 동기화 서비스를 제대로 시작할 수 없습니다. 이제 이 프로세스는 더 이상 필요하지 않습니다.

데스크톱 SSO

* Azure AD Connect 마법사는 패스스루 인증 및 데스크톱 SSO를 구성할 때 더 이상 네트워크에서 포트 9090을 열 필요가 없습니다. 포트 443만 필요합니다.

## <a name="114430"></a>1.1.443.0
릴리스 날짜: 2017년 3월

**수정된 문제:**

Azure AD Connect 동기화
* Azure AD Connect의 표시 이름에 Azure AD 테넌트에 할당된 초기 onmicrosoft.com 도메인이 포함되어 있지 않을 경우 Azure AD Connect 마법사가 작동되지 않는 문제가 해결되었습니다.
* 동기화 서비스 계정의 암호에 아포스트로피, 콜론 및 공백 등의 특수 문자가 포함될 경우 SQL Database에 연결하는 동안 Azure AD Connect 마법사가 실패하는 문제가 해결되었습니다.
* 온-프레미스 AD 개체를 동기화에서 일시적으로 제외시킨 다음, 동기화를 위해 다시 포함시킨 경우 스테이징 모드의 Azure AD Connect 서버에서 "이미지에 이미지와는 다른 앵커가 있습니다." 오류를 발생하는 문제가 해결되었습니다.
* 온-프레미스 AD 개체를 동기화에서 일시적으로 제외시킨 다음 동기화를 위해 다시 포함시킨 경우 스테이징 모드의 Azure AD Connect 서버에서 "DN에서 찾은 개체가 가상입니다." 오류를 발생하는 문제가 해결되었습니다.

AD FS 관리
* 대체 로그인 ID가 구성된 후 Azure AD Connect 마법사가 신뢰 당사자 트러스터에 대해 AD FS 구성을 업데이트하지 않고 적절한 클레임을 설정하지 않는 문제가 해결되었습니다.
* Azure AD Connect 마법사가 해당 서비스 계정이 sAMAccountName 형식 대신 userPrincipalName 형식을 사용하여 구성된 AD FS 서버를 올바르게 처리할 수 없는 문제가 해결되었습니다.

통과 인증
* 통과 인증이 선택되었으나 해당 커넥터 등록이 실패하는 경우 Azure AD Connect 마법사가 실패하도록 하는 문제가 해결되었습니다.
* 데스크톱 SSO 기능이 사용되도록 설정된 경우 Azure AD Connect 마법사가 선택된 로그인 방법에 대한 유효성 검사를 무시하도록 하는 문제가 해결되었습니다.

암호 재설정
* 연결이 방화벽 또는 프록시에 의해 중단된 경우 Azure AD Connect 서버가 다시 연결하려고 하지 않을 수 있는 문제를 해결했습니다.

**새 기능/향상된 기능:**

Azure AD Connect 동기화
* 이제 Get-ADSyncScheduler cmdlet은 SyncCycleInProgress라는 새 부울 속성을 반환합니다. 반환된 값이 true이면 진행 중인 예약된 동기화 주기가 있는 것입니다.
* 로그 파일에 대한 접근성 개선을 위해 Azure AD Connect 설치 및 설정 로그를 저장하기 위한 대상 폴더가 %localappdata%\AADConnect에서 %programdata%\AADConnect로 이동되었습니다.

AD FS 관리
* AD FS 팜 TLS/SSL 인증서를 업데이트하기 위한 지원이 추가되었습니다.
* AD FS 2016을 관리하기 위한 지원이 추가되었습니다.
* 이제 AD FS를 설치하는 동안 기존 gMSA(그룹 관리 서비스 계정)를 지정할 수 있습니다.
* 이제 Azure AD 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘으로 SHA-256을 구성할 수 있습니다.

암호 재설정
* 제품이 더 엄격한 방화벽 규칙이 포함된 환경에서 작동할 수 있도록 향상된 기능을 도입했습니다.
* Azure Service Bus에 대한 향상된 연결 안정성.

## <a name="113800"></a>1.1.380.0
릴리스 날짜: 2016년 12월

**해결된 문제:**

* AD FS(Active Directory Federation Services)에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되는 문제를 해결했습니다.

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.
## <a name="113710"></a>1.1.371.0
릴리스 날짜: 2016년 12월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD(Azure Active Directory)로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](how-to-connect-install-multiple-domains.md)에 대한 다음 문서를 참조하세요.

**해결된 문제:**

* 포트 9090이 아웃바운드 연결에 대해 열려있지 않으면 Azure AD Connect 설치 또는 업그레이드가 실패합니다.

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.
## <a name="113700"></a>1.1.370.0
릴리스 날짜: 2016년 12월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](how-to-connect-install-multiple-domains.md)에 대한 다음 문서를 참조하세요.
* 설치를 완료하려면 포트 9090은 아웃바운드에 열려있어야 합니다.

**새로운 기능:**

* 통과 인증(미리 보기)

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.
## <a name="113430"></a>1.1.343.0
릴리스 날짜: 2016년 11월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](how-to-connect-install-multiple-domains.md)에 대한 다음 문서를 참조하세요.

**수정된 문제:**

* 조직의 암호 정책으로 지정된 복잡성 수준을 충족하는 로컬 서비스 계정을 만들 수 없기 때문에 Azure AD Connect 설치에 실패하는 경우가 있습니다.
* 커넥터 공간에 있는 개체가 동시에 하나의 조인 규칙의 범위를 벗어나게 되고 다른 규칙의 범위에 포함되는 경우 조인 규칙이 다시 평가되지 않는 문제가 해결되었습니다. 이런 상황은 조인 조건이 서로 배타적인 조인 규칙이 둘 이상인 경우 발생할 수 있습니다.
* 조인 규칙을 포함하지 않는 인바운드 동기화 규칙(Azure AD의)이 조인 규칙을 포함하는 규칙보다 낮은 우선 순위 값을 갖는 경우 처리되지 않는 문제가 해결되었습니다.

**향상된 기능:**

* Windows Server 2016 Standard 이상에 Azure AD Connect를 설치할 수 있도록 지원이 추가되었습니다.
* Azure AD Connect에 대한 원격 데이터베이스로 SQL Server 2016을 사용하는 데 대한 지원이 추가되었습니다.

## <a name="112810"></a>1.1.281.0
릴리스 날짜: 2016년 8월

**수정된 문제:**

* 다음 동기화 주기가 완료될 때까지 동기화 간격 변경이 발생하지 않습니다.
* Azure AD Connect 마법사는 사용자 이름이 밑줄(\_)로 시작하는 Azure AD 계정을 허용하지 않습니다.
* 계정 암호에 특수 문자가 너무 많으면 Azure AD Connect 마법사가 Azure AD 계정 인증에 실패합니다. 오류 메시지 "자격 증명의 유효성을 검사할 수 없습니다. 예기치 않은 오류가 발생했습니다." 가 반환됩니다.
* 스테이징 서버를 제거하면 Azure AD 테넌트의 암호 동기화가 비활성화되고 암호 동기화가 활성 서버와 함께 실패합니다.
* 암호 동기화는 사용자에 저장된 암호 해시가 없는 특수한 경우에 실패합니다.
* Azure AD Connect 서버가 준비 모드에서 활성화된 경우 비밀번호 쓰기 저장은 일시적으로 비활성화되지 않습니다.
* Azure AD Connect 마법사는 서버가 준비 모드에 있을 때 실제 암호 동기화 및 비밀번호 쓰기 저장 구성을 표시하지 않습니다. 항상 비활성화로 표시합니다.
* 서버가 준비 모드에 있을 때 암호 동기화 및 비밀번호 쓰기 저장에 대한 구성 변경 내용은 Azure AD Connect 마법사에서 유지되지 않습니다.

**향상된 기능:**

* 새 동기화 주기를 성공적으로 시작할 수 있는지 여부를 표시하도록 Start-ADSyncSyncCycle cmdlet이 업데이트되었습니다.
* 현재 진행 중인 동기화 주기 및 작업을 종료하는 Stop-ADSyncSyncCycle cmdlet이 추가되었습니다.
* 현재 진행 중인 동기화 주기 및 작업을 동기화 주기 및 작업을 종료하는 Stop-ADSyncScheduler cmdlet이 업데이트되었습니다.
* Azure AD Connect 마법사에서 [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 구성할 때 이제 "Teletex 문자열" 형식의 AD 특성을 선택할 수 있습니다.

## <a name="111890"></a>1.1.189.0
릴리스 날짜: 2016년 6월

**수정된 문제 및 향상된 기능:**

* 이제 Azure AD Connect를 FIPS 규격 서버에 설치할 수 있습니다.
  * 암호 동기화에 대해서는 [암호 해시 동기화 및 FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)를 참조하세요.
* Active Directory Connector에서 NetBIOS 이름을 FQDN으로 확인할 수 없던 문제를 해결했습니다.

## <a name="111800"></a>1.1.180.0
릴리스 날짜: 2016년 5월

**새로운 기능:**

* 사용자가 Azure AD Connect를 실행하기 전에 도메인을 확인하지 않았으면 사용자에게 그 사실을 알리고 확인할 수 있도록 도와줍니다.
* [Microsoft Cloud 독일](reference-connect-instances.md#microsoft-cloud-germany)에 대한 지원을 추가했습니다.
* 새 URL 요구 사항을 갖는 최신 [Microsoft Azure Government 클라우드](reference-connect-instances.md#microsoft-azure-government) 인프라에 대한 지원을 추가했습니다.

**수정된 문제 및 향상된 기능:**

* 쉽게 동기화 규칙을 찾을 수 있도록 동기화 규칙 편집기에 필터링을 추가했습니다.
* 커넥터 공간을 삭제할 경우 성능이 향상되었습니다.
* 동일한 개체가 동일한 실행에서 모두 삭제되고 추가되는 경우(삭제/추가라고 함) 발생하는 문제가 수정되었습니다.
* 비활성화된 동기화 규칙은 더 이상 업그레이드 또는 디렉터리 스키마 새로 고침 시 포함된 개체 및 특성을 다시 활성화하지 않습니다.

## <a name="111300"></a>1.1.130.0
릴리스 날짜: 2016년 4월

**새로운 기능:**

* [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)에 다중 값 특성에 대한 지원이 추가되었습니다.
* 업그레이드 가능한 것으로 간주되는 [자동 업그레이드](how-to-connect-install-automatic-upgrade.md) 의 자세한 구성 변형에 대한 지원이 추가되었습니다.
* [사용자 지정 스케줄러](how-to-connect-sync-feature-scheduler.md#custom-scheduler)에 대한 일부 cmdlet이 추가되었습니다.

## <a name="111190"></a>1.1.119.0
릴리스 날짜: 2016년 3월

**수정된 문제:**

* Windows Server 2008(R2 이전 버전)에는 암호 동기화가 지원되지 않기 때문에 이 운영 체제에서는 빠른 설치를 사용할 수 없습니다.
* 사용자 지정 필터가 구성된 Dirsync에서 업그레이드가 예상대로 작동하지 않습니다.
* 새 릴리스로 업그레이드하고 구성이 변경되지 않은 경우 전체 가져오기/동기화를 예약하지 않아야 합니다.

## <a name="111100"></a>1.1.110.0
릴리스 날짜: 2016년 2월

**수정된 문제:**

* 설치 파일이 기본 C:\Program Files 폴더에 없는 경우 이전 릴리스에서 업그레이드되지 않습니다.
* 설치하고 설치 마법사의 마지막 단계에서 **동기화 프로세스 시작** 을 선택 취소하면 설치 마법사를 다시 실행해도 스케줄러를 사용할 수 없습니다.
* 날짜/시간 형식이 US-en이 아닌 서버에서는 스케줄러가 작동하지 않습니다. 또한 올바른 시간을 반환하도록 `Get-ADSyncScheduler`도 차단합니다.
* 로그인 옵션으로 AD FS를 사용하는 Azure AD Connect의 이전 릴리스를 설치하고 업그레이드하는 경우 설치 마법사를 다시 실행할 수 없습니다.

## <a name="111050"></a>1.1.105.0
릴리스 날짜: 2016년 2월

**새로운 기능:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) 기능
* 설치 마법사에서 Azure AD Multi-Factor Authentication 및 Privileged Identity Management를 사용하여 전역 관리자를 지원합니다.
  * Multi-Factor Authentication을 사용하는 경우 프록시에서 https://secure.aadcdn.microsoftonline-p.com 에 대한 트래픽을 허용하도록 설정해야 합니다.
  * Multi-Factor Authentication이 제대로 작동하려면 신뢰할 수 있는 사이트 목록에 https://secure.aadcdn.microsoftonline-p.com 을 추가해야 합니다.
* 초기 설치 후 사용자의 로그인 방법 변경을 허용합니다.
* 설치 마법사에서 [도메인 및 OU 필터링](how-to-connect-install-custom.md#domain-and-ou-filtering) 을 허용합니다. 이는 일부 도메인을 사용할 수 없는 포리스트로의 연결도 허용합니다.
* 동기화 엔진에 [Scheduler](how-to-connect-sync-feature-scheduler.md)가 기본 제공됩니다.

**미리 보기에서 GA로 승격되는 기능:**

* [디바이스 쓰기 저장](how-to-connect-device-writeback.md)
* [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)

**새로운 미리 보기 기능:**

* 새 기본 동기화 주기 간격은 30분입니다. 이전 릴리스에서는 3시간이었습니다. [스케줄러](how-to-connect-sync-feature-scheduler.md) 동작 변경 지원이 추가됩니다.

**수정된 문제:**

* DNS 도메인 확인 페이지에서 도메인을 항상 인식하지 않았습니다.
* AD FS를 구성할 때 도메인 관리자 자격 증명을 묻는 메시지가 표시됩니다.
* 온-프레미스 AD 계정이 루트 도메인이 아닌 다른 DNS 트리를 사용하는 도메인에 있는 경우 설치 마법사에서 인식하지 못합니다.

## <a name="1091310"></a>1.0.9131.0
릴리스 날짜: 2015년 12월

**수정된 문제:**

* AD DS(Active Directory Domain Services)에서 암호를 변경할 때는 암호 동기화가 작동하지 않을 수도 있지만 암호를 설정할 때는 작동합니다.
* 프록시 서버를 보유한 경우 설치하는 동안 또는 구성 페이지에서 업그레이드를 취소하면 Azure AD 인증이 실패할 수 있습니다.
* SQL Server SA(시스템 관리자)가 아닌 경우 전체 SQL Server 인스턴스를 사용하여 Azure AD Connect 이전 릴리스를 업데이트하면 업데이트가 실패합니다.
* 원격 SQL Server를 사용하여 Azure AD Connect 이전 릴리스를 업데이트하면 "ADSync SQL database에 액세스할 수 없습니다."라는 오류가 표시됩니다.

## <a name="1091250"></a>1.0.9125.0
릴리스 날짜: 2015년 11월

**새로운 기능:**

* AD FS와 Azure AD 간의 트러스트를 다시 구성할 수 있습니다.
* Active Directory 스키마를 새로 고치고 동기화 규칙을 다시 생성할 수 있습니다.
* 동기화 규칙을 사용하지 않도록 설정할 수 있습니다.
* 동기화 규칙에서 새 리터럴로 "AuthoritativeNull"을 정의할 수 있습니다.

**새로운 미리 보기 기능:**

* [동기화를 위한 Azure AD Connect Health](how-to-connect-health-sync.md).
* [Azure AD 도메인 서비스](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e) 암호 동기화를 지원합니다.

**지원되는 새 시나리오:**

* 여러 온-프레미스 Exchange 조직을 지원합니다. 자세한 내용은 [여러 Active Directory 포리스트가 있는 하이브리드 배포](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))를 참조하세요.

**수정된 문제:**

* 암호 동기화 문제:
  * 범위 외부에서 범위 내부로 이동된 개체는 해당 암호가 동기화되지 않습니다. 여기에는 OU와 특성 필터링이 모두 포함됩니다.
  * 동기화에 포함할 새 OU를 선택하는 데에는 전체 암호 동기화가 필요하지 않습니다.
  * 비활성화된 사용자가 설정된 경우 암호가 동기화되지 않습니다.
  * 암호 재시도 큐에는 제한이 없으며, 사용 중지될 개체 5, 000개에 대한 이전의 제한이 제거되었습니다.
* Windows Server 2016 포리스트 기능 수준으로 Active Directory에 연결할 수 없습니다.
* 초기 설치 후 그룹 필터링에 사용된 그룹을 변경할 수 없습니다.
* 비밀번호 쓰기 저장을 활성화하고 암호 변경을 수행하는 모든 사용자에 대해 더 이상 Azure AD Connect 서버에 새로운 사용자 프로필을 만들지 않습니다.
* 동기화 규칙 범위 내 정수(Long) 값을 사용할 수 없습니다.
* 연결할 수 없는 도메인 컨트롤러가 있는 경우 "디바이스 쓰기 저장" 확인란이 비활성화된 상태로 유지됩니다.

## <a name="1086670"></a>1.0.8667.0
릴리스 날짜: 2015년 8월

**새로운 기능:**

* Azure AD Connect 설치 마법사가 이제 모든 Windows Server 언어로 현지화되었습니다.
* Azure AD 암호 관리 사용 시 계정 잠금 해제에 대한 지원이 추가되었습니다.

**수정된 문제:**

* 설치를 처음 시작한 사용자가 아닌 다른 사용자가 설치를 계속하면 Azure AD Connect 설치 마법사가 충돌합니다.
* 이전에 Azure AD Connect를 제거할 때 Azure AD Connect Sync가 완전히 제거되지 않았다면 다시 설치할 수 없습니다.
* 사용자가 포리스트 루트 도메인에 없거나 Active Directory의 비영어 버전을 사용할 때 빠른 설치를 사용하여 Azure AD Connect를 설치할 수 없습니다.
* Active Directory 사용자 계정의 FQDN을 확인할 수 없을 때 "스키마 커밋 실패"라는 잘못된 오류 메시지가 표시됩니다.
* Active Directory Connector에 사용되는 계정이 마법사 밖에서 변경되면 마법사가 후속 실행에 실패합니다.
* Azure AD Connect가 도메인 컨트롤러에서 설치에 실패하는 경우가 있습니다.
* 확장 특성을 추가하지 않은 경우 “스테이징 모드"를 활성화 및 비활성화할 수 없습니다.
* Active Directory Connector의 잘못된 암호 때문에 일부 구성에서는 비밀번호 쓰기 저장이 실패합니다.
* 특성 필터링에 DN(고유 이름)이 사용되는 경우 DirSync를 업그레이드할 수 없습니다.
* 암호 재설정을 사용할 경우 CPU가 과도하게 사용됩니다.

**제거된 미리 보기 기능:**

* 미리 보기 기능 [사용자 쓰기 저장](how-to-connect-preview.md#user-writeback) 은 미리 보기 고객의 의견에 따라 일시적으로 제거되었습니다. 고객에게 받은 피드백을 해결한 후 다시 추가할 예정입니다.

## <a name="1086410"></a>1.0.8641.0
릴리스 날짜: 2015년 6월

**Azure AD Connect의 초기 릴리스입니다.**

Azure AD Sync에서 Azure AD Connect로 이름을 변경했습니다.

**새로운 기능:**

* [Express 설정](how-to-connect-install-express.md) 설치
* [AD FS 구성](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) 가능
* [DirSync에서 업그레이드](how-to-dirsync-upgrade-get-started.md) 가능
* [실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* [스테이징 모드](how-to-connect-sync-staging-server.md)

**새로운 미리 보기 기능:**

* [사용자 쓰기 저장](how-to-connect-preview.md#user-writeback)
* [디바이스 쓰기 저장](how-to-connect-device-writeback.md)
* [디렉터리 확장](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
릴리스 날짜: 2015년 5월

**새로운 요구 사항:**

* Azure AD Sync는 이제 .NET Framework 버전 4.5.1을 설치해야 합니다.

**수정된 문제:**

* Azure Service Bus 연결 오류와 함께 Azure AD에서 비밀번호 쓰기 저장이 실패합니다.

## <a name="104910413"></a>1.0.491.0413
릴리스 날짜: 2015년 4월

**수정된 문제 및 향상된 기능:**

* 휴지통이 활성화되어 있고 포리스트에 여러 도메인이 있는 경우 Active Directory Connect가 삭제를 올바르게 처리하지 못했습니다.
* Azure Active Directory Connector에 대해 가져오기 작업의 성능이 향상되었습니다.
* 그룹의 구성원 제한이 초과되면(기본적으로 개체 50,000개로 설정됨) Azure Active Directory에서 해당 그룹이 삭제되었습니다. 이제는 그룹이 삭제되지 않고 오류가 throw되며, 새로운 구성원 변경 내용을 내보내지 않습니다.
* 동일한 DN을 가진 단계적 삭제가 이미 커넥터 공간에 있으면 새 개체를 프로비저닝할 수 없습니다.
* 개체에 스테이징된 변경 사항이 없는데도 일부 개체가 델타 동기화 중에 동기화 대상으로 표시됩니다.
* 강제로 암호를 동기화해도 기본 설정된 DC 목록이 제거됩니다.
* CSExportAnalyzer에 일부 개체 상태에 문제가 있습니다.

**새로운 기능:**

* 이제 조인이 MV에 있는 "ANY" 개체 유형에 연결할 수 있습니다.

## <a name="104850222"></a>1.0.485.0222
릴리스 날짜: 2015년 2월

**향상된 기능:**

* 가져오기 성능이 향상되었습니다.

**수정된 문제:**

* 암호 동기화는 특성 필터링에 사용되는 cloudFiltered 특성을 인식합니다. 필터링된 개체는 더 이상 암호 동기화 범위에 속하지 않습니다.
* 드문 경우지만 토폴로지에 도메인 컨트롤러가 여러 개 있으면 암호 동기화가 작동하지 않습니다.
* Azure AD/Intune에서 디바이스 관리가 활성화된 후 Azure AD Connector로부터 가져오기 시 “중지된-서버"가 발생합니다.
* 동일한 포리스트 내 여러 도메인으로부터 외부 보안 주체(FSP)를 조인하면 모호한 오류가 발생합니다.

## <a name="104751202"></a>1.0.475.1202
릴리스 날짜: 2014년 12월

**새로운 기능:**

* 이제 특성 기반 필터링을 사용하는 암호 동기화가 지원됩니다. 자세한 내용은 [필터링으로 암호 동기화](how-to-connect-sync-configure-filtering.md)를 참조하세요.
* ms-DS-ExternalDirectoryObjectID 특성이 다시 Active Directory에 기록됩니다. 이 기능을 통해 Microsoft 365 애플리케이션에 대한 지원이 추가됩니다. 이 기능은 OAuth2를 사용하여 하이브리드 Exchange 배포의 온라인 및 온-프레미스 사서함에 액세스합니다.

**수정된 업그레이드 문제:**

* 로그인 도우미의 새 버전을 서버에서 사용할 수 있습니다.
* 사용자 지정 설치 경로를 사용하여Azure AD Sync를 설치했습니다.
* 잘못된 사용자 지정 조인 조건이 업그레이드를 차단합니다.

**기타 수정 사항:**

* Office Pro Plus에 대한 템플릿이 수정되었습니다.
* 대시로 시작하는 사용자 이름에 의해 발생한 설치 문제가 수정되었습니다.
* 설치 마법사를 두 번째로 실행할 때 sourceAnchor 설정이 손실되는 문제가 수정되었습니다.
* 암호 동기화를 위한 ETW 추적이 수정되었습니다.

## <a name="104701023"></a>1.0.470.1023
릴리스 날짜: 2014년 10월

**새로운 기능:**

* 여러 온-프레미스 Active Directory에서 Azure AD로 암호 동기화
* 모든 Windows Server 언어로 설치 UI 현지화

**AADSync 1.0 GA에서 업그레이드**

Azure AD Sync가 이미 설치되어 있는 경우 기본적인 동기화 규칙을 하나라도 변경했다면 실행해야 하는 단계가 하나 더 있습니다. 1\.0.470.1023 릴리스로 업그레이드한 후에는 수정한 동기화 규칙이 중복됩니다. 수정된 각 동기화 규칙에 대해 다음을 수행합니다.

1. 수정한 동기화 규칙을 찾아 변경 내용을 기록해둡니다.
1. 해당 동기화 규칙을 삭제합니다.
1. Azure AD Sync에서 생성한 새로운 동기화 규칙을 찾아 변경 내용을 다시 적용합니다.

**Active Directory 계정에 대한 권한**

Active Directory에서 암호 해시를 읽을 수 있으려면 Active Directory 계정에 추가 권한을 부여해야 합니다. 부여할 권한의 이름은 "디렉터리 변경 내용 복제" 및 “디렉터리 변경 내용 모두 복제”입니다. 두 권한이 모두 있어야 암호 해시를 읽을 수 있습니다.

## <a name="104190911"></a>1.0.419.0911
릴리스 날짜: 2014년 9월

**Azure AD Sync의 초기 릴리스**

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
