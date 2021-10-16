---
title: 온-프레미스 SQL Server 등록 및 검사
description: 이 자습서에서는 Azure 부서의 범위에서 자체 호스트 IR을 사용 하 여 온-프레미스 SQL 서버를 검색 하는 방법을 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1921349be07d129e9889da3af6c72a7b25a58ff5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074440"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>온-프레미스 SQL Server 등록 및 검사

이 문서에서는 Purview에서 SQL Server 원본을 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SQL Server 온-프레미스 데이터 원본은 다음 기능을 지원합니다.

- 온-프레미스 네트워크 또는 Azure VM에 설치된 SQL 서버에서 메타데이터 및 분류를 캡처하기 위한 **전체 및 증분 검사**.

- ADF 복사/데이터 흐름에 대한 데이터 자산 간의 **계보**.

SQL Server 온-프레미스 데이터 원본은 다음을 지원합니다.

- SQL Server 2019는 물론 SQL Server 2000까지의 모든 SQL 버전

- 인증 방식: SQL 인증

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.

- [자체 호스팅 통합 런타임](manage-integration-runtimes.md)을 설정하여 데이터 원본을 검사합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

SQL Server 온-프레미스에 대한 인증을 설정하는 방법은 한 가지뿐입니다.

- SQL 인증

### <a name="sql-authentication"></a>SQL 인증

SQL 계정에는 **master** 데이터베이스에 대한 액세스 권한이 있어야 합니다. 이는 `sys.databases`가 master 데이터베이스에 있기 때문입니다. Purview 스캐너는 서버에서 모든 SQL 데이터베이스를 찾으려면 `sys.databases`를 열거해야 합니다.

#### <a name="creating-a-new-login-and-user"></a>새 로그인 및 사용자 만들기

새 로그인 및 사용자를 만들어 SQL Server를 검사하려면 아래 단계를 따르세요.

> [!Note]
   > 아래의 모든 단계는 [여기](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)에 제공된 코드를 사용하여 실행할 수 있습니다.

1. SQL Server Management Studio (SSMS)로 이동 하 여 서버에 연결 하 고, 보안으로 이동 하 고, 로그인을 선택 하 여 저장 하거나 마우스 오른쪽 단추를 클릭 하 고, 새 로그인을 만듭니다. SQL 인증을 선택해야 합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="새 로그인 및 사용자를 만듭니다.":::

2. 왼쪽 탐색에서 서버 역할을 선택하고 공개 역할이 할당되었는지 확인합니다.

3. 왼쪽 탐색에서 사용자 매핑을 선택하고 지도에서 모든 데이터베이스를 선택한 다음 데이터베이스 역할(**db_datareader**)을 선택합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="사용자 매핑":::

4. 확인을 선택하여 저장합니다.

5. 를 선택 하거나 마우스 오른쪽 단추를 클릭 하 고 **속성** 을 선택 하 여 만든 사용자로 다시 이동 합니다. 새 암호를 입력하고 확인합니다. '이전 암호 지정'을 선택하고 이전 암호를 입력합니다. **새 로그인을 만드는 즉시 암호를 변경해야 합니다.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="암호 변경":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>키 자격 증명 모음에 SQL 로그인 암호 저장 및 Purview에서 자격 증명 만들기

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, SQL Server 로그인의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

## <a name="register-a-sql-server-data-source"></a>SQL Server 데이터 원본 등록

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색의 원본 및 검사에서 **통합 런타임** 을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](manage-integration-runtimes.md)에서 설명된 단계를 따라 온-프레미스 네트워크에 액세스할 수 있는 온-프레미스 또는 Azure VM에서 검사하기 위한 자체 호스팅 통합 런타임을 만듭니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **SQL Server** 를 선택한 다음 **계속** 을 선택합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL 데이터 원본 설정":::

1. 서버를 식별 하는 데 사용할 수 있는 약식 이름과 서버 끝점을 식별 하는 친숙 한 이름을 제공 합니다. 
 
1. 그런 다음, **마침** 을 선택하여 데이터 원본을 등록합니다.

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 SQL Server 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 테이블로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
