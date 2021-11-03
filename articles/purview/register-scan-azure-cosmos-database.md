---
title: Azure Cosmos Database(SQL API) 등록 및 검색
description: 이 문서에서는 Azure Cosmos 데이터베이스를 인증하고 상호 작용하는 지침을 포함하여 Azure Purview에서 Azure Cosmos 데이터 원본(SQL API)을 등록하는 프로세스를 간략하게 설명합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7d5556fcd040eaefe1078cddaf084089e471d496
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076956"
---
# <a name="connect-to-azure-cosmos-database-sql-api-in-azure-purview"></a>Azure Purview에서 Azure Cosmos 데이터베이스(SQL API)로 커넥트

이 문서에서는 Azure Cosmos 데이터베이스 원본을 인증하고 상호 작용하는 지침을 포함하여 Azure Purview에서 Azure Cosmos 데이터베이스(SQL API)를 등록하는 프로세스를 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)|[예](#scan) | [예](#scan)|[예](#scan)|아니요|아니요|

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 Azure Cosmos 데이터베이스(SQL API)를 등록하고 데이터 원본을 성공적으로 검사할 수 있도록 적절한 인증 메커니즘을 설정할 수 있습니다.

### <a name="steps-to-register"></a>등록 단계

데이터 원본에 대한 검사를 설정하기 전에 Azure Purview에 데이터 원본을 등록하는 것이 중요합니다.

1. [Azure Portal](https://portal.azure.com)이동하여 **Purview 계정** 페이지로 이동하여 _Purview 계정을_ 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-purview-acct.png" alt-text="데이터 원본을 등록하는 데 사용되는 Purview 계정을 보여 주는 스크린샷":::

1. **Purview Studio를** 열고 **데이터 맵 --> 컬렉션으로 이동합니다.**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-open-purview-studio.png" alt-text="데이터 맵 소스 링크로 이동하는 스크린샷":::

1. 컬렉션 메뉴를 사용하여 **컬렉션** [계층 구조를](./quickstart-create-collection.md) 만들고 필요에 따라 개별 하위 컬렉션에 사용 권한을 할당합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collections.png" alt-text="컬렉션 계층 구조를 만드는 컬렉션 메뉴를 보여 주는 스크린샷":::

1. 원본 메뉴에서 적절한 컬렉션으로 이동하고 **등록** **아이콘을** 선택하여 새 Azure Cosmos 데이터베이스를 등록합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-register-data-source.png" alt-text="데이터 원본을 등록하는 데 사용되는 컬렉션을 보여 주는 스크린샷":::

1. Azure **Cosmos DB(SQL API)** 데이터 원본을 선택하고 **계속을** 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-data-source.png" alt-text="데이터 원본을 선택할 수 있는 스크린샷":::

1. 데이터 원본에 적합한 **이름을** 제공하고, 관련 **Azure 구독을** 선택하고, **DB 계정 이름** 및 **컬렉션을** Cosmos, **적용을** 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-data-source-details.png" alt-text="데이터 원본을 등록하기 위해 입력할 세부 정보를 보여 주는 스크린샷":::

1. _Azure Cosmos 데이터베이스_ 스토리지 계정이 선택한 컬렉션 아래에 표시됩니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collection-mapping.png" alt-text="검색을 시작하기 위해 컬렉션에 매핑된 데이터 원본을 보여 주는 스크린샷":::

## <a name="scan"></a>검사

### <a name="authentication-for-a-scan"></a>검사에 대한 인증

데이터 원본을 검사할 수 있는 액세스 권한을 가지려면 Azure Cosmos 데이터베이스 Storage 계정의 인증 방법을 구성해야 합니다.

Azure Cosmos Database에 대한 인증을 설정하는 방법은 하나뿐입니다.

**계정 키** - Azure Purview가 비밀을 사용하여 데이터 원본을 안전하게 검색할 수 있도록 자격 증명을 저장하기 위해 Azure Key Vault 내에 비밀을 만들 수 있습니다. 비밀은 스토리지 계정 키, SQL 로그인 암호 또는 암호일 수 있습니다.

> [!Note]
> 구독에 Azure _Key Vault_ 리소스를 배포하고 Azure Key Vault 내의 비밀에 대한 필수 액세스 권한이 있는 Azure _Purview 계정의_ MSI를 할당해야 _합니다._

#### <a name="using-account-key-for-scanning"></a>검사에 계정 키 사용

액세스 키를 얻고 키 자격 증명 모음에 저장해야 합니다.

1. Azure Cosmos 데이터베이스 스토리지 계정으로 이동합니다.
1. **설정 > 키** 선택

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-access-keys.png" alt-text="스토리지 계정의 액세스 키를 보여 주는 스크린샷":::

1. *키를* 복사하고 다음 단계를 위해 별도로 저장합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key.png" alt-text="복사할 액세스 키를 보여 주는 스크린샷":::

1. 키 자격 증명 모음으로 이동

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault.png" alt-text="키 자격 증명 모음을 보여 주는 스크린샷":::

1. 비밀 설정 > 선택하고 **+ 생성/가져오기를** **선택합니다.**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-generate-secret.png" alt-text="비밀을 생성하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. 스토리지 계정의 *키로* **이름** 및 **값을** 입력하고 **만들기를** 선택하여 완료합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault-options.png" alt-text="비밀 값을 입력하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로, 키를 사용하여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검사를 설정합니다.

### <a name="creating-the-scan"></a>검사 만들기

1. **Purview 계정을** 열고 **Purview Studio 열기를** 선택합니다.
1. **데이터 맵**  -->  **원본으로** 이동하여 컬렉션 계층 구조를 봅니다.
1. 이전에 등록된 **Azure Cosmos 데이터베이스** 아래에서 새 **검색** 아이콘을 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-create-scan.png" alt-text="새 검사를 만드는 화면을 보여 주는 스크린샷":::

1. 검사에 대한 **이름을** 제공하고, 검사에 적합한 컬렉션을 선택하고, **자격 증명** 아래에서 + **새로** 설정을 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-option.png" alt-text="검사를 위한 계정 키 옵션을 보여 주는 스크린샷":::

1. _계정_ 키를 만드는 동안 사용된 적절한 **키 자격 증명** 모음 연결 및 **비밀 이름을** 선택합니다. _계정 키로_ **인증 방법** 선택

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-details.png" alt-text="계정 키 옵션을 보여 주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 연결에 성공하면 **계속을** 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-test-connection.png" alt-text="연결 테스트 성공을 보여 주는 스크린샷":::

### <a name="scoping-and-running-the-scan"></a>검사 범위 지정 및 실행

1. 목록에서 적절한 항목을 선택하여 검색 범위를 특정 폴더와 하위 폴더로 지정할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-rule-set.png" alt-text="검사 규칙 집합":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-new-scan-rule-set.png" alt-text="새 검사 규칙":::

1. 검사 규칙에 포함할 **분류** 규칙을 선택할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-classification.png" alt-text="검사 규칙 집합 분류 규칙":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-scan-rule-set.png" alt-text="검사 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-trigger.png" alt-text="검사 트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-review-scan.png" alt-text="검사 검토":::

### <a name="viewing-scan"></a>검사 보기

1. _컬렉션에서_ _데이터 원본으로_ 이동하고 **세부 정보 보기를** 선택하여 검사 상태를 확인합니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-view-scan.png" alt-text="검색 보기":::

1. 검색 세부 정보는 **마지막 실행 상태의** 검사 진행률과 검색 및 _분류된_ 자산 수를 나타냅니다. 

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-last-run-status.png" alt-text="검사 세부 정보 보기":::

1. 전체 검색이 성공적으로 실행되면 **마지막 실행 상태가** 진행 **중으로** **업데이트된** 다음 완료됨으로 업데이트됩니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-in-progress.png" alt-text="진행 중인 검사 보기":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-completed.png" alt-text="뷰 검색 완료":::

### <a name="managing-scan"></a>검사 관리

검사를 관리하거나 완료 후 다시 실행할 수 있습니다.

1. 검색 **이름을** 선택하여 검색 관리

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan.png" alt-text="검사 관리":::

1. 검색을 다시 _실행하고, 검사를_ _편집하고, 검색을_ _삭제할_ 수 있습니다.  

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan-options.png" alt-text="검사 옵션 관리":::

1. _전체 검사를_ 다시 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-full-scan.png" alt-text="전체 검사":::

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
