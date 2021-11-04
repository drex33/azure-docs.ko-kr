---
title: 프리미엄 블록 Blob Storage 계정
titleSuffix: Azure Storage
description: 빠르고 일관된 응답 시간이 필요한 Azure Storage 워크로드에 대해 더 낮고 일관된 대기 시간을 달성합니다.
author: normesta
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8f394bb69197e9268fb986f7a8b971206aa74fc4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444841"
---
# <a name="premium-block-blob-storage-accounts"></a>프리미엄 블록 Blob Storage 계정

Premium 블록 Blob Storage 계정은 고성능 하드웨어를 통해 데이터를 사용할 수 있도록 합니다. 데이터는 짧은 대기 시간에 최적화된 SSD(반도체 드라이브)에 저장됩니다. SSD는 기존 하드 드라이브에 비해 더 높은 처리량을 제공합니다. 데이터가 즉시 액세스할 수 있는 메모리 칩에 저장되므로 파일 전송이 훨씬 빠릅니다. 한 번에 액세스할 수 있는 드라이브의 모든 부분입니다. 반면 HDD(하드 디스크 드라이브)의 성능은 읽기/쓰기 헤드에 대한 데이터의 근접도에 따라 달라집니다. 

## <a name="high-performance-workloads"></a>고성능 워크로드

Premium 블록 Blob Storage 계정은 빠르고 일관된 응답 시간이 필요하거나 IOP(초당 입력 출력 작업 수)가 많은 워크로드에 적합합니다. 워크로드의 예는 다음과 같습니다.

- **대화형 워크로드**. 대화형 및 실시간 애플리케이션은 데이터를 빠르게 작성해야 합니다. 전자상거래 및 매핑 애플리케이션에는 즉시 업데이트 및 사용자 피드백이 필요한 경우가 많습니다. 예를 들어 전자상거래 애플리케이션에서 자주 표시되지 않는 항목은 캐시되지 않을 가능성이 높습니다. 그러나 고객이 요청 시 즉시 표시되어야 합니다. 대화형 편집 또는 다중 플레이어 온라인 게임 애플리케이션은 실시간 업데이트를 제공하여 품질 환경을 유지 관리합니다.  

- **IoT/ 스트리밍 분석.** IoT 시나리오에서는 매초 소량의 다양한 쓰기 작업을 클라우드에 푸시할 수 있습니다. 대량 데이터를 수집하고 분석 목적으로 집계한 다음 거의 즉각적으로 삭제할 수 있습니다. 프리미엄 블록 Blob 스토리지의 고성능 수집 기능을 사용하면 해당 유형의 워크로드에 대한 효율성을 개선할 수 있습니다.

- **AI/ML(인공 지능/기계 학습)** AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양한 데이터 형식의 소비와 처리를 다룹니다. 워크로드의 고성능 컴퓨팅 형식은 데이터 분석에 대한 신속한 응답과 효율적인 수집 시간을 필요로 하는 대량의 데이터를 처리합니다.

## <a name="cost-effectiveness"></a>비용 효과
  
Premium 블록 Blob Storage 계정은 표준 범용 v2 계정에 비해 스토리지 비용이 더 높지만 트랜잭션 비용은 낮습니다. 애플리케이션 및 워크로드가 많은 트랜잭션을 실행하는 경우 특히 워크로드가 쓰기가 많은 경우 프리미엄 Blob Blob Storage가 비용 효율적일 수 있습니다.

대부분의 경우 테라바이트당 초당 35~40개 이상의 트랜잭션을 실행하는 워크로드(TPS/TB)가 이 유형의 계정에 적합합니다. 예를 들어 워크로드가 한 달에 5억 개의 읽기 작업과 1억 개의 쓰기 작업을 실행하는 경우 다음과 같이 TPS/TB를 계산할 수 있습니다. 

- 초당 쓰기 트랜잭션 = 100,000,000 / (30 x 24 x 60 x 60) = **39(** _가장 가까운 정수로 반올림)_ 

- 초당 읽기 트랜잭션 = 500,000,000 / (30 x 24 x 60 x 60) = **193** (_가장 가까운 정수로 반올림)_

