---
title: Azure Purview에서 데이터에 민감도 레이블을 자동으로 적용하는 방법
description: 민감도 레이블을 만들고 검사하는 동안 자동으로 데이터에 적용하는 방법을 알아봅니다.
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6bcb751c58b4ba14e84f529ec268beacc93fdcf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339564"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>Azure Purview에서 데이터에 민감도 레이블을 자동으로 적용하는 방법

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>기존 민감도 레이블을 만들거나 Azure Purview로 확장

> [!IMPORTANT]
> Azure Purview 민감도 레이블은 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.
>

민감도 레이블이 아직 없는 경우 해당 레이블을 만들어 Azure Purview에서 사용할 수 있도록 해야 합니다. 기존 민감도 레이블을 수정하여 Azure Purview에 사용하도록 할 수도 있습니다.

### <a name="step-1-licensing-requirements"></a>1단계: 라이선스 요구 사항

민감도 레이블은 Microsoft 365 준수 센터에서 만들고 관리합니다. Azure Purview에서 사용할 민감도 레이블을 만들려면 민감도 레이블을 자동으로 적용하는 이점을 제공하는 활성 Microsoft 365 라이선스가 있어야 합니다.

라이선스의 전체 목록은 [Azure Purview FAQ의 민감도 레이블을 참조하세요.](sensitivity-labels-frequently-asked-questions.yml) 필요한 라이선스가 아직 없는 경우 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) 평가판에 등록하면 됩니다.

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>2단계: Azure Purview에서 민감도 레이블 사용 동의

다음 단계에서는 민감도 레이블을 확장하고 Azure Purview에서 사용할 수 있도록 설정합니다. 여기서 파일 및 데이터베이스 열에 민감도 레이블을 적용할 수 있습니다.

1. Microsoft 365에서 **Information Protection** 페이지로 이동합니다.</br>
   최근에 Information Protection 구독을 프로비전한 경우 **Information Protection** 페이지가 표시되는 데 몇 시간이 걸릴 수 있습니다.
1. Azure **Purview의 자산에 레이블 지정 확장** 영역에서 **켜기** 단추를 선택한 다음, 표시되는 확인 대화 상자에서 **예를** 선택합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="'켜기' 단추를 선택하여 민감도 레이블을 Purview로 확장합니다." lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation-small.png" alt-text="민감도 레이블을 Purview로 확장하도록 선택 확인" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation.png":::

