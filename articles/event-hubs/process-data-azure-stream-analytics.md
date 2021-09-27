---
title: Stream Analytics를 사용하여 Azure Event Hubs에서 데이터 처리 | Microsoft Docs
description: 이 문서에서는 Azure Stream Analytics 작업을 사용하여 Azure 이벤트 허브에서 데이터를 처리하는 방법을 보여 줍니다.
ms.date: 09/15/2021
ms.topic: article
ms.openlocfilehash: 31f8e6b7c5e45006b359c5e9545703f454dbacad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679809"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream Analytics를 사용하여 이벤트 허브에서 데이터 처리 
Azure Stream Analytics 서비스를 사용하면 Azure Event Hubs에서 스트리밍 데이터를 간편하게 수집, 처리하고 분석할 수 있으므로 강력한 인사이트로 실시간 작업을 지원할 수 있습니다. 이러한 통합을 통해 핫 경로 분석 파이프라인을 신속하게 만들 수 있습니다. Azure Portal을 사용하여 들어오는 데이터를 시각화하고 Stream Analytics 쿼리를 작성할 수 있습니다. 쿼리가 준비되면 몇 번의 클릭만으로 프로덕션으로 이동할 수 있습니다. 

## <a name="key-benefits"></a>주요 이점
Azure Event Hubs 및 Azure Stream Analytics 통합의 주요 이점은 다음과 같습니다. 
- **데이터 미리 보기** – Azure Portal의 이벤트 허브에서 들어오는 데이터를 미리 볼 수 있습니다.
- **쿼리 테스트** – 변환 쿼리를 준비하고 Azure Portal에서 직접 테스트합니다. 쿼리 언어 구문은 [Stream Analytics 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics) 설명서를 참조하세요.
- **프로덕션에 쿼리 배포** – Azure Stream Analytics 작업을 만들고 시작하여 쿼리를 프로덕션에 배포할 수 있습니다.

## <a name="end-to-end-flow"></a>엔드투엔드 흐름

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. **Event Hubs 네임스페이스** 로 이동한 다음 들어오는 데이터가 있는 **이벤트 허브** 로 이동합니다. 
1. 이벤트 허브 페이지에서 **데이터 처리** 를 선택합니다.  

    ![데이터 처리 타일](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **이벤트에서 실시간 인사이트 사용** 타일에서 **탐색** 을 선택합니다. 

    ![Stream Analytics 선택](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 다음 필드에 대해 값이 이미 설정된 쿼리 페이지가 표시됩니다.
    1. 쿼리에 대한 입력으로 사용할 **이벤트 허브**.
    1. SELECT 문을 사용하는 샘플 **SQL 쿼리**. 
    1. 쿼리 테스트 결과를 참조하는 **출력** 별칭. 

        ![쿼리 편집기](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  해당 기능을 처음 사용하는 경우 해당 페이지에 들어오는 데이터를 미리 볼 수 있는 이벤트 허브에 대한 소비자 그룹 및 정책을 만들 권한을 요청하는 메시지가 표시됩니다.
1. 위의 이미지에 표시된 것처럼 **입력 미리 보기** 창에서 **만들기** 를 선택합니다. 
1. 해당 탭에 들어오는 최신 데이터의 스냅샷이 즉시 표시됩니다.
    - 데이터의 serialization 형식이 자동으로 검색됩니다(JSON/CSV). JSON/CSV/AVRO로 수동으로 변경할 수 있습니다.
    - 들어오는 데이터를 테이블 형식 또는 원시 형식으로 미리 볼 수 있습니다. 
    - 표시된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택하여 최신 이벤트를 확인합니다. 

        **테이블 형식** 의 데이터 예제는 다음과 같습니다.   ![테이블 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results.png)

        **원시 형식** 의 데이터 예제는 다음과 같습니다. 

        ![원시 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. **쿼리 테스트** 를 선택하여 **테스트 결과** 탭에서 쿼리 테스트 결과의 스냅샷을 확인합니다. 결과를 다운로드할 수도 있습니다.

    ![쿼리 결과 테스트](./media/process-data-azure-stream-analytics/test-results.png)
1. 사용자 고유의 쿼리를 작성하여 데이터를 변환합니다. [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요.
1. 쿼리를 테스트한 다음 프로덕션으로 이동하려면 **쿼리 배포** 를 선택합니다. 쿼리를 배포하려면 작업에 대한 출력을 설정하고 작업을 시작할 수 있는 Azure Stream Analytics 작업을 만듭니다. Stream Analytics 작업을 만들려면 작업의 이름을 지정하고 **만들기** 를 선택합니다.

      ![Azure Stream Analytics 작업 만들기](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Event Hubs 페이지에서 만드는 새 Azure Stream Analytics 작업마다 소비자 그룹과 정책을 만드는 것이 좋습니다. 소비자 그룹은 5명의 동시 판독기만 허용하므로 각 작업에 대한 전용 소비자 그룹을 제공하면 해당 제한 초과로 인해 발생할 수 있는 오류를 방지할 수 있습니다. 전용 정책을 사용하면 다른 리소스에 영향을 주지 않고 키를 회전하거나 사용 권한을 철회할 수 있습니다. 
1. 이제 테스트한 쿼리와 동일한 Stream Analytics 작업이 만들어지고 입력은 이벤트 허브입니다. 

9.  파이프라인을 완료하려면 쿼리의 **출력** 을 설정하고 **시작** 을 선택하여 작업을 시작합니다.

    > [!NOTE]
    > 작업을 시작하기 전에 출력 별칭을 Azure Stream Analytics에서 만든 출력 이름으로 바꾸어야 합니다.

      ![출력 설정 및 작업 시작](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>알려진 제한 사항
쿼리를 테스트하는 동안 테스트 결과를 로드하는 데 6초 정도 걸립니다. Microsoft는 테스트 성능을 향상하기 위해 노력하고 있습니다. 그러나 프로덕션 환경에 배포된 경우 Azure Stream Analytics의 대기 시간은 1초 미만입니다.

## <a name="streaming-units"></a>스트리밍 단위
Azure Stream Analytics 작업은 기본적으로 3개의 SU(스트리밍 단위)로 설정됩니다. 설정을 조정하려면 Azure Portal에서 **Stream Analytics 작업** 페이지의 왼쪽 메뉴에서 **Scale**(스케일링)을 선택합니다. 스트리밍 단위에 대한 자세한 내용은 [스트리밍 단위 이해 및 조정](../stream-analytics/stream-analytics-streaming-unit-consumption.md)을 참조하세요.

![스트리밍 단위 스케일링](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>다음 단계
Stream Analytics 쿼리에 대한 자세한 내용은 [Stream Analytics 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics)를 참조하세요.
