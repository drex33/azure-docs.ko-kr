---
title: 일반적인 보안 요구 사항 해결에 대한 플레이북
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance의 일반적인 보안 요구 사항 및 모범 사례를 제공합니다.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: 47acffc242973e70f9fe89db9304d193307c5695
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335749"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance를 사용하여 일반적인 보안 요구 사항을 해결하는 방법에 대한 플레이북
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 일반적인 보안 요구 사항을 해결하는 방법에 대한 모범 사례를 제공합니다. 모든 요구 사항이 모든 환경에 적용되는 것은 아니므로, 어떤 기능을 구현할 것인지 데이터베이스 및 보안 팀과 상의해야 합니다.

## <a name="solving-common-security-requirements"></a>일반적인 보안 요구 사항 해결

이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance를 사용하여 신규 또는 기존 애플리케이션의 일반적인 보안 요구 사항을 해결하는 방법에 대한 지침을 제공합니다. 이 문서는 높은 수준의 보안 영역을 기준으로 작성되었습니다. 구체적인 위협을 해결하는 방법은 [일반적인 보안 위협 및 잠재적 완화](#common-security-threats-and-potential-mitigations) 섹션을 참조하세요. 제공된 권장 사항 중 일부는 애플리케이션을 온-프레미스에서 Azure로 마이그레이션할 때 적용할 수 있지만, 마이그레이션 시나리오는 이 문서의 주요 주제가 아닙니다.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>이 가이드에서 다루는 Azure SQL Database 배포 제안

- [Azure SQL Database](./index.yml): [서버](logical-servers.md)의 [단일 데이터베이스](single-database-overview.md) 및 [탄력적 풀](elastic-pool-overview.md)
- [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>이 가이드에서 다루지 않는 배포 제안

- Azure Synapse Analytics
- Azure SQL VM(IaaS)
- SQL Server

### <a name="audience"></a>사용자

이 가이드의 대상은 Azure SQL Database를 보호하는 방법이 궁금한 고객입니다. 이 모범 사례 문서에 관심이 있는 역할은 다음과 같습니다(이에 국한되지 않음).

- 보안 설계자
- 보안 관리자
- 규정 준수 책임자
- 개인 정보 책임자
- 보안 엔지니어

### <a name="using-this-guide"></a><a id="using"></a> 이 가이드 사용

이 문서는 기존 [Azure SQL Database 보안](security-overview.md) 설명서와 함께 제공됩니다.

별도로 언급하지 않는 한, 각 섹션에 나열된 모든 모범 사례에 따라 각 목표 또는 요구 사항을 달성하는 것이 좋습니다. 특정 보안 규정 준수 표준 또는 모범 사례를 충족할 수 있도록 중요한 규정 준수 컨트롤이 요구 사항 또는 목표 섹션에 나열됩니다(해당하는 경우). 다음은 이 문서에서 참조하는 보안 표준 및 규정입니다.

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, Cryptography
- [Microsoft OSA(Operational Security Assurance) 규정](https://www.microsoft.com/securityengineering/osa/practices): Practice #1-6 및 #9
- [NIST Special Publication 800-53 Security Controls](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

여기에 나열된 권장 사항 및 모범 사례를 계속해서 업데이트할 예정입니다. 이 문서의 맨 아래에 있는 **피드백** 링크를 사용하여 이 문서에 대한 의견이나 수정 사항을 알려주세요.

## <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database 및 SQL Managed Instance는 다음과 같은 두 가지 유형의 인증을 지원합니다.

- SQL 인증
- Azure Active Directory 인증

> [!NOTE]
> 일부 도구와 타사 애플리케이션에서는 Azure Active Directory 인증이 지원되지 않을 수 있습니다.

### <a name="central-management-for-identities"></a>중앙에서 ID 관리

중앙 ID 관리는 다음과 같은 이점을 제공합니다.

- 서버, 데이터베이스 및 관리형 인스턴스 간에 로그인을 복제하지 않고 그룹 계정을 관리하고 사용자 권한을 제어할 수 있습니다.
- 간단하고 유연하게 권한을 관리할 수 있습니다.
- 대규모로 애플리케이션을 관리할 수 있습니다.

**구현 방법**:

- 중앙 집중식 ID 관리를 위해 Azure AD(Azure Active Directory) 인증을 사용합니다.

**모범 사례**:

- Azure AD 테넌트를 만들고, 인간 사용자를 나타내는 [사용자를 만들고](../../active-directory/fundamentals/add-users-azure-active-directory.md) 앱, 서비스 및 자동화 도구를 나타내는 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)를 만듭니다. 서비스 주체는 Windows 및 Linux의 서비스 계정에 해당합니다.

- 그룹 할당을 통해 Azure AD 사용자에게 리소스 액세스 권한 할당: Azure AD 그룹을 만들고, 그룹에 액세스 권한을 부여하고, 그룹에 개별 구성원을 추가합니다. 데이터베이스 안에서 Azure AD 그룹을 매핑하는 데이터베이스 사용자를 만듭니다. 데이터베이스 내에서 권한을 할당하려면 Azure AD 그룹에 연결된 사용자를 적절한 권한이 있는 데이터베이스 역할에 배치합니다.
  - [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](authentication-aad-configure.md) 및 [SQL 인증에 Azure AD 사용](authentication-aad-overview.md) 문서를 참조하세요.
  > [!NOTE]
  > 또한 SQL Managed Instance에서는 master 데이터베이스의 Azure AD 보안 주체에 매핑되는 로그인을 만들 수 있습니다. [CREATE LOGIN(Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.

- Azure AD 그룹을 사용하면 간편하게 권한을 관리할 수 있으며 그룹 소유자와 리소스 소유자가 모두 그룹 구성원을 추가/제거할 수 있습니다.

- 서버 또는 관리형 인스턴스마다 Azure AD 관리자에 대한 별도의 그룹을 만듭니다.

  - [서버의 Azure Active Directory 관리자 프로비저닝](authentication-aad-configure.md#provision-azure-ad-admin-sql-database) 문서를 참조하세요.

- Azure AD 감사 활동 보고서를 사용하여 Azure AD 그룹 멤버 자격 변경 내용을 모니터링합니다.

- 관리형 인스턴스의 경우 Azure AD 관리자를 만들려면 별도의 단계가 필요합니다.
  - [관리형 인스턴스의 Azure Active Directory 관리자 프로비저닝](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) 문서를 참조하세요.

> [!NOTE]
>
> - Azure AD 인증은 Azure SQL 감사 로그에 기록되지만 Azure AD 로그인 로그에는 기록되지 않습니다.
> - Azure에서 부여된 Azure RBAC 권한은 Azure SQL Database 또는 SQL Managed Instance 권한에 적용되지 않습니다. 이러한 권한은 기존 SQL 권한을 사용하여 수동으로 만들거나 매핑해야 합니다.
> - 클라이언트 쪽에서 Azure AD 인증은 인터넷에 액세스하거나 UDR(사용자 정의 경로)을 통해 가상 네트워크에 액세스해야 합니다.
> - Azure AD 액세스 토큰은 클라이언트 쪽에서 캐시되며 토큰의 수명은 토큰 구성에 따라 달라집니다. [Azure Active Directory에서 구성 가능한 토큰 수명](../../active-directory/develop/active-directory-configurable-token-lifetimes.md) 문서를 참조하세요.
> - Azure AD 인증 문제를 해결하는 방법에 대한 지침은 [Azure AD 문제 해결](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991) 블로그를 참조하세요.

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

> 언급된 위치: OSA Practice #2, ISO AC(Access Control)

Azure AD Multi-Factor Authentication은 두 가지 이상의 인증 형태를 요구하여 보안을 강화합니다.

**구현 방법**:

- 조건부 액세스를 사용하고 대화형 인증을 사용하여 Azure AD에서 [Multi-Factor Authentication을 사용하도록 설정](../../active-directory/authentication/concept-mfa-howitworks.md)합니다.

- 대안은 전체 Azure AD 또는 AD 도메인에 Multi-Factor Authentication을 사용하도록 설정하는 것입니다.

**모범 사례**:

- Azure AD에서 조건부 액세스를 활성화합니다(프리미엄 구독 필요).
  - [Azure AD의 조건부 액세스](../../active-directory/conditional-access/overview.md) 문서를 참조하세요.  

- Azure AD 그룹을 만들고 Azure AD 조건부 액세스를 사용하여 선택한 그룹에 Multi-Factor Authentication 정책을 사용하도록 설정합니다.
  - [조건부 액세스 배포 계획](../../active-directory/conditional-access/plan-conditional-access.md) 문서를 참조하세요.

- 전체 Azure AD 또는 Azure AD와 페더레이션된 전체 Active Directory에 Multi-Factor Authentication을 사용하도록 설정할 수 있습니다.

- 대화형으로 암호를 요청한 후 Multi-Factor Authentication을 요청하는 Azure AD Interactive 인증 모드를 Azure SQL Database 및 Azure SQL Managed Instance에 사용합니다.
  - SSMS에서 유니버설 인증을 사용합니다. [Azure SQL Database, SQL Managed Instance, Azure Synapse에서 Azure AD 다단계 인증 사용(SSMS의 Multi-Factor Authentication 지원)](authentication-mfa-ssms-overview.md) 문서를 참조하세요.
  - SSDT(SQL Server Data Tools)에서 지원되는 대화형 인증을 사용합니다. [SSDT(SQL Server Data Tools)의 Azure Active Directory 지원](/sql/ssdt/azure-active-directory?view=azuresqldb-current&preserve-view=true) 문서를 참조하세요.
  - Multi-Factor Authentication을 지원하는 다른 SQL 도구를 사용합니다.
    - SSMS 마법사의 데이터베이스 내보내기/추출/배포 지원  
    - [sqlpackage.exe](/sql/tools/sqlpackage): '/ua' 옵션
    - [sqlcmd 유틸리티](/sql/tools/sqlcmd-utility): -G 옵션(대화형)
    - [bcp 유틸리티](/sql/tools/bcp-utility): -G 옵션(대화형)

- Multi-Factor Authentication 지원과 함께 대화형 인증을 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance에 연결하는 애플리케이션을 구현합니다.
  - [Azure AD Multi-Factor Authentication을 사용하여 Azure SQL Database에 연결](active-directory-interactive-connect-azure-sql-db.md) 문서를 참조하세요.
  > [!NOTE]
  > 이 인증 모드를 사용하려면 사용자 기반 ID가 필요합니다. 개별 Azure AD 사용자 인증을 무시하는 신뢰할 수 있는 ID 모델을 사용하는 경우(예: Azure 리소스에 관리 ID 사용) Multi-Factor Authentication이 적용되지 않습니다.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>사용자에 대한 암호 기반 인증 사용 최소화

> 언급된 위치: OSA Practice #4, ISO AC(Access Control)

암호 기반 인증은 더 약한 형태의 인증 방법입니다. 자격 증명이 손상되거나 실수로 제공될 수 있습니다.

**구현 방법**:

- 암호를 사용할 필요가 없는 Azure AD 통합 인증을 사용합니다.

**모범 사례**:

- Windows 자격 증명을 사용하는 Single Sign-On 인증을 사용합니다. 온-프레미스 AD 도메인을 Azure AD와 페더레이션하고 통합 Windows 인증을 사용합니다(Azure AD를 사용하는 도메인에 가입된 머신의 경우).
  - [SSMS의 Azure AD 통합 인증 지원](authentication-aad-configure.md#active-directory-integrated-authentication) 문서를 참조하세요.

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>애플리케이션에 대한 암호 기반 인증 사용 최소화

> 언급된 위치: OSA Practice #4, ISO AC(Access Control)

**구현 방법**:

- Azure 관리 ID를 사용합니다. 통합 인증 또는 인증서 기반 인증을 사용할 수도 있습니다.

**모범 사례**:

- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용합니다.
  - [시스템 할당 관리 ID](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [사용자 할당 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [관리 ID와 함께 Azure App Service의 Azure SQL Database 사용(코드 변경 없음)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 애플리케이션에 인증서 기반 인증을 사용합니다.
  - 이 [코드 샘플](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)을 참조하세요.

- 통합 페더레이션 도메인 및 도메인에 조인된 머신에 Azure AD 인증을 사용합니다(위 섹션 참조).
  - [통합 인증을 위한 애플리케이션 샘플](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)을 참조하세요.

### <a name="protect-passwords-and-secrets"></a>암호 및 비밀 보호

암호를 반드시 사용해야 하는 경우 암호를 안전하게 보호해야 합니다.

**구현 방법**:

- Azure Key Vault를 사용하여 암호 및 비밀을 저장합니다. 해당하는 경우 Azure AD 사용자가 있는 Azure SQL Database에 Multi-Factor Authentication을 사용합니다.

**모범 사례**:

- 암호 또는 비밀을 반드시 사용해야 하는 경우 사용자 암호 및 애플리케이션 비밀을 Azure Key Vault에 저장하고 Key Vault 액세스 정책을 통해 액세스를 관리합니다.

- 앱에서 비밀을 보호하기 위해 다양한 앱 개발 프레임워크에서 프레임워크별 메커니즘을 제공할 수도 있습니다. [ASP.NET Core 앱](/aspnet/core/security/app-secrets?tabs=windows)을 예로 들 수 있습니다.

### <a name="use-sql-authentication-for-legacy-applications"></a>레거시 애플리케이션에 SQL 인증 사용

SQL 인증은 사용자 이름과 암호를 사용하여 Azure SQL Database 또는 SQL Managed Instance에 연결할 때 사용자가 수행하는 인증을 지칭합니다. 각 서버 또는 관리형 인스턴스와 각 데이터베이스에서 만든 사용자마다 로그인을 만들어야 합니다.

**구현 방법**:

- SQL 인증을 사용합니다.

**모범 사례**:

- 서버 또는 인스턴스 관리자로 로그인 및 사용자를 만듭니다. 포함된 데이터베이스 사용자와 암호를 함께 사용하지 않는 한, 모든 암호는 master 데이터베이스에 저장됩니다.
  - [SQL Database, SQL Managed Instance 및 Azure Synapse Analytics에 대한 데이터베이스 액세스 권한 제어 및 권한 부여](logins-create-manage.md) 문서를 참조하세요.

## <a name="access-management"></a>액세스 관리

액세스 관리(권한 부여라고도 함)는 권한 있는 사용자의 Azure SQL Database 또는 SQL Managed Instance에 대한 액세스 및 권한을 제어하고 관리하는 프로세스입니다.

### <a name="implement-principle-of-least-privilege"></a>최소 권한 원칙 구현

> 언급된 위치: FedRamp 규제 AC-06, NIST: AC-6, OSA Practice #3

최소 권한 원칙은 사용자에게 작업을 완료하는 데 필요한 권한보다 많은 권한을 부여하면 안 된다는 원칙입니다. 자세한 내용은 [Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview) 문서를 참조하세요.

**구현 방법**:

필수 작업을 완료하는 데 필요한 [권한](/sql/relational-databases/security/permissions-database-engine)만 할당합니다.

- SQL 데이터베이스:
  - 세부적인 권한 및 사용자 정의 데이터베이스 역할(또는 Managed Instance의 서버 역할)을 사용합니다.
    1. 필요한 역할 만들기
       - [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql)
       - [CREATE SERVER ROLE](/sql/t-sql/statements/create-server-role-transact-sql)
    1. 필요한 사용자 만들기
       - [CREATE USER](/sql/t-sql/statements/create-user-transact-sql)
    1. 사용자를 역할에 구성원으로 추가
       - [ALTER_ROLE](/sql/t-sql/statements/alter-role-transact-sql)
       - [ALTER SERVER ROLE](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. 그런 다음, 역할에 권한 할당
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - 사용자에게 불필요한 역할을 할당하면 안 됩니다.

- Azure Resource Manage:
  - 기본 제공 역할(사용 가능한 경우) 또는 Azure 사용자 지정 역할을 사용하고 필요한 권한을 할당합니다.
    - [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
    - [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)

**모범 사례**:

다음 모범 사례는 선택 사항이지만 보안 전략의 관리 효율성과 지원 가능성을 향상합니다.

- 권한을 하나씩 제거하는 방법과는 반대로, 되도록이면 최소 권한 세트로 시작하고 꼭 필요할 때마다 권한을 하나씩 추가합니다.

- 개별 사용자에게 권한을 할당하지 마세요. 대신 역할(데이터베이스 또는 서버 역할)을 일관적으로 사용합니다. 역할은 보고 및 문제 해결 권한에 매우 유용합니다. (Azure RBAC는 역할을 통한 권한 할당만 지원합니다.)

- 꼭 필요한 권한만 있는 사용자 지정 역할을 만들어서 사용합니다. 실제로 사용되는 일반적인 역할은 다음과 같습니다.
  - 보안 배포
  - 관리자
  - 개발자
  - 지원 담당자
  - 감사자
  - 자동화된 프로세스
  - 최종 사용자
  
- 역할의 권한이 사용자에게 필요한 권한과 정확히 일치하는 경우에만 기본 제공 역할을 사용합니다. 사용자에게 여러 역할을 할당할 수 있습니다.

- 다음 범위 내에서 데이터베이스 엔진의 권한을 적용할 수 있습니다(범위가 작을수록 부여된 권한의 영향도 축소).
  - Azure의 서버(master 데이터베이스의 특수 역할)
  - 데이터베이스
  - 스키마
    - 스키마를 사용하여 데이터베이스 내에서 권한을 부여하는 것이 가장 좋습니다. (참고 항목: [스키마 디자인: 보안을 염두에 둔 스키마 디자인을 위한 권장 사항](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - 개체(테이블, 보기, 프로시저 등)

  > [!NOTE]
  > 개체 수준에서는 전체 구현이 불필요하게 복잡해지므로 개체 수준에서 권한을 적용하지 않는 것이 좋습니다. 개체 수준 권한을 사용하기로 결정한다면 이 점을 명확하게 문서화해야 합니다. 이는 열 수준 권한에도 동일하게 적용되며, 같은 이유로 열 수준에서 권한을 적용하지 말 것을 강력하게 권장합니다. 또한 기본적으로 테이블 수준 [DENY](/sql/t-sql/statements/deny-object-permissions-transact-sql)는 열 수준 GRANT를 재정의하지 않습니다. 이렇게 하려면 [일반 조건 규정 준수 호환성 서버 구성](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)을 활성화해야 합니다.

- [VA(취약성 평가)](/sql/relational-databases/security/sql-vulnerability-assessment)를 통해 정기적으로 검사하여 권한이 너무 많지 않은지 테스트합니다.

### <a name="implement-separation-of-duties"></a>의무 분리 구현

> 언급된 위치: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

의무 분리(의무 구분이고도 함)는 중요한 작업을 여러 작업으로 분할하여 여러 사용자에게 할당해야 하는 요구 사항을 설명합니다. 의무를 분리하면 데이터 위반을 방지하는 데 도움이 됩니다.

**구현 방법**:

- 필요한 의무 분리 수준을 파악합니다. 예:
  - 개발/테스트 환경과 프로덕션 환경의 의무를 분리합니다.
  - 보안에 중요한 작업과 DBA(데이터베이스 관리자) 관리 수준 작업과 개발자 작업을 비교합니다.
    - 예: 감사자, RLS(역할 수준 보안)에 대한 보안 정책 만들기, DDL 권한으로 SQL Database 개체 구현

- 시스템에 액세스하는 사용자(및 자동화된 프로세스)의 포괄적인 계층 구조를 파악합니다.

- 필요한 사용자 그룹에 따라 역할을 만들고 역할에 권한을 할당합니다.
  - Azure Portal 또는 PowerShell 자동화를 통한 관리 수준 작업에는 Azure 역할을 사용합니다. 요구 사항과 일치하는 기본 제공 역할을 찾거나 사용 가능한 권한을 사용하여 Azure 사용자 지정 역할을 만듭니다.
  - 관리형 인스턴스에서 서버 수준 작업(새 로그인, 데이터베이스 만들기)을 위한 서버 역할을 만듭니다.
  - 데이터베이스 수준 작업을 위한 데이터베이스 역할을 만듭니다.

- 중요한 작업의 경우 인증서로 서명된 특수 저장 프로시저를 만들어 사용자 대신 작업을 실행하는 것이 좋습니다. 디지털 서명된 저장 프로시저의 중요한 장점 중 하나는 프로시저가 변경되면 이전 버전의 프로시저에 부여된 권한이 즉시 제거된다는 것입니다.
  - 예: [자습서: 인증서로 저장 프로시저 서명](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Azure Key Vault에서 고객 관리형 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하여 데이터 소유자와 보안 소유자의 의무를 분리합니다.
  - [Azure Portal에서 Azure Storage 암호화를 위한 고객 관리형 키 구성](../../storage/common/customer-managed-keys-configure-key-vault.md) 문서를 참조하세요.

- 매우 중요하다고 간주되는 데이터를 DBA에서 볼 수 없게 하면서도 DBA 작업을 계속 수행하려면 역할 구분과 함께 Always Encrypted를 사용하면 됩니다.
  - [Always Encrypted를 위한 키 관리 개요](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [역할 구분이 있는 키 프로비저닝](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles) 및 [역할 구분을 사용하는 열 마스터 키 순환](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation) 문서를 참조하세요.

- Always Encrypted를 구현할 수 없거나 적어도 시스템을 거의 사용할 수 없는 상태로 만들 수 있는 상당한 비용과 노력 없이는 사용할 수 없는 경우에는 타협안으로 다음과 같은 보상 컨트롤을 사용하여 완화할 수 있습니다.
  - 사람이 프로세스에 개입
  - 감사 내역 – 감사에 대한 자세한 내용은 [중요 보안 이벤트 감사](#audit-critical-security-events)를 참조하세요.

**모범 사례**:

- 개발/테스트 및 프로덕션 환경에 서로 다른 계정을 사용합니다. 서로 다른 계정을 사용하면 테스트 시스템과 프로덕션 시스템의 분리를 준수하는 데 도움이 됩니다.

- 개별 사용자에게 권한을 할당하지 마세요. 대신 역할(데이터베이스 또는 서버 역할)을 일관적으로 사용합니다. 역할이 있으면 보고 및 문제 해결 권한에 많은 도움이 됩니다.

- 권한이 필요한 권한과 정확히 일치하는 경우 기본 제공 역할을 사용합니다. 여러 기본 제공 역할의 모든 권한 통합이 100% 일치하는 경우 여러 역할을 동시에 할당할 수 있습니다.

- 기본 제공 역할이 너무 많은 권한을 부여하거나 권한이 부족한 경우 사용자 정의 역할을 만들어서 사용합니다.

- T-SQL의 SQL 에이전트 작업 단계에서 또는 Azure 역할에 Azure PIM을 사용하여 역할 할당을 일시적으로 수행할 수도 있으며 이를 DSD(동적 의무 분리)하고 합니다.

- DBA는 암호화 키 또는 키 저장소에 대한 액세스 권한이 없어야 하고, 키에 대한 액세스 권한이 있는 보안 관리자는 데이터베이스에 대한 액세스 권한이 없어야 합니다. [EKM(확장 가능 키 관리)](/sql/relational-databases/security/encryption/extensible-key-management-ekm)을 사용하면 이러한 분리를 보다 쉽게 수행할 수 있습니다. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 EKM을 구현할 수 있습니다.

- 항상 보안 관련 작업에 대한 감사 내역을 확보합니다.

- Azure 기본 제공 역할의 정의를 검색하여 사용된 권한을 확인하고 PowerShell을 통해 이러한 항목의 발췌 및 누적 정보를 기반으로 사용자 지정 역할을 만들 수 있습니다.

- ㅇb_owner 데이터베이스 역할의 모든 구성원은 TDE(투명한 데이터 암호화)와 같은 보안 설정을 변경하거나 SLO를 변경할 수 있기 때문에 이 멤버 자격을 신중하게 부여해야 합니다. 그러나 db_owner 권한이 필요한 많은 작업이 있습니다. DB 옵션 변경처럼 데이터베이스 설정을 변경하는 작업이 여기에 포함됩니다. 감사는 모든 솔루션에서 핵심적인 역할을 담당합니다.

- db_owner 권한을 제한하여 관리 계정으로 사용자 데이터를 볼 수 없도록 제한할 수는 없습니다. 데이터베이스에 매우 중요한 데이터가 있는 경우 Always Encrypted를 사용하여 db_owner 또는 다른 DBA가 해당 데이터를 볼 수 없도록 안전하게 차단할 수 있습니다.

> [!NOTE]
> 보안 관련 작업 또는 문제 해결 작업에서는 SoD(의무 분리)가 어렵습니다. 개발 및 최종 사용자 역할과 같은 다른 영역에서는 보다 쉽게 분리할 수 있습니다. 대부분의 규정 준수 관련 컨트롤은 다른 솔루션이 실용적이지 않을 때 감사와 같은 대체 제어 기능을 사용할 수 있습니다.

SoD에 대해 자세히 알아보려면 다음 리소스를 살펴보세요.

- Azure SQL Database 및 SQL Managed Instance:  
  - [데이터베이스 액세스 제어 및 권한 부여](logins-create-manage.md)
  - [애플리케이션 개발자를 위한 의무 분리 구현](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [의무 분리](https://www.microsoft.com/download/details.aspx?id=39269)
  - [저장 프로시저 서명](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure 리소스 관리:
  - [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
  - [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)
  - [상승된 액세스 권한에 Azure AD Privileged Identity Management 사용](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>주기적으로 코드 검토

> 언급된 위치: PCI: 6.3.2, SOC: SDL-3

의무 분리는 데이터베이스의 데이터로 제한되지 않고 애플리케이션 코드까지 포함합니다. 악의적인 코드가 보안 컨트롤을 회피할 가능성이 있습니다. 사용자 지정 코드를 프로덕션 환경에 배포하기 전에 무엇이 배포되는지 검토해야 합니다.

**구현 방법**:

- 원본 제어를 지원하는 Azure Data Studio 같은 데이터베이스 도구를 사용합니다.

- 분리된 코드 배포 프로세스를 구현합니다.

- 기본 분기에 커밋하기 전에, 코드 자체의 작성자가 아닌 사람이 코드에서 잠재적 권한 상승 위험과 악의적인 데이터 수정을 검사하여 사기 행위를 위한 액세스를 방지해야 합니다. 이 작업은 원본 제어 메커니즘을 사용하여 수행할 수 있습니다.

**모범 사례**:

- 표준화: 모든 코드 업데이트에서 따라야 하는 표준 절차를 구현하는 데 도움이 됩니다.

- 취약성 평가에는 과도한 권한, 오래된 암호화 알고리즘의 사용, 데이터베이스 스키마 내의 기타 보안 문제를 확인하는 규칙이 포함됩니다.

- 추가 검사는 SQL 삽입에 취약한 코드를 검사하는 Advanced Threat Protection을 사용하여 QA 또는 테스트 환경에서 수행할 수 있습니다.

- 확인할 항목의 예:
  - 자동화된 SQL 코드 업데이트 배포 내에서 사용자 생성 또는 보안 설정 변경
  - 제공된 매개 변수에 따라 규정을 따르지 않는 방식으로 셀의 통화 값을 업데이트하는 저장 프로시저

- 검토를 수행하는 원래 코드 작성자가 아니어야 하며 코드 검토 및 보안 코딩에 대해 잘 알고 있어야 합니다.

- 모든 코드 변경 소스를 알고 있어야 합니다. 코드가 T-SQL 스크립트에 있을 수 있습니다. 보기, 함수, 트리거 및 저장 프로시저의 형태로 실행되거나 배포되는 임시 명령일 수 있습니다. SQL 에이전트 작업 정의(단계)의 일부일 수 있습니다. SSIS 패키지, Azure Data Factory 및 기타 서비스 내에서 실행될 수도 있습니다.

## <a name="data-protection"></a>데이터 보호

데이터 보호는 암호화 또는 난독 처리를 사용하여 중요한 정보가 손상되지 않도록 보호하는 기능 세트입니다.

> [!NOTE]
> Microsoft는 Azure SQL Database 및 SQL Managed Instance가 FIPS 140-2 Level 1 규격이라는 것을 증명합니다. 이를 위해 FIPS 140-2 Level 1 허용 알고리즘과 이러한 알고리즘의 필수 키 길이, 키 관리, 키 생성 및 키 스토리지와의 일관성을 비롯한 FIPS 140-2 Level 1 검증 인스턴스 사용을 엄격하게 확인합니다. 이 증명의 의미는 고객이 데이터를 처리하거나 시스템 또는 애플리케이션을 제공할 때 FIPS 140-2 Level 1의 검증 인스턴스 사용에 대한 요구 사항에 대응할 수 있다는 뜻입니다. 위의 문장에 사용된 "FIPS 140-2 Level 1 규격" 및 "FIPS 140-2 Level 1 규정 준수"라는 용어는 미국과 캐나다 정부에 "FIPS 140-2 Level 1 검증"이라는 용어 사용을 보여주기 위해 Microsoft에서 정의한 용어입니다.

### <a name="encrypt-data-in-transit"></a>전송 중인 데이터 암호화

> 언급된 위치: OSA Practice #6, ISO Control Family: Cryptography

클라이언트와 서버 간에 데이터가 이동하는 동안 데이터를 보호합니다. [네트워크 보안](#network-security)을 참조하세요.

### <a name="encrypt-data-at-rest"></a>저장 데이터 암호화

> 언급된 위치: OSA Practice #6, ISO Control Family: Cryptography

미사용 데이터 암호화는 데이터베이스, 로그 및 백업 파일에 유지되는 데이터를 보호하는 암호화입니다.

**구현 방법**:

- 서비스 관리형 키를 사용하는 [TDE(투명 데이터베이스 암호화](transparent-data-encryption-tde-overview.md))는 2017년 이후에 Azure SQL Database 및 SQL Managed Instance에서 생성된 모든 데이터베이스에 대해 기본적으로 사용됩니다.
- 관리형 인스턴스에서, 온-프레미스 서버를 사용하여 복원 작업에서 데이터베이스를 만드는 경우 원본 데이터베이스의 TDE 설정이 적용됩니다. 원본 데이터베이스에서 TDE를 사용하지 않는 경우 관리형 인스턴스에 대해 TDE를 수동으로 켜는 것이 좋습니다.

**모범 사례**:

- 미사용 데이터 암호화가 필요한 데이터를 master 데이터베이스에 저장하지 않습니다. master 데이터베이스는 TDE로 암호화할 수 없습니다.

- 더 높은 투명도와 TDE 보호에 대한 세밀한 제어가 필요한 경우 Azure Key Vault에서 고객 관리형 키를 사용합니다. Azure Key Vault를 사용하면 언제든지 권한을 철회하여 데이터베이스에 액세스할 수 없게 만들 수 있습니다. 다른 키와 함께 TDE 보호기를 중앙에서 관리하거나 Azure Key Vault를 사용하여 원하는 일정대로 TDE 보호기를 순환할 수 있습니다.

- Azure Key Vault에서 고객 관리형 키를 사용하는 경우 [Azure Key Vault로 TDE를 구성하는 방법에 대한 지침](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) 및 [Azure Key Vault를 사용하여 지역 재해 복구를 구성하는 방법](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde) 문서를 따르세요.

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>높은 권한이 있지만 승인되지 않은 사용자로부터 중요한 사용 중 데이터 보호

사용 중인 데이터는 SQL 쿼리를 실행하는 동안 데이터베이스 시스템의 메모리에 저장되는 데이터입니다. 중요한 데이터가 데이터베이스에 저장되는 경우 조직에서는 높은 권한이 있는 사용자가 데이터베이스의 중요한 데이터를 볼 수 없도록 해야 할 때가 있습니다. 조직의 Microsoft 운영자나 DBA처럼 높은 권한이 있는 사용자는 데이터베이스를 관리할 수 있어야 하지만, SQL 프로세스의 메모리에서 중요한 데이터를 살펴보고 빼내거나 데이터베이스를 쿼리할 수 없게 만들어야 합니다.

어떤 데이터가 중요한지, 관리자가 일반 텍스트로 된 데이터에 액세스할 수 없도록 중요한 데이터를 메모리에서 암호화해야 하는지 여부를 결정하는 정책은 조직마다 그리고 조직에서 준수해야 하는 규정 및 규제에 따라 다릅니다. 관련 요구 사항: [중요한 데이터 식별 및 태그 지정](#identify-and-tag-sensitive-data)을 참조하세요.

**구현 방법**:

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 중요한 데이터가 메모리에 저장된/사용 중인 경우에도 Azure SQL Database 또는 SQL Managed Instance에서 일반 텍스트로 노출되지 않도록 합니다. Always Encrypted는 DBA(데이터베이스 관리자) 및 클라우드 관리자(또는 높은 권한이 있지만 승인되지 않는 사용자를 가장할 수 있는 악의적인 행위자)로부터 데이터를 보호하고, 데이터에 액세스할 수 있는 사용자를 보다 강력하게 제어할 수 있습니다.

**모범 사례**:

- Always Encrypted는 미사용 데이터 암호화(TDE) 또는 전송 중 데이터 암호화(SSL/TLS)를 대체하는 기능이 아닙니다. 성능 및 기능에 미치는 영향을 최소화하기 위해 중요하지 않은 데이터에는 Always Encrypted를 사용하면 안 됩니다. 미사용 데이터, 전송 중 데이터 및 사용 중 데이터를 포괄적으로 보호할 수 있도록 Always Encrypted를 TDE 및 TLS(전송 계층 보안)와 함께 사용하는 것이 좋습니다.

- 프로덕션 데이터베이스에 Always Encrypted를 배포하기 전에, 식별된 중요한 데이터 열을 암호화할 때 미치는 영향을 평가합니다. 일반적으로 Always Encrypted는 암호화된 열에 대한 쿼리 기능을 저하시키며, [Always Encrypted - 기능 정보](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)에 나열된 기타 제한 사항이 있습니다. 따라서 애플리케이션 아키텍처를 변경하여 클라이언트 쪽에서 클라이언트가 지원하지 않는 기능을 다시 구현하고/구현하거나 저장 프로시저, 함수, 보기 및 트리거 정의를 포함하여 데이터베이스 스키마를 리팩터링해야 할 수도 있습니다. 기존 애플리케이션은 Always Encrypted의 제한 사항을 따르지 않는 경우 암호화된 열에서 작동하지 않을 수 있습니다. Always Encrypted를 지원하는 Microsoft 도구, 제품 및 서비스 에코시스템이 확장되고 있지만, 그 중 상당수는 암호화된 열에서 작동하지 않습니다. 열을 암호화하면 워크로드의 특징에 따라 쿼리 성능에도 영향을 미칠 도 있습니다.

- 악의적인 DBA로부터 데이터를 보호하기 위해 Always Encrypted를 사용하는 경우 역할 구분을 통해 Always Encrypted 키를 관리합니다. 역할 구분을 사용하는 경우 보안 관리자는 물리적 키를 만듭니다. DBA는 데이터베이스의 물리적 키를 설명하는 열 마스터 키와 열 암호화 키 메타데이터 개체를 만듭니다. 이 과정에서 보안 관리자는 데이터베이스에 액세스할 필요가 없고, DBA는 일반 텍스트로 된 물리적 키에 액세스할 필요가 없습니다.
  - 자세한 내용은 [역할 구분을 사용하여 키 관리](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) 문서를 참조하세요.

- 관리가 용이하도록 열 마스터 키를 Azure Key Vault에 저장합니다. 중앙 키 관리 솔루션과는 다르게 Windows 인증서 저장소(그리고 일반적으로 분산 키 저장 솔루션)를 사용하지 않습니다. 사용하면 키 관리가 어려워집니다.

- 여러 키(열 마스터 키 또는 열 암호화 키)를 사용할 때의 장단점을 신중하게 생각해 보세요. 키 관리 비용을 줄이려면 키 수를 적게 유지해야 합니다. 일반적으로 안정적 상태의 환경(키가 순환 중이 아닌)에서는 데이터베이스당 열 마스터 키 하나와 열 암호화 키 하나만 있으면 충분합니다. 다른 사용자 그룹이 있고 각 그룹이 서로 다른 키를 사용하여 서로 다른 데이터에 액세스하는 경우에는 추가 키가 필요할 수 있습니다.  

- 규정 준수 요구 사항에 따라 열 마스터 키를 순환합니다. 열 암호화 키를 순환해야 하는 경우 온라인 암호화를 사용하여 애플리케이션 가동 중지 시간을 최소화하는 것이 좋습니다.
  - [성능 및 가용성 고려 사항](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations) 문서를 참조하세요.

- 데이터의 계산(같음)을 지원해야 하는 경우 결정적 암호화를 사용합니다. 그렇지 않으면 임의 암호화를 사용합니다. 낮은 엔트로피 데이터 세트 또는 공개적으로 알려진 배포가 포함된 데이터 세트에는 결정적 암호화를 사용하지 않습니다.

- 타사가 동의 없이 불법적으로 데이터에 액세스하는 것이 우려되는 경우 일반 텍스트로 된 키와 데이터에 액세스할 수 있는 모든 애플리케이션 및 도구가 Microsoft Azure 클라우드 외부에서 실행되도록 합니다. 키에 대한 액세스 권한이 없으면 타사가 암호화를 우회하지 않는 한 데이터를 암호 해독할 수 없습니다.

- Always Encrypted는 키(및 보호된 데이터)에 대한 임시 액세스 권한 부여를 쉽게 지원하지 않습니다. 예를 들어 DBA가 암호화된 중요한 데이터에 대해 정리 작업을 수행할 수 있도록 DBA와 키를 공유해야 한다고 가정하겠습니다. DBA의 데이터 액세스 권한을 안정적으로 철회하는 유일한 방법은 데이터를 보호하는 열 암호화 키와 열 마스터 키를 모두 순환하는 것이지만, 이는 비용이 많이 드는 작업입니다.

- 사용자가 암호화된 열의 일반 텍스트 값에 액세스하려면 열을 보호하는 CMK(열 마스터 키)에 대한 액세스 권한이 필요하며, 이러한 열은 CMK를 보관하는 키 저장소에서 구성합니다. 또한 사용자에게 **VIEW ANY COLUMN MASTER KEY DEFINITION** 및 **VIEW ANY COLUMN ENCRYPTION KEY DEFINITION** 데이터베이스 권한이 있어야 합니다.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>암호화를 통해 중요한 데이터에 대한 애플리케이션 사용자의 액세스 제어

암호화 키에 대한 액세스 권한이 있는 특정 애플리케이션 사용자만 데이터를 보거나 업데이트할 수 있도록 하는 방법으로 암호화를 사용할 수 있습니다.

**구현 방법**:

- CLE(셀 수준 암호화)를 사용합니다. 자세한 내용은 [데이터 열 암호화](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 문서를 참조하세요.
- Always Encrypted를 사용합니다. 하지만 제한 사항을 알고 있어야 합니다. 제한 사항은 아래에 나열되어 있습니다.

**모범 사례**

CLE를 사용하는 경우:

- SQL 권한 및 역할을 통해 키 액세스 권한을 제어합니다.

- 데이터 암호화에 AES(AES 256 권장)를 사용합니다. RC4, DES, TripleDES 등의 알고리즘은 더 이상 사용되지 않으며 알려진 취약성 때문에 사용하면 안 됩니다.

- 3DES를 사용하지 않도록 비대칭 키/인증서(암호 아님)를 사용하여 대칭 키를 보호합니다.

- (bacpac 파일) 내보내기/가져오기를 통해 셀 수준 암호화를 사용하여 데이터베이스를 마이그레이션할 때는 주의해야 합니다.
  - 데이터를 마이그레이션할 때 키 손실을 방지하는 방법과 기타 모범 사례 지침에 대한 [Azure SQL Database에서 셀 수준 암호화 사용에 대한 권장 사항](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)을 참조하세요.

일반적으로 Always Encrypted는 사용 중인 중요한 데이터를 Azure SQL Database의 높은 권한 사용자(클라우드 운영자, DBA)로부터 보호하기 위해 설계되었습니다. [사용 중인 중요한 데이터를 높은 권한이 있지만 승인되지 않은 사용자로부터 보호](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users) 문서를 참조하세요. Always Encrypted를 사용하여 애플리케이션 사용자로부터 데이터를 보호할 때 다음과 같은 문제를 고려해야 합니다.

- 기본적으로 Always Encrypted를 지원하는 모든 Microsoft 클라이언트 드라이버는 열 암호화 키의 전역(애플리케이션마다 하나) 캐시를 유지합니다. 클라이언트 드라이버가 열 마스터 키를 보관하는 키 저장소에 연결하여 일반 텍스트 열 암호화 키를 얻으면 일반 텍스트 열 암호화 키가 캐시됩니다. 그러면 다중 사용자 애플리케이션 사용자로부터 데이터를 격리하기가 어려워집니다. 키 저장소(예: Azure Key Vault)와 상호 작용할 때 애플리케이션이 최종 사용자를 가장하는 경우 사용자의 쿼리가 캐시를 열 암호화 키로 채운 후, 동일한 키가 필요하지만 다른 사용자가 트리거한 후속 쿼리는 캐시된 키를 사용합니다. 드라이버는 키 저장소를 호출하지 않으며 두 번째 사용자에게 열 암호화 키에 대한 액세스 권한이 있는지 확인하지 않습니다. 결과적으로 사용자가 키 액세스 권한이 없어도 암호화된 데이터를 볼 수 있습니다. 다중 사용자 애플리케이션에서 사용자를 격리하려면 열 암호화 키 캐싱을 사용하지 않도록 설정하면 됩니다. 캐싱을 사용하지 않도록 설정하면 드라이버가 데이터 암호화 또는 암호 해독 작업마다 키 저장소에 연결해야 하므로 추가 성능 오버헤드가 발생합니다.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>데이터 형식을 유지하면서도 애플리케이션 사용자가 데이터를 무단으로 볼 수 없도록 보호

권한 없는 사용자가 데이터를 볼 수 없도록 방지하는 또 다른 방법은 데이터 유형 및 형식을 유지하면서 데이터를 난독 처리하거나 마스킹하여 사용자 애플리케이션에서 데이터를 계속 처리하고 표시할 수 있도록 하는 것입니다.

**구현 방법**:

- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)을 사용하여 테이블 열을 난독 처리합니다.

> [!NOTE]
> Always Encrypted는 동적 데이터 마스킹과 함께 작동하지 않습니다. 동일한 열을 암호화하고 마스킹할 수 없습니다. 즉, 사용 중인 데이터 보호와 동적 데이터 마스킹을 통해 앱 사용자에 대한 데이터 마스킹 중 무엇이 우선인지 결정해야 합니다.

**모범 사례**:

> [!NOTE]
> 동적 데이터 마스킹은 높은 권한이 있는 사용자로부터 데이터를 보호하는 데 사용할 수 없습니다. 마스킹 정책은 db_owner처럼 관리자 액세스 권한이 있는 사용자에게는 적용되지 않습니다.

- 앱 사용자가 임시 쿼리를 실행하는 것을 허용하지 않습니다(앱 사용자가 동적 데이터 마스킹을 회피할 수 있으므로).  
  - 자세한 내용은 [유추 또는 무차별 대입 기업을 사용하여 마스킹 무시](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) 문서를 참조하세요.  

- 적절한 액세스 제어 정책을 사용하여(SQL 권한, 역할, RLS를 통해) 마스킹된 열을 업데이트하는 사용자 권한을 제한합니다. 열에서 마스크를 만들어도 해당 열에 대한 업데이트가 차단되지 않습니다. 사용자가 마스킹된 열을 쿼리할 때 마스킹된 데이터를 받더라도 쓰기 권한이 있다면 데이터를 업데이트할 수 있습니다.

- 동적 데이터 마스킹은 마스킹된 값의 통계 속성을 유지하지 않습니다. 이는 쿼리 결과에 영향을 미칠 수 있습니다(예: 마스킹된 데이터에 대한 필터링 조건자 또는 조인이 포함된 쿼리).

## <a name="network-security"></a>네트워크 보안

네트워크 보안은 Azure SQL Database로 전송 중인 데이터를 보호하기 위한 액세스 제어 및 모범 사례를 의미합니다.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>SQL Database/SQL Managed Instance에 안전하게 연결하도록 클라이언트 구성

잘 알려진 취약성(예: 오래된 TLS 프로토콜 및 암호화 도구 모음 사용)이 있는 클라이언트 머신과 애플리케이션에 Azure SQL Database 및 SQL Managed Instance에 연결하지 못하게 차단하는 방법에 대한 모범 사례입니다.

**구현 방법**:

- Azure SQL Database 및 SQL Managed Instance에 연결하는 클라이언트 머신에서 [TLS(전송 계층 보안)](security-overview.md#transport-layer-security-encryption-in-transit)를 사용합니다.

**모범 사례**:

- 암호화를 사용하여 SQL Database에 연결하도록 모든 앱과 도구를 구성합니다.
  - Encrypt = On, TrustServerCertificate = Off(또는 타사의 해당 드라이버).

- 앱이 TLS를 지원하지 않거나 이전 버전의 TLS를 지원하는 드라이버를 사용하는 경우 되도록이면 드라이버를 바꿉니다. 바꿀 수 없는 경우 보안 위험을 신중하게 평가합니다.

- [TLS(전송 계층 보안) 레지스트리 설정](/windows-server/security/tls/tls-registry-settings#tls-10)에 따라 Azure SQL Database에 연결하는 클라이언트 머신에서 SSL 2.0, SSL 3.0, TLS 1.0 및 TLS 1.1을 사용하지 않도록 설정하여 취약성을 통한 공격 벡터를 줄입니다.

- 클라이언트에서 사용할 수 있는 [TLS/SSL의 암호화 도구 모음(Schannel SSP)](/windows/desktop/SecAuthN/cipher-suites-in-schannel)을 확인합니다. 특히 [TLS 암호화 도구 모음 순서 구성](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)에 따라 3DES를 사용하지 않도록 설정합니다.

- Azure SQL Database 및 SQL Managed Instance의 경우 프록시 및 리디렉션 연결 형식 모두에 대해 암호화가 적용됩니다. Azure SQL Managed Instance의 경우 서버 쪽에서 암호화를 적용하는 **프록시** 연결 형식(기본값)을 사용합니다. **리디렉션** 연결 형식은 현재 암호화 적용을 지원하지 않으며 개인 IP 연결에만 사용할 수 있습니다.

- 자세한 내용은 [Azure SQL Database 연결 아키텍처 - 연결 정책](connectivity-architecture.md#connection-policy)을 참조하세요.

### <a name="minimize-attack-surface"></a>공격 노출 영역 최소화

악의적인 사용자가 공격할 수 있는 기능 수를 최소화합니다. Azure SQL Database에 대한 네트워크 액세스 제어를 구현합니다.

> 언급된 위치: OSA Practice #5

**구현 방법**:

SQL 데이터베이스:

- 서버 수준에서 [Azure 서비스에 대한 액세스 허용]을 [끄기]로 설정합니다.
- VNet 서비스 엔드포인트 및 VNet 방화벽 규칙을 사용합니다.
- 프라이빗 링크(미리 보기)를 사용합니다.

SQL Managed Instance:

- [네트워크 요구 사항](../managed-instance/connectivity-architecture-overview.md#network-requirements)의 지침을 따릅니다.

**모범 사례**:

- 프라이빗 엔드포인트에서 연결하여(예: 프라이빗 데이터 경로 사용) Azure SQL Database 및 SQL Managed Instance에 대한 액세스를 제한합니다.
  - 관리형 인스턴스를 가상 네트워크 내부에 격리하여 외부 액세스를 방지할 수 있습니다. 같은 지역의 같은 피어링 가상 네트워크에 있는 애플리케이션과 도구는 관리형 인스턴스에 직접 액세스할 수 있습니다. 다른 지역에 있는 애플리케이션과 도구는 가상 네트워크 간 연결 또는 ExpressRoute 회로 피어링을 사용하여 연결을 설정할 수 있습니다. 고객은 NSG(네트워크 보안 그룹)를 사용하여 포트 1433을 통한 액세스를 관리형 인스턴스에 액세스해야 하는 리소스로만 제한해야 합니다.
  - SQL Database의 경우 가상 네트워크 내의 서버에 전용 개인 IP를 제공하는 [프라이빗 링크](../../private-link/private-endpoint-overview.md) 기능을 사용합니다. [가상 네트워크 방화벽 규칙을 사용하는 가상 네트워크 서비스 엔드포인트](vnet-service-endpoint-rule-overview.md)를 사용하여 서버 액세스를 제한할 수도 있습니다.
  - 모바일 사용자는 지점 및 사이트 간 VPN 연결을 사용하여 데이터 경로를 통해 연결해야 합니다.
  - 온-프레미스 네트워크에 연결된 사용자는 사이트 간 VPN 연결 또는 ExpressRoute를 사용하여 데이터 경로를 통해 연결해야 합니다.

- 퍼블릭 엔드포인트에 연결하여(예: 퍼블릭 데이터 경로 사용) Azure SQL Database 및 SQL Managed Instance에 액세스할 수 있습니다. 다음 모범 사례를 고려해야 합니다.
  - SQL Database의 서버인 경우 [IP 방화벽 규칙](firewall-configure.md)을 사용하여 권한이 부여된 IP 주소에만 액세스할 수 있도록 제한합니다.
  - SQL Managed Instance의 경우 NSG(네트워크 보안 그룹)를 사용하여 포트 3342를 통해 필요한 리소스에만 액세스할 수 있도록 제한합니다. 자세한 내용은 [퍼블릭 엔드포인트를 사용하여 안전하게 관리형 인스턴스 사용](../managed-instance/public-endpoint-overview.md)을 참조하세요.

> [!NOTE]
> SQL Managed Instance 퍼블릭 엔드포인트는 기본적으로 사용되지 않으므로 명시적으로 사용하도록 설정해야 합니다. 회사 정책에서 퍼블릭 엔드포인트 사용을 허용하지 않는 경우 [Azure Policy](../../governance/policy/overview.md)를 사용하여 퍼블릭 엔드포인트 사용을 금지합니다.

- Azure 네트워킹 구성 요소 설정:
  - [Azure 네트워크 보안 모범 사례](../../security/fundamentals/network-best-practices.md)를 따릅니다.
  - [Azure Virtual Network FAQ(질문과 대답)](../../virtual-network/virtual-networks-faq.md) 및 계획에 설명된 모범 사례에 따라 Virtual Network 구성을 계획합니다.
  - 가상 네트워크를 여러 서브넷으로 분할하고 유사한 역할에 대한 리소스를 동일한 서브넷에 할당합니다(예: 프런트 엔드 리소스 또는 백 엔드 리소스).
  - [NSG(네트워크 보안 그룹)](../../virtual-network/network-security-groups-overview.md)를 사용하여 Azure 가상 네트워크 경계 내부의 서브넷 간 트래픽을 제어합니다.
  - 구독에 [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)를 사용하도록 설정하여 인바운드 및 아웃바운드 네트워크 트래픽을 모니터링합니다.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database/SQL Managed Instance에 안전하게 연결할 수 있도록 Power BI 구성

**모범 사례**:

- Power BI Desktop의 경우 되도록이면 프라이빗 데이터 경로를 사용합니다.

- Power BI Desktop이 TLS1.2를 사용하여 연결하도록 [TLS(전송 계층 보안)](/windows-server/security/tls/tls-registry-settings) 레지스트리 설정에 따라 클라이언트 머신에서 레지스트리 키를 설정합니다.

- [Power BI를 사용하는 RLS(행 수준 보안)](/power-bi/service-admin-rls)를 통해 특정 사용자의 데이터 액세스를 제한합니다.

- Power BI 서비스의 경우 [온-프레미스 데이터 게이트웨이](/power-bi/service-gateway-onprem)를 사용하되, [제한 사항 및 고려 사항](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)을 염두에 두어야 합니다.

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database/SQL Managed Instance에 안전하게 연결할 수 있도록 App Service 구성

**모범 사례**:

- 간단한 웹앱의 경우 퍼블릭 엔드포인트를 통해 연결하려면 **Azure 서비스 허용** 을 [켜기]로 설정해야 합니다.

- 프라이빗 데이터 경로가 관리형 인스턴스에 연결되도록 [Azure Virtual Network에 앱을 통합](../../app-service/overview-vnet-integration.md)합니다. 필요에 따라 [ASE(App Service Environments)](../../app-service/environment/intro.md)를 사용하여 웹앱을 배포할 수도 있습니다.

- ASE를 사용하는 웹앱 또는 SQL Database의 데이터베이스에 연결하는 가상 네트워크 통합 웹앱의 경우 [가상 네트워크 서비스 엔드포인트 및 가상 네트워크 방화벽 규칙](vnet-service-endpoint-rule-overview.md)을 사용하여 특정 가상 네트워크 및 서브넷의 액세스를 제한할 수 있습니다. 그런 다음, **Azure 서비스 허용** 을 [끄기]로 설정합니다. 프라이빗 데이터 경로를 통해 SQL Managed Instance의 관리형 인스턴스에 ASE를 연결할 수도 있습니다.  

- [Azure App Service를 사용하여 PaaS(Platform as a Service) 웹 및 모바일 애플리케이션을 보호하는 모범 사례](../../security/fundamentals/paas-applications-using-app-services.md) 문서에 따라 웹앱을 구성합니다.

- [WAF(웹 애플리케이션 방화벽)](../../web-application-firewall/ag/ag-overview.md)를 설치하여 일반적인 악용 및 취약성으로부터 웹 앱을 보호합니다.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database/SQL Managed Instance에 안전하게 연결할 수 있도록 Azure 가상 머신 호스팅 구성

**모범 사례**:

- Azure 가상 머신의 NSG에서 허용 및 거부 규칙의 조합을 사용하여 VM에서 액세스할 수 있는 지역을 제어합니다.

- [Azure의 IaaS 워크로드에 대한 보안 모범 사례](../../security/fundamentals/iaas.md) 문서에 따라 VM을 구성합니다.

- 모든 VM이 특정 가상 네트워크 및 서브넷에 연결되어 있는지 확인합니다.

- [강제 터널링 정보](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)의 지침에 따라 기본 경로 0.0.0.0/인터넷이 필요한지 평가합니다.
  - 필요한 경우(예: 프런트 엔드 서브넷) 기본 경로를 유지합니다.
  - 필요 없는 경우(예: 중간 계층 또는 백 엔드 서브넷) 트래픽이 인터넷을 통해 온-프레미스에 도달하지 않도록(즉, 크로스-프레미스) 강제 터널링을 사용합니다.

- 피어링을 사용하거나 온-프레미스에 연결하는 경우 [선택적 기본 경로](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)를 구현합니다.

- 패킷 검사를 위해 가상 네트워크의 모든 트래픽을 네트워크 가상 어플라이언스로 보내야 하는 경우 [사용자 정의 경로](../../virtual-network/virtual-networks-udr-overview.md#user-defined)를 구현합니다.

- Azure 백본 네트워크를 통해 Azure Storage 같은 PaaS 서비스에 안전하게 액세스할 수 있도록 [가상 네트워크 서비스 엔드포인트](vnet-service-endpoint-rule-overview.md)를 사용합니다.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>DDoS 공격 방어

악의적인 사용자가 Azure 인프라를 과도하게 사용하여 유효한 로그인 및 워크로드를 거부하게 만들려는 목적으로 대량의 네트워크 트래픽을 Azure SQL Database로 보내는 시도가 DDoS 공격입니다.

> 언급된 위치: OSA Practice #9

**구현 방법**:

Azure 플랫폼에서는 DDoS 보호가 자동으로 사용됩니다. 여기에는 퍼블릭 엔드포인트에 대한 네트워크 수준 공격의 상시 트래픽 모니터링 및 실시간 완화가 포함됩니다.

- [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)을 사용하여 가상 네트워크에 배포된 리소스에 연결된 공용 IP 주소를 모니터링합니다.

- [Azure SQL Database의 Advanced Threat Protection](threat-detection-overview.md)을 사용하여 데이터베이스에 대한 서비스 거부 공격을 감지합니다.

**모범 사례**:

- [공격 노출 영역 최소화](#minimize-attack-surface)에 설명된 지침에 따라 DDoS 공격 위협을 최소화합니다.

- Advanced Threat Protection **무차별 암호 대입 SQL 자격 증명** 경고는 무차별 암호 대입 공격을 감지하는 데 도움이 됩니다. 경우에 따라 경고가 침투 테스트 워크로드를 구분할 수도 있습니다.

- SQL Database에 연결하는 Azure VM 호스팅 애플리케이션:
  - 권장 사항에 따라 클라우드의 Microsoft Defender에서 인터넷 연결 끝점을 통해 액세스를 제한 합니다.
  - 가상 머신 확장 집합을 사용하여 Azure VM에서 애플리케이션의 여러 인스턴스를 실행합니다.
  - 인터넷에서 RDP 및 SSH를 사용하지 않도록 설정하여 무차별 암호 대입 공격을 방지합니다.

## <a name="monitoring-logging-and-auditing"></a>모니터링, 로깅 및 감사

이 섹션에서는 비정상적이고 위험할 수 있는 데이터베이스 액세스 또는 익스플로잇 시도를 의미하는 비정상적인 활동을 감지하는 데 도움이 되는 기능을 설명합니다. 또한 데이터베이스 이벤트를 추적하고 캡처하도록 데이터베이스 감사를 구성하는 모범 사례에 대해 설명합니다.

### <a name="protect-databases-against-attacks"></a>공격으로부터 데이터베이스 보호

Advanced Threat Protection을 사용하면 비정상적인 활동에 대한 보안 경고를 제공하여 발생하는 잠재적 위협을 감지 및 대응할 수 있습니다.

**구현 방법**:

- [SQL용 Advanced Threat Protection](threat-detection-overview.md#alerts)을 사용하여 다음을 포함한 비정상적이고 위험할 수 있는 데이터베이스 액세스 또는 익스플로잇 시도를 감지합니다.
  - SQL 삽입 공격
  - 자격 증명 도난/유출
  - 권한 남용
  - 데이터 반출

**모범 사례**:

- 특정 서버 또는 관리 되는 인스턴스에 대 한 [SQL에 대해 Microsoft Defender](azure-defender-for-sql.md)를 구성   합니다. 또한 microsoft defender for [Cloud](../../security-center/security-center-pricing.md)를 사용 하도록 설정 하 여 구독의 모든 서버 및 관리 되는 인스턴스에 대해 microsoft defender SQL를 구성할 수 있습니다.

- 전체 조사 환경에는  [SQL Database 감사](../../azure-sql/database/auditing-overview.md)를 사용하도록 설정하는 것이 좋습니다. 감사를 사용하면 데이터베이스 이벤트를 추적하고 Azure Storage 계정 또는 Azure Log Analytics 작업 영역의 감사 로그에 기록할 수 있습니다.

### <a name="audit-critical-security-events"></a>중요 보안 이벤트 감사

데이터베이스 이벤트를 추적하면 데이터베이스 작업을 이해하는 데 도움이 됩니다. 비즈니스 문제나 의심스러운 보안 위반을 의미할 수 있는 불일치 및 이상 활동에 대한 인사이트를 얻을 수 있습니다. 또한 용이하게 규정 준수 표준을 준수하도록 합니다.

**구현 방법**:

-  [SQL Database 감사](../../azure-sql/database/auditing-overview.md) 또는 [Managed Instance 감사](../managed-instance/auditing-configure.md)를 사용하도록 설정하여 데이터베이스 이벤트를 추적하고 Azure Storage 계정, Log Analytics 작업 영역(미리 보기) 또는 Event Hubs(미리 보기)의 감사 로그에 기록할 수 있습니다.

- 감사 로그는 Azure 스토리지 계정에 기록하거나, Azure Monitor 로그에서 사용할 수 있도록 Log Analytics 작업 영역에 기록하거나, 이벤트 허브를 통해 사용할 수 있도록 이벤트 허브에 기록할 수 있습니다. 이러한 옵션을 조합하여 구성할 수 있으며, 감사 로그는 각각에 대해 작성됩니다.

**모범 사례**:

- 서버에서 [SQL Database 감사](../../azure-sql/database/auditing-overview.md)를 구성하거나 이벤트를 감사하도록 [Managed Instance 감사](../managed-instance/auditing-configure.md)를 구성하면 해당 서버에 있는 모든 기존 데이터베이스와 새로 만든 데이터베이스가 감사됩니다.
- 기본적으로 감사 정책에는 데이터베이스에 대한 모든 작업(쿼리, 저장 프로시저 및 성공/실패한 로그인)이 포함되며, 이로 인해 감사 로그의 볼륨이 커질 수 있습니다. 고객은 [PowerShell을 사용하여 다양한 유형의 작업과 작업 그룹에 대한 감사를 구성](./auditing-overview.md#manage-auditing)하는 것이 좋습니다. 이렇게 구성하면 감사되는 작업의 수를 제어하고 이벤트 손실 위험을 최소화할 수 있습니다. 사용자 지정 감사 구성을 통해 고객은 필요한 감사 데이터만 캡처할 수 있습니다.
- 감사 로그는 [Azure Portal](https://portal.azure.com/)에서 직접 사용하거나 구성된 스토리지 위치에서 사용할 수 있습니다.

> [!NOTE]
> Log Analytics를 감사하도록 설정하면 수집 속도에 따라 비용이 발생합니다. 이 [옵션](https://azure.microsoft.com/pricing/details/monitor/) 사용 시 발생하는 비용을 알아보거나 감사 로그를 Azure 스토리지 계정에 저장하세요.

**추가 리소스**:

- [SQL 데이터베이스 감사](../../azure-sql/database/auditing-overview.md)
- [SQL Server 감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>보안 감사 로그

스토리지 계정에 대한 액세스를 제한하여 의무 분리를 지원하고 감사자와 DBA를 분리합니다.

**구현 방법**:

- Azure Storage에 감사 로그를 저장하는 경우 스토리지 계정에 대한 액세스를 최소한의 보안 원칙으로 제한합니다. 스토리지 계정에 액세스할 수 있는 사용자를 제어합니다.
- 자세한 내용은 [Azure Storage에 대한 액세스 권한 부여](../../storage/common/authorize-data-access.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

**모범 사례**:

- 감사자와 DBA를 분리할 때 가장 중요한 것은 감사 대상에 대한 액세스를 제어하는 것입니다.

- 중요한 데이터에 대한 액세스를 감사할 때 감사자에게 정보가 누출되지 않도록 데이터 암호화를 사용하여 데이터를 보호하는 것이 좋습니다. 자세한 내용은 [높은 권한이 있지만 승인되지 않은 사용자로부터 중요한 사용 중 데이터 보호](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users) 섹션을 참조하세요.

## <a name="security-management"></a>보안 관리

이 섹션에서는 데이터베이스 보안 태세 관리의 다양한 측면과 모범 사례에 대해 설명합니다. 여기에는 보안 표준을 충족하도록 데이터베이스를 구성하고 데이터베이스의 잠재적으로 중요한 데이터에 대한 액세스를 발견, 분류, 추적하도록 데이터베이스를 구성하는 모범 사례가 포함합니다.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>보안 모범 사례를 충족하도록 데이터베이스 구성

잠재적 데이터베이스 취약성을 발견하고 수정하여 데이터베이스 보안을 사전에 향상합니다.

**구현 방법**:

- [SQL VA(취약성 평가)](/sql/relational-databases/security/sql-vulnerability-assessment)를 사용하여 데이터베이스의 보안 문제를 검사하고, 데이터베이스에서 주기적으로 평가를 자동 실행합니다.

**모범 사례**:

- 처음에는 데이터베이스에서 VA를 실행하고 보안 모범 사례에 맞지 않아 실패하는 검사를 수정하여 반복합니다. 검사 결과가 _깨끗_ 하거나 모든 검사를 통과할 때까지 허용 가능한 구성의 기준을 설정합니다.  

- 주기적 되풀이 검사를 구성하여 매주 한 번 검사를 실행하고 요약 정보 이메일을 받을 담당자를 구성합니다.

- 주간 검사 후 VA 요약 정보를 검토합니다. 취약성이 발견되면 이전 검사 결과의 드리프트를 평가하고 검사를 해명해야 하는지 확인합니다. 구성을 변경해야 하는 정당한 이유가 있는지 검토합니다.

- 검사를 해명하고 관련된 기준을 업데이트합니다. 작업 확인을 위한 티켓 항목을 만들고 모두 확인될 때까지 티켓 항목을 추적합니다.

**추가 리소스**:

- [SQL 취약성 평가](/sql/relational-databases/security/sql-vulnerability-assessment)
- [SQL 취약성 평가 서비스는 데이터베이스 취약성을 식별하는 데 도움이 됩니다.](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>중요한 데이터 식별 및 태그 지정

중요한 데이터가 포함될 수 있는 열을 찾습니다. 중요한 데이터로 간주되는 항목은 고객, 규정 준수 규정 등에 따라 크게 달라지며 데이터를 담당하는 사용자의 평가를 받아야 합니다. 고급 민감도 기반 감사 및 보호 시나리오를 사용할 열을 분류합니다.

**구현 방법**:

- [SQL 데이터 검색 및 분류](data-discovery-and-classification-overview.md)를 사용하여 데이터베이스의 중요한 데이터를 검색, 분류, 보호하고 레이블을 지정합니다.
  - SQL 데이터 검색 및 분류 대시보드에서 자동 검색을 사용하여 생성되는 분류 권장 사항을 살펴봅니다. 관련 분류를 수락합니다. 그러면 중요한 데이터에 분류 레이블이 영구적인 태그로 지정됩니다.
  - 자동 메커니즘에서 발견하지 못한 추가적인 중요 데이터 필드의 분류를 수동으로 추가합니다.
- 자세한 내용은 [SQL 데이터 검색 및 분류](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하세요.

**모범 사례**:

- 정기적으로 분류 대시보드를 모니터링하여 데이터베이스의 분류 상태를 정확하게 평가합니다. 규정 준수 및 감사 목적으로 데이터베이스 분류 상태 보고서를 내보내거나 인쇄하여 공유할 수 있습니다.

- 권장하는 중요 데이터의 상태를 SQL 취약성 평가에서 지속적으로 모니터링합니다. 중요한 데이터 검색 규칙을 추적하고 분류에 대한 권장 열의 드리프트를 확인합니다.  

- 조직의 요구 사항에 부합하는 방식으로 분류를 사용합니다. Microsoft Defender for Cloud의 [SQL Information Protection](../../security-center/security-center-info-protection-policy.md) 정책에서 Information Protection 정책 (민감도 레이블, 정보 유형, 검색 논리)을 사용자 지정 합니다.

### <a name="track-access-to-sensitive-data"></a>중요한 데이터에 대한 액세스 추적

중요한 데이터에 액세스하는 사용자를 모니터링하고 감사 로그에서 중요한 데이터에 대한 쿼리를 캡처합니다.

**구현 방법**:

- SQL 감사 및 데이터 분류를 함께 사용합니다.
  - [SQL Database 감사](../../azure-sql/database/auditing-overview.md) 로그에서 특히 중요한 데이터에 대한 액세스를 추적할 수 있습니다. 또한 액세스한 데이터 및 해당 데이터의 민감도 레이블과 같은 정보를 볼 수 있습니다. 자세한 내용은 [데이터 검색 및 분류](data-discovery-and-classification-overview.md) 및 [중요한 데이터에 대한 액세스 감사](data-discovery-and-classification-overview.md#audit-sensitive-data)를 참조하세요.

**모범 사례**:

- 감사 및 데이터 분류 섹션에 대한 모범 사례를 참조하세요.
  - [중요 보안 이벤트 감사](#audit-critical-security-events)
  - [중요한 데이터 식별 및 태그 지정](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>보안 및 규정 준수 상태 시각화

데이터 센터(SQL Database의 데이터베이스 포함)의 보안 태세를 강화하는 통합 인프라 보안 관리 시스템을 사용합니다. 데이터베이스 보안 및 규정 준수 상태와 관련된 권장 사항 목록을 살펴봅니다.

**구현 방법**:

- [Microsoft Defender for Cloud](https://azure.microsoft.com/documentation/services/security-center/)에서 SQL 관련 보안 권장 사항 및 활성 위협을 모니터링 합니다.

## <a name="common-security-threats-and-potential-mitigations"></a>일반적인 보안 위협 및 가능한 완화 방법

이 섹션은 특정 공격 벡터로부터 보호하기 위한 보안 조치를 찾는 데 도움이 됩니다. 대부분의 위협은 위의 보안 지침 중 하나 이상을 수행하여 완화할 수 있습니다.

### <a name="security-threat-data-exfiltration"></a>보안 위협: 데이터 반출

데이터 반출은 컴퓨터 또는 서버의 데이터를 권한 없이 복사, 전송 또는 검색하는 행위입니다. Wikipedia의 [데이터 반출](https://en.wikipedia.org/wiki/Data_exfiltration)에 대한 정의를 참조하세요.

퍼블릭 엔드포인트를 통해 서버에 연결하면 고객이 방화벽을 공용 IP에 대해 열어야 하므로 데이터 반출 위험이 있습니다.  

**시나리오 1**: Azure VM의 애플리케이션이 Azure SQL Database의 데이터베이스에 연결합니다. 악의적인 행위자가 VM 액세스 권한을 획득하여 VM을 손상시킵니다. 이 시나리오에서 데이터 반출은 외부 엔터티가 손상된 VM을 사용하여 데이터베이스에 연결하고, 개인 데이터를 복사하여 다른 구독의 Blob 스토리지 또는 다른 SQL Database에 저장하는 것을 의미합니다.

**시나리오 2**: 악의적인 DBA. 이 시나리오는 규제가 엄격한 산업에 종사하므로 보안에 민감한 고객에게 자주 발생합니다. 이 시나리오에서는 높은 권한이 있는 사용자가 Azure SQL Database의 데이터를 데이터 소유자의 통제를 받지 않는 다른 구독으로 복사할 수 있습니다.

**잠재적 완화**:

현재 Azure SQL Database 및 SQL Managed Instance는 데이터 반출 위협을 완화하는 다음과 같은 기술을 제공합니다.

- Azure VM의 NSG에서 허용 및 거부 규칙의 조합을 사용하여 VM에서 액세스할 수 있는 지역을 제어합니다.
- SQL Database에서 서버를 사용하는 경우 다음 옵션을 설정합니다.
  - Azure 서비스 허용을 [끄기]로 설정합니다.
  - VNet 방화벽 규칙을 설정하여 Azure VM이 포함된 서브넷의 트래픽만 허용합니다.
  - [프라이빗 링크](../../private-link/private-endpoint-overview.md)를 사용합니다.
- SQL Managed Instance의 경우 기본적으로 개인 IP 액세스를 사용하여 손상된 VM의 첫 번째 데이터 반출 문제를 해결합니다. 서브넷에서 서브넷 위임 기능을 사용하여 SQL Managed Instance 서브넷에 가장 제한이 많은 정책을 자동으로 설정합니다.
- 악의적인 DBA의 관심사는 노출 영역이 더 크고 네트워킹 요구 사항이 고객에게 표시되는 SQL Managed Instance를 더 많이 노출하는 것입니다. 이에 대한 가장 좋은 완화 방법은 이 보안 가이드의 모든 모범 사례를 적용하여 데이터 반출 시나리오뿐 아니라 악의적인 DBA 시나리오까지 방지하는 것입니다. Always Encrypted는 중요한 데이터를 암호화하고 DBA가 키에 액세스할 수 없도록 보관하여 중요한 데이터를 보호하는 방법 중 하나입니다.

## <a name="security-aspects-of-business-continuity-and-availability"></a>비즈니스 연속성 및 가용성의 보안 측면

대부분의 보안 표준은 단일 실패 지점이 발생하지 않도록 중복성 및 장애 조치(failover) 기능을 구현하여 달성할 수 있는 운영 연속성 측면에서 데이터 가용성을 해결합니다. 재해 시나리오의 경우 데이터 및 로그 파일의 백업을 유지하는 것이 일반적인 방법입니다.다음 섹션에서는 Azure에 기본 제공되는 기능을 개략적으로 설명합니다. 또한 특정 요구 사항을 충족하도록 구성할 수 있는 추가 옵션을 제공합니다.

- Azure는 [SQL Database 및 SQL Managed Instance를 사용한 고가용성](high-availability-sla.md)을 기본 제공합니다.

- 중요 비즈니스용 계층에는 장애 조치(failover) 그룹, 전체 및 차등 로그 백업, 기본적으로 사용되는 특정 시점 복원 백업이 포함됩니다.  
  - [자동화된 백업](automated-backups-overview.md)
  - [자동화된 데이터베이스 백업 - 특정 시점 복원을 사용하여 데이터베이스 복구](recovery-using-backups.md#point-in-time-restore)

- 여러 Azure 지역에서 영역 중복 구성 및 자동 장애 조치(failover) 그룹과 같은 비즈니스 연속성 기능을 추가로 구성할 수 있습니다. 
    - [고가용성 - 프리미엄 및 중요 비즈니스용 서비스 계층에 대한 영역 중복 구성](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [고가용성 - 범용 서비스 계층에 대한 영역 중복 구성](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 보안 기능의 개요](security-overview.md)를 참조하세요.
