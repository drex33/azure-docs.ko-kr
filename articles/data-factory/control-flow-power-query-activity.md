---
title: Azure Data Factory의 파워 쿼리 작업
description: Data Factory 파이프라인의 데이터 랭글링 기능에 파워 쿼리 활동을 사용하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358527"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Azure Data Factory의 파워 쿼리 작업

파워 쿼리 활동을 사용하여 Data Factory 파이프라인에서 대규모로 랭글링 데이터를 실행하는 파워 쿼리 매시업을 빌드하고 실행할 수 있습니다. 새 리소스 메뉴 옵션 또는 파이프라인에 파워 작업을 추가하여 새 파워 쿼리 매시업을 만들 수 있습니다.

![팩터리 리소스 창의 파워 쿼리 스크린샷](media/data-flow/power-query-activity-1.png)

파워 쿼리 매시업 편집기 내에서 직접 작업하여 대화형 데이터 검색을 수행한 뒤 작업을 저장할 수 있습니다. 완료되면 파워 쿼리 활동을 사용하여 파이프라인에 추가할 수 있습니다. Azure Data Factory는 Azure Data Factory의 데이터 흐름 Spark 환경을 사용하여 자동으로 스케일링하고 데이터 랭글링을 운영합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>데이터 흐름 스크립트로 변환

Azure Data Factory는 파워 쿼리 작업을 사용하여 스케일링하기 위해 ```M``` 스크립트를 데이터 흐름 스크립트로 변환하므로, Azure Data Factory의 데이터 흐름 Spark 환경을 사용하여 규모에 따라 파워 쿼리를 실행할 수 있습니다. 코드 없는 데이터 준비를 사용하여 랭글링 데이터 흐름을 작성합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조하세요.

## <a name="settings"></a>설정

* 파워 쿼리: 기존 파워 쿼리를 선택 하 여 실행 하거나 새 항목을 만듭니다.
* Azure IR에서 실행: 기존 Azure Integration Runtime를 선택 하 여 파워 쿼리에 대 한 계산 환경을 정의 하거나 새 항목을 만듭니다.
* 계산 형식: 기본 자동 해결 통합 런타임을 선택 하는 경우 파워 쿼리 실행을 위해 Spark 클러스터 계산에 적용할 계산 형식을 선택할 수 있습니다.
* 코어 수: 기본 자동 해결 통합 런타임을 선택 하는 경우 파워 쿼리 실행을 위해 Spark 클러스터 계산에 적용할 코어 수를 선택할 수 있습니다.

## <a name="sink"></a>sink

Spark에서 파워 쿼리 M 스크립트가 실행 된 후 변환 된 데이터를 사용 하려는 데이터 집합을 선택 합니다. 싱크를 구성 하는 방법에 대 한 자세한 내용은 [데이터 흐름 싱크에](data-flow-sink.md)대 한 설명서를 참조 하세요.

## <a name="mapping"></a>매핑

매핑 탭에서 선택한 싱크의 대상 스키마에 대 한 파워 쿼리 작업의 출력에서 열 매핑을 구성할 수 있습니다. 자세한 내용은 [데이터 흐름 싱크 매핑 설명서](data-flow-sink.md#field-mapping)에서 열 매핑을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory에서 파워 쿼리](wrangling-tutorial.md)를 사용한 데이터 랭글링의 개념에 대한 자세한 정보.
