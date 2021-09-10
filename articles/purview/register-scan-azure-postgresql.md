---
title: Azure Database for PostgreSQL 등록 및 검색
description: 이 자습서에서는 Azure Database for PostgreSQL 데이터베이스를 검색하는 방법을 설명합니다.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: 622e89392dc10f12fe81ed1c1085e5b9cc524999
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181301"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 등록 및 검색

이 문서에서는 Azure Database for PostgreSQL을 등록하고 검색하는 방법을 설명합니다.


## <a name="supported-capabilities"></a>지원되는 기능
- Azure Database for PostgreSQL에서 메타데이터 및 분류를 캡처하기 위한 **전체 및 증분 검사**.

- **계보** - ADF 복사 및 데이터 흐름 작업에 대한 데이터 자산 간의 계보입니다.

### <a name="known-limitations"></a>알려진 제한 사항

Purview의 Azure Database for PostgreSQL에 대한 SQL 인증만 지원합니다.


## <a name="prerequisites"></a>필수 조건

1. 아직 Purview 계정이 없는 경우 새로 만듭니다.

2. Purview 계정 및 Azure Database for PostgreSQL 간의 네트워킹 액세스.

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 대한 SQL 인증

Azure Database for PostgreSQL 데이터베이스에 연결하려면 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다. [CONNECT AND QUERY](../postgresql/connect-python.md)의 지침에 따라 Azure Database for PostgreSQL에 대한 로그인을 만들 수 있습니다(사용할 수 없는 경우). 다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고 Azure PostgreSQL Database의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 SQL 인증 유형의 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

## <a name="register-an-azure-database-for-postgresql-data-source"></a>Azure Database for PostgreSQL 데이터 원본 등록

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

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

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

> [!NOTE]
> * 스캔을 삭제해도 이전 스캔에서 생성된 카탈로그 자산은 삭제되지 않습니다.
> * 원본 테이블을 변경하고 Purview의 스키마 탭에서 설명을 편집한 후에 원본 테이블을 다시 검사하면 해당 자산이 스키마 변경 내용으로 더 이상 업데이트되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)