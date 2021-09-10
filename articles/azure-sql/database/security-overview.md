---
title: 보안 개요
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: SQL Server와의 차이점을 비롯하여 Azure SQL Database 및 Azure SQL Managed Instance의 보안에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 08/23/2021
ms.openlocfilehash: 9326797e16190b3570ed6faca4d724bec432bc86
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767917"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL Database 및 SQL Managed Instance 보안 기능 개요
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)를 사용하여 애플리케이션의 데이터 계층을 보호하는 기본 사항을 대략적으로 설명합니다. 여기서 설명하는 보안 전략은 아래 그림에 나와 있는 계층형 심층 방어 방식을 따르며 외부에서 내부로 적용됩니다.

![계층형 심층 방어 다이어그램. 고객 데이터는 네트워크 보안, 액세스 관리, 위협 및 정보 보호 계층으로 되어 있습니다.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>네트워크 보안

Microsoft Azure SQL Database, SQL Managed Instance 및 Azure Synapse Analytics는 클라우드 및 엔터프라이즈 애플리케이션에 대한 관계형 데이터베이스 서비스를 제공합니다. 방화벽은 Azure 가상 네트워크 트래픽 출처 또는 IP 주소를 기반으로 액세스 권한이 명시적으로 부여될 때까지 네트워크에서 서버에 액세스하지 못하도록 차단하여 고객 데이터를 보호하도록 합니다.

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 방화벽 규칙은 각 요청이 시작된 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [Azure SQL Database 및 Azure Synapse Analytics 방화벽 규칙 개요](firewall-configure.md)를 참조하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

[가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure 백본을 통해 가상 네트워크 연결을 확장하며, 트래픽이 생성되는 가상 네트워크 서브넷을 Azure SQL Database가 식별할 수 있도록 합니다. 트래픽이 Azure SQL Database로 전송되도록 하려면 SQL [서비스 태그](../../virtual-network/network-security-groups-overview.md)를 사용해 네트워크 보안 그룹을 통한 아웃바운드 트래픽을 허용합니다.

Azure SQL Database는 [가상 네트워크 규칙](vnet-service-endpoint-rule-overview.md)을 통해 Virtual Network 내의 선택한 서브넷에서 전송된 통신만 수락할 수 있습니다.

> [!NOTE]
> 방화벽 규칙을 사용한 액세스 제어는 **SQL Managed Instance** 에 적용되지 *않습니다*. 필요한 네트워킹 구성에 대한 자세한 내용은 [관리되는 인스턴스에 연결](../managed-instance/connect-application-instance.md)을 참조하세요.

## <a name="access-management"></a>액세스 관리

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 문서에 대한 자세한 내용은 [Azure Portal의 Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조하세요.

### <a name="authentication"></a>인증

인증은 사용자의 신원을 증명하는 과정입니다. Azure SQL Database 및 SQL Managed Instance는 다음과 같은 두 가지 유형의 인증을 지원합니다.

- **SQL 인증**:

    SQL 인증이란 사용자 이름과 암호를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance에 연결할 때 사용자가 수행하는 인증을 지칭합니다. 서버를 만들 때는 사용자 이름 및 암호를 사용하여 **서버 관리자** 로그인을 지정해야 합니다. **서버 관리자** 는 이 자격 증명을 사용하여 해당 서버 또는 인스턴스의 모든 데이터베이스에 데이터베이스 소유자로 인증할 수 있습니다. 그리고 나면 서버 관리자는 추가 SQL 로그인 및 사용자를 만들 수 있으며, 그러면 사용자가 사용자 이름과 암호를 사용하여 연결할 수 있습니다.

- **Azure Active Directory 인증**:

    Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결하는 메커니즘입니다. 관리자는 Azure AD 인증을 통해 데이터베이스 사용자의 ID 및 권한과 기타 Azure 서비스를 중앙 위치에서 관리할 수 있습니다. 그러면 스토리지해야 하는 암호를 최소화하고 중앙 집중식 암호 순환 정책을 사용할 수 있습니다.

     SQL Database를 사용한 Azure AD 인증을 사용하려면 서버 관리자 **Active Directory 관리자** 를 만들어야 합니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](authentication-aad-overview.md)을 참조하세요. Azure AD 인증에서는 관리 계정과 페더레이션된 계정이 모두 지원됩니다. 페더레이션된 계정은 Azure AD와 페더레이션된 고객 도메인용 Windows 사용자 및 그룹을 지원합니다.

    사용 가능한 추가 Azure AD 인증 옵션으로는 [다단계 인증](../../active-directory/authentication/concept-mfa-howitworks.md) 및 [조건부 액세스](conditional-access-configure.md)를 비롯한 [SQL Server Management Studio용 Active Directory 유니버설 인증](authentication-mfa-ssms-overview.md) 연결이 있습니다.

> [!IMPORTANT]
> Azure 내에서 데이터베이스와 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당을 통해 제어됩니다. 이 문서에 대한 자세한 내용은 [Azure Portal의 Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조하세요. 방화벽 규칙을 사용한 액세스 제어는 **SQL Managed Instance** 에 적용되지 *않습니다*. 필요한 네트워킹 구성에 대한 자세한 내용은 [관리되는 인스턴스에 연결](../managed-instance/connect-application-instance.md)에 관한 다음 문서를 참조하세요.

## <a name="authorization"></a>권한 부여

권한 부여는 데이터베이스 내의 리소스 및 명령에 대한 액세스 제어를 의미합니다. 이 작업은 Azure SQL Database 또는 Azure SQL Managed Instance 데이터베이스 내의 사용자에게 권한을 할당함으로써 이루어집니다. 권한은 [데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles)에 사용자 계정을 추가하고 해당 역할에 데이터베이스 수준 권한을 할당하는 방식으로 관리합니다. 또는 개별 사용자에게 특정 [개체 수준 사용 권한](/sql/relational-databases/security/permissions-database-engine)을 부여할 수도 있습니다. 자세한 내용은 [로그인 및 사용자](logins-create-manage.md)를 참조하세요.

필요한 경우 사용자 지정 역할을 만드는 것이 가장 좋습니다. 작업을 수행하는 데 필요한 최소한의 권한을 가진 역할에 사용자를 추가합니다. 사용자에게 직접 권한을 할당하지 마십시오. 서버 관리자 계정은 광범위한 권한이 있는 기본 제공 db_owner 역할의 구성원이므로 관리 업무를 담당하는 소수의 사용자에게만 부여해야 합니다. 사용자가 수행할 수 있는 범위를 더 제한하려면 [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql)를 사용하여 호출된 모듈의 실행 컨텍스트를 지정할 수 있습니다. 이러한 모범 사례를 따르는 것은 의무 분리를 위한 기본 단계이기도 합니다.

### <a name="row-level-security"></a>행 수준 보안

RLS(행 수준 보안)를 사용하면 고객이 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)에 따라 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. 행 수준 보안을 사용하여 사용자 지정 레이블 기반 보안 개념을 구현할 수도 있습니다. 자세한 내용은 [행 수준 보안](/sql/relational-databases/security/row-level-security)을 참조하세요.

![행 수준 보안이 클라이언트 앱을 통해 사용자가 액세스할 수 없도록 SQL 데이터베이스의 개별 행을 보호함을 보여 주는 다이어그램.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>위협 보호

SQL Database 및 SQL Managed Instance는 감사 및 위협 검색 기능을 제공하여 고객 데이터를 보호합니다.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor 로그 및 Event Hubs의 SQL 감사

SQL Database 및 SQL Managed Instance 감사는 데이터베이스 활동을 추적하며 고객이 소유한 Azure Storage 계정의 감사 로그에 데이터베이스 이벤트를 기록하여 보안 표준 규정 준수 상태를 유지할 수 있도록 지원합니다. 사용자는 감사를 통해 진행 중인 데이터베이스 활동을 모니터링하고 이전 활동을 분석 및 조사하여 잠재적 위협이나 악용 의심 사례 및 보안 위반을 식별할 수 있습니다. 자세한 내용은 [SQL Database 감사 시작](../../azure-sql/database/auditing-overview.md)을 참조하세요.  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection은 로그를 분석하여 비정상적인 동작 및 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 감지합니다. 경고는 SQL 삽입, 잠재적 데이터 침입 및 무차별 암호 대입 공격(brute force attack)과 같은 의심스러운 활동이 감지되거나, 권한 상승 및 위반된 자격 증명 사용을 포착하기 위한 액세스 패턴에 이상이 생겼을 때 만들어집니다. 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 확인할 수 있습니다. Azure Security Center에서는 의심스러운 활동의 세부 정보를 확인할 수 있으며 위협 완화를 위한 조치와 함께 추가 조사를 위한 권장 사항이 제공됩니다. 추가 요금으로 서버당 Advanced Threat Protection를 사용하도록 설정할 수 있습니다. 자세한 내용은 [SQL Database Advanced Threat Protection](threat-detection-configure.md)을 참조하세요.

![외부 공격자 및 악의적인 내부자로부터 웹앱에 대한 SQL 데이터베이스의 액세스를 모니터링하는 SQL Threat Detection을 보여 주는 다이어그램.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="transport-layer-security-encryption-in-transit"></a>전송 계층 보안(전송 중 암호화)

SQL Database, SQL Managed Instance 및 Azure Synapse Analytics는 [TLS(전송 계층 보안)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)를 사용하여 동작 중인 데이터를 암호화하여 고객 데이터를 보호합니다.

SQL Database, SQL Managed Instance 및 Azure Synapse Analytics는 모든 연결에 대해 항상 암호화(SSL/TLS)를 적용합니다. 이렇게 하면 연결 문자열에서 **Encrypt** 또는 **TrustServerCertificate** 설정에 관계없이 모든 데이터가 클라이언트와 서버 간에 "전송 중"으로 암호화됩니다.

애플리케이션에서 사용하는 연결 문자열에서 암호화된 연결을 지정하고 서버 인증서를 신뢰하지 _**않는**_ 것이 좋습니다. 이렇게 하면 애플리케이션이 서버 인증서를 확인하게 되므로 애플리케이션이 중간 유형의 공격에 취약해지는 것을 방지할 수 있습니다.

예를 들어 ADO.NET 드라이버를 사용하는 경우 **Encrypt=True** 및 **Trustservercertificate=False** 를 통해 이를 수행합니다. Azure Portal에서 연결 문자열을 얻는 경우 올바른 설정이 사용됩니다.

> [!IMPORTANT]
> 일부 타사 드라이버는 기본적으로 TLS를 사용하지 않거나 1.2보다 이전 버전의 TLS를 사용하여 작동할 수 있습니다. 이 경우에도 계속 서버에서 데이터베이스에 연결할 수 있습니다. 그러나, 특히 중요한 데이터를 저장할 때와 같이 이러한 드라이버와 애플리케이션이 SQL Database에 연결되도록 허용할 경우에 발생할 수 있는 보안 위험을 평가하는 것이 좋습니다.
>
> TLS 및 연결에 대한 추가 정보는 [TLS 고려 사항](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조하세요.

### <a name="transparent-data-encryption-encryption-at-rest"></a>투명한 데이터 암호화(미사용 데이터 암호화)

[Azure SQL Database, SQL Managed Instance 및 Azure Synapse Analytics용 TDE(투명한 데이터 암호화)](transparent-data-encryption-tde-overview.md)는 보안 계층을 추가하여 원시 파일이나 백업에 대해 무단/오프라인으로 액세스할 수 없도록 미사용 데이터를 보호합니다. 무단/오프라인 액세스의 일반적인 시나리오에는 데이터 센터 도난, 하드웨어 또는 미디어(예: 디스크 드라이브 및 백업 테이프)의 보안되지 않은 폐기 등이 해당됩니다.TDE는 AES 암호화 알고리즘을 사용하여 전체 데이터베이스를 암호화하므로 애플리케이션 개발자는 기존 애플리케이션을 전혀 변경할 필요가 없습니다.

Azure에서는 새로 만드는 모든 데이터베이스가 기본적으로 암호화되며, 기본 제공 서버 인증서를 통해 데이터베이스 암호화 키가 보호됩니다.  인증서 유지 관리 및 순환은 서비스를 통해 관리되며 사용자의 입력이 필요하지 않습니다. 암호화 키를 직접 제어하려는 고객은 [Azure Key Vault](../../key-vault/general/security-features.md)에서 키를 관리할 수 있습니다.

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault으로 키 관리

TDE( [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption))를 지원하는 BYOK([Bring Your Own Key](transparent-data-encryption-byok-overview.md))를 통해 고객은 Azure의 클라우드 기반 외부 키 관리 시스템인  [Azure Key Vault](../../key-vault/general/security-features.md)를 사용하여 키 관리 및 순환을 직접 제어할 수 있습니다. 데이터베이스의 키 자격 증명 모음 액세스 권한이 철회되면 데이터베이스를 암호 해독하여 메모리로 읽어들일 수 없습니다. 중앙 키 관리 플랫폼을 제공하며 철저하게 모니터링되는 HSM(하드웨어 보안 모듈)을 활용하는 Azure Key Vault를 사용하면 키와 데이터 관리 작업을 분리하여 보안 규정 준수 요구 사항을 충족할 수 있습니다.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted(사용 중인 데이터 암호화)

![Always Encrypted 기능에 대한 기본 사항을 보여 주는 다이어그램. 잠금을 사용하는 SQL Database는 키가 포함된 앱에만 액세스할 수 있습니다.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)는 신용 카드 번호, 주민 등록 번호 또는 _확인이 필요_ 한 데이터와 같이 특정 데이터베이스 열에 저장된 중요한 데이터를 액세스할 수 없도록 보호하는 기능입니다. 예를 들어 이 기능을 통해 데이터베이스에 액세스하여 관리 작업을 수행할 권한은 부여되었지만 업무상 암호화된 열의 특정 데이터에는 액세스할 필요가 없는 데이터베이스 관리자 또는 기타 권한 있는 사용자로부터 데이터를 보호할 수 있습니다. 데이터는 항상 암호화되므로 암호화 키 액세스 권한이 있는 클라이언트 애플리케이션에서 처리해야 하는 경우에만 암호화된 데이터의 암호가 해독됩니다. 암호화 키는 SQL Database 또는 SQL Managed Instance에 표시되지 않으며 [Windows 인증서 저장소](always-encrypted-certificate-store-configure.md) 또는 [Azure Key Vault](always-encrypted-azure-key-vault-configure.md)에 저장할 수 있습니다.

### <a name="dynamic-data-masking"></a>동적 데이터 마스킹

![동적 데이터 마스킹을 보여 주는 다이어그램. 비즈니스 앱은 데이터를 비즈니스 앱으로 다시 보내기 전에 데이터를 마스킹하는 SQL Database에 해당 데이터를 보냅니다.](./media/security-overview/azure-database-ddm.png)

동적 데이터 마스킹에서는 권한이 없는 사용자로 마스킹하여 중요한 데이터 노출을 제한합니다. 동적 데이터 마스킹은 Azure SQL Database 및 SQL Managed Instance에서 잠재적으로 중요한 데이터를 자동으로 검색하여 애플리케이션 계층에 미치는 영향을 최소화하면서 해당 필드를 마스킹할 수 있는 실행 가능한 권장 사항을 제공합니다. 이 기능은 지정된 데이터베이스 필드를 통해 쿼리의 결과 집합에 있는 중요한 데이터를 혼란스럽게 만들면서 작동하지만 데이터베이스의 데이터를 변경하지는 않습니다. 자세한 내용은 [SQL Database 및 SQL Managed Instance 동적 데이터 마스킹 시작](dynamic-data-masking-overview.md)을 참조하세요.

## <a name="security-management"></a>보안 관리

### <a name="vulnerability-assessment"></a>취약점 평가

[취약성 평가](sql-vulnerability-assessment.md)는 전반적인 데이터베이스 보안을 사전에 개선하기 위해 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 손쉽게 구성 가능한 서비스입니다. VA(취약성 평가)는 고급 SQL 보안 기능을 위한 통합 패키지인 Azure Defender for SQL 제품에 포함되어 있습니다. Azure Defender for SQL 중앙 포털을 통해 취약성 평가에 액세스하고 관리할 수 있습니다.

### <a name="data-discovery-and-classification"></a>데이터 검색 및 분류

데이터 검색 및 분류(현재 미리 보기)는 Azure SQL Database 및 SQL Managed Instance에 기본 기능을 기본으로 제공하여 데이터베이스에 있는 중요한 데이터를 검색, 분류, 레이블 지정합니다. 업무/금융, 의료, 개인 데이터 등의 매우 중요한 데이터를 검색하고 분류하는 작업은 조직 정보 보호 상태를 유지하는 데 중추적인 역할을 할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요한 데이터가 들어 있는 데이터베이스에 대한 액세스 제어 및 보안 강화.
- 데이터 프라이버시 표준 및 규정 준수 요구 사항을 충족하도록 지원

자세한 내용은 [데이터 검색 및 분류 시작](data-discovery-and-classification-overview.md)을 참조하세요.

### <a name="compliance"></a>규정 준수

Azure SQL Database는 위의 기능 및 애플리케이션이 다양한 보안 요구 사항을 충족하는 데 도움이 될 수 있는 기능을 포함할 뿐 아니라, 정기 감사도 받고 있으며 다수의 규정 준수 표준 충족 인증도 취득했습니다. 자세한 내용은 [Microsoft Azure 보안 센터](https://www.microsoft.com/trust-center/compliance/compliance-overview)를 참조하세요. 여기서 최신 SQL Database 규정 준수 인증서 목록을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 및 SQL Managed Instance의 로그인, 사용자 계정, 데이터베이스 역할 및 사용 권한에 대한 설명은 [로그인 및 사용자 계정 관리](logins-create-manage.md)를 참조하세요.
- 데이터베이스 감사에 대한 설명은 [감사](../../azure-sql/database/auditing-overview.md)를 참조하세요.
- 위협 검색에 대한 설명은 [위협 검색](threat-detection-configure.md)을 참조하세요.
