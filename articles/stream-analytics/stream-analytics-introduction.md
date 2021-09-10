---
title: Azure Stream Analytics 소개
description: IoT(사물 인터넷)의 스트리밍 데이터를 실시간으로 분석하도록 도와주는 관리되는 서비스인 Azure Stream Analytics에 대해 알아봅니다.
author: jeanb
ms.author: jeanb
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 8/20/2021
ms.openlocfilehash: ee78ca8fee1b83ea10ea86ef180b3804279bbe91
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687688"
---
# <a name="welcome-to-azure-stream-analytics"></a>Azure Stream Analytics 시작

Azure Stream Analytics는 여러 원본에서 대량의 빠른 스트리밍 데이터를 동시에 분석 및 처리하도록 설계된 실시간 분석 및 복잡한 이벤트 처리 엔진입니다. 패턴과 관계는 디바이스, 센서, 클릭스트림, 소셜 미디어 피드 및 애플리케이션을 포함한 여러 입력 원본에서 추출한 정보에서 식별할 수 있습니다. 이러한 패턴을 사용하여 경고를 만들거나, 보고 도구에 정보를 공급하거나, 나중에 사용할 수 있도록 변환된 데이터를 저장하는 등의 작업을 트리거하고 워크플로를 시작할 수 있습니다. 또한 Stream Analytics는 Azure IoT Edge 런타임에서 사용할 수 있으며, IoT 디바이스에서 데이터를 처리할 수 있습니다.

다음 시나리오는 Azure Stream Analytics를 사용할 수 있는 경우의 예입니다.

* IoT 디바이스에서 실시간 원격 분석 스트림 분석
* 웹 로그/클릭 스트림 분석
* fleet 관리 및 드라이버가 없는 자동차에 대한 지리 공간적 분석
* 원격 모니터링 및 높은 가치 자산의 예측 유지 관리
* 인벤토리 제어 및 변칙 검색에 대한 판매 시점 데이터에 대한 실시간 분석

