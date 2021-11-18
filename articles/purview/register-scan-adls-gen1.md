---
title: Azure Data Lake Storage(ADLS) Gen1 등록 및 검사
description: 이 문서에서는 Azure Data Lake Storage Gen1 원본을 인증하고 상호 작용하는 지침을 포함하여 Azure Purview에서 Azure Data Lake Storage Gen1 데이터 원본을 등록하는 프로세스를 간략하게 설명합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2280332b02440386f7ee1d9e6c19ecac3c20a610
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751900"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Azure Purview에서 Azure Data Lake Gen1에 커넥트

이 문서에서는 Azure Data Lake Storage Gen1 원본을 인증하고 상호 작용하는 지침을 포함하여 Azure Purview에서 Azure Data Lake Storage Gen1 데이터 원본을 등록하는 프로세스를 간략하게 설명합니다.

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)|[예](#scan) | [예](#scan)|[예](#scan)| 아니요 |제한** |

\**데이터 세트가 [데이터 팩터리 복사 작업](how-to-link-azure-data-factory.md)에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 ADLS Gen1 데이터 원본을 등록하고 데이터 원본을 성공적으로 검사할 수 있도록 적절한 인증 메커니즘을 설정할 수 있습니다.

### <a name="steps-to-register"></a>등록 단계

데이터 원본에 대한 검사를 설정하기 전에 Azure Purview에서 데이터 원본을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com), **Purview 계정** 페이지로 차례로 이동하여 _Purview 계정_ 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="데이터 원본을 등록하는 데 사용되는 Purview 계정을 보여 주는 스크린샷":::

1. **Purview Studio를** 열고 **데이터 맵 --> 원본으로 이동합니다.**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Purview Studio를 여는 링크를 보여 주는 스크린샷":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="데이터 맵의 원본 링크로 이동하는 스크린샷":::

1. 컬렉션 메뉴를 사용하여 **컬렉션** [계층 구조를](./quickstart-create-collection.md) 만들고 필요에 따라 개별 하위 컬렉션에 사용 권한을 할당합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="컬렉션 계층 구조를 만드는 컬렉션 메뉴를 보여 주는 스크린샷":::

1. 원본 메뉴에서 적절한 컬렉션으로 이동하고 **등록** **아이콘을** 선택하여 새 ADLS Gen1 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="데이터 원본을 등록하는 데 사용되는 컬렉션을 보여 주는 스크린샷":::

1. Azure **Data Lake Storage Gen1** 데이터 원본을 선택하고 **계속을** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="데이터 원본을 선택할 수 있는 스크린샷":::

1. 데이터 원본에 적합한 **이름을** 제공하고, 관련 **Azure 구독,** 기존 **Data Lake Store 계정 이름** 및 **컬렉션을** **선택하고, 적용을** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="데이터 원본을 등록하기 위해 입력하는 세부 정보를 보여 주는 스크린샷":::

1. 선택한 컬렉션 아래에 ADLS Gen1 스토리지 계정이 표시됩니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="검사를 시작하기 위해 컬렉션에 매핑된 데이터 원본을 보여 주는 스크린샷":::

## <a name="scan"></a>검사

### <a name="prerequisites-for-scan"></a>검사에 대한 필수 조건

데이터 원본을 검사할 수 있는 액세스 권한을 가지려면 ADLS Gen1 Storage 계정의 인증 방법을 구성해야 합니다.
다음과 같은 옵션이 지원됩니다.

> [!Note]
> 스토리지 계정에 대해 방화벽을 사용하도록 설정한 경우 검색을 설정할 때 관리 ID 인증 방법을 사용해야 합니다.

* **시스템 할당 관리 ID(권장)** - Azure Purview 계정이 만들어지는 즉시 Azure AD 테넌트에서 시스템 **관리 ID가** 자동으로 만들어집니다. 리소스 유형에 따라 Azure Purview SAMI가 검사를 수행하려면 특정 RBAC 역할 할당이 필요합니다.

