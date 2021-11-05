---
title: Azure SQL DB 등록 및 검사
description: 이 문서에서는 Azure SQL DB 원본을 인증하고 상호 작용하기 위한 지침을 포함하여 Azure Purview에서 Azure SQL 데이터베이스를 등록하는 프로세스에 대해 간략하게 설명합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c2ed5a0a65bd4d10620b0c72dbb5efb2aac4152f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061686"
---
# <a name="connect-to-azure-sql-database-in-azure-purview"></a>Azure Purview에서 Azure SQL Database에 연결

이 문서에서는 Azure SQL 데이터베이스 원본을 인증하고 상호 작용하기 위한 지침을 포함하여 Azure Purview에서 Azure SQL 데이터 원본을 등록하는 프로세스에 대해 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)|[예](#scan) | [예](#scan)|[예](#scan)| 아니요 |[Data Factory 계보](how-to-link-azure-data-factory.md)|

### <a name="known-limitations"></a>알려진 제한 사항

* Azure Purview는 스키마 탭에서 300개를 초과하는 열을 지원하지 않으며 "Additional-Columns-Truncated"를 표시합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 데이터 원본을 성공적으로 검사할 수 있도록 Azure SQL DB 데이터 원본을 등록하고 적절한 인증 메커니즘을 설정할 수 있습니다.

### <a name="steps-to-register"></a>등록 단계

데이터 원본에 대한 검사를 설정하기 전에 Azure Purview에서 데이터 원본을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com), **Purview 계정** 페이지로 차례로 이동하여 _Purview 계정_ 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-purview-acct.png" alt-text="데이터 원본을 등록하는 데 사용되는 Purview 계정을 보여 주는 스크린샷":::

1. **Purview Studio를 열고**, **데이터 맵** 으로 이동합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-open-purview-studio.png" alt-text="데이터 맵의 원본 링크로 이동하는 스크린샷":::

1. **컬렉션** 메뉴를 사용하여 [컬렉션 계층 구조](./quickstart-create-collection.md)를 만들고, 필요에 따라 권한을 개별 하위 컬렉션에 할당합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-collections.png" alt-text="액세스 제어 권한을 컬렉션 계층 구조에 할당하는 컬렉션 메뉴를 보여 주는 스크린샷":::

1. **원본** 메뉴에서 해당 컬렉션으로 이동하고, **등록** 아이콘을 선택하여 새 Azure SQL DB를 등록합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-data-source.png" alt-text="데이터 원본을 등록하는 데 사용되는 컬렉션을 보여 주는 스크린샷":::

1. **Azure SQL Database** 데이터 원본을 선택하고, **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-select-ds.png" alt-text="데이터 원본을 선택할 수 있는 스크린샷":::

1. 데이터 원본에 적합한 **이름** 을 제공하고, 관련 **Azure 구독**, SQL 서버에 대한 **서버 이름** 및 **컬렉션** 을 선택하고, **적용** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-details.png" alt-text="데이터 원본을 등록하기 위해 입력하는 세부 정보를 보여 주는 스크린샷":::

1. Azure SQL Server 데이터베이스가 선택한 컬렉션 아래에 표시됩니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-collections.png" alt-text="검사를 시작하기 위해 컬렉션에 매핑된 데이터 원본을 보여 주는 스크린샷":::

## <a name="scan"></a>검사

### <a name="authentication-for-a-scan"></a>검사 인증

데이터 원본을 검사할 수 있도록 하려면 Azure SQL Database의 인증 방법을 구성해야 합니다.
다음과 같은 옵션이 지원됩니다.

* **SQL 인증**

* **관리 ID** - Azure Purview 계정이 만들어지는 즉시 시스템 **관리 ID** 가 자동으로 Azure AD 테넌트에 만들어집니다. 리소스 종류에 따라 Azure Purview MSI에서 검사를 수행하려면 특정 RBAC 역할 할당이 필요합니다.
관리 ID를 사용하는 경우 Purview 계정에는 만들 때 기본적으로 Purview 이름인 자체 관리 ID가 있습니다.

* **서비스 주체** - 이 방법에서는 Azure Active Directory 테넌트에서 새 서비스 주체를 만들거나 기존 서비스 주체를 사용할 수 있습니다.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>데이터베이스 계정에서 Azure AD 인증 구성

서비스 주체 또는 관리 ID에는 데이터베이스, 스키마 및 테이블에 대한 메타데이터를 가져올 수 있는 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다.

