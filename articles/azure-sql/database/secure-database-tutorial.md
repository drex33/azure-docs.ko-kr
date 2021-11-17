---
title: 데이터베이스 보호
description: 이 자습서에서는 Azure SQL Database에서 단일 또는 풀링된 데이터베이스를 보호하는 기법 및 기능을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 2e6a1889e9b1eea99a81db7b371851240d4c33cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347939"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>자습서: Azure SQL Database의 데이터베이스 보안
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> - 서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기
> - Azure AD(Azure Active Directory) 관리자 구성
> - SQL 인증, Azure AD 인증 및 보안 연결 문자열을 사용하여 사용자 액세스 관리
> - Microsoft Defender for SQL, 감사, 데이터 마스킹 및 암호화와 같은 보안 기능을 사용하도록 설정

Azure SQL Database는 다음과 같은 방법으로 데이터를 보호합니다.

- 방화벽 규칙을 사용하여 액세스 제한
- ID를 요구하는 인증 메커니즘 사용
- 역할 기반 멤버 자격 및 권한을 통한 권한 부여 사용
- 보안 기능 사용

> [!NOTE]
> Azure SQL Managed Instance는 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [연결 아키텍처](../managed-instance/connectivity-architecture-overview.md)에서 설명한 대로 네트워크 보안 규칙 및 프라이빗 엔드포인트를 사용하여 보호됩니다.

자세한 내용은 [Azure SQL Database 보안 개요](./security-overview.md) 및 [기능](security-overview.md) 문서를 참조하세요.

> [!TIP]
> 다음 Microsoft Learn 모듈을 사용하면 [Azure SQL Database의 데이터베이스 보안](/learn/modules/secure-your-azure-sql-database/) 방법에 대해 무료로 배울 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 조건이 충족되어야 합니다.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- [서버](logical-servers.md) 및 단일 데이터베이스
  - [Azure Portal](single-database-create-quickstart.md), [CLI](az-cli-script-samples-content-guide.md) 또는 [PowerShell](powershell-script-content-guide.md)을 사용하여 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

자습서의 모든 단계를 수행하기 위해 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-firewall-rules"></a>방화벽 규칙 만들기

SQL Database의 데이터베이스는 Azure에서 방화벽으로 보호됩니다. 기본적으로 서버 및 데이터베이스에 대한 모든 연결은 거부됩니다. 자세히 알아보려면 [서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기](firewall-configure.md)를 참조하세요.

가장 안전한 구성을 위해 **Azure 서비스 방문 허용** 을 **끄기** 로 설정합니다. 그런 다음, Azure VM 또는 클라우드 서비스와 같이 연결해야 하는 리소스에 대해 [예약된 IP(클래식 배포)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)를 만들고, 해당 IP 주소만 방화벽을 통해 액세스하도록 허용합니다. [Resource Manager](../../virtual-network/ip-services/public-ip-addresses.md) 배포 모델을 사용하는 경우 각 리소스에 대한 전용 공용 IP 주소가 필요합니다.

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 관리자가 1433 포트를 열어야 서버에 연결할 수 있습니다.

### <a name="set-up-server-level-firewall-rules"></a>서버 수준 방화벽 규칙 설정

서버 수준 IP 방화벽 규칙은 동일한 서버 내의 모든 데이터베이스에 적용됩니다.