체험 Azure 구독을 통해 Azure Stream Analytics을 사용해 볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Stream Analytics 사용해 보기](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>Stream Analytics는 어떻게 작동합니까?

Azure Stream Analytics 작업은 입력, 쿼리 및 출력으로 구성됩니다. Stream Analytics는 Azure Event Hubs(Apache Kafka의 Azure Event Hubs 포함), Azure IoT Hub 또는 Azure Blob Storage에서 데이터를 수집합니다. SQL 쿼리 언어를 기반으로 하는 쿼리는 일정 기간 동안 스트리밍 데이터를 쉽게 필터링, 정렬, 집계 및 조인하는 데 사용될 수 있습니다. JavaScript 및 C# UDF(사용자 정의 기능)를 사용하여 이 SQL 언어를 확장할 수도 있습니다. 간단한 언어 구성 및/또는 구성을 통해 집계 작업을 수행할 때 이벤트 순서 지정 옵션 및 시간 범위 기간을 손쉽게 조정할 수 있습니다.

각 작업에는 변환된 데이터에 대한 하나 또는 여러 개의 출력이 있으며, 분석한 정보에 대한 응답으로 수행되는 작업을 제어 할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

* Azure Functions, Service Bus 항목 또는 큐와 같은 서비스에 데이터를 보내 통신을 트리거하거나 워크플로 다운스트림을 사용자 지정합니다.
* 실시간 대시보드를 위해 Power BI 대시보드로 데이터를 전송합니다.
* 기록 데이터를 기반으로 하여 기계 학습 모델을 학습시키거나 일괄 처리 분석을 수행할 수 있도록 데이터를 다른 Azure 스토리지 서비스(예: Azure Data Lake, Azure Synapse Analytics 등)에 저장합니다.

다음 이미지에서는 데이터를 Stream Analytics에 보내고, 스토리지 또는 프레젠테이션과 같은 다른 작업을 위해 분석하고 보내는 방법을 보여 줍니다.

![Stream Analytics 소개 파이프라인](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>주요 기능 및 이점

Azure Stream Analytics는 사용하기 쉽고 유연하며 안정적이고 작업의 규모에 맞게 확장 가능하도록 설계되었습니다. 여러 Azure 지역에서 사용할 수 있으며 IoT Edge 또는 Azure Stack에서 실행됩니다.

## <a name="ease-of-getting-started"></a>시작의 용이성

Azure Stream Analytics는 쉽게 시작할 수 있습니다. 몇 번의 클릭만으로 여러 원본 및 싱크에 연결하여 엔드투엔드 파이프라인을 만듭니다. Stream Analytics는 Azure Event Hubs 및 Azure IoT Hub에 연결하여 데이터 수집을 스트리밍할 수 있으며, Azure Blob 스토리지에 연결하여 기록 데이터를 수집할 수 있습니다. 또한 작업 입력에는 스트리밍 데이터에 조인하여 조회 작업을 수행할 수 있는 Azure Blob 스토리지 또는 SQL Database의 정적 또는 느리게 변경되는 참조 데이터가 포함될 수 있습니다.

Stream Analytics는 작업 출력을 Azure Blob Storage, Azure SQL Database, Azure Data Lake Store 및 Azure CosmosDB와 같은 많은 스토리지 시스템으로 라우팅할 수 있습니다. Azure Synapse Analytics 또는 HDInsight를 사용하여 스트림 출력에 대해 일괄 처리 분석을 실행하거나, 사용을 위한 Event Hubs 또는 실시간 시각화를 위한 Power BI와 같은 다른 서비스로 출력을 보낼 수도 있습니다.

Stream Analytics 출력의 전체 목록은 [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)를 참조하세요.

## <a name="programmer-productivity"></a>프로그래머 생산성

Azure Stream Analytics는 강력한 임시 제약 조건으로 강화된 SQL 쿼리 언어를 사용하여 이동 중인 데이터를 분석합니다. Azure PowerShell, Azure CLI, Stream Analytics Visual Studio 도구, [Stream Analytics Visual Studio Code 확장](quick-create-visual-studio-code.md) 또는 Azure Resource Manager 템플릿과 같은 개발자 도구를 사용하여 작업을 만들 수도 있습니다. 개발자 도구를 사용하면 변환 쿼리를 오프라인에서 개발하고 CI/CD 파이프라인을 사용하여 Azure에 작업을 제출할 수 있습니다.

Stream Analytics 쿼리 언어에서는 스트리밍 데이터를 분석하는 다양한 함수 배열을 제공하여 CEP(Complex Event Processing)를 수행할 수 있습니다. 이 쿼리 언어는 간단한 데이터 조작, 집계 및 분석 함수, 지리 공간적 함수, 패턴 일치 및 변칙 검색을 지원합니다. 포털 또는 개발 도구를 사용하여 쿼리를 편집하고 라이브 스트림에서 추출된 샘플 데이터를 사용하여 테스트할 수 있습니다.

추가 함수를 정의하고 호출하여 쿼리 언어의 기능을 확장할 수 있습니다. Azure Machine Learning에서 함수 호출을 정의하여 Azure Machine Learning 솔루션을 활용하고, JavaScript/C# UDF(사용자 정의 함수) 또는 사용자 정의 집계를 통합하여 Stream Analytics 쿼리의 일환으로 복잡한 계산을 수행할 수 있습니다.

## <a name="fully-managed"></a>완전히 관리

Azure Stream Analytics는 Azure의 완전 관리형(PaaS) 제품입니다. 하드웨어 또는 인프라를 프로비저닝하거나 OS 또는 소프트웨어를 업데이트할 필요가 없습니다. Azure Stream Analytics는 작업을 완벽하게 관리하므로 인프라가 아닌 비즈니스 논리에 집중할 수 있습니다.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>클라우드 또는 인텔리전트 에지에서 실행

Azure Stream Analytics는 대규모 분석을 위해 클라우드에서 실행하거나, 대기 시간이 매우 짧은 분석을 위해 IoT Edge 또는 Azure Stack에서 실행할 수 있습니다. Azure Stream Analytics는 클라우드와 에지 모두에서 동일한 도구와 쿼리 언어를 사용하므로 개발자가 스트림 처리를 위한 진정한 하이브리드 아키텍처를 구축할 수 있습니다. 

## <a name="low-total-cost-of-ownership"></a>낮은 총 소유 비용

클라우드 서비스인 Stream Analytics는 비용에 최적화되어 있습니다. 선불 비용은 없습니다. [사용하는 스트리밍 단위](stream-analytics-streaming-unit-consumption.md)에 대해서만 지불하면 됩니다. 약정 또는 클러스터 프로비저닝이 필요하지 않으며 비즈니스 요구 사항에 따라 작업을 강화하거나 규모 축소할 수 있습니다.

## <a name="mission-critical-ready"></a>중요 업무에 적합

Azure Stream Analytics는 전세계 여러 지역에서 사용할 수 있으며 안정성, 보안 및 규정 준수 요구 사항을 지원하여 중요 업무용 워크로드를 실행하도록 설계되었습니다.

### <a name="reliability"></a>안정성

Azure Stream Analytics는 이벤트를 정확히 한 번 처리하고 이벤트를 한 번만 전달하기 때문에 이벤트가 손실되지 않습니다. 정확히 한 번 처리는 이벤트 전송 보장에서 설명한 대로 선택한 출력을 사용하여 보장됩니다.

Azure Stream Analytics에는 이벤트 전달이 실패할 경우를 대비하여 복구 기능이 내장되어 있습니다. 또한 Stream Analytics는 기본 제공 검사점을 제공하여 작업 상태를 유지하고, 반복 가능한 결과를 제공합니다.

관리형 서비스인 Stream Analytics는 분 수준에서 99.9%의 가용성으로 이벤트 처리를 보장합니다. 

### <a name="security"></a>보안

보안 측면에서 Azure Stream Analytics는 들어오고 나가는 모든 통신을 암호화하고 TLS 1.2를 지원합니다. 기본 제공 검사점도 암호화됩니다. 모든 처리가 메모리에서 완료되기 때문에 Stream Analytics는 들어오는 데이터를 저장하지 않습니다. Stream Analytics는 [Stream Analytics 클러스터](./cluster-overview.md)에서 작업을 실행할 때 Azure VNET(Virtual Network)도 지원합니다.

### <a name="compliance"></a>규정 준수

Azure Stream Analytics는 [Azure 규정 준수 개요](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)에 설명된 대로 여러 규정 준수 인증을 따릅니다. 

## <a name="performance"></a>성능

Stream Analytics는 매초 수백만 개의 이벤트를 처리할 수 있으며 매우 짧은 대기 시간으로 결과를 제공할 수 있습니다. 워크로드에 맞게 [확장](stream-analytics-autoscale.md)할 수 있습니다. Stream Analytics는 분할을 통해 더 높은 성능을 지원하므로 복잡한 쿼리를 여러 스트리밍 노드에서 병렬로 처리하고 실행할 수 있습니다. Azure Stream Analytics는 Microsoft Research와의 협업을 통해 개발된 고성능 메모리 내 스트리밍 분석 엔진인 [Trill](https://github.com/Microsoft/Trill)을 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stream Analytics의 개요를 살펴봤습니다. 다음으로 Stream Analytics 작업을 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio Code를 사용하여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
