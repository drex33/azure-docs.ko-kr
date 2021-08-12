---
title: Azure Event Hubs - Apache Kafka 이벤트 처리
description: '자습서: 이 문서에서는 Azure Stream Analytics를 사용하여 이벤트 허브를 통해 수집되는 Kafka 이벤트를 처리하는 방법을 보여줍니다.'
ms.topic: tutorial
ms.date: 05/10/2021
ms.openlocfilehash: 0a14a4f8a4e82faebe232ac072ebe61e2db427b7
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286376"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>자습서: Stream 분석을 사용하여 Event Hubs 이벤트에 대한 Apache Kafka 처리 
이 문서에서는 데이터를 Event Hubs로 스트리밍하고 Azure Stream Analytics를 사용하여 처리하는 방법을 보여줍니다. 다음 단계를 안내합니다. 

1. Event Hubs 네임스페이스를 만듭니다.
2. 이벤트 허브로 메시지를 전송하는 Kafka 클라이언트를 만듭니다.
3. 이벤트 허브에서 Azure Blob Storage로 데이터를 복사하는 Stream Analytics 작업을 만듭니다. 

이벤트 허브에서 노출한 Kafka 엔드포인트를 사용하는 경우 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행할 필요가 없습니다. Azure Event Hubs는 [Apache Kafka 버전 1.0](https://kafka.apache.org/10/documentation.html)을 지원합니다. 이상 


## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [Java Development Kit(JDK) 1.7+](/azure/developer/java/fundamentals/java-support-on-azure).
* Maven 이진 아카이브를 [다운로드](https://maven.apache.org/download.cgi)하여 [설치](https://maven.apache.org/install.html)합니다.
* [Git](https://www.git-scm.com/)
* **Azure Storage 계정**. 계정이 없는 경우 계속 진행하기 전에 [만듭니다](../storage/common/storage-account-create.md). 이 연습에서 Stream Analytics 작업은 Azure Blob Storage에 출력 데이터를 저장합니다. 


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
Event Hubs 네임스페이스를 만들면 네임스페이스에 대한 Kafka 엔드포인트가 자동으로 활성화됩니다. Kafka 프로토콜을 사용하는 애플리케이션에서 이벤트 허브로 이벤트를 스트리밍할 수 있습니다. [Azure Portal을 사용하여 이벤트 허브 만들기](event-hubs-create.md)의 단계별 지침에 따라 Event Hubs 네임스페이스를 만듭니다. 전용 클러스터를 사용하는 경우 [전용 클러스터에서 네임스페이스 및 이벤트 허브 만들기](event-hubs-dedicated-cluster-create-portal.md#create-a-namespace-and-event-hub-within-a-cluster)를 참조하세요.

> [!NOTE]
> Kafka용 Event Hubs는 **기본** 계층에서 지원되지 않습니다.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Event Hubs에서 Kafka로 메시지 보내기

1. [Kafka용 Azure Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs-for-kafka)를 머신에 복제합니다.
2. `azure-event-hubs-for-kafka/quickstart/java/producer` 폴더로 이동합니다. 
4. `src/main/resources/producer.config`에서 생산자에 대한 구성 세부 정보를 업데이트합니다. **이벤트 허브 네임스페이스** 에 대한 **이름** 및 **연결 문자열** 을 지정합니다. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`으로 이동하고 선택한 편집기에서 **TestDataReporter.java** 파일을 엽니다. 
6. 다음 코드 줄을 주석으로 처리합니다.

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. 주석 처리된 코드 대신 다음 코드 줄을 추가합니다. 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    이 코드는 **JSON** 형식으로 이벤트 데이터를 보냅니다. Stream Analytics 작업에 대한 입력을 구성할 때 입력 데이터 형식으로 JSON을 지정합니다. 
7. **생산자를 실행** 하고 Event Hubs로 스트리밍합니다. Windows 머신에서 **Node.js 명령 프롬프트** 를 사용할 때 이러한 명령을 실행하기 전에 `azure-event-hubs-for-kafka/quickstart/java/producer` 폴더로 전환합니다. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>이벤트 허브에서 데이터를 수신하는지 확인

1. **엔터티** 에서 **Event Hubs** 를 선택합니다. **테스트** 라는 이벤트 허브가 표시되는지 확인합니다. 

    ![이벤트 허브 - 테스트](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. 이벤트 허브로 들어오는 메시지가 표시되는지 확인합니다. 

    ![이벤트 허브 - 메시지](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Stream Analytics 작업을 사용하여 이벤트 데이터 처리
이 섹션에서는 Azure Stream Analytics 작업을 만듭니다. Kafka 클라이언트가 이벤트 허브로 이벤트를 보냅니다. 입력으로 이벤트 데이터를 사용하고 Azure Blob Storage에 출력하는 Stream Analytics 작업을 만듭니다. **Azure Storage 계정** 이 없는 경우 [만듭니다](../storage/common/storage-account-create.md).

Stream Analytics 작업에서 쿼리는 분석을 수행하지 않고 데이터를 통해 전달됩니다. 다른 형식으로 또는 얻은 정보를 사용하여 출력 데이터를 생성하기 위해 입력 데이터를 변환하는 쿼리를 만들 수 있습니다.  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기 

1. [Azure Portal](https://portal.azure.com)에서 **+ 리소스 만들기** 를 선택합니다.
2. **Azure Marketplace** 메뉴에서 **Analytics** 를 선택하고 **Stream Analytics 작업** 을 선택합니다. 
3. **새 Stream Analytics** 페이지에서 다음 작업을 수행합니다. 
    1. 작업의 **이름** 을 입력합니다. 
    2. **구독** 을 선택합니다.
    3. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고 이름을 입력합니다. **기존 리소스 그룹을 사용** 할 수도 있습니다. 
    4. 작업의 **위치** 를 선택합니다.
    5. **만들기** 를 선택하여 작업을 만듭니다. 

        ![새 Stream Analytics 작업](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>작업 입력 구성

1. 알림 메시지에서 **리소스로 이동** 을 선택하여 **Stream Analytics 작업** 페이지를 봅니다. 
2. 왼쪽 메뉴의 **작업 토폴로지** 섹션에서 **입력** 을 선택합니다.
3. **스트림 입력 추가** 를 선택한 다음, **이벤트 허브** 를 선택합니다. 

    ![입력으로 이벤트 허브 추가](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. **이벤트 허브 입력** 구성 페이지에서 다음 작업을 수행합니다. 

    1. 입력의 **별칭** 을 지정합니다. 
    2. Azure **구독** 을 선택합니다.
    3. 이전에 만든 **이벤트 허브 네임스페이스** 를 선택합니다. 
    4. **이벤트 허브** 에 대해 **테스트** 를 선택합니다. 
    5. **저장** 을 선택합니다. 

        ![이벤트 허브 입력 구성](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>작업 출력 구성 

1. 메뉴의 **작업 토폴로지** 섹션에서 **출력** 을 선택합니다. 
2. 도구 모음에서 **+ 추가** 를 선택하고, **Blob Storage** 를 선택합니다.
3. Blob Storage 출력 설정 페이지에서 다음 작업을 수행합니다. 
    1. 출력의 **별칭** 을 지정합니다. 
    2. Azure **구독** 을 선택합니다. 
    3. **Azure Storage 계정** 을 선택합니다. 
    4. Stream Analytics 쿼리에서 출력 데이터를 저장하는 **컨테이너에 대한 이름** 을 입력합니다.
    5. **저장** 을 선택합니다.

        ![Blob Storage 출력 구성](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>쿼리 정의
들어오는 데이터 스트림을 읽을 Stream Analytics 작업을 설정한 후에 할 일은 데이터를 실시간으로 분석하는 변환을 만드는 것입니다. [Stream Analytics 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference)를 사용하여 변환 쿼리를 정의합니다. 이 연습에서는 변환을 수행하지 않고 데이터를 통해 전달되는 쿼리를 정의합니다.

1. **쿼리** 를 선택합니다.
2. 쿼리 창에서 `[YourOutputAlias]`를 앞에서 만든 출력 별칭으로 바꿉니다.
3. `[YourInputAlias]`를 앞에서 만든 입력 별칭으로 바꿉니다. 
4. 도구 모음에서 **저장** 을 선택합니다. 

    ![화면 캡처는 입력 및 출력 변수에 대한 값을 포함하는 쿼리 창을 보여줍니다.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

1. 왼쪽 메뉴에서 **개요** 를 선택합니다. 
2. **시작** 을 선택합니다. 

    ![시작 메뉴](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. **작업 시작** 페이지에서 **시작** 을 선택합니다. 

    ![작업 시작 페이지](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. 작업의 상태가 **시작** 에서 **실행 중** 으로 변경될 때까지 기다립니다. 

    ![작업 상태 - 실행 중](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>시나리오 테스트
1. **Kafka 생산자** 를 다시 실행하여 이벤트 허브로 이벤트를 보냅니다. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. **Azure Blob Storage** 에 **출력 데이터** 가 생성되었는지 확인합니다. 다음 샘플 행처럼 보이는 100개 행이 있는 컨테이너에 JSON 파일이 표시됩니다. 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Azure Stream Analytics 작업은 이벤트 허브에서 입력 데이터를 받고 이 시나리오에서 Azure Blob Storage에 저장했습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Event Hubs로 스트리밍하는 방법을 배웠습니다. Apache Kafka용 Event Hubs에 대한 자세한 내용은 [Azure Event Hubs용 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)를 참조하세요.