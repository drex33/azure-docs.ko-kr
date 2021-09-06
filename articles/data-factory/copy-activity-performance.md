---
title: 복사 작업 성능 및 확장성 가이드
titleSuffix: Azure Data Factory & Azure Synapse
description: 복사 작업을 사용할 때 Azure Data Factory 및 Azure Synapse Analytics 파이프라인의 데이터 이동 성능에 영향을 주는 주요 요소를 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jianleishen
author: jianleishen
manager: shwang
ms.service: data-factory
ms.subservice: data-movement
ms.workload: data-services
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 2a2708c3d84dd83b752db2a0ae56843ae068aabe
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822430"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>복사 작업 성능 및 확장성 가이드

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory 버전을 선택하세요."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data lake 또는 EDW(엔터프라이즈 데이터 웨어하우스)에서 Azure로 대규모 데이터 마이그레이션을 수행하려는 경우가 있습니다. 빅 데이터 분석을 위해 다양한 원본에서 Azure로 많은 양의 데이터를 수집하려는 경우도 있습니다. 각 경우에서 최적의 성능 및 확장성을 구현하는 것이 중요합니다.

Azure Data Factory 및 Azure Synapse Analytics 파이프라인은 데이터를 수집하는 메커니즘을 제공하며, 이점은 다음과 같습니다.

* 대량의 데이터 핸들
* 고성능
* 비용 효과적

이러한 이점은 고성능의 확장성 있는 데이터 수집 파이프라인을 구축하려는 데이터 엔지니어에게 매우 적합합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 데이터 마이그레이션 및 데이터 수집 시나리오에서 복사 작업을 사용하여 어떤 수준의 성능과 확장성을 달성할 수 있나요?
* 복사 작업의 성능을 조정하기 위해 수행해야 하는 단계는 무엇인가요?
* 단일 복사 작업을 실행하는 데 활용할 수 있는 성능 최적화 기능은 무엇인가요?
* 복사 성능을 최적화할 때 고려할 다른 외부 요소는 무엇인가요?

> [!NOTE]
> 일반적으로 복사 작업에 익숙하지 않은 경우 이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md)를 참조하세요.

## <a name="copy-performance-and-scalability-achievable-using-azure-data-factory-and-synapse-pipelines"></a>Azure Data Factory 및 Synapse 파이프라인을 사용하여 달성 가능한 복사 성능과 확장성

Azure Data Factory 및 Synapse 파이프라인은 여러 수준에서 병렬 처리를 허용하는 서버리스 아키텍처를 제공합니다.

이 아키텍처를 통해 사용자 환경에 대한 데이터 이동 처리량을 최대화하는 파이프라인을 개발할 수 있습니다. 이러한 파이프라인은 다음 리소스를 완벽하게 활용합니다.

* 원본 및 대상 데이터 저장소 간의 네트워크 대역폭
* 원본 또는 대상 데이터 저장소 IOPS(초당 입력/출력 작업 수) 및 대역폭

이 전체 사용률을 통해 다음 리소스와 함께 사용할 수 있는 최소 처리량을 측정하여 전체 처리량을 예측할 수 있습니다.

* 원본 데이터 저장소
* 대상 데이터 저장소
* 원본 및 대상 데이터 저장소 간의 네트워크 대역폭

아래 표에서는 데이터 이동 기간의 계산을 보여 줍니다. 각 셀의 기간은 지정된 네트워크 및 데이터 저장소 대역폭과 지정된 데이터 페이로드 크기를 기준으로 계산됩니다.