- [Azure SQL에서 Azure AD 인증 구성 및 관리](../azure-sql/database/authentication-aad-configure.md)
- [Azure SQL Database에서 서비스 주체 사용자 만들기](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database)에 대한 자습서를 수행하여 정확한 Purview의 관리 ID 또는 자체 서비스 주체를 사용하여 Azure SQL Database에서 Azure AD 사용자를 만들어야 합니다. ID에 적절한 권한(예: `db_datareader`)을 할당해야 합니다. 사용자를 만들고 권한을 부여하는 SQL 구문 예제:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username`은 사용자 고유의 서비스 주체 또는 Purview의 관리 ID입니다. [고정 데이터베이스 역할 및 해당 기능](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles)에 대해 자세히 알아볼 수 있습니다.

#### <a name="using-sql-authentication-for-scanning"></a>검사에 SQL 인증 사용

> [!Note]
> 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 **15분** 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

Azure SQL Database에 대한 로그인을 사용할 수 없는 경우 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)의 지침에 따라 해당 로그인을 만들 수 있습니다. 다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="키 자격 증명 모음을 보여 주는 스크린샷":::

1. **설정 > 비밀** 을 차례로 선택하고, **+ 생성/가져오기** 를 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="비밀을 생성하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. Azure SQL Database의 *암호* 로 **이름** 및 **값** 을 입력합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret-sql.png" alt-text="SQL 비밀 값을 입력하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. **만들기** 를 선택하여 완료합니다.

1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.

1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="자격 증명을 설정하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault-options.png" alt-text="비밀을 만드는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

#### <a name="using-managed-identity-for-scanning"></a>검사에 관리 ID 사용

Azure SQL DB를 검사할 수 있는 권한을 Purview 계정에 부여해야 합니다. 검색 권한을 부여할 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 카탈로그의 MSI를 추가할 수 있습니다.

> [!Note] 
> Azure 리소스에 관리 ID를 추가하려면 구독의 소유자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그를 통해 검사하도록 허용하려는 구독, 리소스 그룹 또는 리소스(예: Azure SQL Database)를 찾습니다.

1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택한 다음, **+ 추가** --> **역할 할당 추가** 를 차례로 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-ds.png" alt-text="Azure SQL 데이터베이스를 보여 주는 스크린샷":::

1. **역할** 을 **읽기 권한자** 로 설정하고, **선택** 입력 상자 아래에서 _Azure Purview 계정 이름_ 을 입력합니다. 그런 다음, **저장** 을 선택하여 Purview 계정에 이 역할을 할당합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-access-managed-identity.png" alt-text="Purview 계정에 대한 권한을 할당하기 위한 세부 정보를 보여 주는 스크린샷":::

#### <a name="using-service-principal-for-scanning"></a>검사에 서비스 주체 사용

##### <a name="creating-a-new-service-principal"></a>새 서비스 주체 만들기

[새 서비스 주체를 만들어야](./create-service-principal-azure.md) 하는 경우 애플리케이션을 Azure AD 테넌트에 등록하고 데이터 원본의 서비스 주체에 대한 액세스를 제공해야 합니다. 이 작업은 Azure AD 전역 관리자 또는 애플리케이션 관리자와 같은 다른 역할에서 수행할 수 있습니다.

##### <a name="getting-the-service-principals-application-id"></a>서비스 주체의 애플리케이션 ID 가져오기

1. 이미 만든 [_서비스 주체_](./create-service-principal-azure.md)의 **개요** 에 있는 **애플리케이션(클라이언트) ID** 를 복사합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-appln-id.png" alt-text="서비스 주체의 애플리케이션(클라이언트) ID를 보여 주는 스크린샷":::

##### <a name="granting-the-service-principal-access-to-your-azure-sql-database"></a>Azure SQL Database에 대한 액세스 권한을 서비스 주체에 부여

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="서비스 주체에 대한 비밀을 추가하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. **설정 > 비밀** 을 차례로 선택하고, **+ 생성/가져오기** 를 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="서비스 주체에 대한 비밀을 생성하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. 서비스 주체의 **클라이언트 암호** 로 원하는 **이름** 및 **값** 을 입력합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-create-secret.png" alt-text="비밀 값을 입력하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. **만들기** 를 선택하여 완료합니다.

1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.

1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="서비스 주체에 대한 자격 증명을 추가하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-cred.png" alt-text="서비스 주체에 대한 비밀을 만드는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

### <a name="firewall-settings"></a>방화벽 설정

데이터베이스 서버에서 방화벽을 사용하도록 설정한 경우 다음 두 가지 방법 중 하나로 액세스를 허용하도록 방화벽을 업데이트해야 합니다.

1. 방화벽을 통해 Azure 연결을 허용합니다.
1. 자체 호스팅 통합 런타임을 설치하고 방화벽을 통해 액세스 권한을 부여합니다.

#### <a name="allow-azure-connections"></a>Azure 연결 허용

Azure 연결을 사용하도록 설정하면 방화벽 자체를 업데이트하지 않고도 Azure Purview가 서버에 도달하고 연결할 수 있습니다. [Azure 내부에서 연결](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)에 대한 방법 가이드를 따를 수 있습니다.

1. 데이터베이스 계정으로 이동합니다.
1. **개요** 페이지에서 서버 이름을 선택합니다.
1. **보안 > 방화벽 및 가상 네트워크** 를 선택합니다.
1. **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 에 대해 **예** 를 선택합니다.
:::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-firewall.png" alt-text="Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용" border="true":::

#### <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임

개인 네트워크의 리소스에 연결하기 위해 머신에 SHIR(자체 호스팅 통합 런타임)을 설치할 수 있습니다.

1. 개인용 머신이나 데이터베이스 서버와 동일한 VNet 내의 머신에 [자체 호스팅 통합 런타임을 만들고 설치](./manage-integration-runtimes.md)합니다.
1. 데이터베이스 서버 방화벽을 검사하여 SHIR 머신이 방화벽을 통해 액세스할 수 있는지 확인합니다. 아직 액세스할 수 없는 경우 머신의 IP를 추가합니다.
1. Azure SQL Server가 프라이빗 엔드포인트 뒤에 있거나 VNet에 있는 경우, 엔드투엔드 네트워크 격리를 보장하기 위해 [수집 프라이빗 엔드포인트](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)를 사용할 수 있습니다.

### <a name="creating-the-scan"></a>검사 만들기

1. **Purview 계정** 을 열고, **Purview Studio 열기** 를 선택합니다.
1. **데이터 맵** --> **원본** 으로 차례로 이동하여 컬렉션 계층 구조를 봅니다.
1. 이전에 등록한 **Azure SQL DB** 아래에서 **새 검사** 아이콘을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan.png" alt-text="새 검사를 만드는 화면을 보여 주는 스크린샷":::

#### <a name="if-using-sql-authentication"></a>SQL 인증을 사용하는 경우

1. 검사에 대한 **이름** 을 제공하고, **데이터베이스 선택 방법** 을 _수동으로 입력_ 으로 선택하고, 이전에 만든 **데이터베이스 이름** 및 **자격 증명** 을 입력하고, 검사에 적절한 컬렉션을 선택하고, **연결 테스트** 를 선택하여 연결의 유효성을 검사합니다. 연결에 성공하면 **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-auth.png" alt-text="검사에 대한 SQL 인증 옵션을 보여 주는 스크린샷":::

#### <a name="if-using-managed-identity"></a>관리 ID를 사용하는 경우

1. 검사에 대한 **이름** 을 제공하고, **자격 증명** 아래에서 **Purview MSI** 를 선택하고, 검사에 적절한 컬렉션을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-managed-id.png" alt-text="검사를 실행하는 관리 ID 옵션을 보여 주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 연결에 성공하면 **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-test.png" alt-text="관리 ID 옵션에서 검사를 실행할 수 있는 스크린샷":::

#### <a name="if-using-service-principal"></a>서비스 주체를 사용하는 경우

1. 검사에 대한 **이름** 을 제공하고, 검사에 적절한 컬렉션을 선택하고, **자격 증명** 드롭다운을 선택하여 이전에 만든 자격 증명을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp.png" alt-text="서비스 주체가 검사를 사용하도록 설정하는 옵션을 보여 주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 연결에 성공하면 **계속** 을 선택합니다.

### <a name="scoping-and-running-the-scan"></a>검사 범위 지정 및 실행

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 폴더 및 하위 폴더로 지정할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 새 _검색 규칙 집합_ 을 만드는 경우

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan-rule-set.png" alt-text="새 검사 규칙 집합":::

1. 검사 규칙에 포함할 **분류 규칙** 을 선택할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-classification.png" alt-text="검사 규칙 집합 분류 규칙":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sel-scan-rule.png" alt-text="검사 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-trigger.png" alt-text="검사 트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-review-scan.png" alt-text="검사 검토":::

### <a name="view-scan"></a>검사 보기

1. _컬렉션_ 에서 _데이터 원본_ 으로 이동하고, **세부 정보 보기** 를 선택하여 검사 상태를 확인합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan.png" alt-text="검사 보기":::

1. 검사 세부 정보는 **마지막 실행 상태** 의 검사 진행률과 _검사됨_ 및 _분류됨_ 상태의 자산 수를 나타냅니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan-details.png" alt-text="검사 세부 정보 보기":::

1. 전체 검사가 성공적으로 실행되면 **마지막 실행 상태** 가 **진행 중**, 이어서 **완료됨** 으로 업데이트됩니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-complete.png" alt-text="완료된 검사 보기":::

### <a name="manage-scan"></a>검사 관리

검사를 관리하거나 완료 후 다시 실행할 수 있습니다.

1. 검사를 관리할 **검사 이름** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage scan.png" alt-text="검사 관리":::

1. _검사를 다시 실행_ 하고, _검사를 편집_ 하고, _검사를 삭제_ 할 수 있습니다.  

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage-scan-options.png" alt-text="검사 옵션 관리":::

1. _증분 검사_ 또는 _전체 검사_ 를 다시 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-full-inc.png" alt-text="전체 또는 증분 검사":::

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
