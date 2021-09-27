---
title: 매핑 데이터 흐름의 스키마 드리프트
titleSuffix: Azure Data Factory & Azure Synapse
description: 스키마 드리프트를 사용 하 여 Azure Data Factory 및 Synapse Analytics 파이프라인에서 복원 력 데이터 흐름 빌드
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: f82d28ba819e03e1e4c01b6fda11eeab7ede6e0e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815387"
---
# <a name="schema-drift-in-mapping-data-flow"></a>매핑 데이터 흐름의 스키마 드리프트

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

스키마 드리프트는 원본이 메타데이터를 자주 변경하는 경우입니다. 필드, 열, 형식이 즉석에서 추가, 제거 또는 변경될 수 있습니다. 스키마 드리프트를 처리하지 않으면 데이터 흐름이 업스트림 데이터 원본 변경에 취약해집니다. 일반적인 ETL 패턴은 해당 원본 이름에 연결되는 경향이 있으므로 들어오는 열과 필드가 변경되는 경우 실패합니다.

스키마 드리프트를 방지하려면 데이터 엔지니어가 다음 작업을 수행할 수 있는 기능을 데이터 흐름 도구에 포함해야 합니다.

* 변경 가능한 필드 이름, 데이터 형식, 값, 크기가 있는 원본 정의
* 하드 코드된 필드 및 값 대신 데이터 패턴을 사용할 수 있는 변환 매개 변수 정의
* 명명된 필드를 사용하는 대신 수신 필드와 일치하는 패턴을 이해하는 식 정의

Azure Data Factory는 기본적으로 실행 간에 변경되는 유연한 스키마를 지원하므로 데이터 흐름을 다시 컴파일하지 않아도 제네릭 데이터 변환 논리를 빌드할 수 있습니다.

흐름 전체의 스키마 드리프트를 허용하도록 데이터 흐름의 아키텍처를 결정해야 합니다. 이렇게 하면 원본의 스키마 변경으로부터 보호할 수 있습니다. 그러나 데이터 흐름 전체에서 열과 형식의 초기 바인딩이 손실됩니다. Azure Data Factory는 스키마 드리프트 흐름을 지연 바인딩 흐름으로 처리하므로 변환을 빌드할 때 흐름 전체의 스키마 보기에 드리프트된 열 이름이 제공되지 않습니다.

이 비디오에서는 데이터 흐름의 **스키마 드리프트** 기능을 사용 하 여 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 쉽게 작성할 수 있는 복잡 한 솔루션 중 일부를 소개 합니다. 이 예제에서는 유연한 데이터베이스 스키마를 기반으로 재사용 가능한 패턴을 빌드합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>원본의 스키마 드리프트

원본 정의에서 데이터 흐름으로 들어오는 열은 원본 프로젝션에 없는 경우 “드리프트됨”으로 정의됩니다. 원본 변환의 프로젝션 탭에서 원본 프로젝션을 볼 수 있습니다. 원본에 대 한 데이터 집합을 선택 하는 경우 서비스는 데이터 집합에서 스키마를 자동으로 가져오고 해당 데이터 집합 스키마 정의에서 프로젝션을 만듭니다.

원본 변환에서 스키마 드리프트는 데이터 세트 스키마에 정의되지 않은 열을 읽는 것으로 정의됩니다. 스키마 드리프트를 사용하려면 원본 변환에서 **스키마 드리프트 허용** 을 선택합니다.

:::image type="content" source="media/data-flow/schemadrift001.png" alt-text="스키마 드리프트 원본":::

스키마 드리프트가 사용되면 실행 중에 원본에서 들어오는 모든 필드를 읽고 전체 흐름을 통해 싱크에 전달합니다. 기본적으로 ‘드리프트된 열’이라고 하는 새로 검색된 모든 열은 문자열 데이터 형식으로 도착합니다. 데이터 흐름이 드리프트된 열의 데이터 형식을 자동으로 유추하도록 하려면 원본 설정에서 **드리프트된 열 형식 유추** 를 선택합니다.

## <a name="schema-drift-in-sink"></a>싱크의 스키마 드리프트

싱크 변환에서 스키마 드리프트는 싱크 데이터 스키마에 정의된 항목 위에 추가 열을 쓰는 경우입니다. 스키마 드리프트를 사용하려면 싱크 변환에서 **스키마 드리프트 허용** 을 선택합니다.

:::image type="content" source="media/data-flow/schemadrift002.png" alt-text="스키마 드리프트 싱크":::

스키마 드리프트가 사용되는 경우 매핑 탭에서 **자동 매핑** 슬라이더가 켜져 있는지 확인합니다. 이 슬라이더를 켜면 들어오는 모든 열이 대상에 기록됩니다. 그러지 않으면 규칙 기반 매핑을 사용하여 드리프트된 열을 써야 합니다.

:::image type="content" source="media/data-flow/automap.png" alt-text="싱크 자동 매핑":::

## <a name="transforming-drifted-columns"></a>드리프트된 열 변환

데이터 흐름에 드리프트된 열이 있는 경우 다음 방법을 사용하여 변환에서 해당 열에 액세스할 수 있습니다.

* `byPosition` 및 `byName` 식을 사용하여 이름이나 위치 번호로 열을 명시적으로 참조합니다.
* 이름, 스트림, 위치, 원점 또는 형식의 조합과 일치하도록 파생 열 또는 집계 변환에 열 패턴을 추가합니다.
* 선택 또는 싱크 변환에 규칙 기반 매핑을 추가하여 패턴을 통해 드리프트된 열을 열 별칭에 일치시킵니다.

열 패턴을 구현하는 방법에 관한 자세한 내용은 [매핑 데이터 흐름의 열 패턴](concepts-data-flow-column-pattern.md)을 참조하세요.

### <a name="map-drifted-columns-quick-action"></a>드리프트된 열 매핑 빠른 작업

드리프트된 열을 명시적으로 참조하기 위해 데이터 미리 보기 빠른 작업을 통해 해당 열의 매핑을 빠르게 생성할 수 있습니다. [디버그 모드](concepts-data-flow-debug-mode.md)가 켜지면 데이터 미리 보기 탭으로 이동하고 **새로 고침** 을 클릭하여 데이터 미리 보기를 가져옵니다. 데이터 팩터리가 드리프트된 열이 있음을 검색하면 **드리프트된 열 매핑** 을 클릭하고 스키마 보기 다운스트림의 모든 드리프트된 열을 참조할 수 있는 파생 열을 생성할 수 있습니다.

:::image type="content" source="media/data-flow/mapdrifted1.png" alt-text="트리프트된 열 매핑이 호출된 데이터 미리 보기 탭을 보여 주는 스크린샷":::

생성된 파생 열 변환에서 각 드리프트된 열은 검색된 이름과 데이터 형식에 매핑됩니다. 위 데이터 미리 보기에서 ‘movieId’ 열은 정수로 검색됩니다. **드리프트된 열 매핑** 을 클릭한 후 movieId가 파생 열에 `toInteger(byName('movieId'))`로 정의되고 다운스트림 변환의 스키마 보기에 포함됩니다.

:::image type="content" source="media/data-flow/mapdrifted2.png" alt-text="파생 열의 설정 탭을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계
[데이터 흐름 식 언어](data-flow-expression-functions.md)에서 “byName” 및 “byPosition”을 포함하여 열 패턴과 스키마 드리프트에 대한 추가 기능을 찾습니다.