> [!TIP]
>단추가 표시되지 않고 Purview에서 레이블 지정을 자산으로 확장하기 위한 동의가 부여되었는지 확실하지 않은 경우 상태를 확인하는 방법에 대한 [FAQ](sensitivity-labels-frequently-asked-questions.yml#how-can-i-determine-if-consent-has-been-granted-to-extend-labeling-to-purview) 항목을 참조하세요.
>

Azure Purview에서 자산에 대한 레이블 지정을 확장한 후에는 게시된 모든 민감도 레이블을 Purview에서 사용할 수 있습니다.

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>3단계: 콘텐츠에 자동으로 레이블을 지정하도록 기존 레이블 만들기 또는 수정

**새 민감도 레이블을 만들거나 기존 레이블을 수정하려면 다음을 수행합니다.**

1. [Microsoft 365 규정 준수 센터](https://compliance.microsoft.com/)엽니다.

1. **솔루션** 에서 **Information Protection** 을 선택한 다음, **레이블 만들기** 를 선택합니다.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="Microsoft 365 규정 준수 센터 민감도 레이블 만들기" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. 레이블 이름을 지정합니다. 그런 다음, **이 레이블의 범위 정의** 에서 다음을 수행합니다.

    - 모든 경우에 **Azure Purview 자산** 을 선택합니다.
    - 파일에 레이블을 지정하려면 **파일 및 메일** 도 선택합니다. 이 옵션은 데이터베이스 자산에만 레이블을 지정하는 데 필요하지 않습니다.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="Microsoft 365 규정 준수 센터 자동으로 레이블 지정" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. 나머지 프롬프트에 따라 레이블 설정을 구성합니다.

    특히 파일 및 데이터베이스 열에 대한 자동 레이블 지정 규칙을 정의합니다.

    - [파일에 대한 자동 레이블 지정 규칙 정의](#autolabeling-for-files)
    - [데이터베이스 열에 대한 자동 레이블 지정 규칙 정의](#autolabeling-for-database-columns)

    구성 옵션에 대한 자세한 내용은 Microsoft 365 설명서에서 [민감도 레이블이 수행할 수](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) 있는 작업을 참조하세요.

1. 더 많은 레이블을 만들려면 위에 나온 단계를 반복합니다.

    하위 레이블을 만들려면 상위 레이블 > **...**  > **추가 작업** > **하위 레이블 추가** 을 선택합니다.

1. 기존 레이블을 수정하려면 **Information Protection** > , **레이블** 을 찾아서 레이블을 선택합니다.

    그런 다음 **레이블 편집을** 선택하여 레이블을 만들 때 정의한 모든 설정을 사용하여 **민감도 레이블 편집** 구성을 다시 엽니다.

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="기존 민감도 레이블 편집" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. 모든 레이블을 만든 후에는 레이블 순서를 확인하고 필요에 따라 순서를 다시 지정해야 합니다.

    레이블의 순서를 변경하려면 **...** **> 추가 작업** > **위로 이동** 또는 **아래로 이동을 선택합니다.**

    자세한 내용은 Microsoft 365 설명서에서 [레이블 우선 순위(순서 문제)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)를 참조하세요.

#### <a name="autolabeling-for-files"></a>파일의 자동 레이블 지정

레이블을 만들거나 편집할 때 파일에 대한 자동 레이블 지정 규칙을 정의합니다.

**Office 앱에 대한 자동 레이블 지정** 페이지에서 **Office 앱에 대한 자동 레이블 지정** 을 사용하도록 설정한 다음, 레이블을 데이터에 자동으로 적용할 조건을 정의합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="Microsoft 365 규정 준수 센터 파일에 대한 자동 레이블 지정 규칙 정의" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

자세한 내용은 Microsoft 365 설명서에서 [데이터에 자동으로 민감도 레이블 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)을 참조하세요.

#### <a name="autolabeling-for-database-columns"></a>데이터베이스 열에 대한 자동 레이블 지정

레이블을 만들거나 편집할 때 데이터베이스 열에 대한 자동 레이블 지정 규칙을 정의합니다.

데이터베이스 **열 옵션에서 다음을 수행합니다.**

1. **데이터베이스 열에 대 한 자동 레이블 지정** 슬라이더를 선택합니다.

1. **중요한 정보 유형 확인** 을 선택하여 레이블에 적용할 중요한 정보 유형을 선택합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="Microsoft 365 규정 준수 센터 SQL 열에 대한 자동 레이블 지정 규칙 정의" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>4단계: 레이블 게시

레이블을 만든 후에는 Azure Purview에서 데이터를 스캔하여 정의한 자동 레이블 지정 규칙에 따라 만든 레이블을 자동으로 적용해야 합니다.

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>데이터를 스캔하여 민감도 레이블 자동 적용

Azure Purview에서 데이터를 검사하여 정의한 자동 레이블 지정 규칙에 따라 만든 레이블을 자동으로 적용합니다. Purview에 민감도 레이블 변경 내용이 반영되려면 최대 24시간을 허용합니다.

Azure Purview에서 다양한 자산 검색을 설정하는 방법에 대한 자세한 내용은 다음을 참조하세요.

|원본  |참조  |
|---------|---------|
|**Storage 내의 파일** | [Azure Blob Storage 등록 및 검색](register-scan-azure-blob-storage-source.md) </br> Azure Data Lake Storage [Gen1 등록 및 검사 Azure Files 등록 및](register-scan-adls-gen1.md) [검사](register-scan-azure-files-storage-source.md) </br>[Azure Data Lake Storage Gen2 등록 및 검색](register-scan-adls-gen2.md)</br>[Amazon S3 등록 및 검사](register-scan-amazon-s3.md) |
|**데이터베이스 열** | [Azure SQL Database 등록 및 검사](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance 등록 및 검사](register-scan-azure-sql-database-managed-instance.md) </br> [전용 SQL 풀 등록 및 검사](register-scan-azure-synapse-analytics.md)</br> [Azure Synapse Analytics 작업 영역 등록 및 검사](register-scan-azure-synapse-analytics.md) </br> [Azure Cosmos Database(SQL API) 등록 및 검색](register-scan-azure-cosmos-database.md) </br> [Azure MySQL Database 등록 및 검사](register-scan-azure-mysql-database.md) </br> [Azure Database for PostgreSQL 등록 및 검색](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>카탈로그의 자산에 대한 레이블 보기

Microsoft 365 레이블에 대한 자동 레이블 지정 규칙을 정의하고 Azure Purview에서 데이터를 검사하면 레이블이 자산에 자동으로 적용됩니다.

**Azure Purview 카탈로그에서 자산에 적용된 레이블을 보려면 다음을 수행합니다.**

Azure Purview 카탈로그에서 **레이블** 필터링 옵션을 사용하여 특정 레이블만 있는 자산을 표시합니다. 예를 들면 다음과 같습니다.

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="레이블별 자산 검색" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

발견된 분류 및 적용된 레이블을 포함하여 자산의 세부 정보를 보려면 결과에서 자산을 클릭합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="Azure Blob Storage에 있는 파일의 민감도 레이블 보기" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>분류 및 민감도 레이블에 대한 인사이트 보고서 보기

Azure Purview에서 분류되고 레이블이 지정된 데이터에 대한 인사이트를 찾으면 **분류** 및 **민감도 레이블 지정** 보고서를 사용합니다.

> [!div class="nextstepaction"]
> [분류 인사이트](./classification-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 인사이트](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Azure Purview의 레이블 지정 개요](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [질문과 대답 레이블 지정](sensitivity-labels-frequently-asked-questions.yml)