* **사용자 할당 관리 ID(미리** 보기) - 시스템 관리 ID와 마찬가지로 사용자 할당 관리 ID는 Azure Purview가 Azure Active Directory 대해 인증할 수 있도록 하는 데 사용할 수 있는 자격 증명 리소스입니다. 자세한 내용은 사용자 할당 [관리 ID 가이드 를](manage-credentials.md#create-a-user-assigned-managed-identity)참조하세요.

* **서비스 주체** - 이 방법에서는 Azure Active Directory 테넌트에서 새 서비스 주체를 만들거나 기존 서비스 주체를 사용할 수 있습니다.

### <a name="authentication-for-a-scan"></a>검사 인증

#### <a name="using-system-or-user-assigned-managed-identity-for-scanning"></a>검사를 위해 시스템 또는 사용자 할당 관리 ID 사용

Purview 계정에 ADLS Gen1 데이터 원본을 검사할 수 있는 권한을 부여하는 것이 중요합니다. 검사 권한을 가질 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 시스템 관리 ID 또는 사용자 할당 관리 ID를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 관리 ID를 추가하려면 구독의 소유자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그에서 검사하도록 허용하려는 구독, 리소스 그룹 또는 리소스(예: Azure Data Lake Storage Gen1 스토리지 계정)를 찾습니다.
1. **개요를** 선택한 **다음, 데이터 탐색기를** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="스토리지 계정을 보여 주는 스크린샷":::

1. 위쪽 탐색에서 **액세스를** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="스토리지 계정의 데이터 탐색기를 보여 주는 스크린샷":::

1. **선택을** 선택하고 사용자 또는 그룹 선택 메뉴에서 _이미 Purview에 등록된 Azure Purview 이름(시스템_ 관리 ID) 또는 사용자 할당 관리 _[ID(미리](manage-credentials.md#create-a-user-assigned-managed-identity)_ **보기)를** 추가합니다.
1. **읽기** 및 **실행** 권한을 선택합니다. 아래 스크린샷과 같이 추가 옵션에서 **이 폴더와 모든 자식**, **액세스 권한 항목 및 기본 권한 항목을** 선택해야 합니다. **확인** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="Purview 계정에 대한 권한을 할당하기 위한 세부 정보를 보여 주는 스크린샷":::

> [!Tip]
> **액세스 권한 항목** 은 ‘현재’ 파일과 폴더에 대한 권한 항목입니다. **기본 권한 항목** 은 새 파일과 폴더에 ‘상속’되는 권한 항목입니다.
현재 기존 파일에만 권한을 부여하려면 **액세스 권한 항목을 선택** 합니다.
나중에 추가되는 파일과 폴더를 검색할 수 있는 권한을 부여하려면 **기본 권한 항목을 포함** 합니다.

#### <a name="using-service-principal-for-scanning"></a>검사에 서비스 주체 사용

##### <a name="creating-a-new-service-principal"></a>새 서비스 주체 만들기

[새 서비스 주체를 만들어야](./create-service-principal-azure.md) 하는 경우 애플리케이션을 Azure AD 테넌트에 등록하고 데이터 원본의 서비스 주체에 대한 액세스를 제공해야 합니다. 이 작업은 Azure AD 전역 관리자 또는 애플리케이션 관리자와 같은 다른 역할에서 수행할 수 있습니다.

##### <a name="getting-the-service-principals-application-id"></a>서비스 주체의 애플리케이션 ID 얻기

1. 이미 만든 [_서비스 주체_](./create-service-principal-azure.md)의 **개요** 에 있는 **애플리케이션(클라이언트) ID** 를 복사합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="서비스 주체의 애플리케이션(클라이언트) ID를 보여 주는 스크린샷":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>서비스 주체에 ADLS Gen1 계정에 대한 액세스 권한 부여

서비스 주체에게 ADLS Gen2 데이터 원본을 검사할 수 있는 권한을 부여하는 것이 중요합니다. 필요한 권한에 따라 구독, 리소스 그룹 또는 리소스 수준에서 서비스 주체에 대한 액세스를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 서비스 주체를 추가할 수 있으려면 구독의 소유자이어야 합니다.

1. 스토리지 계정을 열고 **개요**  -->  **데이터 탐색기** 선택하여 스토리지 계정에 대한 서비스 주체 액세스를 제공합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="스토리지 계정을 보여 주는 스크린샷":::

1. 위쪽 탐색에서 **액세스를** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="스토리지 계정의 데이터 탐색기를 보여 주는 스크린샷":::

1. 사용자 또는 그룹 **선택 선택에서** 선택 및 _서비스 주체_ 추가를 **선택합니다.**
1. **읽기** 및 **실행** 권한을 선택합니다. 추가 옵션에서 **이 폴더와 모든 자식**, **액세스 권한 항목 및 기본 권한 항목을** 선택해야 합니다. **확인** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="서비스 주체에 대한 사용 권한을 할당하는 세부 정보를 보여 주는 스크린샷":::

### <a name="creating-the-scan"></a>검사 만들기

1. **Purview 계정** 을 열고, **Purview Studio 열기** 를 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Open Purview Studio를 보여 주는 스크린샷":::

1. **데이터 맵** --> **원본** 으로 차례로 이동하여 컬렉션 계층 구조를 봅니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="컬렉션 계층 구조를 보여 주는 스크린샷":::

1. 이전에 등록된 **ADLS Gen1 데이터 원본** 아래에서 새 **검색** 아이콘을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="새 검색 아이콘이 있는 데이터 원본을 보여 주는 스크린샷":::

#### <a name="if-using-system-or-user-assigned-managed-identity"></a>시스템 또는 사용자 할당 관리 ID를 사용하는 경우

검사의 **이름을** 제공하고, **자격 증명에서** 시스템 또는 사용자 할당 관리 ID를 선택하고, 검사에 적합한 컬렉션을 선택하고, **연결 테스트를** 선택합니다. 연결이 성공적이면 **계속을** 선택합니다.

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="검사를 실행하는 관리 ID 옵션을 보여 주는 스크린샷":::

#### <a name="if-using-service-principal"></a>서비스 주체를 사용하는 경우

1. 검사에 대한 **이름을** 제공하고, 검사에 적합한 컬렉션을 선택하고, **자격 증명** 아래에서 **+ 새로** 설정을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="서비스 주체 옵션을 보여 주는 스크린샷":::

1. _서비스 주체_ 를 만드는 동안 사용된 적절한 **키 자격** 증명 모음 연결 및 **비밀 이름을** 선택합니다. **서비스 주체 ID는** 앞서 표시된 대로 복사된 **애플리케이션(클라이언트) ID입니다.**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="서비스 주체 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 연결에 성공하면 **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="서비스 주체에 대한 테스트 연결을 보여 주는 스크린샷":::

### <a name="scoping-and-running-the-scan"></a>검사 범위 지정 및 실행

1. 목록에서 적절한 항목을 선택하여 검색 범위를 특정 폴더와 하위 폴더로 지정할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 새 검사 _규칙 집합을_ 만드는 경우 검색 규칙에 포함할 **파일 형식을** 선택합니다. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="검사 규칙 집합 파일 형식":::

1. 검사 규칙에 포함할 **분류 규칙** 을 선택할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="검사 규칙 집합 분류 규칙":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="검사 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="검사 트리거":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="검사 트리거 선택":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="검사 검토":::

### <a name="viewing-scan"></a>검사 보기

1. _컬렉션_ 에서 _데이터 원본_ 으로 이동하고, **세부 정보 보기** 를 선택하여 검사 상태를 확인합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="검사 보기":::

1. 검사 세부 정보는 **마지막 실행 상태** 의 검사 진행률과 _검사됨_ 및 _분류됨_ 상태의 자산 수를 나타냅니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="검사 세부 정보 보기":::

1. 전체 검색이 성공적으로 실행되면 **마지막 실행 상태가** 진행 **중으로** 업데이트된 다음 **완료됨으로** 업데이트됩니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="진행 중인 검사 보기":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="완료된 검사 보기":::

### <a name="managing-scan"></a>검사 관리

검사를 관리하거나 완료 후 다시 실행할 수 있습니다.

1. 검사를 관리할 **검사 이름** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="검사 관리":::

1. _검사를 다시 실행_ 하고, _검사를 편집_ 하고, _검사를 삭제_ 할 수 있습니다.  

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-options.png" alt-text="검사 옵션 관리":::

    > [!NOTE]
    > * 스캔을 삭제해도 이전 스캔에서 생성된 카탈로그 자산은 삭제되지 않습니다.
    > * 원본 테이블을 변경하고 Purview의 스키마 탭에서 설명을 편집한 후에 원본 테이블을 다시 검사하면 해당 자산이 스키마 변경 내용으로 더 이상 업데이트되지 않습니다.

1. _증분 검색_ 또는 _전체 검색을_ 다시 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-full-inc-scan.png" alt-text="전체 또는 증분 검사 관리":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-results.png" alt-text="검사 결과 관리":::

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
