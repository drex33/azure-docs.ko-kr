---
title: 오스트레일리아 정부 ISM PROTECTED 청사진 샘플 컨트롤
description: 오스트레일리아 정부 ISM PROTECTED 청사진 샘플의 컨트롤 매핑. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 293c1a0566b830f9aab84d3c675479f91eff4976
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632275"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>오스트레일리아 정부 ISM PROTECTED 청사진 샘플의 제어 매핑

다음 문서에서는 Azure Blueprints 오스트레일리아 정부 ISM PROTECTED 청사진 샘플이 ISM PROTECTED 제어에 매핑되는 방법에 대해 자세히 설명합니다. 제어에 대한 자세한 내용은 [ISM PROTECTED](https://www.cyber.gov.au/ism)를 참조하세요.

다음은 **ISM PROTECTED** 제어에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md)을 참조하세요.

## <a name="location-constraints"></a>위치 제약 조건

이 청사진을 통해 다음과 같은 Azure Policy 정의를 할당하여 모든 리소스 및 리소스 그룹의 배포 위치를 "오스트레일리아 중부", "오스트레일리아 중부 2", "오스트레일리아 동부" 및 "오스트레일리아 남동부"로 제한할 수 있습니다.

- 허용되는 위치("오스트레일리아 중부", "오스트레일리아 중부 2", "오스트레일리아 동부" 및 "오스트레일리아 남동부"로 하드 코딩됨)
- 리소스 그룹에 허용되는 위치("오스트레일리아 중부", "오스트레일리아 중부 2", "오스트레일리아 동부" 및 "오스트레일리아 남동부"로 하드 코딩됨)

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>직원 보안 지침 - 시스템 및 해당 리소스에 대한 액세스

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 시스템 및 해당 리소스에 대한 액세스 권한이 부여된 직원은 고유하게 식별할 수 있음

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 시스템, 애플리케이션 및 데이터 리포지토리에 대한 표준 액세스는 직원이 업무를 수행하는 데 필요한 액세스로 제한됨

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 시스템, 애플리케이션 및 데이터 리포지토리에 대한 권한 있는 액세스는 매년 또는 더 자주 처음 요청하고 유효성을 다시 검사할 때 유효성이 검사됨

- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 시스템, 애플리케이션 및 데이터 리포지토리에 대한 표준 액세스는 직원이 업무를 수행하는 데 필요한 액세스로 제한됨

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포
- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 공유 사용자 계정의 사용은 엄격하게 제어되며, 이러한 계정을 사용하는 직원은 고유하게 식별할 수 있음

- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 권한 있는 액세스 권한이 필요한 작업에만 사용되는 권한 있는 전용 계정이 권한 있는 사용자에게 할당됨

- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 시스템, 애플리케이션 및 데이터 리포지토리에 대한 액세스는 직원에게 더 이상 합법적인 액세스 요구 사항이 없는 당일에 제거되거나 일시 중단됨

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 시스템에 대한 임시 액세스 권한이 직원에게 부여되면 업무를 수행하는 데 필요한 정보에만 액세스하도록 제한하는 효과적인 보안 제어가 적용됨

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>시스템 강화 지침 - 운영 체제 강화

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 운영 체제의 최신 버전(N) 또는 N-1 버전이 SOE(Standard Operating Environment)에 사용됨

- 시스템 업데이트를 머신에 설치해야 합니다.
- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 불필요한 운영 체제 계정, 소프트웨어, 구성 요소, 서비스 및 기능이 제거되거나 사용하지 않도록 설정됨

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 애플리케이션 허용 목록 솔루션이 실행 파일, 소프트웨어 라이브러리, 스크립트 및 설치 관리자를 승인된 세트로 실행하도록 제한하기 위해 모든 서버에서 구현됨

- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 바이러스 백신 소프트웨어가 워크스테이션과 서버에 구현되며, 서명 기반 검색 및 추론 기반 검색을 높은 수준에서 사용하도록 설정하고 통화 검색 서명을 확인하며 모든 고정 디스크 및 이동식 미디어에 구성된 매일(최소), 자동 및 정기 검사 기준으로 업데이트하도록 구성됨

- Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.
- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>시스템 강화 지침 - 인증 강화

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 사용자가 시스템 및 해당 리소스에 대한 액세스 권한을 부여받기 전에 인증됨

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM의 감사 결과를 표시합니다.
- 암호 없이 계정에서 원격으로 연결할 수 있는 Linux VM을 감사하기 위한 필수 조건을 배포합니다.
- 암호 없는 계정이 있는 Linux VM의 감사 결과 표시
- 암호 없는 계정이 있는 Linux VM을 감사하기 위한 필수 조건 배포

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 다단계 인증이 표준 사용자를 인증하는 데 사용됨

- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 다단계 인증이 모든 권한 있는 사용자 및 신뢰할 수 있는 기타 모든 위치를 인증하는 데 사용됨

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 단일 단계 인증에 사용되는 암호는 14자 이상이며, 복잡성이 높은 4개의 임의 단어를 포함하는 것이 좋음

- '보안 설정 - 계정 정책'에서 Windows VM 구성의 감사 결과 표시
- '보안 설정 - 계정 정책'에서 Windows VM 구성을 감사하는 필수 조건 배포

## <a name="guidelines-for-system-management---system-administration"></a>시스템 관리 지침 - 시스템 관리

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 다단계 인증이 권한 있는 작업을 수행할 때마다 사용자를 인증하는 데 사용됨

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 관리 트래픽이 시스템 및 애플리케이션을 관리하는 데 사용되는 네트워크 영역에서만 시작될 수 있음

- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.
- API Apps에 대해 원격 디버깅을 해제해야 합니다.
- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.
- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.

## <a name="guidelines-for-system-management---system-patching"></a>시스템 관리 지침 - 시스템 패치

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 매우 심각한 위험으로 평가되는 애플리케이션 및 드라이버의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 48시간 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- 머신에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 높은 위험으로 평가되는 애플리케이션 및 드라이버의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 2주 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 보통 또는 낮은 위험으로 평가되는 애플리케이션 및 드라이버의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 1개월 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 매우 심각한 위험으로 평가되는 운영 체제 및 펌웨어의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 48시간 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 높은 위험으로 평가되는 운영 체제 및 펌웨어의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 2주 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 보통 또는 낮은 위험으로 평가되는 운영 체제 및 펌웨어의 보안 취약성이 공급업체, 독립적인 타사, 시스템 관리자 또는 사용자로부터 보안 취약성이 확인된 후 1개월 이내에 패치, 업데이트 또는 완화됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>시스템 관리 지침 - 데이터 백업 및 복원

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 중요한 정보, 소프트웨어 및 구성 설정의 백업이 최소한 매일 수행됨

- 재해 복구를 구성하지 않고 가상 머신 감사

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>시스템 모니터링 지침 - 이벤트 로깅 및 감사

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 중앙 집중식 로깅 기능이 구현되고, 각 이벤트가 발생하면 가능한 한 빨리 이벤트 로그를 중앙 집중식 로깅 기능에 저장하도록 시스템이 구성됨

- Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 합니다.

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 중요한 데이터 및 프로세스에 대한 액세스, 애플리케이션 작동 중단 및 오류 메시지, 특수 권한 사용 시도, 계정 변경, 보안 정책 변경, 시스템 구성 변경, DNS(Domain Name System) 및 HTTP(Hypertext Transfer Protocol) 요청, 데이터 및 시스템 리소스에 대한 액세스 시도 실패, 서비스 실패 및 다시 시작, 시스템 시작 및 종료, 외부 미디어로 데이터 전송, 사용자 또는 그룹 관리, 특수 권한 사용과 같은 운영 체제에 대한 이벤트가 기록됨

