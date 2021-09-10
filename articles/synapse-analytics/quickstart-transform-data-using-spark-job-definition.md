---
title: '빠른 시작: Apache Spark 작업 정의를 사용하여 데이터 변환'
description: 이 자습서에서는 Azure Synapse Analytics를 사용하여 데이터를 Apache Spark 작업 정의로 변환하기 위한 단계별 지침을 제공합니다.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2021
ms.openlocfilehash: 2b7392af7a00ec68ad263b7c930842714c94fe1d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967720"
---
# <a name="quickstart-transform-data-using-apache-spark-job-definition"></a>빠른 시작: Apache Spark 작업 정의를 사용하여 데이터 변환

이 빠른 시작에서는 Azure Synapse Analytics를 사용하여 Apache Spark 작업 정의로 파이프라인을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse 작업 영역**: [빠른 시작: Synapse 작업 영역](quickstart-create-workspace.md) 만들기의 지침에 따라 Azure Portal을 사용하여 Synapse 작업 영역을 만듭니다.
* **Apache Spark 작업 정의**: [자습서: Synapse Studio에서 Apache Spark 작업 정의 만들기](spark/apache-spark-job-definitions.md)의 지침에 따라 Synapse 작업 영역에서 Apache Spark 작업 정의를 만듭니다.


### <a name="navigate-to-the-synapse-studio"></a>Synapse Studio로 이동

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com/#home)에서 Synapse 작업 영역을 엽니다. 시작하기 아래의 Open Synapse Studio 카드에서 **열기** 를 선택합니다.
* [Azure Synapse Analytics](https://web.azuresynapse.net/)를 열고, 작업 영역에 로그인합니다.

이 빠른 시작에서는 "sampletest"라는 작업 영역을 예로 사용합니다. 그러면 자동으로 Synapse Studio 홈 페이지로 이동합니다.

![Synapse Studio 홈페이지](media/quickstart-transform-data-using-spark-job-definition/synapse-studio-home.png)

## <a name="create-a-pipeline-with-an-apache-spark-job-definition"></a>Apache Spark 작업 정의를 사용하여 파이프라인 만들기

파이프라인에는 일련의 활동을 실행하기 위한 논리적 흐름이 포함됩니다. 이 섹션에서는 Apache Spark 작업 정의 활동이 포함된 파이프라인을 만듭니다.

1. **통합** 탭으로 이동합니다. 파이프라인 헤더 옆의 더하기 아이콘을 선택한 다음, **파이프라인** 을 선택합니다.

     ![새 파이프라인 만들기](media/doc-common-process/new-pipeline.png)

2. 파이프라인의 **속성** 설정 페이지에서 **이름** 에 **demo** 를 입력합니다.

3. **활동** 창의 **Synapse** 아래에서 **Spark 작업 정의** 를 파이프라인 캔버스로 끌어다 놓습니다.

     ![spark 작업 정의 끌기](media/quickstart-transform-data-using-spark-job-definition/drag-spark-job-definition.png)


## <a name="set-apache-spark-job-definition-canvas"></a>Apache Spark 작업 정의 캔버스 설정

Apache Spark 작업 정의를 만들면 Spark 작업 정의 캔버스로 자동으로 전송됩니다.

### <a name="general-settings"></a>일반 설정

1. 캔버스에서 Spark 작업 정의 모듈을 선택합니다.

2. **일반** 탭에서 **이름** 으로 **sample** 을 입력합니다.

3. (옵션) 설명을 입력할 수도 있습니다.

4. 시간 제한: 활동을 실행할 수 있는 최대 시간입니다. 기본값은 최대 허용 시간인 7일입니다. 형식은 D.HH:MM:SS입니다.

5. 재시도: 최대 재시도 횟수입니다.

6. 재시도 간격: 각 연결 재시도 간의 시간(초)을 지정합니다.

7. 보안 출력: 선택하면 작업의 출력값이 로깅에서 캡처되지 않습니다.

8. 보안 입력: 선택하면 작업의 입력값이 로깅에서 캡처되지 않습니다.

     ![Spark 작업 정의 일반](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-general.png)

### <a name="settings-tab"></a>설정 탭 

이 패널에서 실행할 Spark 작업 정의를 참조할 수 있습니다.

* Spark 작업 정의 목록을 확장하면 기존 Apache Spark 작업 정의를 선택할 수 있습니다. 실행할 Spark 작업 정의를 참조하는 **신규** 단추를 클릭하여 새 Apache Spark 작업 정의를 생성할 수도 있습니다.

* **새로 만들기** 단추를 클릭하여 명령줄 인수를 추가할 수 있습니다. 명령줄 인수를 추가하면 Spark 작업 정의에 의해 정의된 명령줄 인수가 다시 정의됩니다. <br> *샘플: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result`* <br>

     ![spark 작업 정의 파이프라인 설정](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-pipline-settings.png)

* **동적 콘텐츠 추가** 단추를 클릭하거나 바로 가기 키 <kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd>를 눌러 동적 콘텐츠를 추가할 수 있습니다. **동적 콘텐츠 추가** 페이지에서 식, 함수 및 시스템 변수 조합을 사용하여 동적 콘텐츠에 추가할 수 있습니다.

     ![동적 콘텐츠 추가](media/quickstart-transform-data-using-spark-job-definition/add-dynamic-content.png)

### <a name="user-properties-tab"></a>사용자 속성 탭

이 패널에서 Apache Spark 작업 정의 활동에 대한 속성을 추가할 수 있습니다.

![사용자 속성](media/quickstart-transform-data-using-spark-job-definition/user-properties.png)

## <a name="next-steps"></a>다음 단계

Azure Synapse Analytics 지원에 대해 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [파이프라인 및 활동](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [데이터 흐름 매핑 개요](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [데이터 흐름 식 언어](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
