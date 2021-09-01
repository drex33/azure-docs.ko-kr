---
title: 데이터 흐름 매핑
description: Azure Data Factory의 매핑 데이터 흐름 개요
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/26/2021
ms.openlocfilehash: 1d4e8ad4fd7d0b5690f506e9db7f1043c8f21045
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039094"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory의 매핑 데이터 흐름

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>데이터 흐름 매핑이란?

매핑 데이터 흐름은 Azure Data Factory에서 시각적으로 디자인된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성하지 않고도 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 스케일 아웃 Apache Spark 클러스터를 사용하는 Azure Data Factory 파이프라인 내에서 작업으로 실행됩니다. 데이터 흐름 작업은 기존 Azure Data Factory 일정, 제어, 흐름, 모니터링 기능을 사용해 운용할 수 있습니다.

매핑 데이터 흐름은 코딩 없이도 완전히 시각적인 환경을 제공합니다. 데이터 흐름은 스케일 아웃 데이터 처리를 위해 ADF가 관리하는 실행 클러스터에서 실행됩니다. Azure Data Factory는 모든 코드 변환, 경로 최적화, 데이터 흐름 작업의 실행을 처리합니다.

## <a name="getting-started"></a>시작

데이터 흐름은 파이프라인 및 데이터 세트 같은 팩터리 리소스 창에서 만들어집니다. 데이터 흐름을 만들려면 **팩터리 리소스** 옆에 있는 더하기 기호를 선택한 다음, **데이터 흐름** 을 선택합니다. 

![새 데이터 흐름](media/data-flow/new-data-flow.png)

이 작업을 수행하면 변환 논리를 만들 수 있는 데이터 흐름 캔버스로 이동합니다. **원본 추가** 를 선택하여 원본 변환 구성을 시작합니다. 자세한 내용은 [원본 변환](data-flow-source.md)을 참조하세요.

## <a name="authoring-data-flows"></a>작성 데이터 흐름

매핑 데이터 흐름에는 변환 논리를 쉽게 빌드할 수 있도록 디자인된 고유한 제작 캔버스가 있습니다. 데이터 흐름 캔버스는 위쪽 막대, 그래프, 구성 패널의 세 부분으로 구분됩니다. 

![위쪽 막대, 그래프, 구성 패널이 있는 데이터 흐름 캔버스를 보여 주는 스크린샷](media/data-flow/canvas-1.png "캔버스")

### <a name="graph"></a>그래프

그래프는 변환 스트림을 표시합니다. 원본 데이터가 하나 이상의 싱크로 이동함에 따라 원본 데이터의 계보를 보여줍니다. 새 원본을 추가하려면 **원본 추가** 를 선택합니다. 새 변환을 추가하려면 기존 변환의 오른쪽 아래에 있는 더하기 기호를 선택합니다. [데이터 흐름 그래프를 관리](concepts-data-flow-manage-graph.md)하는 방법에 관해 자세히 알아봅니다.

