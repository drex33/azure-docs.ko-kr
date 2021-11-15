---
title: Azure Database for PostgreSQL에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 Azure Database for PostgreSQL에 연결하고 Purview의 기능을 사용하여 Azure Database for PostgreSQL 원본을 검사하고 관리하는 방법을 설명합니다.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 1f5fa328a7a7a2f61647274eba0ba0282a820ec8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841906"
---
# <a name="connect-to-and-manage-an-azure-database-for-postgresql-in-azure-purview"></a>Azure Purview에서 Azure Database for PostgreSQL에 연결 및 관리

이 문서에서는 Azure Database for PostgreSQL을 등록하는 방법과 Azure Purview에서 Azure Database for PostgreSQL을 인증하고 상호 작용하는 방법을 간략하게 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)| [예](#scan) | [예](#scan) | [예](#scan) | 예 | 아니요** |

\**데이터 세트가 [데이터 팩터리 복사 작업에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다](how-to-link-azure-data-factory.md). 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에서 Azure Database for PostgreSQL을 등록하는 방법에 대해 설명합니다.

### <a name="authentication-for-registration"></a>등록 인증

현재 Azure Database for PostgreSQL을 관리하고 상호 작용할 수 있도록 SQL 인증만 지원됩니다.

#### <a name="sql-authentication"></a>SQL 인증

Azure Database for PostgreSQL 데이터베이스에 연결하려면 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다. [CONNECT AND QUERY](../postgresql/connect-python.md)의 지침에 따라 Azure Database for PostgreSQL에 대한 로그인을 만들 수 있습니다(사용할 수 없는 경우). 다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

1. Azure Key Vault가 아직 없는 경우 [이 가이드에 따라 Azure Key Vault를 만듭니다](../key-vault/certificates/quick-create-portal.md).
1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고 Azure PostgreSQL Database의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 SQL 인증 유형의 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

### <a name="steps-to-register"></a>등록 단계

새 Azure Database for PostgreSQL을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **원본 등록** 에서 **Azure Database for PostgreSQL** 을 선택합니다. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="새 데이터 원본 등록" border="true":::

**Azure Database for PostgreSQL 원본 등록** 화면에서 다음을 수행합니다.

1. 데이터 원본의 **이름** 을 입력합니다. 이 이름은 카탈로그에서 이 데이터 원본의 표시 이름이 됩니다.
1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택하고, **서버 이름** 드롭다운 상자에서 적절한 서버를 선택합니다.
1. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="원본 등록 옵션" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 Azure Database for PostgreSQL 데이터베이스를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 Azure Database for PostgreSQL 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 특정 폴더 또는 하위 폴더로 검색 범위를 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="검사 트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
