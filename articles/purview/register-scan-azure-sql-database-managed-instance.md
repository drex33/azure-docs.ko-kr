---
title: Azure SQL Database Managed Instance 등록 및 검사
description: 이 자습서에서는 Azure SQL Database Managed Instance를 검사하는 방법을 설명합니다.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/08/2021
ms.openlocfilehash: 06f3ee8b820d732c0f605adc3201cff57fa70b42
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739087"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance 등록 및 검사

이 문서에서는 Purview에서 Azure SQL Database Managed Instance 데이터 원본을 등록하고 이 데이터 원본에 대한 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database Managed Instance 데이터 원본에서 지원하는 기능은 다음과 같습니다.

- **전체 및 증분 검사** - Azure SQL Database Managed Instance에서 메타데이터 및 분류를 캡처합니다.

- **계보** - ADF 복사 및 데이터 흐름 작업에 대한 데이터 자산 간의 계보입니다.

## <a name="prerequisites"></a>필수 조건

- 아직 Purview 계정이 없는 경우 새로 만듭니다.

- [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > Purview에서 **프라이빗 엔드포인트를 아직 지원하지 않으므로** 조직에서 퍼블릭 엔드포인트를 허용할 수 있어야 합니다. 따라서 프라이빗 엔드포인트를 사용하면 검사가 성공하지 않습니다.

### <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure SQL Database Managed Instance를 검사하기 위한 인증입니다. 새 인증을 만들어야 하는 경우 [SQL Database Managed Instance에 대한 데이터베이스 액세스 권한을 부여](../azure-sql/database/logins-create-manage.md)해야 합니다. 현재 Purview에서 지원하는 세 가지 인증 방법은 다음과 같습니다.

- SQL 인증
- 서비스 주체
- 관리 ID

#### <a name="sql-authentication"></a>SQL 인증

> [!Note]
> 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 **15분** 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

Azure SQL Database Managed Instance에 대한 로그인을 사용할 수 없는 경우 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)의 지침에 따라 해당 로그인을 만들 수 있습니다. 다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정 > 비밀** 을 차례로 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, Azure SQL Database Managed Instance의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

#### <a name="service-principal-and-managed-identity"></a>서비스 주체 및 관리 ID

Purview에서 서비스 주체를 사용하여 Azure SQL Database Managed Instance를 검사할 수 있도록 허용하는 몇 가지 단계가 있습니다.

##### <a name="create-or-use-an-existing-service-principal"></a>기존 서비스 주체 만들기 또는 사용

> [!Note]
> **관리 ID** 를 사용하는 경우 이 단계를 건너뛰세요.

서비스 주체를 사용하려면 기존 주체를 사용하거나 새 주체를 만들 수 있습니다. 

> [!Note]
> 새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행하세요.
> 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
> 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
> 1. **앱 등록** 을 선택합니다.
> 1. **+ 새 애플리케이션 등록** 을 선택합니다.
> 1. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.
> 1. **이 조직 디렉터리의 계정만** 을 선택합니다.
> 1. 리디렉션 URI에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요가 없습니다.
> 1. 그런 다음, **등록** 을 선택합니다.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>데이터베이스 계정에서 Azure AD 인증 구성

서비스 주체 또는 관리 ID에는 데이터베이스, 스키마 및 테이블에 대한 메타데이터를 가져올 수 있는 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다.
- [Azure SQL에서 Azure AD 인증 구성 및 관리](../azure-sql/database/authentication-aad-configure.md)
- [Azure AD ID에 매핑된 포함된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)에 대한 필수 구성 요소 및 자습서에 따라 Azure SQL Database Managed Instance에서 Azure AD 사용자를 만듭니다.
- `db_datareader` 권한을 ID에 할당

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>키 자격 증명 모음 및 Purview의 자격 증명에 서비스 주체 추가

> [!Note]
> **관리 ID** 를 사용하려는 경우 기본 Purview ID가 이미 **Purview-MSI** 에 있으므로 이 단계를 건너뛸 수 있습니다.

서비스 주체의 애플리케이션 ID 및 비밀을 가져오는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 차례로 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Azure SQL Database Managed Instance 데이터 원본 등록

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **Azure SQL Database Managed Instance**, **계속** 을 차례로 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="SQL 데이터 원본 설정":::

1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택하고, **서버 이름** 드롭다운 상자에서 적절한 서버를 선택합니다.

1. **퍼블릭 엔드포인트 정규화된 도메인 이름** 및 **포트 번호** 를 제공합니다. **등록** 을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database Managed Instance 추가":::

    예를 들어 `foobar.public.123.database.windows.net,3342`

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 Azure SQL Database Managed Instance 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 테이블로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> 검사를 삭제해도 이전 Azure SQL Database Managed Instance 검사에서 자산이 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