![검색 텍스트 상자가 있는 캔버스의 그래프 부분을 보여주는 스크린샷입니다.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>구성 패널

구성 패널에는 현재 선택된 변환과 관련된 설정이 표시됩니다. 선택된 변환이 없는 경우 데이터 흐름이 표시됩니다. 전체 데이터 흐름 구성에서는 **매개 변수** 탭을 통해 매개 변수를 추가할 수 있습니다. 자세한 내용은 [매핑 데이터 흐름 매개 변수](parameters-data-flow.md)를 참조하세요.

각 변환에는 네 개 이상의 구성 탭이 포함됩니다.

#### <a name="transformation-settings"></a>변환 설정

각 변환의 구성 창에 있는 첫 번째 탭에는 해당 변환과 관련된 설정이 포함됩니다. 자세한 내용은 해당 변형의 설명서 페이지를 참조하세요.

![원본 설정 탭](media/data-flow/source1.png "원본 설정 탭")

#### <a name="optimize"></a>최적화

**최적화** 탭에는 파티션 구성표를 구성하기 위한 설정이 포함됩니다. 데이터 흐름을 최적화하는 방법에 관해 자세히 알아보려면 [매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)를 참조하세요.

![파티션 옵션, 파티션 유형 및 파티션 수로 구성된 최적화 탭을 보여 주는 스크린샷](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>검사

**검사** 탭에서는 변환 중인 데이터 스트림의 메타데이터를 볼 수 있습니다. 열 개수, 변경된 열, 추가된 열, 데이터 형식, 열 순서 및 열 참조를 볼 수 있습니다. **검사** 는 메타데이터의 읽기 전용 보기입니다. **검사** 창에서 메타데이터를 보기 위해 디버그 모드를 사용하도록 설정하지 않아도 됩니다.

![검사](media/data-flow/inspect1.png &quot;검사")

변환을 통해 데이터의 셰이프를 변경하면 **검사** 창에서 메타데이터가 흐름을 변경하는 것을 확인할 수 있습니다. 원본 변환에 정의된 스키마가 없으면 메타데이터가 **검사** 창에 표시되지 않습니다. 스키마 드리프트 시나리오에서는 메타데이터가 없는 것이 일반적입니다.

#### <a name="data-preview"></a>데이터 미리 보기

디버그 모드가 켜지면 **데이터 미리 보기** 탭에서는 각 변환 시 데이터의 대화형 스냅샷을 제공합니다. 자세한 내용은 [디버그 모드의 데이터 미리 보기](concepts-data-flow-debug-mode.md#data-preview)를 참조하세요.

### <a name="top-bar"></a>위쪽 막대

위쪽 막대에는 저장 및 유효성 검사와 같이 전체 데이터 흐름에 영향을 주는 작업이 포함됩니다. 변환 논리의 기본 JSON 코드 및 데이터 흐름 스크립트도 볼 수 있습니다. 자세한 내용은 [데이터 흐름 스크립트](data-flow-script.md)에 관해 알아보세요.

## <a name="available-transformations"></a>사용 가능한 변환

[매핑 데이터 흐름 변환 개요](data-flow-transformation-overview.md)를 확인하여 사용 가능한 변환 목록을 가져옵니다.

## <a name="data-flow-data-types"></a>데이터 흐름 데이터 형식

* array
* binary
* boolean
* complex
* decimal(정밀도 포함)
* date
* float
* 정수
* long
* map
* short
* 문자열
* timestamp

## <a name="data-flow-activity"></a>데이터 흐름 작업

매핑 데이터 흐름은 [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)을 사용하여 ADF 파이프라인 내에서 조작 가능합니다. 사용자는 매개 변수 값을 사용하고 전달할 통합 런타임만 지정하면 됩니다. 자세한 내용은 [Azure 통합 런타임](concepts-integration-runtime.md#azure-integration-runtime)을 참조하세요.

## <a name="debug-mode"></a>디버그 모드

디버그 모드에서는 데이터 흐름을 빌드하고 디버그하는 동안 각 변환 단계의 결과를 대화형으로 볼 수 있습니다. 디버그 세션은 데이터 흐름 논리를 빌드할 경우와 데이터 흐름 작업을 사용하여 파이프라인 디버그 실행을 실행하는 경우에 둘 다 사용할 수 있습니다. 자세히 알아보려면 [디버그 모드 설명서](concepts-data-flow-debug-mode.md)를 참조하세요.

## <a name="monitoring-data-flows"></a>데이터 흐름 모니터링

매핑 데이터 흐름은 기존 Azure Data Factory 모니터링 기능과 통합됩니다. 데이터 흐름 모니터링 출력을 이해하는 방법을 알아보려면 [매핑 데이터 흐름 모니터링](concepts-data-flow-monitoring.md)을 참조하세요.

Azure Data Factory 팀은 비즈니스 논리를 빌드한 후 데이터 흐름의 실행 시간을 최적화하는 데 도움이 되는 [성능 조정 가이드](concepts-data-flow-performance.md)를 만들었습니다.


## <a name="available-regions"></a>사용 가능한 지역

매핑 데이터 흐름은 ADF의 다음 지역에서 사용할 수 있습니다.

| Azure 지역 | ADF의 데이터 흐름 |
| ------------ | ----------------- |
| 오스트레일리아 중부 | |
| 오스트레일리아 중부 2 | |
| 오스트레일리아 동부 | ✓ |
| 오스트레일리아 남동부   | ✓ |
| 브라질 남부  | ✓ |
| 캐나다 중부 | ✓ |
| 인도 중부 | ✓ |
| 미국 중부    | ✓ |
| 중국 동부 |      |
| 중국 동부 2  |   |
| 중국(비지역) | |
| 중국 북부 | ✓ |
| 중국 북부 2 | ✓ |
| 동아시아 | ✓ |
| 미국 동부   | ✓ |
| 미국 동부 2 | ✓ |
| 프랑스 중부 | ✓ |
| 프랑스 남부  | |
| 독일 중부(소버린) | |
| 독일(비지역)(소버린) | |
| 독일 북부(퍼블릭) | |
| 독일 북동부(소버린) | |
| 독일 중서부(퍼블릭) |  |
| 일본 동부 | ✓ |
| 일본 서부 |  |
| 한국 중부 | ✓ |
| 한국 남부 | |
| 미국 중북부  | ✓ |
| 북유럽  | ✓ |
| 노르웨이 동부 | ✓ |
| 노르웨이 서부 | |
| 남아프리카 북부    | ✓ |
| 남아프리카 공화국 서부 |  |
| 미국 중남부  | |
| 인도 남부 | |
| 동남 아시아    | ✓ |
| 스위스 북부 | ✓ |
| 스위스 서부 | |
| 아랍에미리트 중부 | |
| 아랍에미리트 북부 | ✓ |
| 영국 남부  | ✓ |
| 영국 서부 |     |
| US DoD 중부 | |
| US DoD 동부 | |
| US Gov 애리조나 | ✓ |
| US Gov(비지역) | |
| US Gov 텍사스 | |
| US Gov 버지니아 | ✓ |
| 미국 중서부 |     |
| 서유럽   | ✓ |
| 인도 서부 | |
| 미국 서부   | ✓ |
| 미국 서부 2 | ✓ |

## <a name="next-steps"></a>다음 단계

* [원본 변환](data-flow-source.md)을 만드는 방법을 알아봅니다.
* [디버그 모드](concepts-data-flow-debug-mode.md)에서 데이터 흐름을 빌드하는 방법을 알아봅니다.
