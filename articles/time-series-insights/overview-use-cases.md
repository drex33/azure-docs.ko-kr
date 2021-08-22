---
title: Gen2 사용 사례 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 사용 사례에 대해 알아봅니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 5e6b5a7034c5fc8917ac3797ebf8071553fe37f2
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112370458"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 자습서

이 문서에서는 Azure Time Series Insights Gen2의 몇 가지 일반적인 사용 사례를 요약해서 설명합니다. 이 문서의 권장 사항을 참조하면 Azure Time Series Insights Gen2로 애플리케이션 및 솔루션 개발을 시작할 때 도움이 될 수 있습니다.

특히 이 문서에서는 다음과 같은 질문에 대한 대답을 제공합니다.

* Azure Time Series Insights Gen2의 일반적인 사용 사례는 무엇인가요?
* [데이터 탐색 및 시각적 변칙 검색](#data-exploration-and-visual-anomaly-detection)에 Azure Time Series Insights Gen2를 사용하면 어떤 장점이 있나요?
* [운영 분석 및 프로세스 효율성](#operational-analysis-and-driving-process-efficiency)에 Azure Time Series Insights Gen2를 사용하면 어떤 장점이 있나요?
* [고급 분석](#advanced-analytics)에 Azure Time Series Insights Gen2를 사용하면 어떤 장점이 있나요?

이러한 사용 시나리오에 대한 개요는 다음 섹션에 설명되어 있습니다.

## <a name="introduction"></a>소개

Azure Time Series Insights Gen2는 엔드투엔드 PaaS(Platform as a Service) 제품입니다. 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장, 분석 및 쿼리하는 데 사용됩니다. 임시 데이터 탐색 및 운영 분석에 적합합니다. Azure Time Series Insights Gen2는 산업 IoT 배포의 광범위한 요구를 충족하는 고유하게 확장 가능하고 사용자 지정된 서비스 제품입니다.

## <a name="data-exploration-and-visual-anomaly-detection"></a>데이터 탐색 및 시각적 변칙 검색

수십억 개의 이벤트를 즉시 살펴보고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견하세요. Azure Time Series Insights Gen2는 IoT 및 DevOps 분석 워크로드에 대한 거의 실시간에 가까운 성능을 제공합니다.

[![데이터 탐색기](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

대부분의 고객은 인사이트 획득 시간을 최소로 유지할 수 있다는 것이 Azure Time Series Insights Gen2의 두드러진 기능 중 하나라는 사실에 동의합니다.

* Azure Time Series Insights Gen2는 데이터를 미리 준비할 필요가 없습니다.
* 몇 분 안에 고객을 Azure IoT Hub 또는 Azure Event Hubs 인스턴스의 수십억 개 이벤트에 신속하게 연결합니다.
* 연결이 설정되면 수십억 개의 이벤트를 시각화하고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견할 수 있습니다.

Azure Time Series Insights Gen2는 직관적이고 사용 방법이 간단합니다. 코드를 전혀 작성하지 않고도 데이터와 상호 작용할 수 있습니다. Azure Time Series Insights Gen2는 SQL을 잘 알고 있는 고급 사용자를 위한 세분화된 텍스트 기반 쿼리 언어를 제공하지만 새 언어를 학습해야 할 필요가 없다는 장점도 있습니다. 초보 사용자를 위한 선택-클릭 탐색도 제공합니다.

고객이 자산 관련 문제를 신속하게 진단할 수 있다는 장점이 있습니다. 고객은 DevOps 분석을 수행하여 IoT 솔루션에 발생하는 버그의 근본 원인을 파악할 수 있습니다. 또한 데이터 과학 이니셔티브의 일부로 추가 조사를 위해 플래그를 지정할 영역을 식별할 수 있습니다.

Azure Time Series Insights Gen2에 저장된 데이터와 상호 작용하는 세 가지 기본적인 방법이 있습니다.

* 가장 쉬운 첫 번째 시작 방법은 Azure Time Series Insights Gen2 탐색기입니다. 이 탐색기를 사용하면 한 장소에서 모든 IoT 데이터를 신속하게 시각화할 수 있습니다. 열 지도처럼 데이터에서 이상 현상을 포착할 수 있는 도구를 제공합니다. 큐브 뷰도 제공합니다. 단일 대시보드를 통해 하나 이상의 Azure Time Series Insights Gen2 환경에서 최대 4개의 보기를 비교할 수 있습니다. 대시보드는 모든 위치의 시계열 데이터 보기를 제공합니다. [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)에 대해 자세히 알아보세요. 환경을 계획하려면 [Azure Time Series Insights Gen2 계획](./how-to-plan-your-environment.md)을 읽어보세요.

* 시작하는 두 번째 방법은 JavaScript SDK를 사용하여 신속하게 웹 애플리케이션에 강력한 차트와 그래프를 포함하는 것입니다. 코드 몇 줄이면 강력한 쿼리를 작성할 수 있습니다. 꺾은선형 차트, 원형 차트, 가로 막대형 차트, 열 지도, 데이터 표 등을 채우는 데 사용됩니다. 이 모든 요소는 SDK를 사용하여 기본적으로 존재합니다. 또한 SDK는 Azure Time Series Insights Gen2 쿼리 API를 추상화합니다. 이러한 API를 사용하여 대시보드에 표시할 데이터를 쿼리하는 SQL과 유사한 조건자를 작성할 수 있습니다. 하이브리드 프레젠테이션 레이어 솔루션의 경우 Azure Time Series Insights Gen2는 매개 변수가 있는 URL을 제공합니다. 이러한 URL은 데이터를 심층 분석할 수 있도록 Azure Time Series Insights Gen2 탐색기와의 끊김 없는 연결점을 제공합니다.

  * JavaScript SDK에 대해 자세히 알아보려면 [JS 클라이언트 라이브러리](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 및 [샘플 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 참조하세요.

  * [Azure Time Series Insights Gen2 탐색기에서 데이터 시각화](./concepts-ux-panels.md)를 검토하여 URL 및 새 UI를 공유하는 방법에 대해 자세히 알아보세요.

* 시작하는 세 번째 방법은 Azure Time Series Insights Gen2에 저장된 데이터를 쿼리하는 강력한 API를 사용하는 것입니다. Azure Time Series Insights Gen2에는 `from`, `to`, `first` 및 `last`와 같은 임시 연산자가 있습니다. `average`, `sum`, `min`, `max`, `time-weighted average`, `time-weighted sum` 등과 같은 집계 및 변형이 있습니다. 또한 필터링, 산술 및 부울 연산자, 스칼라 함수 등도 사용할 수 있습니다. 이러한 모든 연산자를 통해 다운스트림 애플리케이션은 데이터에서 흥미로운 추세 및 패턴을 신속하게 찾을 수 있습니다. 이러한 연산자를 사용하여 자체 제작 시각화를 채우고 이상 현상을 발견할 수 있습니다.

## <a name="operational-analysis-and-driving-process-efficiency"></a>운영 분석 및 프로세스 효율성 확보

Azure Time Series Insights Gen2를 사용하여 대규모 장비의 상태, 사용량 및 성능을 모니터링하고 작동 효율성을 측정할 수 있습니다. Azure Time Series Insights Gen2는 수집 또는 쿼리 성능의 저하 없이 다양하고 예기치 않은 IoT 워크로드를 관리할 수 있습니다.

[![스크린샷에는 Azure Time Series Insights Gen2의 IoT 디바이스/애플리케이션 데이터, 스트림 처리, 운영 효율성, 인텔리전스/인사이트, 고급 분석이 표시됩니다.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

적절한 기술 또는 솔루션과 결합할 경우 운영 프로세스에서 오는 데이터를 스트리밍하고 지속적으로 처리하여 비즈니스를 성공적으로 변환할 수 있습니다. 종종 이러한 솔루션은 여러 시스템을 조합하여 구성됩니다. 계속해서 변화하고(특히 IoT 영역에서) 공통 패턴을 공유하는 데이터를 탐색하고 분석할 수 있습니다.

이러한 패턴은 종종 다양한 로캘의 디바이스 및 센서에서 수십억 개의 이벤트를 수집하는 IoT 지원 플랫폼에서 시작됩니다. 이러한 시스템은 스트리밍 데이터를 처리 및 분석하여 실시간 인사이트와 작업을 끌어냅니다. 데이터는 실시간에 가까운 일괄 처리 분석이 가능하도록 일반적으로 웜 및 콜드 스토리지에 보관됩니다.

수집된 데이터는 일련의 처리 과정을 거쳐 다운스트림 쿼리 및 분석 시나리오에 적합하도록 정리되고 컨텍스트화됩니다. Azure는 자산 유지 관리, 제조 등의 IoT 시나리오에 적용할 수 있는 다양한 서비스를 제공합니다. 이러한 서비스로는 Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning 및 Power BI가 있습니다.

솔루션 아키텍처는 다음과 같은 방식으로 얻을 수 있습니다.

* 동급 최고의 보안, 처리량 및 대기 시간을 제공하도록 IoT Hub 또는 Event Hubs를 통해 데이터를 수집합니다.
* 데이터 처리 및 컴퓨팅을 수행합니다. Stream Analytics, Logic Apps, Azure Functions 같은 서비스를 통해 수집된 데이터를 필터링합니다. 사용하는 서비스는 구체적인 데이터 처리 요구 사항에 따라 달라집니다.
* 처리 파이프라인에서 컴퓨팅된 신호는 저장 및 분석을 위해 Azure Time Series Insights Gen2에 푸시됩니다.

Azure Time Series Insights Gen2는 거의 실시간으로 기록 데이터에 대한 데이터 탐색 및 자산 기반 인사이트를 제공합니다. 비즈니스 요구 사항에 따라, Azure Time Series Insights Gen2를 Azure HDInsight에 연결하여 Azure Time Series Insights Gen2에 저장된 데이터에서 MapReduce 및 Hive 작업을 실행할 수 있습니다. Azure Time Series Insights Gen2에 저장된 데이터는 Azure Time Series Insights Gen2 퍼블릭 화면 쿼리 API를 통해 Power BI 및 기타 고객 애플리케이션에서 사용할 수 있습니다. 이 데이터는 심층 비즈니스 및 운영 인텔리전스 시나리오에 사용할 수 있습니다.

## <a name="advanced-analytics"></a>고급 분석

Machine Learning 및 Azure Databricks 같은 고급 분석 서비스와 통합할 수 있습니다. Azure Time Series Insights Gen2는 수백만 대의 디바이스에서 원시 데이터를 수신합니다. 또한 여러 Azure 분석 서비스에서 원활하게 사용할 수 있는 상황에 맞는 데이터를 추가합니다.

[![분석](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

고급 분석 및 기계 학습은 대량의 데이터를 사용하고 처리합니다. 이 데이터는 데이터에 근거한 의사 결정을 내리고 예측 분석을 수행하는 데 사용됩니다. IoT 사용 사례에서, 고급 분석 알고리즘은 수백만 개의 디바이스에서 수집된 데이터를 통해 학습합니다. 이러한 디바이스는 1초에 여러 번 데이터를 전송합니다. IoT 디바이스에서 수집된 데이터는 원시 데이터입니다. 디바이스의 위치, 센서 판독값의 단위 같은 컨텍스트 정보가 부족합니다. 따라서 원시 데이터를 고급 분석에 바로 사용하기는 어렵습니다.

Azure Time Series Insights Gen2는 다음과 같은 간단하고 비용 효율적인 방법으로 IoT 데이터와 고급 분석 사이의 간극을 메웁니다.

* 첫째, Azure Time Series Insights Gen2는 IoT Hub를 사용하여 수백만 대의 디바이스에서 원시 원격 분석 데이터를 수집합니다. 컨텍스트 정보를 사용하여 데이터를 보강하고 데이터를 parquet 형식으로 변환합니다. 이 형식은 Machine Learning, Azure Databricks 같은 다른 고급 분석 서비스 및 타사 애플리케이션과 쉽게 통합할 수 있습니다.

    Azure Time Series Insights Gen2는 조직 전체에서 모든 데이터의 믿을 수 있는 소스로 사용할 수 있습니다. 다운스트림 분석 워크로드에서 사용할 중앙 리포지토리를 만듭니다. Azure Time Series Insights Gen2는 실시간에 가까운 스토리지 서비스이므로, 고급 분석 모델이 들어오는 IoT 원격 분석 데이터를 통해 지속적으로 학습할 수 있습니다. 결과적으로 모델이 보다 정확하게 예측할 수 있습니다.

* 둘째, Azure Time Series Insights Gen2에 기계 학습 및 예측 모델의 출력을 제공하여 결과를 시각화하고 저장할 수 있습니다. 이 프로시저는 조직에서 모델을 최적화하고 조정하는 데 도움이 됩니다. Azure Time Series Insights Gen2를 사용하면 스트리밍 원격 분석 데이터를 학습된 모델 출력과 동일한 평면에 간단하게 시각화할 수 있습니다. 이러한 방식으로 데이터 과학 팀이 이상 현상을 찾아내고 패턴을 식별할 수 있도록 도와줍니다.

## <a name="next-steps"></a>다음 단계

* [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)에 대해 자세히 알아보세요.
* 환경을 계획하려면 [Azure Time Series Insights Gen2 모범 사례](./how-to-plan-your-environment.md)를 참조하세요.
* [샘플 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 참조하세요.
