---
title: 전용 SQL 풀에서 Azure Stream Analytics 사용
description: 실시간 솔루션을 개발하기 위해 Azure Synapse의 전용 SQL 풀에 Azure Stream Analytics를 사용하는 팁입니다.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: wiassaf
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1362d5dc101bd8da7c6584ae1b6fff54de774ade
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361277"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 Azure Stream Analytics 사용

Azure Stream Analytics는 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. [Azure Stream Analytics 소개](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 읽어 기본 사항을 배울 수 있습니다. [Azure Stream Analytics를 사용하여 시작](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 자습서에 따라 Stream Analytics로 엔드투엔드 솔루션을 만드는 방법에 대해 알 수 있습니다.

이 문서에서는 처리량이 높은 데이터 수집에 대한 출력 싱크로 Azure Stream Analytics 작업과 전용 SQL 풀을 사용하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Stream Analytics 작업 - Azure Stream Analytics 작업을 만들려면 [Azure Stream Analytics 사용 시작](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 자습서의 단계를 따르세요.  

    1. 이벤트 허브 입력 만들기
    2. 이벤트 생성기 애플리케이션 구성 및 시작
    3. Stream Analytics 작업 프로비전
    4. 작업 입력 및 쿼리 지정
* 전용 SQL 풀 - 새 전용 SQL 풀을 만들려면 [빠른 시작: 전용 SQL 풀 만들기](../quickstart-create-sql-pool-portal.md)에 나와 있는 단계를 따릅니다.

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>전용 SQL 풀을 가리키도록 스트리밍 출력을 지정합니다.

### <a name="step-1"></a>1단계

Azure Portal에서 Stream Analytics 작업으로 이동하고 **작업 토폴로지** 메뉴에서 **출력** 을 클릭합니다.

### <a name="step-2"></a>2단계

**추가** 단추를 클릭하고 드롭다운 메뉴에서 **Azure Synapse Analytics** 를 선택합니다.

![Azure Synapse Analytics 선택](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>3단계

다음 값을 입력합니다.

* *출력 별칭*: 이 작업 출력의 이름을 입력합니다.
* *구독*:
  * 전용 SQL 풀이 Stream Analytics 작업과 동일한 구독에 있는 경우 ***구독에서 Azure Synapse Analytics 선택*** 을 클릭합니다.
  * 전용 SQL 풀이 다른 구독에 있는 경우 Azure Synapse Analytics 설정을 수동으로 제공을 클릭합니다.
* ‘데이터베이스’: 드롭다운 목록에서 대상 데이터베이스를 선택합니다.
* *사용자 이름*: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 지정합니다.
* *암호*: 지정된 사용자 계정에 대한 암호를 제공합니다.
* *테이블*: 데이터베이스에서 대상 테이블의 이름을 지정합니다.
* **저장** 단추를 클릭합니다.

![완료된 Azure Synapse Analytics 양식](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>4단계

테스트를 실행하기 전에 전용 SQL 풀에 테이블을 만들어야 합니다.  SSMS(SQL Server Management Studio) 또는 원하는 쿼리 도구를 사용하여 다음 테이블 생성 스크립트를 실행합니다.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>5단계

Stream Analytics 작업에 대한 Azure Portal에서 작업 이름을 클릭합니다.  출력 세부 정보 창에서 ***테스트** _ 단추를 클릭합니다._*

![Outpout에 대한 테스트 단추 세부 정보](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)데이터베이스에 대한 연결이 성공하면 포털에 알림이 표시됩니다.

### <a name="step-6"></a>6단계

작업 토폴로지의 ***쿼리** _ 메뉴를 클릭하고 쿼리를 변경하여 만들어 둔 스트림 출력에 데이터를 삽입합니다.  선택한 쿼리 테스트 단추를 클릭하여 쿼리를 테스트합니다.  쿼리 테스트가 성공하면 쿼리 저장 단추를 클릭합니다._*

![쿼리 저장](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7단계

Azure Stream Analytics 작업을 시작합니다.  개요 메뉴에서 ***시작** _단추를 클릭합니다._*

![Stream Analytics 작업 시작](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

작업 시작 창에서 ***시작*** 단추를 클릭합니다.

![시작](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>다음 단계

통합에 대한 개요는 [기타 서비스 통합](sql-data-warehouse-overview-integrate.md)을 참조하세요.
더 많은 개발 팁은 [전용 SQL 풀에 대한 디자인 결정 및 코딩 기법](sql-data-warehouse-overview-develop.md)을 참조하세요.
