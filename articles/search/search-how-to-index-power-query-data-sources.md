---
title: 파워 쿼리 커넥터를 사용한 데이터 인덱싱(미리 보기)
titleSuffix: Azure Cognitive Search
description: 파워 쿼리 커넥터를 사용하여 다양한 데이터 원본에서 데이터를 가져옵니다.
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: fb7a16824ccb74ad2cab7c0175532d297ebaf88c
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451208"
---
# <a name="index-data-using-power-query-connectors-preview"></a>파워 쿼리 커넥터를 사용한 데이터 인덱싱(미리 보기)

> [!IMPORTANT] 
> 파워 쿼리 커넥터 지원은 현재 **제어된 퍼블릭 미리 보기** 로 제공됩니다. 액세스를 요청하려면 [가입](https://aka.ms/azure-cognitive-search/indexer-preview)하세요.

인덱싱을 위해 외부 데이터 원본을 크롤링하는 데 인덱서를 사용 중인 경우 이제 Azure Cognitive Search에서 데이터 원본을 연결하는 데 [파워 쿼리](/power-query/power-query-what-is-power-query) 커넥터를 사용할 수 있습니다.

파워 쿼리 커넥터는 다른 클라우드 공급자의 데이터 원본을 포함하여 광범위한 데이터 원본에 도달할 수 있습니다. 이 미리 보기에서 지원되는 새 데이터 원본은 다음과 같습니다.

+ Amazon Redshift
+ Elasticsearch
+ PostgreSQL
+ Salesforce 개체
+ Salesforce 보고서
+ Smartsheet
+ Snowflake

이 문서에서는 파워 쿼리 커넥터를 사용하여 인덱서를 설정하는 Azure Portal 기반 접근 방식을 보여 줍니다. 현재 SDK는 지원되지 않습니다.

> [!NOTE]
> 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="supported-functionality"></a>지원되는 기능
파워 쿼리 커넥터는 인덱서에 사용됩니다. Azure Cognitive Search의 인덱서는 외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. 이 접근 방식은 인덱스에 데이터를 추가하는 코드를 작성하지 않고도 서비스에서 데이터를 가져오기 때문에 ‘끌어오기 모델’로도 불립니다. 인덱서를 사용하면 사용자가 자신의 크롤러 또는 푸시 모델을 작성하지 않고도 데이터 원본에서 콘텐츠를 편리하게 인덱싱할 수 있습니다.

파워 쿼리 데이터 원본을 참조하는 인덱서는 다른 인덱서가 지원하는 기술 세트, 일정, 상위 워터마크 변경 검색 논리 및 대부분의 매개 변수를 동일한 수준으로 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항
지원되는 데이터 원본 중 하나에서 데이터를 끌어오기 전에 모든 리소스에 대한 설정을 완료해야 합니다.
+ Azure Cognitive Search 서비스
    + Azure Cognitive Search 서비스는 [지원되는 지역](search-how-to-index-power-query-data-sources.md#regional-availability)에 설정되어 있습니다.
    + Azure Cognitive Search 팀이 검색 서비스를 미리 보기에 사용하도록 설정했는지 확인합니다. [이 양식](https://aka.ms/azure-cognitive-search/indexer-preview)을 작성하여 미리 보기에 가입할 수 있습니다. 
+ Azure Blob Storage 계정
    + 미리 보기를 데이터의 중개자로 사용하려면 Blob Storage 계정이 필요합니다. 데이터는 데이터 원본, Blob Storage, 인덱스로 차례로 전달됩니다. 이 요구 사항은 초기의 제어된 미리 보기에만 있습니다.

## <a name="getting-started-using-the-azure-portal"></a>Azure Portal을 사용하여 시작하기
Azure Portal은 파워 쿼리 커넥터에 대한 지원을 제공합니다. Azure Cognitive Search의 데이터 가져오기 마법사는 데이터를 샘플링하고 컨테이너에서 메타데이터를 읽음으로써 기본 인덱스를 만들고, 원본 필드를 대상 인덱스 필드에 매핑하고, 인덱스를 로드하는 과정을 단일 작업으로 수행할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

 다음 비디오에서는 Azure Cognitive Search에서 파워 쿼리 커넥터를 설정하는 방법을 보여 줍니다.
 
> [!VIDEO https://www.youtube.com/embed/uy-l4xFX1EE]

### <a name="step-1--prepare-source-data"></a>1단계 - 원본 데이터 준비
데이터 원본에 데이터가 포함되어 있는지 확인합니다. 데이터 가져오기 마법사는 메타데이터를 읽고 데이터 샘플링을 수행하여 인덱스 스키마를 유추하지만 데이터 원본에서도 데이터를 로드합니다. 데이터가 없으면 마법사가 중지되고 오류를 반환합니다. 

### <a name="step-2--start-import-data-wizard"></a>2단계 - 데이터 가져오기 마법사 시작
미리 보기가 승인되면 Azure Cognitive Search 팀에서 기능 플래그를 사용하는 Azure Portal 링크를 제공하며 사용자는 이 링크를 사용하여 파워 쿼리 커넥터에 액세스할 수 있습니다. 이 페이지를 열고 **데이터 가져오기** 를 선택하여 Azure Cognitive Search 서비스 페이지의 명령 모음에서 마법사를 시작합니다.

:::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="데이터 가져오기 명령의 스크린샷" border="true":::

### <a name="step-3--select-your-data-source"></a>3단계 - 데이터 원본 선택
이 미리 보기를 사용하여 데이터를 끌어올 수 있는 출처로 몇 가지 데이터 원본이 있습니다. 파워 쿼리를 사용하는 모든 데이터 원본은 타일에 “파워 쿼리에서 제공”이라는 문구가 포함되어 있습니다. 데이터 원본을 선택합니다. 
 
![데이터 원본 선택](./media/search-power-query-connectors/power-query-import-data.png "데이터 원본 선택")

데이터 원본을 선택한 후 **다음: 데이터 구성** 을 선택하여 다음 섹션으로 이동합니다.

### <a name="step-4--configure-your-data"></a>4단계 - 데이터 구성
데이터 원본을 선택한 후에는 연결을 구성합니다. 데이터 원본별로 필요한 정보가 서로 다릅니다. 일부 데이터 원본의 데이터 연결 방법에 대해서는 파워 쿼리 설명서에서 추가 상세 정보를 제공합니다. 

+ [PostgreSQL](/power-query/connectors/postgresql)
+ [Salesforce 개체](/power-query/connectors/salesforceobjects)
+ [Salesforce 보고서](/power-query/connectors/salesforcereports)

연결 자격 증명을 입력한 후 **다음** 을 선택합니다.

### <a name="step-5--select-your-data"></a>5단계 - 데이터 선택
가져오기 마법사가 데이터 원본에서 사용할 수 있는 다양한 테이블을 미리 보여 줍니다. 이 단계에서는 인덱스로 가져올 데이터가 포함된 테이블 1개를 확인합니다.
 
![데이터 미리 보기](./media/search-power-query-connectors/power-query-preview-data.png "데이터 미리 보기")

테이블을 선택한 후 **다음** 을 선택합니다.

### <a name="step-6--transform-your-data-optional"></a>6단계 – 데이터 변환(선택 사항)
파워 쿼리 커넥터는 인덱스에 올바른 데이터가 전송되도록 데이터를 조작할 수 있는 풍부한 UI 환경을 제공합니다. 열 제거, 행 필터링 등 다양한 작업을 수행할 수 있습니다. 

데이터를 변환한 후에 Azure Cognitive Search로 가져와야 하는 것은 아닙니다.

![데이터 변환](./media/search-power-query-connectors/power-query-transform-your-data.png "데이터 변환") 

파워 쿼리를 사용한 데이터 변환에 대해 자세히 알아보려면 [Power BI Desktop에서 파워 쿼리 사용](/power-query/power-query-quickstart-using-power-bi)을 참조하세요. 

데이터 변환을 완료한 후 **다음** 을 선택합니다.

### <a name="step-7--add-azure-blob-storage"></a>7단계 – Azure Blob Storage 추가
현재 파워 쿼리 커넥터 미리 보기를 사용하려면 Blob Storage 계정을 제공해야 합니다. 이 단계는 초기의 제어된 미리 보기에만 있습니다. 이 Blob Storage 계정은 데이터 원본에서 Azure Cognitive Search 인덱스로 이동하는 데이터의 임시 스토리지 역할을 합니다.

전체 액세스 스토리지 계정 연결 문자열을 제공하는 것이 좋습니다. 
```
{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }
```

Azure Portal에서 스토리지 계정 블레이드 &gt; 설정 &gt; 키(클래식 스토리지 계정) 또는 설정 &gt; 액세스 키(Azure Resource Manager 스토리지 계정)로 이동하여 연결 문자열을 가져올 수 있습니다.

데이터 원본 이름 및 연결 문자열을 제공한 후 “다음: 인식 기술 추가(선택 사항)”를 선택합니다. 

### <a name="step-8--add-cognitive-skills-optional"></a>8단계 - 인지 기술 추가(선택 사항)
[AI 보강](cognitive-search-concept-intro.md)은 콘텐츠의 검색 가능성을 높일 수 있도록 인덱서를 확장한 것입니다.

이는 이 미리 보기에 대해 선택 사항으로 수행하는 단계입니다. 완료되면 **다음: 대상 인덱스 사용자 지정** 을 선택합니다.

### <a name="step-9--customize-target-index"></a>9단계 - 대상 인덱스 사용자 지정
인덱스 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 메타데이터에 따라, 원본 데이터를 샘플링하여 필드 목록을 생성할 수 있습니다.

특성 열의 맨 위에 있는 확인란을 클릭하여 특성을 일괄 선택할 수 있습니다. 클라이언트 앱에 반환되어야 하고 전체 텍스트 검색 처리를 적용받아야 하는 모든 필드에 대해 조회 가능 및 검색 가능을 선택합니다. 정수는 전체 텍스트 또는 유사 항목 검색이 불가능하다는 것을 알 수 있습니다(숫자는 그대로 평가되며 필터에 유용한 경우가 많습니다).

자세한 내용은 인덱스 특성 및 언어 분석기에 대한 설명을 검토하세요.

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 해당 필드의 속성 대부분을 편집할 수 없습니다.

![인덱스 만들기](./media/search-power-query-connectors/power-query-index.png "인덱스 만들기")

완료되면 **다음: 인덱서 만들기** 를 선택합니다.

### <a name="step-10--create-an-indexer"></a>10단계 - 인덱서 만들기
마지막 단계에서는 인덱서를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

데이터 가져오기 마법사를 실행하면 데이터 원본을 크롤링하고 선택한 데이터를 Azure Cognitive Search의 인덱스로 가져오는 인덱서가 제공됩니다.

인덱서를 만들 때 필요에 따라 인덱서가 예약된 시간에 실행되도록 선택하고 변경 검색을 추가할 수 있습니다. 변경 검색을 추가하려면 ‘상위 워터마크’ 열을 지정합니다.

![인덱서 만들기](./media/search-power-query-connectors/power-query-indexer-configuration.png "인덱서 만들기")

페이지 작성을 마쳤으면 **제출** 을 선택합니다.

## <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책
이 변경 검색 정책은 행이 마지막으로 업데이트된 버전 또는 시간을 캡처하는 “상위 워터 마크” 열을 사용합니다.

### <a name="requirements"></a>요구 사항
+ 모든 삽입 시 열의 값을 지정합니다.
+ 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
+ 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.

## <a name="unsupported-column-names"></a>지원되지 않는 열 이름
Azure Cognitive Search 인덱스의 필드 이름은 특정 요구 사항을 충족해야 합니다. 요구 사항 중 하나는 “/”와 같은 일부 문자는 허용되지 않는다는 것입니다. 데이터베이스의 열 이름이 해당 요구 사항을 충족하지 않는 경우 인덱스 스키마 검색에서 해당 열을 유효한 필드 이름으로 인식하지 못하며 해당 열이 인덱스의 제안되는 필드 목록에 표시되지 않습니다. 일반적으로 이 문제는 [필드 매핑](search-indexer-field-mappings.md)을 통해 해결되지만 포털에서는 필드 매핑이 지원되지 않습니다.

지원되지 않는 필드 이름이 있는 테이블의 열에서 콘텐츠를 인덱싱하려면 데이터 가져오기 프로세스의 “데이터 변환” 단계에서 열의 이름을 바꿉니다. 예를 들어 “청구 코드/우편 번호”라는 열의 이름을 “우편 번호”로 바꿀 수 있습니다. 열 이름을 바꾸면 인덱스 스키마 검색에서 열 이름을 유효한 필드 이름으로 인식하고 인덱스 정의에 제안 사항으로 추가합니다.

## <a name="regional-availability"></a>국가별 가용성
미리 보기는 다음 지역에서 검색 서비스를 사용하는 고객에게만 제공됩니다.
+ 미국 중부
+ 미국 동부
+ 미국 동부 2
+ 미국 중북부
+ 북유럽
+ 미국 중남부
+ 미국 중서부
+ 서유럽
+ 미국 서부
+ 미국 서부 2

## <a name="preview-limitations"></a>미리 보기 제한 사항
미리 보기에는 훌륭한 점이 많지만 몇 가지 제한 사항도 있습니다. 이 섹션에서는 현재 미리 보기 버전과 관련한 제한 사항에 대해 설명합니다.
+ 이 미리 보기 버전에서는 데이터 원본에서 이진 데이터를 끌어오는 기능이 지원되지 않습니다. 
+ 현재 [디버그 세션](cognitive-search-debug-session.md)이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
파워 쿼리 커넥터를 사용하여 새 데이터 원본에서 데이터를 끌어오는 방법을 알아보았습니다. 인덱서에 대한 자세한 내용은 [Azure Cognitive Search의 인덱서](search-indexer-overview.md)를 참조하세요.