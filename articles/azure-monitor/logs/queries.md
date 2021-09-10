---
title: Azure Monitor Log Analytics에서 쿼리 사용
description: 사용할 수 있는 다양한 유형의 쿼리 및 샘플 쿼리를 포함하는 Azure Monitor Log Analytics의 로그 쿼리 개요입니다.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 004196117512282ee960ed275dd970b472cde918
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662320"
---
# <a name="using-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics에서 쿼리 사용
Log Analytics를 열면 기존 로그 쿼리에 액세스할 수 있습니다. 이러한 쿼리를 수정 없이 사용하거나 또는 사용자 쿼리의 시작점으로 사용할 수도 있습니다. 사용 가능한 쿼리에는 Azure Monitor가 제공하는 예제와 조직에서 저장한 쿼리가 포함됩니다. 이 문서에서는 사용할 수 있는 쿼리와, 해당 쿼리를 검색하고 사용하는 방법을 설명합니다.


## <a name="queries-interface"></a>쿼리 인터페이스
Log Analytics의 서로 다른 두 위치에서 사용할 수 있는 쿼리 인터페이스에서 쿼리를 선택합니다.

### <a name="queries-dialog"></a>쿼리 대화 상자

Log Analytics를 열면 *쿼리* 대화 상자가 자동으로 표시됩니다. 이 대화 상자를 자동으로 표시하지 않으려면 **항상 쿼리 표시** 스위치를 끕니다.

![쿼리 화면](media/queries/query-start.png)


각 쿼리는 카드로 표시됩니다. 쿼리를 빠르게 검색하여 필요한 내용을 찾을 수 있습니다. 대화 상자에서 직접 쿼리를 실행하거나 수정을 위해 쿼리 편집기로 로드하도록 선택할 수 있습니다.

또한 **쿼리** 화면의 오른쪽 위를 클릭해 액세스할 수도 있습니다.

