---
title: 여러 Azure 소스에 커넥트 및 관리
description: 이 가이드에서는 Azure 부서의 범위에서 여러 Azure 원본에 한 번에 연결 하는 방법을 설명 하 고, 부서의 범위의 기능을 사용 하 여 소스를 검색 하 고 관리 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 49e6ca8cf0fc3121aba3c3216d2a1dd5936b56d7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449529"
---
# <a name="connect-to-and-manage-multiple-azure-sources-in-azure-purview"></a>Azure 부서의 범위에서 여러 Azure 원본에 커넥트 및 관리

이 문서에서는 여러 Azure 소스를 등록 하는 방법 및 Azure 부서의 범위에서 인증 하 고 상호 작용 하는 방법을 간략하게 설명 합니다. Azure 부서의 범위에 대 한 자세한 내용은 [소개 문서](overview.md)를 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan) | [예](#scan) | [예](#scan)| [예](#scan)| 아니요| [원본 종속](catalog-lineage-user-guide.md)|

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 [부서의 범위 Studio](https://web.purview.azure.com/)를 사용 하 여 azure 부서의 범위에서 여러 azure 소스를 등록 하는 방법을 설명 합니다.

### <a name="prerequisites-for-registration"></a>등록을 위한 필수 구성 요소

구독 또는 리소스 그룹의 리소스를 열거할 수 있도록 일부 인증을 설정 해야 합니다.

1. Azure Portal에서 구독 또는 리소스 그룹으로 이동합니다.  
1. 왼쪽 메뉴에서  **액세스 제어(IAM)**  를 선택합니다.
1. **+추가** 를 선택합니다.
1. **입력 선택** 상자에서 **읽기 권한자** 역할을 선택하고 Azure Purview 계정 이름(MSI 파일 이름을 나타냄)을 입력합니다. 
1. **저장** 을 선택하여 역할 할당을 완료합니다.

### <a name="authentication-for-registration"></a>등록 인증

Azure에서는 다음 두 가지 방법으로 여러 원본에 대한 인증을 설정할 수 있습니다.

* 관리 ID
* 서비스 사용자

등록 및 검사하려는 구독 또는 리소스 그룹 내의 각 리소스에 대한 인증을 설정해야 합니다. Azure Storage 리소스 종류(Azure Blob Storage 및 Azure Data Lake Storage Gen2)을 사용하면 구독 또는 리소스 그룹 수준에서 MSI 파일 또는 서비스 주체를 스토리지 Blob 데이터 읽기 권한자로 손쉽게 추가할 수 있습니다. 그런 다음, 사용 권한이 해당 구독 또는 리소스 그룹 내의 각 스토리지 계정으로 전달됩니다. 다른 모든 리소스 종류의 경우, 각 리소스에 MSI 파일 또는 서비스 주체를 적용하거나 이를 위한 스크립트를 만들어야 합니다.

구독 또는 리소스 그룹 내의 각 리소스 종류에 대한 권한을 추가하는 방법을 알아보려면 다음 문서를 참조하세요.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md#authentication-for-a-scan)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

### <a name="steps-to-register"></a>등록 단계

1. Azure Purview 계정으로 이동합니다.
1. 왼쪽 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure(다중)** 를 선택합니다.

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="여러 원본을 등록하는 화면에서 Azure Multiple 타일을 보여주는 스크린샷.":::

1. **계속** 을 선택합니다.
1. **원본 등록(Azure)** 화면에서 다음과 같이 합니다.

   1. **이름** 상자에 데이터 원본이 카탈로그에 나열될 이름을 입력합니다. 
   1. **관리 그룹** 상자에서 필요에 따라 필터링할 관리 그룹을 선택합니다.
   1. **구독** 및 **리소스 그룹** 드롭다운 목록 상자에서 구독 또는 특정 리소스 그룹을 각각 선택합니다. 등록 범위가 선택한 구독 또는 리소스 그룹으로 설정됩니다.  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="구독 및 리소스 그룹을 선택하는 상자를 보여주는 스크린샷.":::

   1. **컬렉션 선택** 상자에서 컬렉션을 선택하거나 새 컬렉션을 만듭니다(선택 사항).
   1. **등록** 을 선택하여 데이터 원본을 등록합니다.

## <a name="scan"></a>검사

여러 Azure 원본을 검색 하 여 자산을 자동으로 식별 하 고 데이터를 분류 하려면 아래 단계를 따르세요. 일반적으로 검색 하는 방법에 대 한 자세한 내용은 [검색 및 수집 소개](concept-scans-and-ingestion.md)를 참조 하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.
1. 등록한 데이터 원본을 선택합니다.
1. **세부 정보 보기** >  **+ 새 검사** 를 선택하거나 원본 타일에 **검사** 빠른 작업 아이콘을 사용합니다.
1. **이름** 에 이름을 입력합니다.
1. **종류** 에서 이 원본 내에서 검사할 리소스의 종류를 선택합니다. 다음 옵션 중 하나를 선택합니다.

    - **모두** 로 둡니다. 이 선택에는 해당 구독 또는 리소스 그룹 내에 현재 존재하지 않을 수 있는 향후 리소스 종류가 포함됩니다.
    - 상자를 사용하여 검사하려는 리소스 종류를 구체적으로 선택합니다. 이 옵션을 선택하면 나중에 검사가 명시적으로 편집되지 않는 한 이 구독 또는 리소스 그룹 내에서 나중에 생성될 수 있는 리소스 종류가 검사에 포함되지 않습니다.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="여러 원본 검사 옵션을 보여주는 스크린샷.":::

1. 데이터 원본 내의 리소스에 연결할 자격 증명을 선택합니다.
    - 부모 수준의 자격 증명을 MSI 파일로 선택하거나 특정 서비스 주체 유형에 대한 자격 증명을 선택할 수 있습니다. 그런 다음, 구독 또는 리소스 그룹 아래의 모든 리소스 종류에 대해 이 자격 증명을 사용할 수 있습니다.
    - 구체적으로 리소스 종류를 선택하고 이 리소스 종류에 대해 다른 자격 증명을 적용할 수 있습니다.

    각 자격 증명은 특정 종류의 모든 리소스에 대한 인증 방법으로 간주됩니다. [이 문서의 앞부분에서](#authentication-for-registration) 설명된 것처럼 리소스를 성공적으로 검사하려면 선택한 자격 증명을 리소스에 설정해야 합니다.
1. 각 종류 내에서 모든 리소스를 검사하거나 이름별로 일부 리소스만 검사할 수 있습니다.
    - 옵션을 **모두** 로 두면 해당 종류의 향후 리소스도 향후 검사 실행 시 검사됩니다.
    - 특정 스토리지 계정 또는 SQL 데이터베이스를 선택할 경우, 나중에 검사를 명시적으로 편집하지 않는 한 이 구독 또는 리소스 그룹 내에 생성된 해당 종류의 향후 리소스가 검사에 포함되지 않습니다.

1. **연결 테스트** 를 클릭합니다. 이렇게 하면 먼저 Azure 부서의 범위 MSI 파일을 구독 또는 리소스 그룹에 대 한 판독기로 적용 했는지 확인 하는 액세스를 테스트 합니다. 오류 메시지가 표시되면 [다음 지침](#prerequisites-for-registration)에 따라 해결합니다. 그런 다음 사용자가 선택한 각 원본에 대 한 인증 및 연결을 테스트 하 고 보고서를 생성 합니다. 선택한 원본 수는이 보고서를 생성 하는 데 걸리는 시간에 영향을 줍니다. 일부 리소스에서 실패 한 경우 **X** 아이콘을 마우스로 가리키면 자세한 오류 메시지가 표시 됩니다.

    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection.png" alt-text="연결 테스트 단추가 강조 표시 된 스캔 설정 슬라이더를 보여 주는 스크린샷":::
    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection-report.png" alt-text="일부 연결이 전달 되 고 일부 연결이 실패 하는 테스트 연결 보고서의 예를 보여 주는 스크린샷 실패 한 연결 중 하나를 가리키면 자세한 오류 보고서가 표시 됩니다.":::

1. 연결을 테스트 한 후 **계속** 을 선택 하 여 계속 진행 합니다.

1. 이전 단계에서 선택한 각 리소스 종류에 대한 검사 규칙 세트를 선택합니다. 검사 규칙 세트를 인라인으로 만들 수도 있습니다.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="각 리소스 종류의 검사 규칙을 보여주는 스크린샷.":::

1. 검사 트리거를 선택합니다. 매주, 매월 또는 한 번 실행되도록 예약할 수 있습니다.

1. 검사를 검토하고 **저장** 을 선택하여 설정을 완료합니다.

## <a name="view-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. **데이터 맵** 섹션 아래에 있는 타일에서 **세부 정보 보기** 를 선택하여 원본 세부 정보를 봅니다.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="원본 세부 정보를 보여주는 스크린샷."::: 

1. **검사 세부 정보** 페이지로 이동하여 검사 실행 세부 정보를 볼 수 있습니다.
   
    *상태 표시줄* 에는 하위 리소스의 실행 상태가 간단히 요약되어 있습니다. 구독 수준 또는 리소스 그룹 수준에서 표시됩니다. 각 색상의 의미는 다음과 같습니다.
    
    - 녹색: 검사에 성공했습니다.
    - 빨간색: 검사에 실패했습니다. 
    - 회색: 검사가 아직 진행 중입니다.
   
    각 검사를 선택하면 세부 정보를 볼 수 있습니다.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="검사 세부 정보를 보여주는 스크린샷.":::

1. 원본 세부 정보 맨 아래에 최근 실패한 검사 실행이 요약되어 표시됩니다. 이러한 실행에 대한 보다 자세한 세부 정보를 확인할 수도 있습니다.

## <a name="manage-your-scans-edit-delete-or-cancel"></a>검사 관리: 편집, 삭제 또는 취소

검사를 관리하려면 다음과 같이 합니다.

1. 관리 센터로 이동합니다.
1. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 원하는 데이터 원본을 선택합니다.
1. 관리할 검사를 선택합니다. 그렇다면 

   - **편집** 을 선택하여 검사를 편집할 수 있습니다.
   - **삭제** 를 선택하여 검사를 삭제할 수 있습니다.
   - 검사가 실행 중인 경우 **취소** 를 선택하여 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
