---
title: Microsoft Sentinel | 사용자 고유의 ML 가져오기 Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel에서 데이터 분석을 위해 사용자 고유의 기계 학습 알고리즘을 만들고 사용하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8d071fe7f24015f64734a07d25844df265a88307
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724580"
---
# <a name="bring-your-own-machine-learning-ml-into-microsoft-sentinel"></a>Microsoft Sentinel에 ML(사용자 고유의 Machine Learning) 가져오기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Machine Learning ML(ML)는 Microsoft Sentinel의 주요 핵심 중 하나이며 이를 구분하는 주요 특성 중 하나입니다. Microsoft Sentinel은 [Fusion](fusion.md) 상관 관계 엔진 및 Jupyter Notebook에 기본 제공 및 새로 사용 가능한 BYO ML(Build-Your-Own ML) 플랫폼 등 여러 가지 환경의 ML 제공합니다. 

ML 검색 모델은 개별 환경과 사용자 동작 변경에 맞게 조정하여 [가양성](false-positives.md)을 줄이고 기존 접근 방식으로는 발견할 수 없는 위협을 식별할 수 있습니다. 많은 보안 조직에서 보안을 위한 ML의 가치를 이해하고 있지만 보안과 ML 모두에 대한 전문 지식을 갖춘 전문가는 많지 않습니다. 여기에 제시된 프레임워크는 보안 조직 및 전문가가 ML 여정에서 우리와 함께 성장할 수 있도록 설계되었습니다. ML 없거나 필요한 전문 지식이 없는 조직은 Microsoft Sentinel의 기본 제공 ML 기능에서 상당한 보호 가치를 얻을 수 있습니다.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="기계 학습 프레임워크":::


## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>BYO-ML(Bring Your Own Machine Learning) 플랫폼이란?

