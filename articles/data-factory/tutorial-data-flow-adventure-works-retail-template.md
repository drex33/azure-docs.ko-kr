---
title: 소매 산업 데이터 모델에 대한 ETL 템플릿
description: 이 자습서에서는 Adventure Works 샘플 데이터를 사용하여 소매업용 업계 데이터 모델 템플릿을 사용하는 단계를 제공합니다.
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 99f91c76addbacb64290c54435b5b3c760eabc5c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704791"
---
# <a name="adventureworks-template-documentation"></a>AdventureWorks 템플릿 설명서

이 문서에서는 Microsoft의 AdventureWorks 파이프라인 템플릿을 설정 및 사용하여 Azure Synapse Analytics 및 Retail 데이터베이스 템플릿을 사용하여 AdventureWorks 데이터 세트 탐색을 바로 시작하는 방법을 설명합니다.

## <a name="overview"></a>개요
AdventureWorks는 Microsoft 애플리케이션 데모에 사용되는 가상의 스포츠 장비 소매점입니다. 이 경우 Synapse Pipelines 사용하여 소매 데이터를 소매 데이터베이스 템플릿에 매핑하여 Azure Synapse 내에서 추가 분석을 하는 방법에 대한 예제로 사용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **작업 영역을 Azure Synapse.** [Azure Synapse 작업 영역이](../storage/common/storage-account-create.md) 없는 경우 만듭니다.

## <a name="find-the-template"></a>템플릿 찾기

다음 단계에 따라 템플릿을 찾습니다.

1. Synapse 작업 영역으로 이동합니다. 홈페이지에서 **학습을** 선택한 **다음, 갤러리 찾아보기를** 선택합니다. Synapse 갤러리가 열립니다. 데이터 세트, 스크립트, 파이프라인 등을 검색하여 작업 영역에 설치할 수 있습니다. 

1. **Pipelines** 선택하고 "AdventureWorks" 키워드로 결과를 필터링합니다.

1. **AdventureWorks 템플릿을** 선택한 다음, **계속을** 선택합니다.

이러한 단계를 수행하면 템플릿 개요 페이지가 열립니다.

## <a name="configure-the-template"></a>템플릿 구성
템플릿은 최소한의 구성이 필요하도록 설계되었습니다. 템플릿 개요 페이지에서 파이프라인의 초기 시작 구성 미리 보기를 확인하고 **파이프라인 열기를** 클릭하여 고유한 작업 영역에 리소스를 만들 수 있습니다. 템플릿의 31개 리소스가 모두 생성되었다는 알림을 받게 되며, 이를 커밋하거나 게시하기 전에 검토할 수 있습니다. 템플릿의 아래 구성 요소를 찾을 수 있습니다.

* 17개 파이프라인: 데이터가 대상 테이블에 올바르게 로드되도록 예약되고 원본 테이블당 하나의 파이프라인과 예약된 파이프라인을 포함합니다.
* 14개 데이터 흐름: 원본 시스템에서 로드하고 대상 데이터베이스에 데이터를 넣는 논리를 포함합니다.

AdventureWorks 데이터 세트를 다른 데이터베이스에 로드한 경우 해당 데이터 세트를 가리키도록 데이터 흐름 원본을 업데이트할 수 있습니다. 그렇지 않으면 아래 단계에 따라 템플릿에 정의된 스키마와 일치하는 원본 및 대상 DB를 만듭니다.


## <a name="dataset-and-sourcetarget-models"></a>데이터 세트 및 원본/대상 모델
Excel 형식의 AdventureWorks 데이터 세트는 이 GitHub [사이트에서](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip)다운로드할 수 있습니다. 또한 [원본 및 대상 데이터베이스 모두에 대한 스키마 정의에](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx)액세스할 수 있습니다. Synapse에서 데이터베이스 디자이너를 사용하여 이전에 다운로드한 Excel 스키마를 사용하여 원본 및 대상 데이터베이스를 다시 만듭니다. 데이터베이스 디자이너에 대한 자세한 내용은 이 [설명서를 참조하세요.](../synapse-analytics/database-designer/concepts-database-templates.md)

데이터베이스를 만든 후 작업 영역 DB 원본 및 싱크 설정의 드롭다운을 편집하여 데이터 흐름이 올바른 테이블을 가리키는지 확인합니다. 예제 데이터 세트에 제공된 CSV 파일을 테이블에 지정된 올바른 폴더에 배치하여 데이터를 원본 모델에 로드할 수 있습니다. 작업이 완료되면 파이프라인을 실행하기만 하면 됩니다.

## <a name="troubleshoot-the-pipelines"></a>파이프라인 문제 해결
파이프라인이 성공적으로 실행되지 않으면 오류를 확인해야 하는 몇 가지 주요 사항이 있습니다.

* 데이터 세트 스키마. CSV 파일에 대한 데이터 설정이 정확한지 확인합니다. 행 머리글을 포함하는 경우 데이터베이스 테이블에서 헤더 옵션이 선택되어 있는지 확인합니다.
* 데이터 흐름 원본. 예제 스키마에 제공된 것과 다른 열 또는 테이블 이름을 사용한 경우 데이터 흐름을 단계별로 진행하여 열이 올바르게 매핑되었는지 확인해야 합니다.
* 데이터 흐름 싱크. 대상 데이터베이스의 스키마 및 데이터 형식 구성은 데이터 흐름 템플릿과 일치해야 합니다. 위와 같이 변경 내용이 있는 경우 해당 항목을 정렬해야 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 흐름 매핑에](concepts-data-flow-overview.md)대해 자세히 알아봅니다.
* [파이프라인 템플릿에](solution-templates-introduction.md) 대해 자세히 알아보기