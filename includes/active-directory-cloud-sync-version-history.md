---
ms.openlocfilehash: 14d2372defc67b5cd619c888a43d09b5b10af846
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122636095"
---
이 문서에서는 릴리스된 Azure Active Directory Connect Provisioning Agent의 버전과 기능을 나열합니다. Azure AD 팀은 새로운 기능과 기능으로 프로비전 에이전트를 정기적으로 업데이트합니다. 

Microsoft에서는 최신 에이전트 버전 및 이전 버전을 직접 지원합니다.

## <a name="download-link"></a>다운로드 링크
[이 링크](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/provisioningAgentInstaller)를 사용하여 최신 버전의 에이전트를 다운로드할 수 있습니다.

## <a name="115820"></a>1.1.582.0

2021년 8월 8일 - 다운로드용으로 릴리스됨

>[!NOTE] 
>Azure AD Connect 보안 업데이트 릴리스입니다. 이 릴리스는 [이 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949)에 설명된 대로 취약성을 해결합니다. 이 취약성에 대한 자세한 내용은 CVE를 참조하세요.

## <a name="113590"></a>1.1.359.0

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- 권한을 설정/다시 설정하는 GMSA Cmdlet

### <a name="fixed-issues"></a>해결된 문제
- GMSA 폴더 권한 버그 수정(원래 이 문제로 인해 부트스트랩 문제가 발생함)
- 단일 값 참조 특성(예: 관리자)에 대한 여러 변경 내용을 처리하기 위한 버그 수정
- 초기 열거형의 오류에 대한 버그 수정 및 향상된 오류 추적
- 범위 지정 그룹에 대한 그룹 멤버 자격 업데이트를 최적화합니다. 이를 통해 고객은 이제 그룹 범위 지정 필터를 사용하여 최대 50,000명의 멤버 그룹을 동기화할 수 있습니다. 
- 범위 지정 논리를 준수하기 위해 주문형 프로비저닝에서 사용하는 범위 지정을 통해 DN으로 단일 개체 검색 지원





## <a name="113540"></a>1.1.354.0

2021년 1월 20일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- 미리 작성된 사용자 지정 GMSA 계정에 대한 지원을 포함하여 GMSA 환경 개선
- GMSA 설치를 위한 [PowerShell cmdlet](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) 지원
- 에이전트 설치를 위한 [CLI 지원](../articles/active-directory/cloud-sync/how-to-install-pshell.md)(자동 설치)
- 에이전트 원본 격리 문제에 대한 추가 진단
- OU 범위 지정 필터의 메모리 사용량 감소, 범위 내 사용자에 대해서만 PHS 실행, OU 범위를 사용할 때 OU의 중첩된 개체 처리 등을 포함한 버그 수정 


### <a name="fixed-issues"></a>해결된 문제
-    범위 지정 그룹이 범위를 벗어난 경우 격리 방지
-   범위 지정 필터가 구성된 경우 - PHS 작업은 이제 범위 내 사용자에 대해서만 작동
-   업그레이드하는 동안 에이전트가 잠시 중단됨
-   OU 범위 지정을 사용하는 경우 중첩된 OU의 개체에 대한 초기 동기화
-   더 강력해진 Repair-AADCloudSyncToolsAccount
-   OU 범위 지정 필터의 대규모 메모리 사용량 감소
-   역할 멤버가 보안 그룹을 포함하는 경우 관리자 역할 검사가 실패함
-   에이전트 인증서 갱신을 방지하는 GMSA 폴더 권한 문제 해결







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>릴리스 상태

2020년 11월 23일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) 지원
* 증분 또는 델타 동기화 주기 동안 1500명 미만의 멤버까지 그룹 지원. 이는 그룹 범위 지정 필터를 사용하는 경우에 적용할 수 있습니다.
* 구성원 크기가 최대 15K인 대규모 그룹 지원
* 초기 동기화 기능 향상
* 고급 정보 로깅
* [AADCloudSyncTools PowerShell 모듈](../articles/active-directory/cloud-sync/reference-powershell.md) 도입
* 영어 외 서버에 에이전트를 설치할 수 있도록 제한 사항이 수정됨
* 범위에 있는 개체에 대해서만 PHS 필터링 지원(원래 모든 개체에 대해 암호 해시를 동기화했음)
* 에이전트의 메모리 누수 문제 수정
* 향상된 프로비전 로그
* [LDAP 연결 제한 시간](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 구성을 위한 지원 
* [조회 추적](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 구성을 위한 지원 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>릴리스 상태

2019년 12월 4일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 온-프레미스 Active Directory에서 Azure AD로 사용자, 연락처 및 그룹 데이터를 동기화하는 [Azure AD Connect 클라우드 동기화](../articles/active-directory/cloud-sync/what-is-cloud-sync.md)에 대한 지원 포함


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>릴리스 상태

2019년 9월 9일: 자동 업데이트를 위해 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 프로비전 에이전트 문제를 디버깅하기 위해 추가 추적 및 로깅을 구성하는 기능
* 동기화 성능을 향상시키기 위해 매핑에 구성된 Azure AD 특성만 페치할 수 있는 기능

### <a name="fixed-issues"></a>해결된 문제

* Azure AD 연결 오류와 관련된 문제가 있는 경우 에이전트가 응답하지 않는 상태로 전환되는 버그가 수정됨
* Azure Active Directory에서 이진 데이터를 읽을 때 문제를 일으킨 버그가 수정됨
* 에이전트가 클라우드 하이브리드 ID 서비스와의 트러스트를 갱신하지 못한 버그가 수정됨

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>릴리스 상태

2019년 1월 23일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 더 나은 성능, 안정성 및 안정성을 위해 프로비전 에이전트 및 커넥터 아키텍처가 개선됨 
* UI 기반 설치 마법사를 사용하여 프로비전 에이전트 구성이 간소화됨
