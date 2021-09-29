---
title: Purview Insights를 사용하는 Azure Purview의 보유 데이터 관련 파일 확장명 보고
description: 본 방법 가이드에서는 보유 데이터 관련 Purview 파일 확장명 보고를 확인하고 사용하는 방법을 설명합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 706c27603233369836368239645cf28222da8eaa
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208544"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure Purview의 보유 데이터 관련 파일 확장명 인사이트 

본 방법 가이드에서는 보유 데이터에서 찾은 파일 확장명이나 파일 형식에 대한 인사이트에 액세스, 확인 및 필터링하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Purview Insights 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

지원하는 데이터 원본에는 Azure Blob Storage, ADLS(Azure Data Lake Storage) GEN 1, ADLS(Azure Data Lake Storage) GEN 2, Amazon S3 버킷이 포함됩니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.
> [!div class="checklist"]
> * Azure에서의 Purview 계정 시작하기 
> - 보유 데이터 관련 파일 확장명 인사이트 확인하기
> - 보유 데이터 관련 파일 확장명에 대한 추가적인 세부 정보를 드릴다운하기

## <a name="prerequisites"></a>필수 구성 요소

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

- Azure 리소스 설정 및 테스트 데이터를 통해 관련 계정 채우기

- 데이터 원본별로 테스트 데이터에 대한 스캔 설정 및 완료 자세한 내용은 [Azure Purview에서 데이터 원본 관리](manage-data-sources.md) 및 검사 규칙 집합 [만들기를 참조하세요.](create-a-scan-rule-set.md)

- [데이터 판독기 또는 데이터 큐레이터 역할](catalog-permissions.md#roles)이 있는 계정으로 Purview에 로그인했습니다.


자세한 내용은 [Azure Purview에서 데이터 원본 관리를 참조하세요.](manage-data-sources.md)

## <a name="use-purview-file-extension-insights"></a>Purview 파일 확장명 인사이트 사용하기

자산을 스캔할 때, Azure Purview는 데이터 자산에서 찾은 파일 형식을 검색하여 파일 형식별로 추가적인 세부 정보를 제공할 수 있습니다. 세부 정보에는 보유 중인 파일이 형식별로 몇 개씩인지, 해당 파일들이 어디 있는지와 중요한 데이터에 대해 스캔할 수 있는지가 포함됩니다.

> [!NOTE]
> 원본 형식을 스캔한 뒤, **파일 확장명** 인사이트가 새로운 자산을 반영할 수 있도록 몇 시간 정도 기다립니다.

**파일 이름 확장명 인사이트 확인 방법:**

1. [Azure Portal](https://aka.ms/purviewportal)의 **Azure Purview** 인스턴스 화면으로 이동해 Purview 계정을 선택합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Purview 계정 시작** 타일을 선택합니다.

1. Purview에서 왼쪽의 **인사이트** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택하여 **인사이트** 영역에 액세스합니다.
    
1. **인사이트** 에서 **파일 확장명** 탭을 선택합니다.

    보고서에는 선택한 기간 (기본값은 30일) 동안 고유한 파일 확장명이 몇 개가 발견되었는지에 대한 요약과 발견된 확장명 상위 10개의 그래프가 표시됩니다.

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="파일 확장명 보고서 - 개요" lightbox="media/file-extension-insights/file-extension-overview.png":::

    자세한 내용을 확인하려면 다음 중 한 가지를 수행합니다.

    - 보고서 맨 위에서 **시간** 선택기를 선택하여 해당 파일 확장명을 찾은 시간 범위를 변경합니다.
    
    - 그래프 아래의 **자세히 보기** 를 선택하여 발견한 파일 확장명의 전체 목록을 확인합니다. 자세한 내용은 [파일 확장명 인사이트 드릴다운](#file-extension-insights-drilldown)을 참조하세요. 

### <a name="file-extension-insights-drilldown"></a>파일 확장명 인사이트 드릴다운

데이터 자산에서 찾은 파일 형식과 관련해 중요한 정보를 확인하였으면, 해당 정보의 위치 및 중요 데이터 검색 가능성 여부에 대한 추가적인 세부 정보를 드릴다운합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="파일 확장명 보고서 - 드릴다운" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

표에는 발견한 파일 확장명별로 다음을 포함하는 세부 정보를 표시합니다.

- **파일 수**. 지정된 확장명을 사용하는 파일의 수입니다.
- **콘텐츠 검색**. 해당 파일 확장명이 중요 데이터 검색의 지원 대상인지 여부입니다.
- **구독**. 지정된 확장명을 찾은 구독 수입니다.
- **원본**. 지정된 파일 확장명으로 찾은 원본 수입니다.



표 위의 필터를 사용해 다음에 표시되는 데이터를 필터링합니다.

|옵션  |Description  |
|---------|---------|
|**키워드로 필터링**     |    **키워드로 필터링** 상자에 텍스트를 입력하여 이름별로 파일 형식 필터링을 확인합니다. 예를 들어, PDF만 보려면 `PDF`라고 입력합니다.     |
|**Time**        | 데이터가 생성된 특정 시간 범위를 기준으로 필터링하려면 선택합니다. <br>**기본값:** 30일  |
|**파일 확장명**     |표를 하나 이상의 파일 형식으로 필터링하려면 선택합니다.        |
|**Sources**    |표를 특정 데이터 원본으로 필터링하려면 선택합니다. |
|**콘텐츠 검색**     |중요한 데이터에 대해 추가적으로 검색할 수 있는 파일 형식이나 **.cert** 또는 **.jpg** 파일처럼 검색할 수 없는 데이터만 표시하려면 **지원됨** 또는 **지원되지 않음** 을 선택합니다. |
| | |

필터 위에서 **열 편집** :::image type="icon" source="media/insights/ico-columns.png" border="false":::을 선택하여 표에 표시되는 열의 개수나 순서를 변경합니다. 

표를 정렬하려면 기준으로 삼을 열의 헤더를 선택합니다.
## <a name="next-steps"></a>다음 단계

Azure Purview 인사이트 보고서에 대해 자세히 알아보기
> [!div class="nextstepaction"]
> [용어집 인사이트](glossary-insights.md)

> [!div class="nextstepaction"]
> [인사이트 검사](scan-insights.md)

> [!div class="nextstepaction"]
> [분류 인사이트](./classification-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 인사이트](sensitivity-insights.md)
