---
title: Azure Data Lake Storage(ADLS) Gen1 등록 및 검사
description: 이 문서에서는 Azure Data Lake Storage Gen 1 소스를 인증 하 고 상호 작용 하는 지침을 포함 하 여 Azure 부서의 범위에 Azure Data Lake Storage Gen1 데이터 원본을 등록 하는 프로세스에 대해 간략하게 설명 합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2887511d9d92fcf9112473207ba3445282b8b87f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015616"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Azure 부서의 범위에서 Azure Data Lake Gen1에 커넥트

이 문서에서는 Azure Data Lake Storage Gen1 소스를 인증 하 고 상호 작용 하는 지침을 포함 하 여 Azure 부서의 범위에 Azure Data Lake Storage Gen1 데이터 원본을 등록 하는 프로세스에 대해 간략하게 설명 합니다.

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)|[예](#scan) | [예](#scan)|[예](#scan)| 아니요 |[Data Factory 계보](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 ADLS Gen1 데이터 원본을 등록 하 고 적절 한 인증 메커니즘을 설정 하 여 데이터 원본을 성공적으로 검색할 수 있도록 합니다.

### <a name="steps-to-register"></a>등록 단계

데이터 원본에 대 한 검색을 설정 하기 전에 Azure 부서의 범위에서 데이터 원본을 등록 하는 것이 중요 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **부서의 범위 계정** 페이지로 이동 하 고 _부서의 범위 계정을_ 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="데이터 원본을 등록 하는 데 사용 되는 부서의 범위 계정을 보여 주는 스크린샷":::

1. **부서의 범위 Studio를 열고** 데이터 맵으로 이동 합니다. **> 원본**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="부서의 범위 Studio 열기 링크를 보여 주는 스크린샷":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="데이터 맵의 원본 링크로 이동 하는 스크린샷":::

1. **컬렉션 메뉴를** 사용 하 여 [컬렉션 계층 구조](./quickstart-create-collection.md) 를 만들고 필요에 따라 개별 하위 컬렉션에 사용 권한을 할당 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="컬렉션 계층 구조를 만드는 컬렉션 메뉴를 보여 주는 스크린샷":::

1. **소스** 메뉴에서 적절 한 컬렉션으로 이동 하 고 **등록** 아이콘을 클릭 하 여 새 ADLS Gen1 데이터 소스를 등록 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="데이터 원본을 등록 하는 데 사용 되는 컬렉션을 보여 주는 스크린샷":::

1. **Azure Data Lake Storage Gen1** 데이터 원본을 선택 하 고 **계속** 을 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="데이터 소스를 선택할 수 있는 스크린샷":::

1. 데이터 원본에 적합 한 **이름을** 제공 하 고, 관련 **Azure 구독**, 기존 **Data Lake Store 계정 이름** 및 **컬렉션** 을 선택 하 고, **적용** 을 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="데이터 원본을 등록 하기 위해 입력 해야 하는 세부 정보를 보여 주는 스크린샷":::

1. ADLS Gen1 저장소 계정이 선택한 컬렉션 아래에 표시 됩니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="검색을 시작 하기 위해 컬렉션에 매핑된 데이터 원본을 보여 주는 스크린샷":::

## <a name="scan"></a>검사

### <a name="prerequisites-for-scan"></a>검사를 위한 필수 구성 요소

에서 데이터 원본을 검색할 수 있도록 하려면 ADLS Gen1 Storage 계정의 인증 방법을 구성 해야 합니다.
다음과 같은 옵션이 지원됩니다.

> [!Note]
> 스토리지 계정에 대해 방화벽을 사용하도록 설정한 경우에는 검색을 설정할 때 관리 ID 인증 방법을 사용해야 합니다.

* **관리 id (권장)** -Azure 부서의 범위 계정이 생성 되 면 azure AD 테 넌 트에서 시스템 **관리 id** 가 자동으로 생성 됩니다. 리소스 유형에 따라 Azure 부서의 범위 MSI에서 검사를 수행 하는 데 특정 RBAC 역할 할당이 필요 합니다.

* **서비스 사용자** -이 방법에서는 새를 만들거나 Azure Active Directory 테 넌 트에서 기존 서비스 주체를 사용할 수 있습니다.

### <a name="authentication-for-a-scan"></a>검색에 대 한 인증

#### <a name="using-managed-identity-for-scanning"></a>관리 Id를 사용 하 여 검색

부서의 범위 계정에 ADLS Gen1 데이터 원본을 검색할 수 있는 권한을 부여 하는 것이 중요 합니다. 검색 권한을 부여할 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 카탈로그의 MSI를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 관리 ID를 추가하려면 구독의 소유자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그에서 검사하도록 허용하려는 구독, 리소스 그룹 또는 리소스(예: Azure Data Lake Storage Gen1 스토리지 계정)를 찾습니다.
1. **개요** 를 클릭한 다음, **데이터 탐색기** 를 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="저장소 계정을 보여 주는 스크린샷":::

1. 위쪽 탐색 모음에서 **액세스** 를 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="저장소 계정에 대 한 데이터 탐색기를 보여 주는 스크린샷":::

1. **선택** 을 클릭 하 고 **사용자 또는 그룹 선택** 선택 영역에서 _부서의 범위 카탈로그_ 를 추가 합니다.
1. **읽기** 및 **실행** 권한을 선택합니다. 아래 스크린샷에 표시 된 것 처럼 추가 옵션에서 **이 폴더와 모든 자식**, **액세스 권한 항목 및 기본 권한 항목** 을 선택 해야 합니다. **확인** 을 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="부서의 범위 계정에 대 한 사용 권한을 할당 하는 세부 정보를 보여 주는 스크린샷":::

> [!Tip]
> **액세스 권한 항목** 은 ‘현재’ 파일과 폴더에 대한 권한 항목입니다. **기본 권한 항목** 은 새 파일과 폴더에 ‘상속’되는 권한 항목입니다.
현재 기존 파일에만 권한을 부여하려면 **액세스 권한 항목을 선택** 합니다.
나중에 추가되는 파일과 폴더를 검색할 수 있는 권한을 부여하려면 **기본 권한 항목을 포함** 합니다.

#### <a name="using-service-principal-for-scanning"></a>검색을 위해 서비스 주체 사용

##### <a name="creating-a-new-service-principal"></a>새 서비스 주체 만들기

[새 서비스 주체를 만들어야](./create-service-principal-azure.md)하는 경우 Azure AD 테 넌 트에 응용 프로그램을 등록 하 고 데이터 원본에서 서비스 주체에 대 한 액세스를 제공 해야 합니다. Azure AD 전역 관리자 또는 응용 프로그램 관리자와 같은 다른 역할은이 작업을 수행할 수 있습니다.

##### <a name="getting-the-service-principals-application-id"></a>서비스 주체의 응용 프로그램 ID를 가져오는 중

1. 이미 만든 [_서비스 사용자_](./create-service-principal-azure.md) 의 **개요** 에 있는 **응용 프로그램 (클라이언트) ID** 를 복사 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="서비스 사용자의 응용 프로그램 (클라이언트) ID를 보여 주는 스크린샷":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>ADLS Gen1 계정에 대 한 서비스 사용자 액세스 권한 부여

서비스 사용자에 게 ADLS Gen2 데이터 원본을 검색할 수 있는 권한을 부여 하는 것이 중요 합니다. 검색 권한을 부여할 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 카탈로그의 MSI를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에서 서비스 주체를 추가할 수 있으려면 구독의 소유자 여야 합니다.

1. 저장소 계정을 열고 **개요** 를 클릭 하 여 저장소 계정에 대 한 서비스 주체 액세스를 제공  -->  **데이터 탐색기**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="저장소 계정을 보여 주는 스크린샷":::

1. 위쪽 탐색 모음에서 **액세스** 를 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="저장소 계정에 대 한 데이터 탐색기를 보여 주는 스크린샷":::

1. **선택** 을 클릭 하 고 **사용자 또는 그룹 선택** 선택에서 _서비스 주체_ 를 추가 합니다.
1. **읽기** 및 **실행** 권한을 선택합니다. **이 폴더와 모든 자식** 항목을 선택 하 고 추가 옵션에서 **액세스 권한 항목 및 기본 권한 항목** 을 선택 해야 합니다. **확인** 을 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="서비스 사용자에 대 한 사용 권한을 할당 하는 세부 정보를 보여 주는 스크린샷":::

### <a name="creating-the-scan"></a>검색 만들기

1. **부서의 범위 계정을** 열고 **오픈 부서의 범위 Studio** 를 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Open 부서의 범위 Studio를 보여 주는 스크린샷":::

1. **데이터 맵** 원본으로 이동 하 여  -->   컬렉션 계층 구조를 확인 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="컬렉션 계층 구조를 보여 주는 스크린샷":::

1. 이전에 등록 된 **ADLS Gen1 데이터 소스** 에서 **새 검사** 아이콘을 클릭 합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="새 검색 아이콘이 있는 데이터 원본을 보여 주는 스크린샷":::

#### <a name="if-using-managed-identity"></a>관리 ID를 사용하는 경우

검사에 대한 **이름을** 제공하고, **자격 증명에서** **Purview MSI를** 선택하고, 검사에 적절한 컬렉션을 선택하고, **연결 테스트를** 클릭합니다. 연결이 성공적이면 **계속을** 클릭합니다.

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="검사를 실행하는 관리 ID 옵션을 보여 주는 스크린샷":::

#### <a name="if-using-service-principal"></a>서비스 주체를 사용하는 경우

1. 검사에 대한 **이름을** 제공하고, 검사에 적합한 컬렉션을 선택하고, **자격 증명** 아래에서 **+ 새로** 만들기를 클릭합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="서비스 주체 옵션을 보여 주는 스크린샷":::

1. _서비스 주체_ 를 만드는 동안 사용된 적절한 **키 자격** 증명 모음 연결 및 **비밀 이름을** 선택합니다. **서비스 주체 ID는** 앞서 표시된 대로 복사된 **애플리케이션(클라이언트) ID입니다.**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="서비스 주체 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. 연결 **테스트를** 클릭합니다. 연결이 성공적이면 **계속을** 클릭합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="서비스 주체에 대한 테스트 연결을 보여 주는 스크린샷":::

### <a name="scoping-and-running-the-scan"></a>검사 범위 지정 및 실행

1. 목록에서 적절한 항목을 선택하여 검색 범위를 특정 폴더 및 하위 폴더로 지정할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 새 검사 _규칙 집합을_ 만드는 경우 검색 규칙에 포함할 **파일 형식을** 선택합니다. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="검사 규칙 집합 파일 형식":::

1. 검사 규칙에 포함할 **분류** 규칙을 선택할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="검사 규칙 집합 분류 규칙":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="검사 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="검사 트리거":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="검사 트리거 선택":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="검사 검토":::

### <a name="viewing-scan"></a>검사 보기

1. _컬렉션에서_ _데이터 원본으로_ 이동하고 **세부 정보 보기를** 클릭하여 검사 상태를 확인합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="검색 보기":::

1. 검색 세부 정보는 **마지막 실행 상태의** 검사 진행률과 검색 및 _분류된_ 자산 수를 나타냅니다. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="검사 세부 정보 보기":::

1. **마지막 실행 상태는** 진행 **중으로** 업데이트되고 전체 검색이 성공적으로 실행되면 **완료됨으로** 업데이트됩니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="진행 중인 검사 보기":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="뷰 검색 완료":::

### <a name="managing-scan"></a>검사 관리

검사를 관리하거나 완료 후 다시 실행할 수 있습니다.

1. **검색 이름을** 클릭하여 검색을 관리합니다.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="검사 관리":::

1. 검색을 다시 _실행하고, 검사를_ _편집하고, 검색을_ _삭제할_ 수 있습니다.  

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
