---
title: Azure Purview에서 여러 원본 검사
description: Azure Purview 데이터 카탈로그에서 전체 Azure 구독 또는 리소스 그룹을 검사하는 방법을 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: ce78b36a07a0648e869a2a382554fdc41b408f96
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656329"
---
# <a name="register-and-scan-multiple-sources-in-azure-purview"></a>Azure Purview에서 여러 원본 등록 및 검사

이 문서에서는 Azure Purview에서 여러 원본(Azure 구독 또는 리소스 그룹)을 등록하고 이러한 원본에 대한 검사를 설정하는 방법을 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

여러 원본을 검사하여 Azure Purview에서 지원되는 대부분의 Azure 리소스 종류에서 메타데이터 및 스키마를 캡처할 수 있습니다. Azure Purview에서는 시스템 및 사용자 지정 분류 규칙에 따라 데이터가 자동으로 분류됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 데이터 원본을 등록하기 전에 먼저 Azure Purview 계정을 만듭니다. 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다. 구독 또는 리소스 그룹에 역할을 추가하려면 소유자 또는 사용자 액세스 권한 관리자여야 합니다.
- 다음 섹션에 설명된 대로 인증을 설정합니다.

### <a name="set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>구독 또는 리소스 그룹에서 리소스를 열거하기 위한 인증 설정

1. Azure Portal에서 구독 또는 리소스 그룹으로 이동합니다.  
1. 왼쪽 메뉴에서  **액세스 제어(IAM)**  를 선택합니다. 
1. **+추가** 를 선택합니다. 
1. **입력 선택** 상자에서 **읽기 권한자** 역할을 선택하고 Azure Purview 계정 이름(MSI 파일 이름을 나타냄)을 입력합니다. 
1. **저장** 을 선택하여 역할 할당을 완료합니다.

### <a name="set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>구독 또는 리소스 그룹에서 리소스를 검사하기 위한 인증 설정

Azure에서는 다음 두 가지 방법으로 여러 원본에 대한 인증을 설정할 수 있습니다.

- 관리 ID
- 서비스 사용자

등록 및 검사하려는 구독 또는 리소스 그룹 내의 각 리소스에 대한 인증을 설정해야 합니다. Azure Storage 리소스 종류(Azure Blob Storage 및 Azure Data Lake Storage Gen2)을 사용하면 구독 또는 리소스 그룹 수준에서 MSI 파일 또는 서비스 주체를 스토리지 Blob 데이터 읽기 권한자로 손쉽게 추가할 수 있습니다. 그런 다음, 사용 권한이 해당 구독 또는 리소스 그룹 내의 각 스토리지 계정으로 전달됩니다. 다른 모든 리소스 종류의 경우, 각 리소스에 MSI 파일 또는 서비스 주체를 적용하거나 이를 위한 스크립트를 만들어야 합니다. 

구독 또는 리소스 그룹 내의 각 리소스 종류에 대한 권한을 추가하는 방법을 알아보려면 다음 문서를 참조하세요.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-multiple-sources"></a>여러 원본 등록

데이터 카탈로그에 새 여러 원본을 등록하려면 다음과 같이 합니다.

1. Azure Purview 계정으로 이동합니다.
1. 왼쪽 메뉴에서 **원본** 을 선택합니다.
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


## <a name="create-and-run-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. **원본** 섹션으로 이동합니다.
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
    
    각 자격 증명은 특정 종류의 모든 리소스에 대한 인증 방법으로 간주됩니다. [이 문서의 앞부분에서](#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) 설명된 것처럼 리소스를 성공적으로 검사하려면 선택한 자격 증명을 리소스에 설정해야 합니다.
1. 각 종류 내에서 모든 리소스를 검사하거나 이름별로 일부 리소스만 검사할 수 있습니다.
    - 옵션을 **모두** 로 두면 해당 종류의 향후 리소스도 향후 검사 실행 시 검사됩니다.
    - 특정 스토리지 계정 또는 SQL 데이터베이스를 선택할 경우, 나중에 검사를 명시적으로 편집하지 않는 한 이 구독 또는 리소스 그룹 내에 생성된 해당 종류의 향후 리소스가 검사에 포함되지 않습니다.
 
1. **계속** 을 선택하여 진행합니다. Azure Purview는 액세스 권한을 테스트하여 구독 또는 리소스 그룹의 읽기 권한자로 Azure Purview MSI 파일을 적용했는지 확인합니다. 오류 메시지가 표시되면 [다음 지침](#set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)에 따라 해결합니다.

1. 이전 단계에서 선택한 각 리소스 종류에 대한 검사 규칙 세트를 선택합니다. 검사 규칙 세트를 인라인으로 만들 수도 있습니다.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="각 리소스 종류의 검사 규칙을 보여주는 스크린샷.":::

1. 검사 트리거를 선택합니다. 매주, 매월 또는 한 번 실행되도록 예약할 수 있습니다.

1. 검사를 검토하고 **저장** 을 선택하여 설정을 완료합니다. 

## <a name="view-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. **원본** 섹션 아래 타일에서 **세부 정보 보기** 를 클릭하면 원본 세부 정보를 볼 수 있습니다. 

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

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)    
