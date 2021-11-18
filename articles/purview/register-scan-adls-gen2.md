---
title: ADLS(Azure Data Lake Storage) Gen2 등록 및 검사
description: 이 문서에서는 Azure Data Lake Storage Gen2 소스를 인증 하 고 상호 작용 하는 지침을 포함 하 여 Azure 부서의 범위에 Azure Data Lake Storage Gen2 데이터 원본을 등록 하는 프로세스에 대해 간략하게 설명 합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 5edc1074d9003aa905c052ed14be9a334a963ebe
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757809"
---
# <a name="connect-to-azure-data-lake-gen2-in-azure-purview"></a>Azure 부서의 범위에서 Azure Data Lake Gen2에 커넥트

이 문서에서는 Azure Data Lake Storage Gen2 소스를 인증 하 고 상호 작용 하는 지침을 포함 하 여 Azure 부서의 범위에 Azure Data Lake Storage Gen2 데이터 원본을 등록 하는 프로세스에 대해 간략하게 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)|[예](#scan) | [예](#scan)|[예](#scan)| 예 | 제한 됨 * * |

\**데이터 세트가 [데이터 팩터리 복사 작업](how-to-link-azure-data-factory.md)에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 ADLS Gen2 데이터 원본을 등록 하 고 적절 한 인증 메커니즘을 설정 하 여 데이터 원본을 성공적으로 검색할 수 있도록 합니다.

### <a name="steps-to-register"></a>등록 단계

데이터 원본에 대한 검사를 설정하기 전에 Azure Purview에서 데이터 원본을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com), **Purview 계정** 페이지로 차례로 이동하여 _Purview 계정_ 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-purview-acct.png" alt-text="데이터 원본을 등록하는 데 사용되는 Purview 계정을 보여 주는 스크린샷":::

1. **부서의 범위 Studio를 열고** 데이터 맵으로 이동 합니다. **> 원본**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-open-purview-studio.png" alt-text="부서의 범위 Studio 열기 링크를 보여 주는 스크린샷":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sources.png" alt-text="데이터 맵의 원본 링크로 이동하는 스크린샷":::

1. **컬렉션 메뉴를** 사용 하 여 [컬렉션 계층 구조](./quickstart-create-collection.md) 를 만들고 필요에 따라 개별 하위 컬렉션에 사용 권한을 할당 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-collections.png" alt-text="컬렉션 계층 구조를 만드는 컬렉션 메뉴를 보여 주는 스크린샷":::

1. **소스** 메뉴에서 적절 한 컬렉션으로 이동 하 고 **등록** 아이콘을 선택 하 여 새 ADLS Gen2 데이터 소스를 등록 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-register-source.png" alt-text="데이터 원본을 등록하는 데 사용되는 컬렉션을 보여 주는 스크린샷":::

1. **Azure Data Lake Storage Gen2** 데이터 원본을 선택 하 고 **계속** 을 선택 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-data-source.png" alt-text="데이터 원본을 선택할 수 있는 스크린샷":::

1. 데이터 원본에 적합 한 **이름을** 제공 하 고, 관련 **Azure 구독**, 기존 **Data Lake Store 계정 이름** 및 **컬렉션** 을 선택 하 고, **적용** 을 선택 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-details.png" alt-text="데이터 원본을 등록하기 위해 입력하는 세부 정보를 보여 주는 스크린샷":::

1. ADLS Gen2 저장소 계정이 선택한 컬렉션 아래에 표시 됩니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-collection.png" alt-text="검사를 시작하기 위해 컬렉션에 매핑된 데이터 원본을 보여 주는 스크린샷":::

## <a name="scan"></a>검사

### <a name="prerequisites-for-scan"></a>검사를 위한 필수 구성 요소

에서 데이터 원본을 검색할 수 있도록 하려면 ADLS Gen2 Storage 계정의 인증 방법을 구성 해야 합니다.
다음과 같은 옵션이 지원됩니다.

> [!Note]
> 저장소 계정에 대해 방화벽을 사용 하도록 설정한 경우에는 검색을 설정할 때 관리 id 인증 방법을 사용 해야 합니다.

* **시스템 할당 관리 id (권장)** -Azure 부서의 범위 계정이 생성 되 면 azure AD 테 넌 트에서 시스템 할당 관리 ID (SAMI)가 자동으로 만들어집니다. 리소스 유형에 따라, 검색을 수행 하기 위해 Azure 부서의 범위 시스템 할당 관리 id (SAMI)에 특정 RBAC 역할 할당이 필요 합니다.