서버 수준 방화벽 규칙을 설정하려면,

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

    ![서버 방화벽 규칙](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > 이 자습서의 뒷부분에서 사용할 수 있도록 정규화된 서버 이름(예: *yourserver.database.windows.net*)을 복사합니다.

1. **개요** 페이지에서 **서버 방화벽 설정** 을 선택합니다. 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   1. 도구 모음에서 **클라이언트 IP 추가** 를 선택하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 이 규칙은 단일 IP 주소 또는 IP 주소 범위에 대해 1433 포트를 열 수 있습니다. **저장** 을 선택합니다.

      ![set server firewall rule](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. **확인** 을 선택하고, **방화벽 설정** 페이지를 닫습니다.

이제 지정된 IP 주소 또는 IP 주소 범위로 서버의 모든 데이터베이스에 연결할 수 있습니다.

### <a name="setup-database-firewall-rules"></a>데이터베이스 방화벽 규칙 설정

데이터베이스 수준 방화벽 규칙은 개별 데이터베이스에만 적용됩니다. 데이터베이스는 서버를 장애 조치(failover)하는 동안 이러한 규칙을 유지합니다. 데이터베이스 수준 방화벽 규칙은 서버 수준 방화벽 규칙을 구성한 후에 T-SQL(Transact-SQL) 문만 사용하여 구성할 수 있습니다.

데이터베이스 수준 방화벽 규칙을 설정하려면 다음을 수행합니다.

1. [SQL Server Management Studio](connect-query-ssms.md)와 같은 도구를 사용하여 데이터베이스에 연결합니다.

1. **개체 탐색기** 에서 마우스 오른쪽 단추로 데이터베이스를 클릭하고 **새 쿼리** 를 선택합니다.

1. 쿼리 창에서 다음 명령문을 추가하고 IP 주소를 공용 IP 주소로 수정합니다.

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. 도구 모음에서 **실행** 을 선택하여 방화벽 규칙을 만듭니다.

> [!NOTE]
> *master* 데이터베이스에 연결해야 하지만 SSMS에서 [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current&preserve-view=true) 명령을 사용하여 서버 수준 방화벽 규칙을 만들 수도 있습니다.

## <a name="create-an-azure-ad-admin"></a>Azure AD 관리자 만들기

적절한 Azure AD(Active Directory) 관리되는 도메인을 사용하고 있는지 확인합니다. AD 도메인을 선택하려면 Azure Portal의 오른쪽 위 모서리를 사용합니다. 이 프로세스는 데이터베이스 또는 데이터 웨어하우스를 호스팅하는 논리적 SQL 서버와 Azure AD 모두에서 동일한 구독을 사용하는지 확인합니다.

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

Azure AD 관리자를 설정하려면,

1. Azure Portal의 **SQL Server** 페이지에서 **Active Directory 관리자** 를 선택합니다. 다음으로, **관리자 설정** 을 선택합니다.

    ![Active Directory 선택](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > 이 작업을 수행하려면 “전역 관리자”여야 합니다.

1. **관리자 추가** 페이지에서 AD 사용자 또는 그룹을 검색하여 선택한 다음, **선택** 을 선택합니다. Active Directory의 모든 멤버와 그룹이 나열되며, 회색으로 표시된 항목은 Azure AD 관리자로 지원되지 않습니다. [Azure AD 기능 및 제한 사항](authentication-aad-overview.md#azure-ad-features-and-limitations)을 참조하세요.

    ![관리자 선택](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Azure RBAC(Azure 역할 기반 액세스 제어)는 포털에만 적용되며 SQL Server에 전파되지 않습니다.

1. **Active Directory 관리자** 페이지의 위쪽에서 **저장** 을 선택합니다.

    관리자를 변경하는 프로세스에는 몇 분이 걸릴 수 있습니다. 새 관리자가 **Active Directory 관리자** 상자에 표시됩니다.

> [!NOTE]
> Azure AD 관리자를 설정할 때 새 관리자 이름(사용자 또는 그룹)은 *master* 데이터베이스에서 SQL Server 로그인 또는 사용자로 있을 수 없습니다. 이 사용자로 있는 경우 설정이 실패하고 변경 내용이 롤백되며, 해당 관리자 이름이 이미 있다고 나타냅니다. SQL Server 로그인 또는 사용자는 Azure AD에 속하지 않으므로 Azure AD 인증을 사용하여 이 사용자에 연결하려는 작업은 모두 실패합니다.

Azure AD를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Azure AD와 온-프레미스 ID의 통합](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Azure AD에 고유한 도메인 이름 추가](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure에서 Windows Server AD와의 페더레이션 지원](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Azure AD 디렉터리 관리](../../active-directory/fundamentals/active-directory-whatis.md)
- [PowerShell을 사용하여 Azure AD 관리](/powershell/azure/)
- [포트 및 프로토콜이 필요한 하이브리드 ID](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>데이터베이스 액세스 관리

데이터베이스에 사용자를 추가하거나 보안 연결 문자열을 사용하여 사용자 액세스를 허용하여 데이터베이스 액세스를 관리합니다. 연결 문자열은 외부 애플리케이션에 유용합니다. 자세히 알아보려면 [로그인 및 사용자 계정 관리](logins-create-manage.md) 및 [AD 인증](authentication-aad-overview.md)을 참조하세요.

사용자를 추가하려면 다음 데이터베이스 인증 유형을 선택합니다.

- **SQL 인증** - 로그인에서 사용자 이름과 암호를 사용하며, 이는 서버 내의 특정 데이터베이스 컨텍스트에서만 유효합니다.

- **Azure AD 인증** - Azure AD에서 관리하는 ID를 사용합니다.

### <a name="sql-authentication"></a>SQL 인증

SQL 인증을 사용하여 사용자를 추가하려면,

1. [SQL Server Management Studio](connect-query-ssms.md)와 같은 도구를 사용하여 데이터베이스에 연결합니다.

1. **개체 탐색기** 에서 마우스 오른쪽 단추로 데이터베이스를 클릭하고 **새 쿼리** 를 선택합니다.

1. 쿼리 창에서 다음 명령을 입력합니다.

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. 도구 모음에서 **실행** 을 선택하여 사용자를 만듭니다.

1. 기본적으로 사용자는 데이터베이스에 연결할 수 있지만 데이터를 읽거나 쓰는 권한은 없습니다. 이러한 권한을 부여하려면 새 쿼리 창에서 다음 명령을 실행합니다.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> 새 사용자 만들기와 같은 관리자 작업을 실행할 필요가 없는 경우 데이터베이스 수준에서 관리자가 아닌 계정을 만듭니다.

### <a name="azure-ad-authentication"></a>Azure AD 인증

Azure Active Directory 인증을 사용하려면 포함된 데이터베이스 사용자로 만들어야 합니다. 포함된 데이터베이스 사용자는 데이터베이스와 연결된 Azure AD 디렉터리의 ID에 매핑되며, *master* 데이터베이스에는 로그인할 수 없습니다. Azure AD ID는 개별 사용자 또는 그룹에 대한 것일 수 있습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 이식 가능한 데이터베이스 만들기](/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 참조하고, Azure AD를 사용하여 인증하는 방법은 [Azure AD 자습서](authentication-aad-configure.md)를 검토하세요.

> [!NOTE]
> 데이터베이스 사용자(관리자 예외)는 Azure Portal을 사용하여 만들 수 없습니다. Azure 역할은 SQL 서버, 데이터베이스 또는 데이터 웨어하우스에 전파되지 않습니다. 이러한 역할은 Azure 리소스를 관리하는 데만 사용되며 데이터베이스 권한에는 적용되지 않습니다.
>
> 예를 들어 *SQL Server 기여자* 역할은 데이터 웨어하우스 또는 데이터 웨어하우스에 연결할 수 있는 액세스 권한을 부여하지 않습니다. 이 권한은 데이터베이스 내에서 T-SQL 문을 사용하여 부여해야 합니다.

> [!IMPORTANT]
> 콜론(`:`) 또는 앰퍼샌드(`&`)와 같은 특수 문자는 `CREATE LOGIN` 및 `CREATE USER` T-SQL 문에서 사용자 이름으로 지원되지 않습니다.

Azure AD 인증을 사용하여 사용자를 추가하려면,

1. *ALTER ANY USER* 이상의 권한이 있는 Azure AD 계정을 사용하여 Azure의 서버에 연결합니다.

1. **개체 탐색기** 에서 마우스 오른쪽 단추로 데이터베이스를 클릭하고 **새 쿼리** 를 선택합니다.

1. 쿼리 창에서 다음 명령을 입력하고, `<Azure_AD_principal_name>`을 Azure AD 사용자의 보안 주체 이름 또는 Azure AD 그룹의 표시 이름으로 수정합니다.

   ```sql
   CREATE USER [<Azure_AD_principal_name>] FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD 사용자가 그룹에 대해 `E (EXTERNAL_USER)` 및 `X (EXTERNAL_GROUPS)` 형식의 메타데이터로 표시됩니다. 자세한 내용은 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)를 참조하세요.

### <a name="secure-connection-strings"></a>보안 연결 문자열

클라이언트 애플리케이션과 SQL Database 간에 암호화된 보안 연결을 보장하려면 연결 문자열을 다음과 같이 구성해야 합니다.

- 암호화된 연결 요청
- 서버 인증서를 신뢰하지 않음

연결은 TLS(전송 계층 보안)를 사용하여 설정되고 중간자(man-in-the-middle) 공격의 위험을 줄입니다. 연결 문자열은 데이터베이스별로 사용할 수 있으며, ADO.NET, JDBC, ODBC 및 PHP와 같은 클라이언트 드라이버를 지원하도록 미리 구성되어 있습니다. TLS 및 연결에 대한 정보는 [TLS 고려 사항](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조하세요.

보안 연결 문자열을 복사하려면,

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

1. **개요** 페이지에서 **데이터베이스 연결 문자열 표시** 를 선택합니다.

1. 드라이버 탭을 선택하고 전체 연결 문자열을 복사합니다.

    ![ADO.NET 연결 문자열](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>보안 기능 사용

Azure SQL Database는 Azure Portal을 사용하여 액세스하는 보안 기능을 제공합니다. 이러한 기능은 데이터베이스에서만 사용할 수 있는 데이터 마스킹을 제외하고는 데이터베이스와 서버 모두에서 사용할 수 있습니다. 자세한 내용은 [Microsoft Defender for SQL](azure-defender-for-sql.md), [감사](../../azure-sql/database/auditing-overview.md), [동적 데이터 마스킹](dynamic-data-masking-overview.md) 및 [투명한 데이터 암호화](transparent-data-encryption-tde-overview.md)를 참조하세요.

### <a name="microsoft-defender-for-sql"></a>Microsoft Defender for SQL

Microsoft Defender for SQL 기능은 발생할 수 있는 잠재적 위협을 탐지하고 비정상적인 활동에 대한 보안 경고를 제공합니다. 사용자는 감사 기능을 사용하여 이러한 의심스러운 이벤트를 검색하고, 해당 이벤트가 데이터베이스의 데이터를 액세스, 위반 또는 악용했는지 여부를 확인할 수 있습니다. 또한 취약성 평가, 데이터 검색 및 분류 도구가 포함된 보안 개요도 사용자에게 제공됩니다.

> [!NOTE]
> 위협의 한 가지 예로, 공격자가 애플리케이션 입력에 악의적인 SQL을 삽입하는 프로세스인 SQL 삽입이 있습니다. 이 경우 애플리케이션에서 자체적으로 인식하지 못한 채 악성 SQL을 실행하여 공격자가 데이터베이스의 데이터를 위반하거나 수정할 수 있습니다.

Microsoft Defender for SQL을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

1. **개요** 페이지에서 **서버 이름** 링크를 선택합니다. 서버 페이지가 열립니다.

1. **SQL Server** 페이지에서 **보안** 섹션을 찾아서 **Defender for Cloud** 를 선택합니다.

   1. **Microsoft Defender for SQL** 아래에서 **켜기** 를 선택하여 이 기능을 사용하도록 설정합니다. 취약성 평가 결과를 저장할 스토리지 계정을 선택합니다. 그런 다음 **저장** 을 선택합니다.

      ![탐색 창](./media/secure-database-tutorial/threat-settings.png)

      또한 보안 경고, 스토리지 세부 정보 및 위협 탐지 유형을 받을 수 있도록 이메일을 구성할 수도 있습니다.

1. 데이터베이스의 **SQL 데이터베이스** 페이지로 돌아가서 **보안** 섹션 아래에서 **Defender for Cloud** 를 선택합니다. 여기서는 데이터베이스에 사용할 수 있는 다양한 보안 표시기를 찾을 수 있습니다.

    ![위협 상태](./media/secure-database-tutorial/threat-status.png)

비정상적인 활동이 탐지되면 이벤트에 대한 정보가 포함된 이메일을 받게 됩니다. 여기에는 활동, 데이터베이스, 서버, 이벤트 시간, 가능한 원인 및 잠재적 위협을 조사하고 완화하기 위해 추천되는 조치가 포함됩니다. 이러한 이메일을 받은 경우 Azure Portal을 시작하고 이벤트 시간에 대한 관련 감사 레코드를 표시하려면 **Azure SQL 감사 로그** 링크를 선택합니다.

   ![위협 감지 전자 메일](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>감사

감사 기능은 데이터베이스 이벤트를 추적하고 Azure Storage, Azure Monitor 로그 또는 Event Hub 중 감사 로그에 이벤트를 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 활동을 살펴보고, 잠재적인 보안 위반을 나타낼 수 있는 불일치 및 비정상을 파악하는 데 도움이 됩니다.

감사를 사용하도록 설정하려면,

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

1. **보안** 섹션에서 **감사** 를 선택합니다.

1. **감사** 설정 아래에서 다음 값을 설정합니다.

   1. **감사** 를 **켜기** 로 설정합니다.

   1. **감사 로그 대상** 을 다음 중 하나로 선택합니다.

       - **스토리지** - 이벤트 로그가 저장되고 *.xel* 파일로 다운로드될 수 있는 Azure 스토리지 계정입니다.

          > [!TIP]
          > 감사 보고서 템플릿을 최대한 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 스토리지 계정을 사용하세요.

       - **Log Analytics** - 쿼리 또는 추가 분석을 위해 이벤트를 자동으로 저장합니다.

           > [!NOTE]
           > 분석, 사용자 지정 경고 규칙, Excel 또는 Power BI 내보내기와 같은 고급 기능을 지원하려면 **Log Analytics 작업 영역** 이 필요합니다. 작업 영역이 없으면 쿼리 편집기만 사용할 수 있습니다.

       - **Event Hub** - 다른 애플리케이션에서 사용할 수 있도록 이벤트를 라우팅할 수 있습니다.

   1. **저장** 을 선택합니다.

      ![감사 설정](./media/secure-database-tutorial/audit-settings.png)

1. 이제 **감사 로그 보기** 를 선택하여 데이터베이스 이벤트 데이터를 볼 수 있습니다.

    ![감사 레코드](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> PowerShell 또는 REST API를 사용하여 감사 이벤트를 추가로 사용자 지정하는 방법은 [SQL Database 감사](../../azure-sql/database/auditing-overview.md)를 참조하세요.

### <a name="dynamic-data-masking"></a>동적 데이터 마스킹

데이터 마스킹 기능은 데이터베이스에서 중요한 데이터를 자동으로 숨깁니다.

데이터 마스킹을 사용하도록 설정하려면,

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

1. **보안**  섹션에서 **동적 데이터 마스킹** 을 선택합니다.

1. **동적 데이터 마스킹** 설정 아래에서 **마스크 추가** 를 선택하여 마스킹 규칙을 추가합니다. Azure는 사용할 수 있는 데이터베이스 스키마, 테이블 및 열을 자동으로 채웁니다.

    ![마스크 설정](./media/secure-database-tutorial/mask-settings.png)

1. **저장** 을 선택합니다. 선택한 정보가 이제 개인 정보 보호를 위해 마스킹되었습니다.

    ![마스크 예제](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>투명한 데이터 암호화

암호화 기능은 저장 데이터를 자동으로 암호화하며, 암호화된 데이터베이스에 액세스하는 애플리케이션을 변경할 필요가 없습니다. 새 데이터베이스의 경우 기본적으로 암호화가 설정됩니다. 또한 SSMS 및 [Always Encrypted](always-encrypted-certificate-store-configure.md)(상시 암호화) 기능을 사용하여 데이터를 암호화할 수도 있습니다.

암호화를 사용하도록 설정하거나 확인하려면,

1. Azure Portal의 왼쪽 메뉴에서 **SQL 데이터베이스** 를 선택하고 **SQL 데이터베이스** 페이지에서 데이터베이스를 선택합니다.

1. **보안** 섹션에서 **투명 데이터 암호화** 를 선택합니다.

1. 필요한 경우 **데이터 암호화** 를 **켜기** 로 설정합니다. **저장** 을 선택합니다.

    ![투명한 데이터 암호화](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> 암호화 상태를 보려면 [SSMS](connect-query-ssms.md)를 사용하여 데이터베이스에 연결하고, [sys.dm_database_암호화_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) 보기의 `encryption_state` 열을 쿼리합니다. `3` 상태는 데이터베이스가 암호화되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 몇 가지 간단한 단계를 통해 데이터베이스의 보안을 향상시키는 방법을 알아보았습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> - 서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기
> - Azure AD(Active Directory) 관리자 구성
> - SQL 인증, Azure AD 인증 및 보안 연결 문자열을 사용하여 사용자 액세스 관리
> - Microsoft Defender for SQL, 감사, 데이터 마스킹 및 암호화와 같은 보안 기능을 사용하도록 설정

지리적 배포를 구현하는 방법에 대해 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
>[지역 분산 데이터베이스 구현](geo-distributed-application-configure-tutorial.md)