> [!NOTE]
> 아래에 제공된 기간은 ForEach를 사용해 여러 개의 동시 복사 작업을 분할 및 생성하는 등 [복사 성능 최적화 기능](#copy-performance-optimization-features)에 설명된 성능 최적화 기술을 하나 이상 이용하여 엔드투엔드 데이터 통합 솔루션에서 달성 가능한 성능을 나타냅니다. 특정 데이터 세트 및 시스템 구성에 대한 복사 성능을 최적화하려면 [성능 튜닝 단계](#performance-tuning-steps)에 나와 있는 단계를 따르는 것이 좋습니다. 성능 튜닝 테스트에서 얻은 숫자를 프로덕션 배포 계획, 용량 계획 및 청구 프로젝션에 사용해야 합니다.

&nbsp;

| 데이터 크기 / <br/> bandwidth | 50Mbps    | 100Mbps  | 500Mbps  | 1Gbps   | 5Gbps   | 10Gbps  | 50Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1GB**                    | 2.7분    | 1.4분   | 0.3분   | 0.1분  | 0.03분 | 0.01분 | 0.0분   |
| **10GB**                   | 27.3분   | 13.7분  | 2.7분   | 1.3분  | 0.3분  | 0.1분  | 0.03분  |
| **100GB**                  | 4.6시간    | 2.3시간   | 0.5시간   | 0.2시간  | 0.05시간 | 0.02시간 | 0.0시간   |
| **1TB**                    | 46.6시간   | 23.3시간  | 4.7시간   | 2.3시간  | 0.5시간  | 0.2시간  | 0.05시간  |
| **10TB**                   | 19.4일  | 9.7일  | 1.9일  | 0.9일 | 0.2일 | 0.1일 | 0.02일 |
| **100TB**                  | 194.2일 | 97.1일 | 19.4일 | 9.7일 | 1.9일 | 1일    | 0.2일  |
| **1PB**                    | 64.7달    | 32.4달   | 6.5달    | 3.2달   | 0.6달   | 0.3달   | 0.06달   |
| **10 PB**                   | 647.3달   | 323.6달  | 64.7달   | 31.6달  | 6.5달   | 3.2달   | 0.6달    |
| | |  | | |  | | |

복사본은 다양한 수준에서 확장 가능합니다.

![복사본 크기 조정 방법](media/copy-activity-performance/adf-copy-scalability.png)

* 제어 흐름에서는 여러 복사 작업을 병렬로 시작할 수 있습니다(예: [For Each 루프](control-flow-for-each-activity.md) 사용).

* 단일 복사 작업은 확장 가능한 컴퓨팅 리소스를 활용할 수 있습니다.
  * Azure IR(통합 런타임)을 사용하는 경우 각 복사 작업에 대해 [최대 256개의 DIUs(데이터 통합 단위)](#data-integration-units)를 서버를 사용하지 않는 방식으로 지정할 수 있습니다.
  * 자체 호스팅 IR을 사용하는 경우 다음 방법 중 하나를 사용할 수 있습니다.
    * 수동으로 컴퓨터를 스케일 업합니다.
    * 여러 컴퓨터 ([최대 4 개 노드](create-self-hosted-integration-runtime.md#high-availability-and-scalability))로 규모를 스케일 아웃하고, 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 파티션합니다.

* 단일 복사 작업은 여러 스레드를 [병렬로](#parallel-copy) 사용하여 데이터 저장소에서 읽고 씁니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

복사 작업을 사용하여 서비스의 성능을 조정하려면 다음 단계를 수행합니다.

1. **테스트 데이터 집합을 선택하고 기준선을 설정합니다.**

    개발 중 대표적인 데이터 샘플에 대한 복사 작업을 사용하여 파이프라인을 테스트합니다. 선택한 데이터 집합은 다음과 같은 특성을 따르는 일반적인 데이터 패턴을 나타내야 합니다.

    * 폴더 구조
    * 파일 패턴
    * 데이터 스키마

    그리고 데이터 집합은 복사 성능을 평가할 만큼 충분히 커야 합니다. 복사 작업이 완료되기까지 적절한 크기는 10분 이상 소요됩니다. [복사 작업 모니터링](copy-activity-monitoring.md)에서 실행 세부 정보 및 성능 특성을 파악합니다.

2. **단일 복사 작업의 성능을 최대화하는 방법**:

    먼저 단일 복사 작업을 사용하여 성능을 최대화하는 것이 좋습니다.

    * **_Azure_ 통합 런타임에서 복사 작업을 실행하는 경우:**

        [DIU(데이터 통합 단위)](#data-integration-units) 및 [병렬 복사](#parallel-copy) 설정에 대한 기본값을 사용하여 시작합니다.

    * **복사 작업이 _자체 호스팅_ 통합 런타임에서 실행되는 경우:**

        전용 컴퓨터를 사용하여 통합 런타임을 호스팅하는 것이 좋습니다. 컴퓨터는 데이터 저장소를 호스트하는 서버와 분리해야 합니다. [병렬 복사](#parallel-copy) 설정의 기본값을 사용하여 시작하고 자체 호스팅 IR에 단일 노드를 사용합니다.

    성능 테스트 실행을 수행합니다. 달성한 성능을 적어둡니다. 사용되는 실제 값(예: DIUs 및 병렬 복사본)을 포함합니다. 사용되는 실행 결과 및 성능 설정을 수집하는 방법에 대한 [복사 작업 모니터링](copy-activity-monitoring.md)을 참조하세요. 병목 상태를 식별하고 해결하기 위해 [복사 작업 성능 문제를 해결](copy-activity-performance-troubleshooting.md)하는 방법에 대해 알아봅니다.

    문제 해결 및 튜닝 지침에 따라 추가 성능 테스트 실행을 수행하려면 반복합니다. 단일 복사 작업 실행이 더 나은 처리량을 달성할 수 없는 경우 여러 복사본을 동시에 실행하여 집계 처리량을 최대화할 지 여부를 고려합니다. 이 옵션은 번호가 매겨진 다음 글머리 기호에 설명되어 있습니다.

3. **여러 복사본을 동시에 실행하여 집계 처리량을 최대화하는 방법:**

    이제 단일 복사 작업의 성능을 최대화 했습니다. 환경에 대한 처리량 상한을 아직 달성하지 않은 경우 여러 개의 복사 작업을 병렬로 실행할 수 있습니다. 제어 흐름 구문을 사용하여 병렬로 실행할 수 있습니다. 이러한 구문 중 하나는 [For each 루프](control-flow-for-each-activity.md)입니다. 자세한 내용은 솔루션 템플릿에 대한 다음 문서를 참조하세요.

    * [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
    * [Amazon s 3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md)
    * [컨트롤 테이블을 사용하여 대량 복사](solution-template-bulk-copy-with-control-table.md)

4. **전체 데이터 집합으로 구성 확장.**

    실행 결과 및 성능에 만족하면 정의 및 파이프라인을 확장하여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

[성능 튜닝 단계](#performance-tuning-steps)를 따라 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 그리고 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)에서 각 복사 작업 실행의 성능 문제를 해결하는 방법에 대해 알아봅니다.

## <a name="copy-performance-optimization-features"></a>복사 작업 성능 최적화 기능

서비스에서 제공하는 성능 최적화 기능은 다음과 같습니다.

* [데이터 통합 단위](#data-integration-units)
* [자체 호스팅 통합 런타임 확장성](#self-hosted-integration-runtime-scalability)
* [병렬 복사](#parallel-copy)
* [준비된 복사](#staged-copy)

### <a name="data-integration-units"></a>데이터 통합 단위

DIU(데이터 통합 단위)는 Azure Data Factory 및 Synapse 파이프라인에 있는 단일 유닛의 전원을 나타내는 단위입니다. 전원은 CPU, 메모리 및 네트워크 리소스 할당의 조합입니다. DIU는 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용됩니다. DIU는 [자체 호스팅 통합 런타임](concepts-integration-runtime.md#self-hosted-integration-runtime)에 적용되지 않습니다. [여기를 참조하세요](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

늘어난 동시 워크로드를 호스트할 수 있습니다. 또는 현재 워크로드 수준에서 더 높은 성능을 달성하길 원할 수 있습니다. 다음 방법으로 처리 규모를 향상 시킬 수 있습니다.

* 노드에서 실행할 수 있는 [동시 작업](create-self-hosted-integration-runtime.md#scale-up) 수를 스케일 업해서 자체 호스팅 IR을 _확장할 수_ 있습니다.  
스케일 업 작업은 노드의 프로세서와 메모리가 완전히 활용된 것 보다 작은 경우에만 작동합니다.
* 노드 (컴퓨터)를 더 스케일 _아웃_ 하여 자체 호스팅 IR을 확장할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [복사 작업 성능 최적화 기능: 자체 호스팅 통합 런타임 확장성](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [자체 호스팅 통합 런타임 만들기 및 구성: 크기 조정 고려 사항](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>병렬 복사

`parallelCopies` 속성을 설정하여 복사 작업에 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성은 복사 작업 내의 최대 스레드 수로 간주합니다. 스레드가 병렬로 작동합니다. 스레드는 원본에서 읽거나 싱크 데이터 저장소에 기록합니다. [자세히 알아봅니다](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>준비된 복사

데이터 복사 작업은 데이터를 싱크 데이터 저장소로 _직접_ 보낼 수 있습니다. 또는 Blob Storage를 _임시 준비_ 저장소로 사용하도록 선택할 수 있습니다. [자세히 알아보기](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

* [복사 작업 개요](copy-activity-overview.md)
* [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
* [복사 작업 성능 최적화 기능](copy-activity-performance-features.md)
* [Azure Data Factory를 사용하여 데이터 레이크에서 데이터 마이그레이션 또는 데이터 웨어하우스에서 Azure로 데이터 마이그레이션](data-migration-guidance-overview.md)
* [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
