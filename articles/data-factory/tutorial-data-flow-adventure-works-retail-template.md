---
title: 소매 업계 데이터 모델용 ETL 템플릿
description: 이 자습서에서는 놀이 Works 샘플 데이터를 사용 하 여 소매에 업계 데이터 모델 템플릿을 사용 하는 단계를 제공 합니다.
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 888a6c78572940dd69ef2161fc1ded979259d63f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182356"
---
# <a name="adventureworks-template-documentation"></a>AdventureWorks 템플릿 설명서

이 문서에서는 Microsoft의 AdventureWorks 파이프라인 템플릿을 설정 하 고 사용 하 여 Azure Synapse Analytics 및 소매 데이터베이스 템플릿을 사용 하 여 AdventureWorks 데이터 집합 탐색을 시작 하는 방법을 설명 합니다.

## <a name="overview"></a>개요
AdventureWorks는 Microsoft 응용 프로그램을 시연 하는 데 사용 되는 가상의 스포츠 장비 대리점입니다. 이 경우 Synapse Pipelines를 사용 하 여 Azure Synapse 내에서 추가 분석을 위해 소매점 데이터를 소매점 데이터베이스 템플릿에 매핑하는 방법에 대 한 예제로 사용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse 작업 영역**. 아직 없는 경우 [Azure Synapse 작업 영역을 만듭니다](../storage/common/storage-account-create.md) .

## <a name="find-the-template"></a>템플릿 찾기

템플릿을 찾으려면 다음 단계를 수행 합니다.

1. Synapse 작업 영역으로 이동합니다. 홈 페이지에서 **배우기** 를 선택 하 고 **갤러리 찾아보기** 를 선택 합니다. Synapse 갤러리가 열립니다. 작업 영역에 설치 하기 위해 데이터 집합, 스크립트, 파이프라인 등을 검색할 수 있습니다. 

1. **Pipelines** 를 선택 하 고 "AdventureWorks" 라는 키워드를 사용 하 여 결과를 필터링 합니다.

1. **AdventureWorks** 템플릿을 선택 하 고 **계속** 을 선택 합니다.

이러한 단계는 템플릿 개요 페이지를 엽니다.

## <a name="configure-the-template"></a>템플릿 구성
템플릿은 최소한의 구성이 필요 하도록 디자인 되었습니다. 템플릿 개요 페이지에서 파이프라인의 초기 시작 구성에 대 한 미리 보기를 확인 하 고 **파이프라인 열기** 를 클릭 하 여 자체 작업 영역에서 리소스를 만들 수 있습니다. 템플릿의 31 개 리소스가 모두 생성 되었다는 알림이 표시 되며,이를 커밋하거나 게시 하기 전에 검토할 수 있습니다. 템플릿의 아래 구성 요소를 찾을 수 있습니다.

* 17 파이프라인: 데이터를 대상 테이블에 올바르게 로드 하 고, 원본 테이블당 하나의 파이프라인과 일정을 포함 하는 것을 확인 하도록 예약 됩니다.
* 14 개의 데이터 흐름: 원본 시스템에서 로드 하 고 대상 데이터베이스에 데이터를 전달 하는 논리를 포함 합니다.

AdventureWorks 데이터 집합이 다른 데이터베이스에 로드 된 경우 해당 데이터 집합을 가리키도록 데이터 흐름 소스를 업데이트할 수 있습니다. 그렇지 않으면 다음 단계를 수행 하 여 템플릿에 정의 된 스키마와 일치 하는 원본 및 대상 DB를 만듭니다.


## <a name="dataset-and-sourcetarget-models"></a>데이터 집합 및 원본/대상 모델
Excel 형식의 AdventureWorks 데이터 집합은이 [GitHub 사이트](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip)에서 다운로드할 수 있습니다. 또한 [원본 데이터베이스와 대상 데이터베이스에 대 한 스키마 정의](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx)에 액세스할 수 있습니다. Synapse의 데이터베이스 디자이너를 사용 하 여 이전에 다운로드 한 Excel 스키마를 사용 하 여 원본 및 대상 데이터베이스를 다시 만듭니다. 데이터베이스 디자이너에 대 한 자세한 내용은이 [설명서](https://aka.ms/SynapseDatabaseDesignerDocumentation)를 참조 하세요.

데이터베이스를 만든 후에는 작업 영역 DB 원본 및 싱크 설정에서 드롭다운을 편집 하 여 데이터 흐름가 올바른 테이블을 가리키는지 확인 합니다. 예제 데이터 집합에 제공 된 CSV 파일을 테이블에 지정 된 올바른 폴더에 배치 하 여 원본 모델에 데이터를 로드할 수 있습니다. 이 작업을 완료 한 후에는 파이프라인을 실행 하기만 하면 됩니다.

## <a name="troubleshoot-the-pipelines"></a>파이프라인 문제 해결
파이프라인이 성공적으로 실행 되지 않으면 오류를 확인 하는 몇 가지 주요 작업이 있습니다.

* 데이터 집합 스키마. CSV 파일에 대 한 데이터 설정이 정확한 지 확인 합니다. 행 머리글을 포함 한 경우 데이터베이스 테이블에서 헤더 옵션을 선택 했는지 확인 합니다.
* 데이터 흐름 원본. 예제 스키마에 제공 된 것과 다른 열 또는 테이블 이름을 사용한 경우에는 데이터 흐름을 단계별로 실행 하 여 열이 올바르게 매핑되는지 확인 해야 합니다.
* 데이터 흐름 싱크입니다. 대상 데이터베이스의 스키마 및 데이터 형식 구성은 데이터 흐름 템플릿과 일치 해야 합니다. 위와 같이 변경 내용을 적용 한 경우 해당 항목을 정렬 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 흐름 매핑](concepts-data-flow-overview.md)에 대해 자세히 알아보세요.
* [파이프라인 템플릿에](solution-templates-introduction.md) 대 한 자세한 정보
