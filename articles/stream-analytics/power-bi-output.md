---
title: Azure Stream Analytics의 Power BI 출력
description: 이 문서에서는 Azure Stream Analytics에서 Power BI로 데이터를 출력하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 7/7/2021
ms.openlocfilehash: d3f8bab950af095a457347b825966a0845ef36f6
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506784"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure Stream Analytics의 Power BI 출력

[Power BI](https://powerbi.microsoft.com/)를 Stream Analytics 작업의 출력으로 사용하여 분석 결과에 대한 풍부한 시각화 환경을 제공할 수 있습니다. 이 기능은 운영 대시보드, 보고서 생성 및 메트릭 기반 보고에 사용할 수 있습니다.

Stream Analytics의 Power BI 출력은 현재 Azure 중국 21Vianet 및 Azure 독일(T-Systems International) 지역에서 사용할 수 없습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에는 Power BI 출력을 구성하기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리에서 쿼리 출력을 이 Power BI 출력으로 보내는 데 사용되는 식별 이름입니다. |
| 그룹 작업 영역 |다른 Power BI 사용자와 데이터를 공유할 수 있게 하려면 Power BI 계정 내에서 그룹을 선택하거나 그룹에 쓰지 않으려는 경우 **내 작업 영역** 을 선택할 수 있습니다. 기존 그룹을 업데이트하려면 Power BI 인증을 갱신해야 합니다. |
| 데이터 세트 이름 |Power BI 출력에 사용할 데이터 세트 이름을 제공합니다. |
| 테이블 이름 |Power BI 출력의 데이터 세트 아래에 테이블 이름을 제공합니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 세트에 하나의 테이블만 있을 수 있습니다. |
| 연결 권한 부여 | 출력 설정을 구성하려면 Power BI를 사용하여 권한을 부여해야 합니다. 이 출력에 대한 액세스 권한이 Power BI 대시보드에 부여되면 사용자 계정 암호를 변경하거나 작업 출력을 삭제하거나 Stream Analytics 작업을 삭제하여 액세스 권한을 철회할 수 있습니다. | 

Power BI 출력 및 대시보드를 구성하는 방법에 대한 연습은 [Azure Stream Analytics 및 Power BI](stream-analytics-power-bi-dashboard.md) 자습서를 참조하세요.

> [!NOTE]
> Power BI 대시보드에서 데이터 세트와 테이블을 명시적으로 만들지 마세요. 작업이 시작되고 출력을 Power BI로 보내기 시작하면 데이터 세트와 테이블이 자동으로 채워집니다. 작업 쿼리에서 결과를 생성하지 않으면 데이터 세트와 테이블이 만들어지지 않습니다. 이 Stream Analytics 작업에서 제공한 것과 동일한 이름의 데이터 세트와 테이블이 Power BI에 이미 있는 경우 기존 데이터를 덮어씁니다.
>

### <a name="create-a-schema"></a>스키마 만들기

Azure Stream Analytics는 사용자를 위한 Power BI 데이터 세트 및 테이블 스키마가 아직 없는 경우 새로 만듭니다. 다른 모든 경우에는 테이블이 새 값으로 업데이트됩니다. 데이터 세트 내에는 현재 하나의 테이블만 있을 수 있습니다. 

Power BI는 FIFO(선입선출) 보존 정책을 사용합니다. 200,000개 행에 도달할 때까지 데이터가 테이블에 수집됩니다.

> [!NOTE]
> 몇 가지 문제가 생길 수 있으므로 여러 출력을 사용하여 동일한 데이터 세트에 작성하지 않는 것이 좋습니다. 각 출력에서 Power BI 데이터 세트를 독립적으로 만들려고 시도하면 이름이 동일한 여러 데이터 세트가 생성될 수 있습니다. 또한 출력에 일관된 스키마가 없는 경우 데이터 세트에서 각 쓰기마다 스키마가 변경되어 스키마 변경 요청이 지나치게 많아집니다. 이런 문제가 방지되더라도 여러 출력은 병합된 단일 출력 보다 성능이 떨어집니다.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics에서 Power BI로 데이터 형식 변환

Azure Stream Analytics는 출력 스키마가 변경되면 런타임 시 동적으로 데이터 모델을 업데이트합니다. 열 이름 변경, 열 형식 변경 및 열 추가 또는 제거 작업이 모두 추적됩니다.

다음 표에서는 Power BI 데이터 세트 및 테이블이 없는 경우 데이터 형식을 [Stream Analytics 데이터 형식](/stream-analytics-query/data-types-azure-stream-analytics)에서 Power BI [EDM(엔터티 데이터 모델) 형식](/dotnet/framework/data/adonet/entity-data-model)으로 변환하는 방법에 대해 설명합니다.

Stream Analytics에서 | Power BI로
-----|-----
bigint | Int64
nvarchar(max) | String
Datetime | DateTime
float | Double
레코드 배열 | 문자열 형식, "IRecord" 또는 "IArray" 상수 값

### <a name="update-the-schema"></a>스키마 업데이트

Stream Analytics는 출력의 첫 번째 이벤트 집합을 기반으로 데이터 모델 스키마를 유추합니다. 나중에 필요한 경우 데이터 모델 스키마는 원래 스키마에 맞지 않을 수 있는 들어오는 이벤트를 수용하도록 업데이트됩니다.

행에서 동적 스키마가 업데이트되지 않도록 방지하려면 `SELECT *` 쿼리를 사용하지 마세요. 잠재적인 성능 영향 외에도 결과에 걸리는 시간이 불확실해질 수 있습니다. Power BI 대시보드에 표시해야 하는 필드를 정확히 선택합니다. 또한 데이터 값은 선택한 데이터 형식을 준수해야 합니다.

이전/현재 | Int64 | String | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
DateTime | String | String |  DateTime | String

## <a name="limitations-and-best-practices"></a>제한 사항 및 모범 사례
현재는 대략 1초당 한 번 Power BI를 호출할 수 있습니다. 스트리밍 시각적 개체는 15KB의 패킷을 지원합니다. 이보다 크면 스트리밍 시각적 개체가 실패합니다(푸시는 계속 작동). 이러한 제한 사항 때문에 Power BI는 Azure Stream Analytics가 데이터 부하를 상당히 줄이는 경우에 가장 적합합니다. 연속 창 또는 도약 창을 사용하여 데이터 푸시가 최대 초당 한번의 푸시를 수행하고 쿼리가 처리량 요구 사항 범위 내에 있도록 하는 것이 좋습니다.

출력 일괄 처리 크기에 관한 자세한 내용은 [Power BI Rest API 한도](/power-bi/developer/automation/api-rest-api-limitations)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [관리 ID를 사용하여 Power BI에 Azure Stream Analytics 작업 인증](powerbi-output-managed-identity.md)
* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