- 초당 총 트랜잭션 수 = **193**  +  **39**  =  **232** 

- 계정에 평균 **5TB** 데이터가 있다고 가정하면 TPS/TB는 **230/5**  =  **46입니다.** 

> [!NOTE]
> 가격은 운영 및 지역별로 다릅니다. Azure [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 사용하여 표준 및 프리미엄 성능 계층 간의 가격 책정을 비교합니다. 

다음 표에서는 프리미엄 블록 Blob Storage 계정의 비용 효율성을 보여줍니다. 이 테이블의 숫자는 Azure Data Lake Storage Gen2 사용 프리미엄 블록 Blob Storage [계정(Azure Data Lake Storage 대한 프리미엄 계층이라고도](premium-tier-for-data-lake-storage.md)함)을 기반으로 합니다. 각 열은 한 달의 트랜잭션 수를 나타냅니다. 각 행은 읽은 트랜잭션의 백분율을 나타냅니다. 테이블의 각 셀은 읽기 트랜잭션 백분율 및 실행된 트랜잭션 수와 관련된 비용 감소 백분율을 보여 줍니다.

예를 들어 계정이 미국 동부 2 지역에 있다고 가정하면 계정이 있는 트랜잭션 수가 90M을 초과하고 해당 트랜잭션의 70%가 읽기 트랜잭션이며 프리미엄 블록 Blob Storage 계정이 더 비용 효율적입니다.

> [!div class="mx-imgBorder"]
> ![성능 테이블](./media/storage-blob-performance-tiers/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> 각 TB급 데이터의 초당 트랜잭션 수를 기반으로 비용 효율성을 평가하려는 경우 테이블의 아래쪽에 표시되는 열 제목을 사용할 수 있습니다. 

## <a name="premium-scenarios"></a>Premium 시나리오

이 섹션에는 일부 Azure Storage 파트너가 프리미엄 블록 Blob Storage를 사용하는 방법에 대한 실제 예제가 포함되어 있습니다. 그 중 일부는 특정 시나리오에서 트랜잭션 성능을 더욱 향상시킬 수 있는 계층적 파일 구조를 도입하는 Azure Data Lake Storage Gen2를 사용하도록 설정합니다. 

> [!TIP]
> 분석 사용 사례가 있는 경우 프리미엄 블록 Blob Storage 계정과 함께 Azure Data Lake Storage Gen2를 사용하는 것이 좋습니다. 

이 섹션에는 다음 예제가 포함되어 있습니다.

- [빠른 데이터 수화](#fast-data-hydration)
- [대화형 편집 애플리케이션](#interactive-editing-applications)
- [데이터 시각화 소프트웨어](#data-visualization-software)
- [전자상거래 기업](#e-commerce-businesses)
- [대화형 가격 분석](#interactive-analytics)
- [데이터 처리 파이프라인](#data-processing-pipelines)
- [IoT(사물 인터넷)](#internet-of-things-iot)
- [Machine Learning](#machine-learning)
- [실시간 스트리밍 분석](#real-time-streaming-analytics)

### <a name="fast-data-hydration"></a>빠른 데이터 수화

Premium 블록 Blob Storage를 사용하면 환경을 신속하게 *하이레이트하거나* 가져올 수 있습니다. 은행 등의 업계에서는 특정 규정 요구 사항에 따라 회사에서 정기적으로 환경을 중단한 다음 처음부터 다시 가동해야 할 수 있습니다. 환경을 Hydrate하는 데 사용되는 데이터는 신속하게 로드되어야 합니다. 

일부 파트너는 매주 MongoDB 인스턴스의 복사본을 프리미엄 블록 Blob Storage 계정에 저장합니다. 그러면 시스템이 중단됩니다. 시스템을 다시 신속하게 다시 온라인으로 만들기 위해 MangoDB 인스턴스의 최신 복사본을 읽고 로드합니다. 감사를 위해 이전 복사본은 일정 기간 동안 클라우드 스토리지에 유지 관리됩니다.

### <a name="interactive-editing-applications"></a>대화형 편집 애플리케이션

여러 사용자가 동일한 콘텐츠를 편집하는 애플리케이션에서는 원활한 사용자 환경을 위해 업데이트 속도가 중요합니다. 

일부 파트너는 비디오 편집 소프트웨어를 개발합니다. 사용자가 비디오에 대해 하는 모든 업데이트는 다른 사용자에게 즉시 표시됩니다. 사용자는 콘텐츠 업데이트가 나타날 때까지 기다리지 않고 작업에 집중할 수 있습니다. 프리미엄 블록 Blob Storage와 관련된 짧은 대기 시간을 통해 원활하고 공동 작업적인 환경을 만들 수 있습니다.  

### <a name="data-visualization-software"></a>데이터 시각화 소프트웨어

렌더링 시간이 빠를 경우 사용자는 데이터 시각화 소프트웨어로 생산성을 훨씬 높일 수 있습니다. 
 
매핑 업계의 회사는 매핑 편집기를 사용하여 맵 문제를 검색하는 것을 보았습니다. 이러한 편집기는 고객 GPS(Global Positioning System) 데이터에서 생성된 데이터를 사용합니다. 맵 겹침을 만들기 위해 편집 소프트웨어는 신속하게 키 조회를 수행하여 맵의 작은 섹션을 렌더링합니다. 

한 경우 프리미엄 블록 Blob Storage를 사용하기 전에 파트너가 표준 범용 v2 스토리지에서 백업하는 HBase 클러스터를 사용했습니다. 그러나 항상 대규모 클러스터를 계속 실행하는 데 비용이 많이 들었습니다. 이 파트너는 이 아키텍처에서 벗어나기로 결정했으며 대신 빠른 키 조회를 위해 프리미엄 블록 Blob Storage를 사용했습니다. 겹침을 만들기 위해 REST API를 사용하여 GPS 좌표에 해당하는 타일을 렌더링했습니다. 프리미엄 블록 Blob Storage 계정은 비용 효율적인 솔루션을 제공했으며 대기 시간이 훨씬 더 예측 가능했습니다.

### <a name="e-commerce-businesses"></a>전자상거래 기업

전자상거래 기업은 고객 관련 상점을 지원하는 것 외에도 내부 팀에 데이터 웨어하우징 및 분석 솔루션을 제공할 수 있습니다. 파트너가 프리미엄 블록 Blob Storage 계정을 사용하여 이러한 데이터 웨어하우징 및 분석 솔루션으로 짧은 대기 시간 요구 사항을 지원하는 것을 보았습니다. 한 경우 카탈로그 팀은 제품, 가격 책정, 배송 방법, 공급업체, 재고 및 물류와 관련된 데이터에 대한 데이터 웨어하우징 애플리케이션을 유지 관리합니다. 정보는 여러 사용 사례에 대해 쿼리, 검색, 추출 및 축소됩니다. 팀은 이 데이터에 대한 분석을 실행하여 다양한 상품 팀에 관련 인사이트와 정보를 제공합니다. 

### <a name="interactive-analytics"></a>대화형 가격 분석

거의 모든 업계에서 기업은 대화형으로 데이터를 쿼리하고 분석해야 합니다. 

데이터 과학자, 분석가 및 개발자는 프리미엄 블록 Blob Storage 계정에 저장된 데이터에 대한 쿼리를 실행하여 시간이 중요한 인사이트를 더 빠르게 얻을 수 있습니다. 임원은 해당 대시보드에 표시되는 데이터가 표준 범용 v2 계정 대신 프리미엄 블록 Blob Storage 계정에서 제공되는 경우 대시보드를 훨씬 더 빠르게 로드할 수 있습니다. 

한 시나리오에서 분석가는 수백만 개의 디바이스에서 원격 분석 데이터를 신속하게 분석하여 제품이 사용되는 방식을 더 잘 이해하고 제품 릴리스 결정을 내려야 했습니다. SQL 데이터베이스에 데이터를 저장하는 것은 비용이 많이 듭니다. 비용을 줄이고 쿼리 가능한 표면 영역을 늘리기 위해 Azure Data Lake Storage Gen2 사용 프리미엄 블록 Blob Storage 계정을 사용하고 Presto 및 Spark에서 계산을 수행하여 Hive 테이블에서 인사이트를 생성했습니다. 이러한 방식으로 거의 액세스하지 않는 데이터도 자주 액세스하는 데이터와 동일한 컴퓨팅 능력을 갖습니다.

SQL 1초 이하의 성능과 외부 스토리지에 대한 Presto의 IDP(초당 입력 출력 작업) 간의 간격을 좁히려면 일관성 및 속도가 중요합니다. 특히 ORC(작은 최적화 행 열 형식) 파일을 처리할 때는 특히 중요합니다. Data Lake Storage Gen2와 함께 사용하는 프리미엄 블록 Blob Storage 계정은 이 시나리오에서 표준 범용 v2 계정에 비해 3배 성능 향상을 반복적으로 보여 했습니다. 쿼리는 컴퓨팅 머신의 로컬 느낌을 충분히 빠르게 실행했습니다. 

또 다른 경우 파트너는 보안 솔루션에서 생성된 로그를 저장하고 쿼리합니다. 로그는 Databricks를 사용하여 생성된 다음 Data Lake Storage Gen2 사용 프리미엄 블록 Blob Storage 계정에 저장됩니다. 최종 사용자는 Azure Data Explorer 사용하여 이 데이터를 쿼리하고 검색합니다. 안정성을 높이고 대화형 쿼리의 성능을 높이기 위해 이 유형의 계정을 선택했습니다. 또한 수명 주기 관리 `Delete Action` 정책을 며칠로 설정하여 비용을 절감할 수 있습니다. 이 정책은 데이터를 계속 유지하지 못하게 합니다. 대신 데이터가 더 이상 필요하지 않은 경우 삭제됩니다. 

### <a name="data-processing-pipelines"></a>데이터 처리 파이프라인

거의 모든 업계에서는 기업에서 데이터를 처리해야 합니다. 사용자가 결정을 내리는 데 도움이 되는 데이터 대시보드와 같은 도구에서 다운스트림 소비에 유용하게 사용할 수 있도록 여러 원본의 원시 데이터를 정리하고 처리해야 합니다. 

데이터를 처리할 때 처리 속도가 항상 가장 중요하지는 않지만 일부 업계에서는 이를 요구합니다. 예를 들어 금융 서비스 산업의 회사는 데이터를 안정적으로 그리고 가능한 가장 빠른 방식으로 처리해야 하는 경우가 많습니다. 사기 행위를 감지하려면 해당 회사는 다양한 출처의 입력을 처리하고, 고객에게 위험을 식별하고, 신속한 조치를 취해야 합니다. 

경우에 따라 파트너가 여러 표준 스토리지 계정을 사용하여 다양한 원본의 데이터를 저장하는 것을 보았습니다. 이 데이터 중 일부는 데이터 처리 애플리케이션이 새로 도착하는 데이터를 자주 읽는 Data Lake Storage 설정된 프리미엄 블록 Blob Storage 계정으로 이동됩니다. 이 계정의 디렉터리 목록 호출은 표준 범용 v2 계정에서 수행하는 것보다 훨씬 더 빠르고 일관적으로 수행되었습니다. 계정에서 제공하는 속도와 일관성 덕분에 다운스트림 처리 시스템에서 항상 가능한 한 빨리 새 데이터를 사용할 수 있었습니다. 이를 통해 잠재적인 보안 위험을 신속하게 파악하고 조치를 할 수 있습니다.
     
### <a name="internet-of-things-iot"></a>IoT(사물 인터넷)

IoT는 일상의 중요한 부분이 되었습니다. IoT는 자동차 이동을 추적하고, 조명을 제어하고, 상태를 모니터링하는 데 사용됩니다. 또한 산업용 애플리케이션도 있습니다. 예를 들어 회사는 IoT를 사용하여 스마트 팩터리 프로젝트를 사용하도록 설정하고, 농업 출력을 개선하고, 예측 유지 관리를 위해 오일 장비를 사용합니다. Premium 블록 Blob Storage 계정은 이러한 시나리오에 상당한 가치를 추가합니다.
 
마이닝 업계에 파트너가 있습니다. Data Lake Storage Gen2를 사용하면 Hbase(HDInsight)와 함께 프리미엄 블록 Blob Storage 계정을 사용하여 여러 마이닝 장비 유형에서 매우 부담이 큰 부하 프로필을 통해 Time Series 센서 데이터를 수집할 수 있습니다. Premium 블록 Blob Storage는 높은 샘플 속도의 소비에 대한 요구 사항을 충족하는 데 도움이 되었습니다. 프리미엄 블록 Blob Storage는 많은 수의 쓰기 트랜잭션을 수행하는 워크로드에 최적화된 비용이며, 이 워크로드는 초당 수만 개의 작은 쓰기 트랜잭션을 생성하기 때문에 비용 효율적입니다.  

### <a name="machine-learning"></a>Machine Learning

대부분의 경우 기계 학습 모델을 학습시키기 위해 많은 데이터를 처리해야 합니다. 이 처리를 완료하려면 컴퓨팅 머신을 오랫동안 실행해야 합니다. 스토리지 비용에 비해 컴퓨팅 비용은 일반적으로 청구서의 훨씬 더 큰 비율을 차지하므로 컴퓨팅 머신이 실행되는 시간을 줄이면 상당한 절감액이 발생할 수 있습니다. 프리미엄 블록 Blob Storage를 사용하여 얻을 수 있는 짧은 대기 시간은 이 시간과 청구서를 크게 줄일 수 있습니다.

기계 학습 학습 및 유추를 실행하는 Spark 클러스터에 데이터 처리 파이프라인을 배포하는 파트너가 있습니다. Spark 테이블(parquet 파일) 및 검사점을 프리미엄 블록 Blob Storage 계정에 저장합니다. Spark 검사점은 많은 수의 중첩된 파일 및 폴더를 만들 수 있습니다. 프리미엄 블록 Blob Storage 계정의 짧은 대기 시간을 Data Lake Storage Gen2에서 사용할 수 있는 계층적 데이터 구조와 결합했기 때문에 디렉터리 나열 작업은 빠릅니다. 

또한 IoT 및 기계 학습과 교차하는 사용 사례가 있는 반도체 산업의 파트너도 있습니다. 제조 공장의 머신에 연결된 IoT 디바이스는 반도체 wafer 이미지를 사용하여 해당 계정에 보냅니다. 딥 러닝 유추를 사용하여 프로덕션에 문제가 있고 작업을 수행해야 하는 경우 시스템은 온-프레미스 머신에 알릴 수 있습니다. 이미지를 빠르고 안정적으로 로드하고 처리할 수 있습니다. Data Lake Storage Gen2 사용 프리미엄 블록 Blob Storage 계정을 사용하면 이를 가능하게 하는 데 도움이 됩니다. 

### <a name="real-time-streaming-analytics"></a>실시간 스트리밍 분석

대화형 분석을 거의 실시간으로 지원하려면 시스템에서 대량의 데이터를 수집 및 처리한 다음 다운스트림 시스템에서 해당 데이터를 사용할 수 있도록 해야 합니다. Data Lake Storage Gen2 사용 프리미엄 블록 Blob Storage 계정을 사용하는 것은 이러한 유형의 시나리오에 적합합니다.

미디어 및 엔터테인먼트 업계의 회사는 이벤트를 브로드캐스트할 때 짧은 시간 안에 많은 수의 로그 및 원격 분석 데이터를 생성할 수 있습니다. 일부 파트너는 스트리밍을 위해 여러 CDN(콘텐츠 배달 네트워크) 파트너를 활용합니다. 트래픽을 할당할 CDN 파트너에 대해 거의 실시간으로 결정해야 합니다. 따라서 데이터를 수집한 후 몇 초 후에 쿼리에 사용할 수 있어야 합니다. 이 빠른 의사 결정을 용이하게 하기 위해 프리미엄 블록 Blob Storage 내에 저장된 데이터를 사용하고 ADX(Azure Data Explorer)에서 해당 데이터를 처리합니다. 스토리지에 업로드된 모든 원격 분석은 ADX로 변환되며, 운영자와 임원이 빠르고 안정적으로 쿼리할 수 있는 친숙한 형식으로 저장할 수 있습니다.

데이터는 여러 프리미엄 성능 Blob Storage 계정에 업로드됩니다. 각 계정은 Event Grid 및 이벤트 허브 리소스에 연결됩니다. ADX는 Blob Storage 데이터를 검색하고, 데이터를 정규화하는 데 필요한 변환을 수행합니다(예: zip 파일 압축 풀기 또는 JSON에서 CSV로 변환). 그런 다음, Grafana에 표시된 ADX 및 대시보드를 통해 데이터를 쿼리에 사용할 수 있습니다. Grafana 대시보드는 운영자, 임원 및 기타 사용자가 사용합니다. 고객은 프리미엄 성능 스토리지에 원래 로그를 유지하거나 장기 보존 및 향후 분석을 위해 핫 또는 쿨 액세스 계층에 저장할 수 있는 범용 v2 스토리지 계정에 복사합니다.

## <a name="getting-started-with-premium"></a>프리미엄 시작

먼저 즐겨찾는 Blob Storage 기능이 프리미엄 블록 Blob Storage 계정과 호환되는지 확인한 다음, 계정을 만듭니다. 

>[!NOTE]
> 기존 표준 범용 v2 스토리지 계정을 프리미엄 블록 Blob Storage 계정으로 변환할 수 없습니다. 프리미엄 블록 Blob Storage 계정으로 마이그레이션하려면 프리미엄 블록 Blob Storage 계정을 만들고 데이터를 새 계정으로 마이그레이션해야 합니다. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Blob Storage 기능 호환성 확인

일부 Blob Storage 기능은 아직 지원되지 않거나 프리미엄 블록 Blob Storage 계정에서 부분적으로 지원됩니다. 프리미엄을 선택하기 전에 [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md) 문서를 검토하여 사용하려는 기능이 계정에서 완전히 지원되는지 확인합니다. 기능 지원은 항상 확장되므로 업데이트를 위해 이 문서를 정기적으로 검토해야 합니다.

### <a name="create-a-new-storage-account"></a>새 Storage 계정 만들기

프리미엄 블록 Blob Storage 계정을 만들려면 계정을 만들 때 **Premium** 성능 옵션 및 **블록 Blob** 계정 유형을 선택해야 합니다.

> [!div class="mx-imgBorder"]
> ![blockblobstorageacount 만들기](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

> [!NOTE]
> 일부 Blob Storage 기능은 아직 지원되지 않거나 프리미엄 블록 Blob Storage 계정에서 부분적으로 지원됩니다. 프리미엄을 선택하기 전에 [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md) 문서를 검토하여 사용하려는 기능이 계정에서 완전히 지원되는지 확인합니다. 기능 지원은 항상 확장되므로 업데이트를 위해 이 문서를 정기적으로 검토해야 합니다.

스토리지 계정을 분석에 사용하려는 경우 프리미엄 블록 Blob Storage 계정과 함께 Azure Data Lake Storage Gen2를 사용하는 것이 좋습니다. Azure Data Lake Storage Gen2 기능의 잠금을 해제하려면 스토리지 계정 만들기 페이지의 **고급** 탭에서 **계층 구조 네임스페이스** 설정을 사용하도록 **설정합니다.** 

다음 이미지는 **스토리지 계정 만들기** 페이지의 이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

전체 지침은 [스토리지 계정 만들기를](../common/storage-account-create.md) 참조하세요.



## <a name="see-also"></a>참조

- [Storage 계정 개요](../common/storage-account-overview.md)
- [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)
- [Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기](create-data-lake-storage-account.md)
- [Azure Data Lake Storage에 대한 프리미엄 계층](premium-tier-for-data-lake-storage.md)