**BYO-ML 플랫폼** 은 ML 리소스가 있고 자체 비즈니스 요구 사항에 맞는 사용자 지정 ML 모델을 구축하려는 조직을 위해 제공됩니다. 이 플랫폼은 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)/[Apache Spark](http://spark.apache.org/) 환경과 Jupyter Notebook을 사용하여 ML 환경을 생성합니다. 다음 구성 요소가 제공됩니다.

- BYO-ML 패키지. 데이터에 액세스하고 결과를 다시 LA(Log Analytics)로 푸시하는 데 도움이 되는 라이브러리가 포함되어 있어 결과를 검색, 조사 및 헌팅과 통합할 수 있습니다. 

- ML 알고리즘 템플릿. 조직의 특정 보안 문제에 맞게 사용자 지정할 수 있습니다. 

- 샘플 Notebook. 모델을 학습시키고 모델 채점을 예약할 수 있습니다. 

이 모든 것 외에도 고유한 ML 모델 및/또는 사용자 고유의 Spark 환경을 가져와 Microsoft Sentinel과 통합할 수 있습니다.

BYO-ML 플랫폼을 사용하면 자체 ML 모델 구축을 시작할 수 있습니다. 

- 샘플 데이터가 포함된 Notebook을 사용하면 프로덕션 데이터 처리에 대해 걱정하지 않고도 엔드투엔드 실습 경험을 얻을 수 있습니다.

- Spark 환경과 통합된 패키지는 인프라 관리의 문제와 마찰을 줄여줍니다.

- 라이브러리는 데이터 이동을 지원합니다. 학습 및 채점 Notebook은 엔드투엔드 환경을 보여 주고 사용자 환경에 맞게 조정할 수 있는 템플릿 역할을 합니다.

### <a name="use-cases"></a>사용 사례
 
BYO ML 플랫폼 및 패키지는 사용자 고유의 ML 검색을 빌드하는 데 필요한 시간과 노력을 크게 줄이고 Microsoft Sentinel에서 특정 보안 문제를 해결하는 기능을 제공합니다. 이 플랫폼은 다음 사용 사례를 지원합니다.

**사용자 지정 모델을 얻기 위한 ML 알고리즘 학습:** 기존 ML 알고리즘(Microsoft 또는 사용자 커뮤니티에서 공유)을 사용하고 자신의 데이터에 대해 이를 쉽게 학습시켜 데이터와 환경에 더 적합한 사용자 지정 ML 모델을 얻을 수 있습니다.

**사용자 지정 모델을 얻기 위해 ML 알고리즘 템플릿 수정:** ML 알고리즘 템플릿(Microsoft 또는 사용자 커뮤니티에서 공유)을 수정하고 수정된 알고리즘을 자신의 데이터에 대해 이를 학습시켜 특정 문제에 맞는 사용자 지정 모델을 도출할 수 있습니다.

**사용자 고유의 모델을 만듭니다.** Microsoft Sentinel의 BYO ML 플랫폼 및 유틸리티를 사용하여 처음부터 사용자 고유의 모델을 만듭니다.

**Databricks/Spark 환경 통합:** 기존 Databricks/Spark 환경을 Microsoft Sentinel에 통합하고 BYO ML 라이브러리 및 템플릿을 사용하여 고유한 상황에 대한 ML 모델을 빌드합니다.

**고유한 ML 모델 가져오기:** 사용자 고유의 ML 모델을 가져오고 BYO ML 플랫폼 및 유틸리티를 사용하여 Microsoft Sentinel과 통합할 수 있습니다.

**ML 알고리즘 공유:** 커뮤니티가 채택하고 적응할 수 있도록 ML 알고리즘을 공유합니다.

**ML을 사용하여 SecOps 역량 강화:** 헌팅, 검색, 조사 및 응답을 위해 자체 사용자 지정 ML 모델 및 결과를 사용합니다.

이 문서에서는 BYO-ML 플랫폼의 구성 요소와 플랫폼 및 비정상적인 리소스 액세스 알고리즘을 활용하여 Microsoft Sentinel을 사용하여 사용자 지정된 ML 검색을 제공하는 방법을 보여줍니다.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 환경

[Apache Spark](http://spark.apache.org/) 데이터 파이프라인을 빌드하기 위한 통합 프레임워크를 제공하여 빅 데이터 간소화를 한층 발전시켰습니다. Azure Databricks는 Spark를 기반으로 구축된 제로 관리 클라우드 플랫폼을 제공하여 이를 더욱 향상시킵니다. BYO-ML 플랫폼에 Databricks를 사용하여 데이터 파이프라인 및 플랫폼 문제를 해결하는 대신 비즈니스에 즉각적인 영향을 주는 답변을 찾는 데 집중할 수 있도록 하는 것이 좋습니다.

이미 Databricks 또는 다른 Spark 환경이 있고 기존 설정을 사용하는 것을 선호하는 경우 BYO-ML 패키지를 사용할 수도 있습니다. 

## <a name="byo-ml-package"></a>BYO-ML 패키지

BYO ML 패키지에는 보안을 위해 ML의 프런트 엔드에 Microsoft의 모범 사례와 연구가 포함되어 있습니다. 이 패키지에서는 보안 문제에 대한 다음 유틸리티, Notebook 및 알고리즘 템플릿 목록을 제공합니다.

| 파일 이름 | Description |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Azure에서 Blob을 읽고 Log Analytics에 쓰는 데 필요한 유틸리티가 포함되어 있습니다. |
| AnomalousRASampleData | Notebook은 생성된 학습 및 테스트 샘플 데이터와 함께 Microsoft Sentinel에서 비정상적인 리소스 액세스 모델을 사용하는 방법을 보여줍니다. |
| AnomalousRATraining.ipynb | 알고리즘을 학습시키고 모델을 구축 및 저장하는 Notebook입니다. |
| AnomalousRAScoring.ipynb | 모델을 실행하도록 예약하고, 결과를 시각화하고, 점수를 Microsoft Sentinel에 다시 쓰는 Notebook입니다. |
|

제공된 첫 번째 ML 알고리즘 템플릿은 [비정상적인 리소스 액세스 검색](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)을 위한 것입니다. 이는 공동 작업 필터링 알고리즘을 기반으로 하며 Windows 파일 공유 액세스 로그(이벤트 ID가 5140인 보안 이벤트)를 사용하여 학습합니다. 로그에서 이 모델에 필요한 주요 정보는 액세스된 사용자와 리소스의 쌍입니다. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>예제 연습: 비정상적인 파일 공유 액세스 검색 

이제 BYO-ML 플랫폼의 주요 구성 요소에 대해 알게 되었습니다. 플랫폼 및 구성 요소를 사용하여 사용자 지정 ML 검색을 제공하는 방법을 보여 주는 예는 다음을 참조하세요.

### <a name="setup-the-databricksspark-environment"></a>Databricks/Spark 환경 설정

아직 없는 경우 자체 Databricks 환경을 설정해야 합니다. 지침은 [Databricks 빠른 시작](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) 설명서를 참조하세요.

### <a name="auto-export-instruction"></a>자동 내보내기 명령

Microsoft Sentinel에서 사용자 고유의 데이터를 기반으로 사용자 지정 ML 모델을 빌드하려면 ML 모델이 Databricks에서 액세스할 수 있도록 Log Analytics에서 Blob Storage 또는 이벤트 허브 리소스로 데이터를 내보내야 합니다. [Microsoft Sentinel 에 데이터를 수집 하는](connect-data-sources.md)방법에 대해 알아봅니다.

이 예를 사용하려면 Azure Blob Storage에 파일 공유 액세스 로그에 대한 학습 데이터가 있어야 합니다. 데이터 형식은 Notebook 및 라이브러리에 문서화되어 있습니다.

[Azure CLI(명령줄 인터페이스)](/cli/azure/monitor/log-analytics)를 사용하여 Log Analytics에서 데이터를 자동으로 내보낼 수 있습니다. 

명령을 실행하려면 Log Analytics 작업 영역, 스토리지 계정 및 EventHub 리소스에서 **기여자** 역할이 할당되어야 합니다. 

다음은 자동 내보내기를 설정하는 샘플 명령 세트입니다.

```azurecli

az –version

# Login with Azure CLI
az login

# List all Log Analytics clusters
az monitor log-analytics cluster list

# Set to specific subscription
az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>사용자 지정 데이터 내보내기

Log Analytics 자동 내보내기에서 지원하지 않는 사용자 지정 데이터의 경우 논리 앱 또는 기타 솔루션을 사용하여 데이터를 이동할 수 있습니다. [Blob 저장소로 Log Analytics 데이터 내보내기](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) 블로그 및 스크립트를 참조할 수 있습니다.

### <a name="correlate-with-data-outside-of-microsoft-sentinel"></a>Microsoft Sentinel 외부의 데이터와 상관 관계

Microsoft Sentinel 외부에서 Blob Storage 또는 Event Hub로 데이터를 가져와 Microsoft Sentinel 데이터와 상호 연결하여 ML 모델을 빌드할 수도 있습니다.

### <a name="copy-and-install-the-related-packages"></a>관련 패키지 복사 및 설치

앞서 언급한 Microsoft Sentinel GitHub 리포지토리에서 Databricks 환경으로 BYO-ML 패키지를 복사합니다. 그런 다음 Notebook을 열고 Notebook의 지침에 따라 클러스터에 필요한 라이브러리를 설치합니다.

### <a name="model-training-and-scoring"></a>모델 학습 및 채점

두 Notebook의 지침을 수행하여 사용자 환경 및 리소스에 따라 구성을 변경하고 단계를 수행하여 모델을 학습시키고 빌드한 다음 수신 파일 공유 액세스 로그를 채점하도록 모델을 예약합니다.

### <a name="write-results-to-log-analytics"></a>Log Analytics에 결과 쓰기

점수 매기기 예약이 완료되면 점수 매기기 Notebook의 모듈을 사용하여 Microsoft Sentinel 인스턴스와 연결된 Log Analytics 작업 영역에 점수 결과를 쓸 수 있습니다.

### <a name="check-results-in-microsoft-sentinel"></a>Microsoft Sentinel에서 결과 확인

점수가 매긴 결과를 관련 로그 세부 정보와 함께 보려면 Microsoft Sentinel 포털로 돌아갑니다. **로그** > 사용자 지정 로그에서 **AnomalousResourceAccessResult_CL** 테이블(또는 사용자 지정 테이블 이름)에 결과가 표시됩니다. 이러한 결과를 사용하여 조사 및 헌팅 환경을 향상시킬 수 있습니다.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="비정상적인 리소스 액세스 로그":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>ML 결과를 사용하여 사용자 지정 분석 규칙 빌드

ML 결과가 사용자 지정 로그 테이블에 있는지 확인하고 점수의 충실도에 만족하는 경우 결과를 기반으로 검색을 만들 수 있습니다. Microsoft Sentinel 포털에서 **분석으로** 이동하여 [새 검색 규칙 를 만듭니다.](detect-threats-custom.md) 다음은 검색을 만드는 데 사용되는 쿼리를 보여 주는 예입니다.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="BYO-ML 검색을 위한 사용자 지정 분석 규칙 만들기":::

### <a name="view-and-respond-to-incidents"></a>인시던트 보기 및 응답
ML 결과에 따라 분석 규칙을 설정하면 쿼리에서 설정한 임계값을 초과하는 결과가 있으면 Microsoft Sentinel의 **인시던트 페이지에 인시던트** 페이지가 생성되고 표시됩니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel의 BYO ML 플랫폼을 사용하여 사용자 고유의 기계 학습 알고리즘을 만들거나 가져와서 데이터를 분석하고 위협을 검색하는 방법을 배웠습니다.

- [Microsoft Sentinel 블로그](https://aka.ms/azuresentinelblog)에서 기계 학습에 대한 게시물 및 기타 많은 관련 항목을 참조하세요.