[![쿼리 단추](media/queries/queries-button.png)](media/queries/queries-button.png#lightbox)

### <a name="query-sidebar"></a>쿼리 사이드바

Log Analytics의 왼쪽 사이드바에 있는 쿼리 창에서 동일한 대화 상자 환경 기능에 액세스할 수 있습니다. 쿼리 이름을 마우스로 가리키면 쿼리 설명 및 추가 기능을 볼 수 있습니다.

[![쿼리 사이드바](media/queries/query-sidebar.png)](media/queries/query-sidebar.png#lightbox)

## <a name="finding-and-filtering-queries"></a>쿼리 찾기 및 필터링

이 섹션의 옵션은 대화 상자 및 사이드바 쿼리 환경에서 사용할 수 있지만, 사용자 인터페이스가 약간 다릅니다.  


### <a name="group-by"></a>Group By

*그룹화* 드롭다운 목록을 클릭하여 쿼리 그룹화를 변경합니다. 그룹화 값은 콘텐츠의 활성 테이블 역할도 합니다. 화면 왼쪽에 있는 값 중 하나를 클릭하면 쿼리 보기가 클릭된 항목의 오른쪽으로 스크롤됩니다. 조직에서 태그를 사용하여 쿼리 팩을 만든 경우 사용자 지정 태그는 이 목록에 포함됩니다.

[![예제 쿼리 화면 그룹화](media/queries/example-query-groupby.png)](media/queries/example-query-groupby.png#lightbox)



### <a name="filter"></a>Assert

또한 앞에서 언급한 **그룹화** 값에 따라 쿼리를 필터링할 수도 있습니다. 예제 쿼리 대화 상자에서 필터는 위쪽에 있습니다.

[![예제 쿼리 화면 필터](media/queries/example-query-filter.png)](media/queries/example-query-filter.png#lightbox)

### <a name="combining-group-by-and-filter"></a>그룹화 및 필터 조합

필터 및 그룹화 기능은 함께 작동하도록 설계되었습니다. 이는 쿼리가 정렬되는 방식에 유연성을 제공합니다. 예를 들어, 여러 리소스를 포함하는 리소스 그룹을 사용하는 경우 특정 리소스 종류로 필터링하고 결과 쿼리를 항목별로 정렬할 수 있습니다.

## <a name="query-properties"></a>쿼리 속성
각 쿼리에는 그룹화하고 찾는 데 도움이 되는 여러 속성이 있습니다. 이러한 속성은 정렬 및 필터링에 사용할 수 있으며, [사용자 고유의 쿼리를 저장](save-query.md)할 때 여러 속성을 정의할 수 있습니다. 속성의 형식은 다음과 같습니다.

- **리소스 종류** – 가상 머신과 같이 Azure에 정의된 리소스입니다. Azure Monitor Logs/Log Analytics 테이블을 리소스 유형에 대해 전체 매핑하려면 [Azure Monitor 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype) 를 참조하세요.  
- **범주** – *보안* 또는 *감사* 와 같은 정보의 유형입니다. 범주는 테이블 쪽 창에 정의된 범주와 동일합니다. 범주의 전체 목록은 [Azure Monitor 테이블 참조](/azure/azure-monitor/reference/tables/tables-category)를 참조하세요.  
- **솔루션** – 쿼리와 연결된 Azure Monitor 솔루션
- **토픽** – *활동 로그* 또는 *앱 로그* 와 같은 예제 쿼리의 토픽입니다. 토픽 속성은 예제 쿼리에 고유하며 특정 리소스 종류에 따라 다를 수 있습니다.
- - **쿼리 형식**  - 쿼리 형식을 정의합니다. 쿼리 형식은 예제 쿼리, 쿼리 팩 쿼리 또는 레거시 쿼리가 될 수 있습니다.
- **레이블** - [사용자 고유의 쿼리를 저장](save-query.md)할 때 정의하고 할당할 수 있는 사용자 지정 레이블입니다.
- **태그** - [쿼리 팩을 만들](query-packs.md) 때 정의할 수 있는 사용자 지정 속성입니다. 태그를 사용하면 조직에서 쿼리를 구성하기 위한 고유한 분류를 만들 수 있습니다.

## <a name="favorites"></a>즐겨찾기
자주 사용하는 쿼리를 즐겨찾기로 지정하여 더 빠르게 액세스할 수 있습니다. 쿼리 옆에 있는 별을 클릭하여 **즐겨찾기** 에 추가합니다. 쿼리 인터페이스의 **즐겨찾기** 옵션에서 즐겨찾는 쿼리를 봅니다.

## <a name="types-of-queries"></a>쿼리 유형
쿼리 인터페이스는 다음과 같은 유형의 쿼리로 채워집니다.

**예제 쿼리:** 예제 쿼리는 리소스에 대한 즉각적인 인사이트를 제공하고 KQL 학습 및 사용을 시작하는 좋은 방법을 제공하므로, Log Analytics 사용을 시작하는 데 걸리는 시간을 단축할 수 있습니다. 즉각적인 가치를 제공하도록 설계된 500개 이상의 예제 쿼리를 수집하고 큐레이팅했으며, 이러한 예제 쿼리 수는 지속적으로 증가하고 있습니다.

**쿼리 팩:** 이 [쿼리 팩](query-packs.md)은 직접 저장한 쿼리를 포함하여 로그 쿼리의 컬렉션을 보유합니다. 여기에는 [기본값 쿼리 팩](query-packs.md#default-query-pack) 및 조직에서 구독 상에 만들 수 있는 다른 쿼리 팩이 포함됩니다.

**레거시 쿼리:** 이전에 쿼리 탐색기 환경에 저장된 로그 쿼리 및 작업 영역에 설치된 Azure 솔루션의 쿼리입니다. 이러한 쿼리는 **레거시 쿼리** 아래의 쿼리 대화 상자에 나열됩니다.
>[!TIP]
> 레거시 Quereis는 Log Analytics 작업 영역에서만 사용할 수 있습니다.

## <a name="effect-of-query-scope"></a>쿼리 범위의 효과
Log Analytics를 열 때 사용할 수 있는 쿼리는 현재 [쿼리 범위](scope.md)에 의해 결정됩니다.

- **작업 영역** 의 경우 – 쿼리 팩의 모든 예제 쿼리 및 쿼리입니다. 작업 영역의 레거시 쿼리입니다.
- **단일 리소스** 의 경우 – 리소스 종류에 대한 쿼리 팩의 예제 쿼리 및 쿼리입니다. 
- **리소스 그룹** 의 경우 - 리소스 그룹의 리소스 종류에 대한 쿼리 팩의 예제 쿼리 및 쿼리입니다. 

> [!TIP]
> 범위에 포함된 리소스가 많을수록 포털에서 쿼리 대화 상자를 필터링하고 표시하는 데 걸리는 시간이 길어집니다.


## <a name="next-steps"></a>다음 단계

[KQL 쿼리 시작](get-started-queries.md)

