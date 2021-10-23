---
title: 사용 사례 - 고객 프로파일링
description: Azure 데이터 팩터리로 데이터 기반 워크플로를(파이프라인) 만들어 게임 고객을 프로파일링하는 방법을 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: a8cd46fa12b1641f9ff3dd79dc8e3f077b8e3ad8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242863"
---
# <a name="use-case---customer-profiling"></a>사용 사례 - 고객 프로파일링
Azure Data Factory는 솔루션 가속기의 Cortana Intelligence Suite를 구현하는 데 사용되는 다양한 서비스 중 하나입니다.  Cortana Intelligence에 대한 자세한 내용은 [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics)를 참조하세요. 이 문서에서는 Azure 데이터 팩터리가 어떻게 일반적인 분석 문제를 해결할 수 있는지를 이해하기 시작하는 데 도움이 되는 간단한 사용 사례를 설명합니다.

## <a name="scenario"></a>시나리오
Contoso는 게임 콘솔, 핸드헬드 디바이스, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 플레이어가 이러한 게임을 플레이하면 사용 패턴, 게임 스타일 및 사용자 기본 설정을 추적하는 많은 양의 로그 데이터가 생성됩니다.  인구 통계, 지역 및 제품 데이터가 결합되는 경우 Contoso는 분석을 수행하여 플레이어의 환경을 개선하고 업그레이드 및 게임 내 구매를 특정화하는 방법을 안내합니다. 

Contoso의 목표는 플레이어의 게임 기록을 기반으로 상향 판매/교차 판매 기회를 식별하고 매력적인 기능을 추가하여 비즈니스를 성장시키고 고객에게 더 나은 환경을 제공하는 것입니다. 이 사용 사례의 경우 비즈니스의 예로 게임 회사를 사용합니다. 이 회사에서는 플레이어의 동작에 따라 게임을 최적화하려고 합니다. 이러한 원칙은 상품 및 서비스에 고객을 참여시키고 고객의 환경을 개선하고자 하는 모든 비즈니스에 적용됩니다.

이 솔루션에서는 Contoso가 최근 시작한 마케팅 캠페인의 효과를 평가하려고 합니다. 원시 게임 로그로 시작하여, 지리적 위치 데이터를 처리하고 보강하고, 광고 참조 데이터와 조인하고 마지막으로 Azure SQL Database에 복사하여 캠페인의 영향을 분석합니다.

## <a name="deploy-solution"></a>솔루션 배포
액세스하여 이 간단한 사용 사례를 시도하는데 필요한 것은 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/), [Azure Blob Storage 계정](../../storage/common/storage-account-create.md) 및 [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md)입니다. 데이터 팩터리의 홈 페이지에 **샘플 파이프라인** 타일에서 파이프라인을 프로파일링하는 고객을 배포합니다.

1. 데이터 팩터리를 만들거나 기존 데이터 팩터리를 엽니다. Data Factory를 만드는 단계는 [Data Factory를 사용하여 Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
2. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **샘플 파이프라인** 타일을 클릭합니다.

    :::image type="content" source="./media/data-factory-samples/SamplePipelinesTile.png" alt-text="샘플 파이프라인 타일":::
3. **샘플 파이프라인** 블레이드에서 배포할 **고객 프로파일링** 을 클릭합니다.

    :::image type="content" source="./media/data-factory-samples/SampleTile.png" alt-text="샘플 파이프라인 블레이드":::
4. 샘플에 대한 구성 설정을 지정합니다. 예를 들어 Azure Storage 계정 이름과 키, 논리적 SQL Server 이름, 데이터베이스, 사용자 ID, 암호 등입니다.

    :::image type="content" source="./media/data-factory-samples/SampleBlade.png" alt-text="샘플 블레이드":::
5. 구성 설정 지정을 마쳤으면 **만들기** 를 클릭하여 샘플 파이프라인 및 파이프라인에서 사용되는 연결된 서비스/테이블을 만듭니다/배포합니다.
6. 이전에 **샘플 파이프라인** 블레이드에서 클릭한 샘플 타일에 배포 상태가 표시됩니다.

    :::image type="content" source="./media/data-factory-samples/DeploymentStatus.png" alt-text="배포 상태":::
7. 샘플 타일에 **배포 성공** 메시지가 표시되면 **샘플 파이프라인** 블레이드를 닫습니다.  
8. **데이터 팩터리** 블레이드에서 연결된 서비스, 데이터 집합 및 파이프라인이 데이터 팩터리에 추가된 것을 확인할 수 있습니다.  

    :::image type="content" source="./media/data-factory-samples/DataFactoryBladeAfter.png" alt-text="데이터 팩터리 블레이드":::

## <a name="solution-overview"></a>솔루션 개요
이 간단한 사용 사례는 Azure 데이터 팩터리를 사용하여 데이터를 수집, 준비, 변환, 분석 및 게시하는 방법의 예로 사용할 수 있습니다.

:::image type="content" source="./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png" alt-text="엔드투엔드 워크플로":::

이 그림은 배포된 후 Azure 포털에서 데이터 파이프라인을 표시하는 방법을 보여줍니다.

1. **PartitionGameLogsPipeline** 은 Blob Storage에서 원시 게임 이벤트를 읽고 연도, 월 및 일을 기준으로 파티션을 만듭니다.
2. **EnrichGameLogsPipeline** 은 분할된 게임 이벤트를 지역 코드 참조 데이터와 조인하고 IP 주소를 해당하는 지리적 위치에 매핑하여 데이터를 강화합니다.
3. **AnalyzeMarketingCampaignPipeline** 파이프라인은 강화된 데이터를 사용하고 이 데이터를 광고 데이터와 함께 처리하여 마케팅 캠페인 효과가 포함된 최종 출력을 만듭니다.

이 예에서 Data Factory는 입력 데이터의 복사, 변환 및 처리를 조정하고 Azure SQL Database로 최종 데이터를 출력하는 데 사용됩니다.  또한 데이터 파이프라인의 네트워크를 시각화하고 관리하며 UI에서 상태를 모니터링할 수 있습니다.

## <a name="benefits"></a>이점
사용자 프로필 분석을 최적화하고 비즈니스 목표에 맞추어 게임 회사는 신속하게 사용 패턴을 수집하고 마케팅 캠페인의 효과를 분석할 수 있습니다.