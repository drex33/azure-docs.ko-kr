---
title: Synapse Studio를 사용하여 Apache Spark 애플리케이션 모니터링
description: Synapse Studio를 사용하여 Apache Spark 애플리케이션을 모니터링합니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.custom: contperf-fy21q4
ms.openlocfilehash: f83e84e3aa747c41d0e45a5650c704c796c62447
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968270"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio를 사용하여 Apache Spark 애플리케이션 모니터링

Azure Synapse Analytics에서 Apache Spark를 사용하여 작업 영역의 Apache Spark 풀에서 Notebook, 작업 및 기타 종류의 애플리케이션을 실행할 수 있습니다.

이 문서에서는 Apache Spark 애플리케이션을 모니터링하여 최신 상태, 문제 및 진행 상황을 파악할 수 있도록 하는 방법을 설명합니다.

## <a name="view-apache-spark-applications"></a>Apache Spark 애플리케이션 보기 
모든 Apache Spark 애플리케이션은 **모니터** -> **Apache Spark 애플리케이션** 에서 볼 수 있습니다.
   ![Apache Spark 애플리케이션](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>완료된 Apache Spark 애플리케이션 보기

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 완료된 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

  ![완료된 작업 선택](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 작업을 **새로 고칩니다**.

3. **애플리케이션 비교** 를 클릭하여 비교 기능을 사용합니다. 이 기능에 대한 자세한 내용은 [**Apache Spark 애플리케이션 비교**](#compare-apache-spark-applications)를 참조하세요.

4. **Spark 기록 서버** 를 클릭하여 기록 서버 페이지를 엽니다.

5. **요약** 정보를 확인합니다.

6. **진단** 탭에서 진단을 선택합니다.

7. **로그** 를 확인합니다. 드롭다운 목록에서 다른 옵션을 선택하여 **Livy**, **사전 실행**, **드라이버** 로그의 전체 로그를 볼 수 있습니다. 또한 키워드를 검색하여 필요한 로그 정보를 직접 검색할 수 있습니다. **로그 다운로드** 를 클릭하여 로그 정보를 로컬에 다운로드하고 **오류 및 경고 필터링** 확인란을 선택하여 필요한 오류와 경고를 필터링합니다.

8. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. 기본적으로 이 그래프는 모든 작업을 표시합니다. **작업 ID** 를 기준으로 이 뷰를 필터링할 수 있습니다.

9. 기본적으로 **진행률** 표시가 선택되어 있습니다. **보기** 드롭다운 목록에서 **진행률**/**읽기**/**쓰기**/**기간** 을 선택하여 데이터 흐름을 확인할 수 있습니다.

10. 작업을 재생하려면 **재생** 단추를 클릭합니다. 언제든지 **중지** 단추를 클릭하여 중지할 수 있습니다.

11. 스크롤 막대를 사용하여 작업 그래프를 확대 및 축소하고 **크기에 맞게** 를 선택하여 화면에 맞출 수도 있습니다.

    [![완료된 작업 보기](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. 작업 그래프 노드에는 각 단계에 대한 다음 정보가 표시됩니다.

    - 작업 ID
    - 작업 번호
    - 지속 시간
    - 행 개수
    - 읽은 데이터: 입력 크기 및 무작위 읽기 크기의 합
    - 쓴 데이터: 출력 크기 및 순서 섞기 쓰기 크기의 합
    - 스테이지 번호

       ![작업 그래프 노드](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. 작업을 마우스로 가리키면 작업 세부 정보가 도구 설명에 표시됩니다.
    
    - 작업 상태 아이콘: 작업 상태가 성공이면 녹색 “√”로 표시되고, 작업에서 문제가 검색되면 노란색 “!”가 표시됩니다.
    - 작업 ID입니다.
    - 일반 파트:
      - 진행률
      - 지속 시간
      - 총 작업 수
    - 데이터 파트:
      - 총 행 수
      - 읽은 크기
      - 쓴 크기
    - 기울이기 파트:
      - 데이터 기울이기
      - 시간 기울이기
    - 스테이지 번호

      ![작업 가리키기](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. **스테이지 번호** 를 클릭하여 작업에 포함된 모든 스테이지를 확장합니다. 작업 ID 옆의 **축소** 를 클릭하여 작업의 모든 스테이지를 축소합니다.

15. 스테이지 그래프에서 **세부 정보 보기** 를 클릭하면 스테이지 세부 정보가 표시됩니다.

    [![모든 스테이지 확장](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>실행 중인 Apache Spark 애플리케이션 모니터링

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 실행 중인 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 제출하는 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다. Apache Spark 애플리케이션이 여전히 실행 중인 경우 진행률을 모니터링할 수 있습니다.

   ![실행 중인 작업 선택](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. Apache Spark 애플리케이션을 **취소** 합니다.

3. 작업을 **새로 고칩니다**.

4. **Spark UI** 단추를 클릭하여 Spark 작업 페이지로 이동합니다.

5. **작업 그래프**, **요약**, **진단**, **로그** 의 경우 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션 보기](#view-completed-apache-spark-application)의 5~15단계를 참조하세요. 

    [![실행 중인 작업 보기](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>취소된 Apache Spark 애플리케이션 보기

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 취소된 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

 ![취소된 작업 선택](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 작업을 **새로 고칩니다**.

3. **애플리케이션 비교** 를 클릭하여 비교 기능을 사용합니다. 이 기능에 대한 자세한 내용은 [**Apache Spark 애플리케이션 비교**](#compare-apache-spark-applications)를 참조하세요.

4. **Spark 기록 서버** 를 클릭하여 Apache 기록 서버 링크를 엽니다.

5. 그래프를 봅니다. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션 보기](#view-completed-apache-spark-application)의 5~15단계를 참조하세요.

  [![취소한 작업 보기](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>실패한 Apache Spark 애플리케이션 디버그

**모니터** 를 연 다음, **Apache Spark 애플리케이션** 을 선택합니다. 실패한 Apache Spark 애플리케이션에 대한 세부 정보를 보려면 Apache Spark 애플리케이션을 선택하고 세부 정보를 확인합니다.

![실패한 작업 선택](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **완료된 작업**, **상태** 및 **전체 기간** 을 확인합니다.

2. 작업을 **새로 고칩니다**.

3. **애플리케이션 비교** 를 클릭하여 비교 기능을 사용합니다. 이 기능에 대한 자세한 내용은 [**Apache Spark 애플리케이션 비교**](#compare-apache-spark-applications)를 참조하세요.

4. **Spark 기록 서버** 를 클릭하여 Apache 기록 서버 링크를 엽니다.

5. 그래프를 봅니다. 생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. [완료된 Apache Spark 애플리케이션 보기](#view-completed-apache-spark-application)의 5~15단계를 참조하세요.

   [![실패한 작업 정보](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>Apache Spark 애플리케이션의 입력 데이터/출력 데이터 보기

Apache Spark 애플리케이션을 선택하고 **입력 데이터/출력 데이터 탭** 을 클릭하여 Apache Spark 애플리케이션의 입력과 출력 날짜를 봅니다. 이 기능은 Spark 작업을 디버그하는 데 더 효율적일 수 있습니다. 또한 데이터 원본은 gen1, gen2, blob의 세 가지 저장 방법을 지원합니다.
    
**입력 데이터 탭**
     
1. **입력 복사** 단추를 클릭하여 입력 파일을 로컬에 붙여넣습니다.

2. **CSV로 내보내기** 단추를 클릭하여 입력 파일을 CSV 형식으로 내보냅니다.

3. **검색 상자** 에서 입력 키워드로 파일을 검색할 수 있습니다(키워드에는 파일 이름, 읽기 형식, 경로가 포함됨).

4. **이름**, **읽기 형식**, **경로** 를 클릭하여 입력 파일을 정렬할 수 있습니다.

5. 입력 파일을 마우스로 가리키면 **다운로드/경로 복사/자세히** 단추의 아이콘이 표시됩니다.

   ![입력 탭](./media/how-to-monitor-spark-applications/input-tab.png)

6. **자세히** 단추를 클릭하면 **경로 복사/탐색기에 표시/속성** 에 상황에 맞는 메뉴가 표시됩니다.
      
    ![추가 입력](./media/how-to-monitor-spark-applications/input-more.png)

   * 경로 복사: **전체 경로** 와 **상대 경로** 를 복사할 수 있습니다.
   * 탐색기에 표시: 연결된 스토리지 계정(데이터 > 연결됨)으로 이동할 수 있습니다.
   * 속성: 파일의 기본 속성(파일 이름/파일 경로/읽기 형식/크기/수정됨)을 표시합니다.

     ![속성 이미지](./media/how-to-monitor-spark-applications/properties.png)

**출력 데이터 탭**

   입력과 동일한 기능이 있습니다.

   ![output-image](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark 애플리케이션 비교

애플리케이션을 비교하는 방법에는 두 가지가 있습니다. **애플리케이션 비교** 를 선택하거나, **Notebook에서 비교** 단추를 클릭하여 전자 필기장에서 볼 수 있습니다.

### <a name="compare-by-choosing-an-application"></a>애플리케이션을 선택하여 비교

**애플리케이션 비교** 단추를 클릭하고 애플리케이션을 선택하여 성능을 비교할 때 두 애플리케이션 간의 차이를 한눈에 확인할 수 있습니다.

![애플리케이션 비교](./media/how-to-monitor-spark-applications/compare-applications.png)

![애플리케이션 비교 세부 정보](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. 마우스를 애플리케이션 위로 가져하면 **애플리케이션 비교** 아이콘이 표시됩니다.

2. **애플리케이션 비교** 아이콘을 클릭하면 애플리케이션 비교 페이지가 팝업됩니다.

3. **애플리케이션 선택** 단추를 클릭하여 **비교 애플리케이션 선택** 페이지를 엽니다.

4. 비교 애플리케이션을 선택할 때 애플리케이션 URL을 입력하거나 되풀이 목록에서 선택해야 합니다. 그런 후 **확인** 단추를 클릭합니다. 

   ![비교 애플리케이션 선택](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. 비교 결과는 애플리케이션 비교 페이지에 표시됩니다.

   ![비교 결과](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Notebook에서 비교하여 비교

**애플리케이션 비교** 페이지에서 **Notebook에서 비교** 단추를 클릭하여 Notebook을 엽니다. *.ipynb* 파일의 기본 이름은 **Recurrent Application Analytics** 입니다.

![Notebook에서 비교](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Notebook: Recurrent Application Analytics 파일에서 Spark 풀 및 언어를 설정한 후 바로 실행할 수 있습니다.

![recurrent application analytics](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요.
