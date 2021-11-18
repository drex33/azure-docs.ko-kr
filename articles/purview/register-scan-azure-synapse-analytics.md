---
title: 전용 SQL 풀(이전의 SQL DW)에 커넥트 및 관리
description: 이 가이드에서는 Azure Purview에서 전용 SQL 풀(이전의 SQL DW)에 연결하고 Purview의 기능을 사용하여 전용 SQL 풀 원본을 검사하고 관리하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 06de8616985ee342754a94ea97291143b360df83
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758892"
---
# <a name="connect-to-and-manage-dedicated-sql-pools-in-azure-purview"></a>Azure Purview에서 전용 SQL 풀 커넥트 및 관리

이 문서에서는 전용 SQL 풀(이전의 SQL DW)을 등록하는 방법과 Azure Purview에서 전용 SQL 풀을 인증하고 상호 작용하는 방법을 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

> [!NOTE]
> Synapse 작업 영역 내에서 전용 SQL 데이터베이스를 등록하고 검사하려는 경우 [여기](register-scan-synapse-workspace.md)에 나와 있는 지침을 따라야 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)| [예](#scan)| [예](#scan)| [예](#scan)| 예 | 아니요** |

\**데이터 세트가 [데이터 팩터리 복사 작업에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다](how-to-link-azure-data-factory.md). 

### <a name="known-limitations"></a>알려진 제한 사항

* Azure Purview는 스키마 탭에서 300개를 초과하는 열을 지원하지 않으며 "Additional-Columns-Truncated"를 표시합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio를](https://web.purview.azure.com/)사용하여 Azure Purview에서 전용 SQL 풀을 등록하는 방법을 설명합니다.

### <a name="authentication-for-registration"></a>등록 인증

인증을 설정하는 방법에는 다음 세 가지가 있습니다.

- [시스템 또는 사용자 할당 관리](#system-or-user-assigned-managed-identity-to-register) ID(권장)
- Service Principal
- [SQL 인증](#sql-authentication-to-register)

    > [!Note]
    > 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 15분 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

#### <a name="system-or-user-assigned-managed-identity-to-register"></a>등록할 시스템 또는 사용자 할당 관리 ID

Azure Purview SAMI(시스템 할당 관리 ID) 또는 UAMI(사용자 [할당 관리 ID)를](manage-credentials.md#create-a-user-assigned-managed-identity) 사용하여 인증할 수 있습니다. 두 옵션 모두 다른 사용자, 그룹 또는 서비스 주체와 마찬가지로 Azure Purview에 직접 인증을 할당할 수 있습니다. Azure Purview SAMI는 계정을 만들 때 자동으로 만들어집니다. UAMI는 독립적으로 만들 수 있는 리소스이며, UAMI를 만들려면 [사용자 할당 관리 ID 가이드 를](manage-credentials.md#create-a-user-assigned-managed-identity)따를 수 있습니다. Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기의 필수 조건 및 자습서에 따라 관리 ID 개체 이름을 사용하여 전용 SQL 풀에서 [Azure AD 사용자를 만듭니다.](../azure-sql/database/authentication-aad-service-principal-tutorial.md)

사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

인증에는 데이터베이스, 스키마 및 테이블에 대한 메타데이터를 가져오기 위한 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다. ID에 `db_datareader` 사용 권한을 할당하는 것이 좋습니다.

#### <a name="service-principal-to-register"></a>등록할 서비스 주체

검사에 서비스 주체 인증을 사용하려면 기존 주체를 사용하거나 새 주체를 만들 수 있습니다.

새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행합니다.
 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
 1. 1. **앱 등록** 을 선택합니다.
 1. **+ 새 애플리케이션 등록** 을 선택합니다.
 1. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.
 1. **이 조직 디렉터리의 계정만** 을 선택합니다.
 1. 리디렉션 URI에 **대해 웹을** 선택하고 원하는 URL을 입력합니다. 실제 또는 작업일 필요는 없습니다.
 1. 그런 다음, **등록** 을 선택합니다.

서비스 주체의 애플리케이션 ID 및 비밀을 가져오는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

##### <a name="granting-the-service-principal-access"></a>서비스 주체 액세스 권한 부여

또한 Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기의 필수 조건 및 자습서에 따라 전용 풀에서 [Azure AD 사용자를 만들어야 합니다.](../azure-sql/database/authentication-aad-service-principal-tutorial.md) 사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview는 검사를 위해 **애플리케이션(클라이언트) ID** 및 **클라이언트 암호** 가 필요합니다.

#### <a name="sql-authentication-to-register"></a>등록할 SQL 인증

[CREATE LOGIN의](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) 지침에 따라 전용 SQL 풀(이전의 SQL DW)에 대한 로그인을 만들 수 있습니다(DW가 없는 경우).

선택한 인증 방법이 **SQL 인증** 인 경우 암호를 가져오고 키 자격 증명 모음에 저장해야 합니다.

1. SQL 로그인 암호 가져오기
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, SQL 로그인 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로, 키를 사용하여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검사를 설정합니다.

### <a name="steps-to-register"></a>등록 단계

Purview에서 새 SQL 전용 풀을 등록하려면 다음 단계를 완료합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록에서** **Azure Dedicated SQL 풀(이전의 SQL DW) 을** 선택합니다.
1. **계속** 을 선택합니다.

원본 등록 화면에서 다음 단계를 **완료합니다.**

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. 전용 SQL 풀을 필터링하려면 Azure 구독을 선택합니다.
3. 전용 SQL 풀을 선택합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
5. **등록** 을 선택하여 데이터 원본을 등록합니다.

## <a name="scan"></a>검사

아래 단계에 따라 전용 SQL 풀을 검색하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행 하려면 다음 단계를 완료 합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 SQL 전용 풀 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 테이블로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
