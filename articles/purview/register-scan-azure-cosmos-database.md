---
title: Azure Cosmos Database(SQL API)를 검색하는 방법
description: 이 방법 가이드에서는 Azure Cosmos Database(SQL API)를 검사하는 방법을 자세히 설명합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 5cbf179cd28a36705379049a66010d02d2e7cf24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536274"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos Database(SQL API) 등록 및 검색

이 문서에서는 Azure Purview에서 Azure Cosmos Database(SQL API) 계정을 등록하고 검색을 설정하는 방법을 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos Database(SQL API)는 메타데이터 및 스키마를 캡처하기 위해 전체 및 증분 검색을 지원합니다. 또한 검색은 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Cosmos Database(SQL API)에 대한 인증을 설정하는 방법은 다음 한 가지 뿐입니다.

- 계정 키
 
### <a name="account-key"></a>계정 키

선택한 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 Key Vault에 저장해야 합니다.

1. Azure Portal에서 Cosmos DB 계정으로 이동합니다. 
1. **설정** > **키** 를 차례로 선택합니다. 
1. *읽기/쓰기 키* 또는 *읽기 전용 키* 에서 PRIMARY 키 또는 SECONDARY 키를 복사하고 다음 단계를 위해 어딘가에 저장합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고 Azure Cosmos DB 계정의 *키* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos Database(SQL API) 계정 등록

새 Azure Cosmos Database(SQL API)를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Cosmos DB(SQL API)** 를 선택합니다.
1. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure Cosmos DB(SQL API))** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. Azure Cosmos DB를 필터링하려면 Azure 구독을 선택합니다.
3. 적절한 Cosmos DB 계정 이름을 선택합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
5. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="원본 등록 옵션" border="true":::


## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 Azure Cosmos DB 데이터 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다. 

   :::image type="content" source="media/register-scan-azure-cosmos-database/set-up-scan-cosmos.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검사 범위를 특정 데이터베이스로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-cosmos-database/cosmos-database-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-azure-cosmos-database/select-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-azure-cosmos-database/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
