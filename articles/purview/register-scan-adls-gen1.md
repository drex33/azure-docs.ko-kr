---
title: Azure Data Lake Storage(ADLS) Gen1 등록 및 검사
description: 이 자습서에서는 Azure Data Lake Storage Gen1에서 Azure Purview로 데이터를 검사하는 방법을 설명합니다.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 093375e30fa9f9a3d6a558c5e230a8bc81a701ad
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215779"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 등록 및 검사

이 문서에서는 Azure Purview에서 Azure Data Lake Storage Gen1을 데이터 원본으로 등록하고 이에 대한 검사를 설정하는 방법을 간략하게 설명합니다.

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Data Lake Storage Gen1 데이터 원본은 다음과 같은 기능을 지원합니다.

- Azure Data Lake Storage Gen1에서 메타데이터 및 분류를 캡처하기 위한 **전체 및 증분 검사**.

- ADF 복사/데이터 흐름에 대한 데이터 자산 간의 **계보**.

csv, tsv, psv, ssv와 같은 파일 형식의 경우 다음 논리가 있을 때 스키마가 추출됩니다.

1. 첫 번째 행 값이 비어 있지 않음
2. 첫 번째 행 값이 고유함
3. 첫 번째 행 값은 날짜 및 숫자가 아님

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Data Lake Storage Gen1에 대해 다음 인증 방법이 지원됩니다.

- 관리 ID
- 서비스 사용자

### <a name="managed-identity-recommended"></a>관리 ID(권장)

간편함과 보안을 위해 Purview MSI를 사용하여 데이터 저장소를 인증할 수 있습니다.

Data Catalog의 MSI를 사용하여 검사를 설정하려면 먼저 Purview 계정에 데이터 소스를 검사할 수 있는 권한을 부여해야 합니다. 이 단계는 검사를 설정하기 *전* 에 수행해야 하며 그렇지 않으면 검사가 실패합니다.

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 계정에 Purview MSI 추가

검색 권한을 부여할 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 카탈로그의 MSI를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 관리 ID를 추가하려면 구독의 소유자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그에서 검사하도록 허용하려는 구독, 리소스 그룹 또는 리소스(예: Azure Data Lake Storage Gen1 스토리지 계정)를 찾습니다.

2. **개요** 를 선택한 다음 **데이터 탐색기** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="액세스 제어 선택":::

3. 위쪽 탐색에서 **액세스** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="액세스 선택":::

4. **추가** 를 선택합니다. 사용자 또는 그룹 선택 영역에서 **Purview 카탈로그** 를 추가합니다. **읽기** 및 **실행** 권한을 선택합니다. 아래 스크린샷에 표시된 것처럼 추가 옵션에서 **이 폴더 및 모든 하위 폴더** 와 **액세스 권한 항목 및 기본 권한 항목** 을 선택해야 합니다. **확인** 
    :::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="MSI 인증 세부 정보"::: 를 선택 합니다.
   
> [!Tip]
> **액세스 권한 항목** 은 ‘현재’ 파일과 폴더에 대한 권한 항목입니다.
> **기본 권한 항목** 은 새 파일과 폴더에 ‘상속’되는 권한 항목입니다.
> 
> 현재 기존 파일에만 권한을 부여하려면 **액세스 권한 항목을 선택** 합니다.
> 
> 나중에 추가되는 파일과 폴더를 검색할 수 있는 권한을 부여하려면 **기본 권한 항목을 포함** 합니다.
> 
> 자세한 내용은 [권한 설명서](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)를 참조하세요.

5. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.

6. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다.

> [!Note]
> 데이터 원본에 카탈로그의 MSI를 추가한 후에는 검사를 설정하기 전에 권한이 전파될 때까지 최대 15분 정도 기다립니다.

약 15분 후 카탈로그에 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

### <a name="service-principal"></a>서비스 사용자

서비스 주체를 사용하려면 먼저 다음 단계에 따라 서비스 주체를 만들어야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.

3. **앱 등록** 을 선택합니다.

4. **+ 새 애플리케이션 등록** 을 선택합니다.

5. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.

6. **이 조직 디렉터리의 계정만** 을 선택합니다.

7. **리디렉션 URI** 에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요는 없습니다.

8. 그런 다음, **등록** 을 선택합니다.

9. 표시 이름과 애플리케이션 ID 모두에서 값을 복사합니다.

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 계정에 Purview 서비스 주체 추가

1. [Azure Portal](https://portal.azure.com)에서 카탈로그에서 검사하도록 허용하려는 구독, 리소스 그룹 또는 리소스(예: Azure Data Lake Storage Gen1 스토리지 계정)를 찾습니다.

2. **개요** 를 선택한 다음 **데이터 탐색기** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="액세스 제어 선택":::

3. 위쪽 탐색에서 **액세스** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="액세스 선택":::

4. **추가** 를 선택합니다. 사용자 또는 그룹 선택에서 **서비스 주체 애플리케이션** 을 추가합니다. **읽기** 및 **실행** 권한을 선택합니다. 아래 스크린샷에 표시된 것처럼 추가 옵션에서 **이 폴더 및 모든 하위 폴더** 와 **액세스 권한 항목 및 기본 권한 항목** 을 선택해야 합니다. **확인** 
    :::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="서비스 주체 인증 세부 정보"::: 를 선택 합니다.

> [!Tip]
> **액세스 권한 항목** 은 ‘현재’ 파일과 폴더에 대한 권한 항목입니다.
> **기본 권한 항목** 은 새 파일과 폴더에 ‘상속’되는 권한 항목입니다.
>
> 현재 기존 파일에만 권한을 부여하려면 **액세스 권한 항목을 선택** 합니다.
>
> 나중에 추가되는 파일과 폴더를 검색할 수 있는 권한을 부여하려면 **기본 권한 항목을 포함** 합니다.
>
> 자세한 내용은 [권한 설명서](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)를 참조하세요.

5. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.

6. 마지막으로 서비스 주체를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 데이터 원본 등록

새 ADLS Gen1 계정을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview Data Catalog로 이동합니다.
2. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3. **등록** 을 선택합니다.
4. **원본 등록** 에서 **Azure Data Lake Storage Gen1** 을 선택합니다. 
5. **계속** 을 선택합니다.

원본 등록(Azure Data Lake Storage Gen1) 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. 스토리지 계정을 필터링하려면 구독을 선택합니다.
3. 스토리지 계정을 선택합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
5. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [부서의 범위 Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택 합니다.

1. 등록한 Azure Data Lake Storage Gen1 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다.

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 선택하여 검색 범위를 특정 폴더와 하위 폴더로 지정할 수 있습니다.

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="검사 범위 지정":::

1. 그런 다음, 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 규칙 집합 중 하나를 선택하거나 새 규칙 집합을 인라인으로 만들 수 있습니다.

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