* **사용자 할당 관리 id** (미리 보기)-시스템 관리 Id와 마찬가지로 UAMI (사용자 할당 관리 id)는 Azure 부서의 범위가 Azure Active Directory에 대해 인증 하도록 허용 하는 데 사용할 수 있는 자격 증명 리소스입니다. 자세한 내용은 [사용자 할당 관리 id 가이드](manage-credentials.md#create-a-user-assigned-managed-identity)를 참조 하세요.

* **계정 키** -암호를 사용 하 여 데이터 원본을 안전 하 게 검색할 수 있도록 자격 증명을 저장 하기 위해 Azure Key Vault 내에 암호를 만들 수 있습니다. 암호는 저장소 계정 키, SQL 로그인 암호 또는 암호 일 수 있습니다.

   > [!Note]
   > 이 옵션을 사용 하는 경우 구독에 _azure 주요 자격 증명 모음_ 리소스를 배포 하 고 azure _key vault_ 내에서 암호에 대 한 필수 액세스 권한을 사용 하 여 _azure 부서의 범위 계정의_ SAMI를 할당 해야 합니다.

* **서비스 주체** - 이 방법에서는 Azure Active Directory 테넌트에서 새 서비스 주체를 만들거나 기존 서비스 주체를 사용할 수 있습니다.

### <a name="authentication-for-a-scan"></a>검사 인증

#### <a name="using-a-system-or-user-assigned-managed-identity-for-scanning"></a>검색에 시스템 또는 사용자 할당 관리 id 사용

부서의 범위 계정 또는 UAMI (사용자 할당 관리 id)에 ADLS Gen2 데이터 원본을 검색할 수 있는 권한을 부여 하는 것이 중요 합니다. 부서의 범위 계정의 시스템 할당 관리 id (부서의 범위 계정과 동일한 이름) 또는 필요한 수준 스캔 권한에 따라 구독, 리소스 그룹 또는 리소스 수준에서 UAMI를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 관리 ID를 추가하려면 구독의 소유자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그를 검색할 수 있도록 허용 하려는 구독, 리소스 그룹 또는 리소스 (예: Azure Data Lake Storage Gen2 저장소 계정)를 찾습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="저장소 계정을 보여 주는 스크린샷":::

1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택한 다음, **+ 추가** --> **역할 할당 추가** 를 차례로 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="저장소 계정에 대 한 액세스 제어를 보여 주는 스크린샷":::

1. **Blob 데이터 판독기 Storage** **역할** 을 설정 하 고 **선택** 입력란 아래에 _Azure 부서의 범위 계정 이름_ 또는 _[사용자 할당 관리 id](manage-credentials.md#create-a-user-assigned-managed-identity)_ 를 입력 합니다. 그런 다음, **저장** 을 선택하여 Purview 계정에 이 역할을 할당합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-assign-permissions.png" alt-text="Purview 계정에 대한 권한을 할당하기 위한 세부 정보를 보여 주는 스크린샷":::

> [!Note]
> 자세한 내용은 [Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](../storage/blobs/authorize-access-azure-active-directory.md)의 단계를 참조하세요.

> [!NOTE]
> 저장소 계정에 대해 방화벽을 사용 하도록 설정한 경우에는 검색을 설정할 때 **관리 id** 인증 방법을 사용 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 ADLS Gen2 스토리지 계정으로 이동
1. **보안 + 네트워킹 > 네트워킹** 으로 이동 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-networking.png" alt-text="방화벽 액세스를 제공 하는 세부 정보를 보여 주는 스크린샷":::

1. **다음에서 액세스 허용** 에서 **선택한 네트워크** 선택

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-network-access.png" alt-text="선택한 네트워크에 대 한 액세스를 허용 하는 세부 정보를 보여 주는 스크린샷":::

1. **예외** 섹션에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스할 수 있도록 허용** 을 선택하고 **저장** 선택

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-permission-microsoft-services.png" alt-text="신뢰할 수 있는 Microsoft 서비스 저장소 계정에 액세스할 수 있도록 허용 하는 예외를 보여 주는 스크린샷":::

#### <a name="using-account-key-for-scanning"></a>검색을 위해 계정 키 사용

선택한 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 Key Vault에 저장해야 합니다.

1. ADLS Gen2 스토리지 계정으로 이동합니다.
1. **보안 + 네트워킹 > 액세스 키** 선택

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-keys.png" alt-text="저장소 계정의 액세스 키를 보여 주는 스크린샷":::

1. *키* 를 복사 하 여 다음 단계를 위해 별도로 저장 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key.png" alt-text="복사할 선택 키를 보여 주는 스크린샷":::

1. 키 자격 증명 모음으로 이동

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key-vault.png" alt-text="키 자격 증명 모음을 보여 주는 스크린샷":::

1. 비밀을 생성 하 **설정** 키  
 :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-generate-secret.png" alt-text="자격 증명 모음 옵션을 보여 주는"::: + 생성/가져오기 스크린샷을 선택 하 > 비밀을 선택 합니다.

1. 저장소 계정의 *키* 로 **이름과** **값** 을 입력 합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret-values.png" alt-text="비밀 값을 입력하는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. **만들기** 를 선택하여 완료합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret.png" alt-text="비밀을 만드는 키 자격 증명 모음 옵션을 보여 주는 스크린샷":::

1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

#### <a name="using-service-principal-for-scanning"></a>검사에 서비스 주체 사용

##### <a name="creating-a-new-service-principal"></a>새 서비스 주체 만들기

[새 서비스 주체를 만들어야](./create-service-principal-azure.md) 하는 경우 애플리케이션을 Azure AD 테넌트에 등록하고 데이터 원본의 서비스 주체에 대한 액세스를 제공해야 합니다. 이 작업은 Azure AD 전역 관리자 또는 애플리케이션 관리자와 같은 다른 역할에서 수행할 수 있습니다.

##### <a name="getting-the-service-principals-application-id"></a>서비스 주체의 애플리케이션 ID 가져오기

1. 이미 만든 [_서비스 주체_](./create-service-principal-azure.md)의 **개요** 에 있는 **애플리케이션(클라이언트) ID** 를 복사합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-appln-id.png" alt-text="서비스 주체의 애플리케이션(클라이언트) ID를 보여 주는 스크린샷":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>서비스 주체에 ADLS Gen2 계정에 대한 액세스 권한 부여

서비스 주체에게 ADLS Gen2 데이터 원본을 검사할 수 있는 권한을 부여하는 것이 중요합니다. 필요한 수준 검색 권한에 따라 구독, 리소스 그룹 또는 리소스 수준에서 서비스 주체에 대한 액세스를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 서비스 주체를 추가할 수 있으려면 구독의 소유자이어야 합니다.

1. [Azure Portal](https://portal.azure.com)카탈로그에서 검색할 수 있도록 허용할 구독, 리소스 그룹 또는 리소스(예: Azure Data Lake Storage Gen2 스토리지 계정)를 찾습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="스토리지 계정을 보여 주는 스크린샷":::

1. 왼쪽 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택한 다음, **+ 추가** --> **역할 할당 추가** 를 차례로 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="스토리지 계정에 대한 액세스 제어를 보여 주는 스크린샷":::

1. **역할을** **Blob 데이터 읽기 Storage** 설정하고 입력 **선택** 상자에 _서비스 주체를_ 입력합니다. 그런 다음, **저장** 을 선택하여 Purview 계정에 이 역할을 할당합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-permission.png" alt-text="서비스 주체에 스토리지 계정 권한을 제공하는 세부 정보를 보여 주는 스크린샷":::

### <a name="create-the-scan"></a>검사 만들기

1. **Purview 계정** 을 열고, **Purview Studio 열기** 를 선택합니다.
1. **데이터 맵** --> **원본** 으로 차례로 이동하여 컬렉션 계층 구조를 봅니다.
1. 이전에 등록된 **ADLS Gen2 데이터 원본** 아래에서 새 **검색** 아이콘을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-new-scan.png" alt-text="새 검사를 만드는 화면을 보여 주는 스크린샷":::

#### <a name="if-using-a-system-or-user-assigned-managed-identity"></a>시스템 또는 사용자 할당 관리 ID를 사용하는 경우

1. 검사의 **이름을** 제공하고, **자격 증명에서** 시스템 할당 또는 사용자 할당 관리 ID를 선택하고, 검사에 적합한 컬렉션을 선택하고, **연결 테스트를** 선택합니다. 연결이 성공적이면 **계속을** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-managed-identity.png" alt-text="검사를 실행하는 관리 ID 옵션을 보여 주는 스크린샷":::

#### <a name="if-using-account-key"></a>계정 키를 사용하는 경우

1. 검사에 대한 **이름을** 제공하고, 검사에 적합한 컬렉션을 선택하고, **인증 방법을** _계정 키로_ 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-acct-key.png" alt-text="검사를 위한 계정 키 옵션을 보여 주는 스크린샷":::

#### <a name="if-using-service-principal"></a>서비스 주체를 사용하는 경우

1. 검사에 대한 **이름을** 제공하고, 검사에 적합한 컬렉션을 선택하고, **자격 증명** 아래에서 **+ 새로** 설정을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-option.png" alt-text="서비스 주체가 검사를 사용하도록 설정하는 옵션을 보여 주는 스크린샷":::

1. _서비스 주체_ 를 만드는 동안 사용된 적절한 **키 자격** 증명 모음 연결 및 **비밀 이름을** 선택합니다. **서비스 주체 ID는** 이전에 복사한 **애플리케이션(클라이언트) ID입니다.**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-service-principal-option.png" alt-text="서비스 주체 옵션을 보여 주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 연결에 성공하면 **계속** 을 선택합니다.

### <a name="scope-and-run-the-scan"></a>검사 범위 및 실행

1. 목록에서 적절한 항목을 선택하여 검색 범위를 특정 폴더와 하위 폴더로 지정할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scope-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 새 검사 _규칙 집합을_ 만드는 경우 검색 규칙에 포함할 **파일 형식을** 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-file-types.png" alt-text="검사 규칙 집합 파일 형식":::

1. 검사 규칙에 포함할 **분류 규칙** 을 선택할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-classification rules.png" alt-text="검사 규칙 집합 분류 규칙":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-scan-rule-set.png" alt-text="검사 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-trigger.png" alt-text="검사 트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-review-scan.png" alt-text="검사 검토":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