- \[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- VMSS의 Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- VM용 Log Analytics 작업 영역 감사 - 보고서 불일치
- 진단 설정 감사

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 특히 중요한 정보에 대한 액세스, 새 사용자, 특히 권한이 있는 사용자, 설명이 포함된 쿼리, 여러 포함된 쿼리가 있는 쿼리, 쿼리/데이터베이스 경고 또는 실패, 권한 상승 시도, 성공적이거나 실패한 액세스 시도, 데이터베이스 구조 변경, 사용자 역할 또는 데이터베이스 권한 변경, 데이터베이스 관리자 작업, 데이터베이스 로그온 및 로그오프, 데이터 수정, 실행 가능한 명령 사용과 같은 데이터베이스에 대한 이벤트가 기록됨

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- 진단 설정 감사
- SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>시스템 모니터링 지침 - 취약성 관리

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 취약성 평가 및 침투 테스트가 숙련된 직원을 통해 시스템을 배포하기 전, 시스템을 크게 변경한 후 및 최소한 매년 또는 시스템 소유자가 지정한 대로 수행됨

- SQL 데이터베이스의 취약성을 수정해야 합니다.
- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.

## <a name="guidelines-for-database-systems-management---database-servers"></a>데이터베이스 시스템 관리 지침 - 데이터베이스 서버

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 데이터베이스 서버의 하드 디스크가 전체 디스크 암호화를 사용하여 암호화됨

- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 데이터베이스 서버와 웹 애플리케이션 간에 전달되는 정보가 암호화됨

- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버의 감사 결과 표시
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버를 감사하기 위한 필수 조건 배포

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>데이터베이스 시스템 관리 지침 - 데이터베이스 관리 시스템 소프트웨어

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 기본 데이터베이스 관리자 계정을 사용하지 않도록 설정되거나, 이름이 바뀌거나, 해당 암호가 변경됨

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 데이터베이스 관리자에게 고유하고 식별 가능한 계정이 있음

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 데이터베이스 관리자 계정이 여러 데이터베이스에서 공유되지 않음

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 데이터베이스 관리자 계정이 데이터베이스와의 범용 상호 작용에 표준 데이터베이스 계정과 함께 관리 작업에만 독점적으로 사용됨

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 데이터베이스 관리자 액세스가 기본 관리 권한 또는 모든 권한이 있는 계정이 아니라 정의된 역할로 제한됨

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>암호화 사용 지침 - 암호화 기본 사항

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 저장 데이터에 사용되는 암호화 소프트웨어에서 전체 디스크 암호화 또는 액세스 제어를 통해 암호화된 파티션에 대한 쓰기만 허용하는 부분 암호화를 구현함

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>암호화 사용 지침 - 전송 계층 보안

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 최신 버전의 TLS만 사용됨

- API 앱에서 최신 TLS 버전을 사용해야 합니다.
- 웹앱에서 최신 TLS 버전을 사용해야 합니다.
- 함수 앱에서 최신 TLS 버전을 사용해야 합니다.
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버를 감사하기 위한 필수 조건 배포
- 보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버의 감사 결과 표시

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>데이터 전송 및 콘텐츠 필터링 지침 - 콘텐츠 필터링

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 여러 다른 검색 엔진을 사용하는 바이러스 백신 검사가 모든 콘텐츠에 대해 수행됨

- Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.
- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>데이터 전송 및 콘텐츠 필터링 지침 - 웹 애플리케이션 개발

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 모든 웹 애플리케이션 콘텐츠가 HTTPS를 사용하여 독점적으로 제공됨

- 함수 앱에 HTTPS를 통해서만 액세스 가능
- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능
- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 웹 애플리케이션과 해당 사용자를 모두 보호하기 위해 웹 애플리케이션에 대한 웹 브라우저 기반 보안 제어가 구현됨

- CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>네트워크 관리 지침 - 네트워크 설계 및 구성

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 권한이 없는 네트워크 디바이스의 연결을 방지하기 위해 네트워크에 대한 네트워크 액세스 제어가 구현됨

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 네트워크 세그먼트 내 및 네트워크 세그먼트 간 트래픽을 비즈니스 목적에 필요한 트래픽으로만 제한하도록 네트워크 액세스 제어가 구현됨

- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>네트워크 관리 지침 - 온라인 서비스에 대한 서비스 연속성

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 서비스 거부 공격 방지 및 완화 전략이 서비스 공급자, 특히 서비스 거부 공격을 견딜 수 있는 용량, 서비스 거부 공격으로 인해 고객에게 발생될 수 있는 모든 비용, 서비스 거부 공격 중에 고객에게 알리거나 온라인 서비스를 해제하기 위한 임계값, 서비스 거부 공격 중에 수행할 수 있는 사전 승인된 작업, 악성 트래픽을 가능한 한 업스트림까지 차단하는 업스트림 공급자와의 서비스 거부 공격 방지 협정을 통해 설명됨

- DDoS Protection 표준을 사용하도록 설정해야 합니다.

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다.

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

> [!div class="nextstepaction"]
> [ISM PROTECTED 청사진 - 개요](./index.md)
> [ISM PROTECTED 청사진 - 배포 단계](./deploy.md)

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
