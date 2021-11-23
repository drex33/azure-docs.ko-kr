---
title: Microsoft Sentinel의 새로운 내용
description: 이 문서에서는 지난 몇 개월 동안 Microsoft Sentinel의 새로운 기능에 대해 설명합니다.
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/22/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fe85d0a94ba5d483bf86b71d8604ca7b704210ae
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935126"
---
# <a name="whats-new-in-microsoft-sentinel"></a>Microsoft Sentinel의 새로운 내용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft Sentinel에 대해 추가된 최신 기능과 Microsoft Sentinel에서 향상된 사용자 환경을 제공하는 관련 서비스의 새로운 기능을 나열합니다.

6개월보다 오래된 항목을 찾는 경우 [Sentinel](whats-new-archive.md)의 새로운 내용 보관에서 찾을 수 있습니다. 이전에 제공된 기능에 대한 자세한 내용은 [기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)를 참조하세요.

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Microsoft의 위협 헌팅 팀은 팀이 조정하고 사용할 수 있는 특정 [헌팅 쿼리를](https://github.com/Azure/Azure-Sentinel) 포함하여 [Microsoft Sentinel Community](https://github.com/Azure/Azure-Sentinel)쿼리, 플레이북, 통합 문서 및 Notebook을 제공합니다.
>
> 여러분도 참여할 수 있습니다! [Microsoft Sentinel Threat GitHub 커뮤니티에 참여하세요.](https://github.com/Azure/Azure-Sentinel/wiki)

## <a name="november-2021"></a>2021년 11월

- [Amazon Web Services S3 커넥터 사용 가능(공개 미리 보기)](#amazon-web-services-s3-connector-now-available-public-preview)
- [Windows 전달된 이벤트 커넥터 사용 가능(공개 미리 보기)](#windows-forwarded-events-connector-now-available-public-preview)
- [NRT(근 실시간) 위협 탐지 규칙 사용 가능(공개 미리 보기)](#near-real-time-nrt-threat-detection-rules-now-available-public-preview)
- [Fusion 엔진은 이제 새로운 위협과 알 수 없는 위협을 감지합니다(공개 미리 보기).](#fusion-engine-now-detects-emerging-and-unknown-threats-public-preview)
- [분석 규칙에 대한 미세 조정 권장 사항 얻기(공개 미리 보기)](#get-fine-tuning-recommendations-for-your-analytics-rules-public-preview)
- [평가판 업데이트](#free-trial-updates)
- [콘텐츠 허브 및 새 솔루션(공개 미리 보기)](#content-hub-and-new-solutions-public-preview)
- [콘텐츠 리포지토리에서 지속적인 배포 사용(공개 미리 보기)](#enable-continuous-deployment-from-your-content-repositories-public-preview)
- [지리적 위치 및 WhoIs 데이터를 통해 보강된 위협 인텔리전스(공개 미리 보기)](#enriched-threat-intelligence-with-geolocation-and-whois-data-public-preview)
- [Microsoft Sentinel에서 Azure Synapse Analytics Notebook 사용(공개 미리 보기)](#use-notebooks-with-azure-synapse-analytics-in-microsoft-sentinel-public-preview)
- [Microsoft Sentinel의 향상된 Notebook 영역](#enhanced-notebooks-area-in-microsoft-sentinel)
- [Microsoft Sentinel 이름 바꾸기](#microsoft-sentinel-renaming)
- [Azure Sentinel Azure Key Vault honeytoken 배포 및 모니터링](#deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel)

### <a name="amazon-web-services-s3-connector-now-available-public-preview"></a>Amazon Web Services S3 커넥터 사용 가능(공개 미리 보기)

이제 다양한 AWS 서비스에서 로그를 받기 위해 Microsoft Sentinel을 AWS(Amazon Web Services) S3 스토리지 버킷에 연결할 수 있습니다.

지금은 이 연결을 사용하여 AWS CloudTrail뿐만 아니라 VPC Flow 로그 및 GuardDuty 결과를 검색할 수 있습니다.

자세한 내용은 [microsoft Sentinel을 S3 버킷으로 커넥트 AWS(Amazon Web Services) 데이터를 얻으려면 를 참조하세요.](connect-aws.md)

### <a name="windows-forwarded-events-connector-now-available-public-preview"></a>Windows 전달된 이벤트 커넥터 사용 가능(공개 미리 보기)

이제 이 새로운 데이터 커넥터 덕분에 WEC/WEF(Windows 이벤트 컬렉션/Windows 이벤트 전달)를 사용하여 Azure Sentinel 작업 영역에 연결된 Windows 서버에서 이벤트 로그를 스트리밍할 수 있습니다. 커넥터는 레거시 Log Analytics 에이전트(MMA라고도 함)에 비해 많은 이점을 제공하는 새 AMA(Azure Monitor 에이전트)를 사용합니다.

- **확장성:** WINDOWS 이벤트 수집(WEC)을 사용하도록 설정한 경우 WEC 컴퓨터에 AMA(Azure Monitor 에이전트)를 설치하여 단일 연결 지점이 있는 여러 서버에서 로그를 수집할 수 있습니다.

- **속도:** AMA는 5K EPS의 향상된 속도로 데이터를 보낼 수 있어 데이터 새로 고침 속도가 빨라집니다.

- **효율성:** AMA를 사용하면 복잡한 DCR(데이터 수집 규칙)을 디자인하여 원본에서 로그를 필터링하고 작업 영역으로 스트리밍할 정확한 이벤트를 선택할 수 있습니다. DCR은 원치 않는 이벤트를 제외하여 네트워크 트래픽 및 저장 비용을 줄이는 데 도움이 됩니다.

- **범위:** WEC/WEF를 사용하면 레거시(온-프레미스 및 물리적) 서버와 에이전트 설치가 원치 않는 도메인 컨트롤러와 같은 높은 사용량 또는 중요한 컴퓨터에서 Windows 이벤트 로그를 수집할 수 있습니다.

데이터 정규화를 완전히 지원하려면 [ASIM(Azure Sentinel 정보 모델)](normalization.md) 파서가 설치된 이 커넥터를 사용하는 것이 좋습니다.

[Windows 전달된 이벤트 커넥터에](data-connectors-reference.md#windows-forwarded-events-preview)대해 자세히 알아봅니다.

### <a name="near-real-time-nrt-threat-detection-rules-now-available-public-preview"></a>NRT(근 실시간) 위협 탐지 규칙 사용 가능(공개 미리 보기)

보안 위협에 직면하는 경우 시간과 속도가 가장 핵심입니다. 위협이 구체화될 때 위협을 파악하여 신속하게 분석하고 대응하여 포함할 수 있어야 합니다. Microsoft Sentinel의 NRT(근 실시간) 분석 규칙은 온-프레미스 SIEM에 더 가까운 빠른 위협 탐지와 특정 시나리오에서 응답 시간을 단축하는 기능을 제공합니다.

Microsoft Sentinel의 [거의 실시간 분석 규칙은](detect-threats-built-in.md#nrt) 가장 가까운 시간 동안 위협 탐지 기능을 제공합니다. 이 유형의 규칙은 단 1분 간격으로 쿼리를 실행하여 응답성이 뛰어나도록 설계되었습니다.

[NRT 규칙](near-real-time-rules.md) 및 사용 방법에 대해 자세히 알아봅니다. [](create-nrt-rules.md)

### <a name="fusion-engine-now-detects-emerging-and-unknown-threats-public-preview"></a>Fusion 엔진은 이제 새로운 위협과 알 수 없는 위협을 감지합니다(공개 미리 보기).

[미리 정의된 시나리오에](fusion-scenario-reference.md)따라 공격을 검색하는 것 외에도 Microsoft Sentinel의 ML 기반 Fusion 엔진은 확장된 ML 분석을 적용하고 광범위한 비정상 신호 범위의 상관 관계를 지정하여 사용자 환경에서 새로운 위협과 알 수 없는 위협을 찾는 데 도움이 될 수 있습니다.

Fusion 엔진의 ML 알고리즘은 지속적으로 기존 공격으로부터 학습하고 보안 분석가의 생각 방식에 따라 분석을 적용합니다. 따라서 환경 전체의 킬 체인에서 수백만 개의 비정상적인 동작에서 이전에 검색되지 않은 위협을 검색할 수 있으므로 공격자보다 한 단계 앞서 있는 데 도움이 됩니다.

[새로운 위협에 대한 Fusion에](fusion.md#fusion-for-emerging-threats)대해 자세히 알아보세요.

또한 Fusion 분석 규칙은 이제 향상된 기능을 반영하여 [더 구성이](configure-fusion-rules.md)가능해졌습니다.

### <a name="get-fine-tuning-recommendations-for-your-analytics-rules-public-preview"></a>분석 규칙에 대한 미세 조정 권장 사항 얻기(공개 미리 보기)

SIEM에서 위협 탐지 규칙을 미세 조정하는 것은 위협 탐지 범위를 극대화하고 가양성 비율을 최소화하는 것 사이의 균형을 조정하는 어려운 연속 프로세스일 수 있습니다. Microsoft Sentinel은 기계 학습을 사용하여 시간별 인시던트 대응뿐만 아니라 데이터 원본에서 수십억 개의 신호를 분석하고, 패턴을 추론하고, 튜닝 오버헤드를 크게 낮출 수 있는 실행 가능한 권장 사항 및 인사이트를 제공하여 실제 위협을 감지하고 대응하는 데 집중할 수 있도록 함으로써 이 프로세스를 간소화하고 간소화합니다.

이제 [튜닝 권장 사항 및 인사이트가](detection-tuning.md) 분석 규칙에 기본 제공되었습니다.

### <a name="free-trial-updates"></a>평가판 업데이트

Microsoft Sentinel의 평가판은 처음 31일 동안 추가 비용 없이 신규 또는 기존 Log Analytics 작업 영역을 계속 지원합니다.
다음 업데이트를 포함하도록 현재 무료 평가판 환경을 발전시키고 있습니다.

- **새 Log Analytics 작업 영역은** 처음 31일 동안 최대 10GB/일의 로그 데이터를 무료로 수집할 수 있습니다. 새 작업 영역에는 3일 미만의 작업 영역이 포함됩니다.

   Log Analytics 데이터 수집 및 Microsoft Sentinel 요금은 모두 31일 평가 기간 동안 청구됩니다. 이 무료 평가판에는 Azure 테넌트당 20개 작업 영역 제한이 적용됩니다.

- **기존 Log Analytics 작업 영역은** 추가 비용 없이 Microsoft Sentinel을 사용하도록 설정할 수 있습니다. 기존 작업 영역에는 3일 전에 만든 모든 작업 영역이 포함됩니다.

   31일 평가 기간 동안에는 Microsoft Sentinel 요금만 청구됩니다.

이러한 제한을 초과하는 사용량은 [Microsoft Sentinel 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel) 페이지에 나열된 가격 책정에 따라 요금이 청구됩니다. [자동화](automation.md) 및 [사용자 고유의 기계 학습을 위한](bring-your-own-ml.md) 추가 기능과 관련된 요금은 평가판 중에 계속 적용됩니다.

> [!TIP]
> 평가판 중에 Microsoft Sentinel의 뉴스 & 가이드 **> 무료 평가판** 탭에서 비용 관리, 교육 등에 대한 리소스를 찾습니다. 이 탭에는 평가판 날짜 및 만료될 때까지 남은 날짜에 대한 세부 정보도 표시됩니다.
>

자세한 내용은 [Microsoft Sentinel에 대한 비용 계획 및 관리를 참조하세요.](billing.md)

### <a name="content-hub-and-new-solutions-public-preview"></a>콘텐츠 허브 및 새 솔루션(공개 미리 보기)

Microsoft Sentinel은 이제 Microsoft Sentinel 기본 제공 콘텐츠 및 솔루션을 찾아 Microsoft Sentinel 작업 영역에 배포하기 위한 중앙 집중식 위치인 콘텐츠 **허브를** 제공합니다. 콘텐츠 형식을 필터링하여 필요한 콘텐츠를 찾거나, 모델, 범주 등을 지원하거나, 강력한 텍스트 검색을 사용합니다.

**콘텐츠 관리에서** **콘텐츠 허브를** 선택합니다. 오른쪽에서 자세한 내용을 볼 솔루션을 선택하고 **설치를** 클릭하여 작업 영역에 설치합니다.

:::image type="content" source="media/whats-new/solutions-list.png" alt-text="새 Microsoft Sentinel 콘텐츠 허브의 스크린샷." lightbox="media/whats-new/solutions-list.png":::

다음 목록에는 콘텐츠 허브에 추가된 새로운 주요 솔루션이 포함되어 있습니다.

:::row:::
   :::column span="":::
      - Microsoft Sentinel 교육 랩
      - Cisco ASA
      - Cisco Duo 보안
      - Cisco Meraki
      - Cisco StealthWatch
      - 디지털 보호자
      - 365 Dynamics
      - GCP 클라우드 DNS
   :::column-end:::
   :::column span="":::
      - GCP CloudMonitor
      - GCP ID 및 액세스 관리
      - ForceForce
      - FireEye NX
      - 2016년 3월 2
      - Forescout
      - Fortinet Fortigate
      - Imperva CloudSERS
   :::column-end:::
   :::column span="":::
      - 참가자 위험 관리
      - IronNet CyberSecurity Iron Defense
      - Lookout
      - McAfee 네트워크 보안 플랫폼
      - Microsoft MITRE ATT&CK Solution for Cloud
      - Palo Alto PAN-OS
   :::column-end:::
   :::column span="":::
      - Rapid7 Nexpose/Insight VM
      - ReversingLabs
      - RSA SecurID
      - Semperis
      - Tenable Nessus Scanner
      - Vectra Stream
      - 제로 트러스트
   :::column-end:::
:::row-end:::

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel 솔루션 정보](sentinel-solutions.md)
- [Microsoft Sentinel 솔루션 검색 및 배포](sentinel-solutions-deploy.md)
- [Microsoft Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)

### <a name="enable-continuous-deployment-from-your-content-repositories-public-preview"></a>콘텐츠 리포지토리에서 지속적인 배포 사용(공개 미리 보기)

새 Microsoft Sentinel **리포지토리** 페이지는 Azure Portal 관리하는 대신 GitHub 또는 Azure DevOps 리포지토리에서 사용자 지정 콘텐츠를 관리하고 배포하는 기능을 제공합니다. 이 기능은 Microsoft Sentinel 작업 영역에서 콘텐츠를 관리하고 배포하기 위한 보다 간소화되고 자동화된 접근 방식을 도입합니다.

Microsoft Sentinel 외부에서 유지 관리하기 위해 사용자 지정 콘텐츠를 외부 리포지토리에 저장하는 경우 이제 해당 리포지토리를 Microsoft Sentinel 작업 영역에 연결할 수 있습니다. 리포지토리에서 추가, 만들기 또는 편집하는 콘텐츠는 Microsoft Sentinel 작업 영역에 자동으로 배포되며 분석, **헌팅** 또는 통합 문서 페이지와 같은 다양한 Microsoft Sentinel 갤러리에서 볼 수 **있습니다.**

자세한 내용은 [리포지토리에서 사용자 지정 콘텐츠 배포를 참조하세요.](ci-cd.md)

### <a name="enriched-threat-intelligence-with-geolocation-and-whois-data-public-preview"></a>지리적 위치 및 WhoIs 데이터를 통해 보강된 위협 인텔리전스(공개 미리 보기)

이제 데이터 커넥터 및 논리 앱 플레이북을 통해 Microsoft Sentinel로 가져오거나 Microsoft Sentinel에서 만드는 위협 인텔리전스 데이터는 GeoLocation 및 WhoIs 정보로 자동으로 보강됩니다.

GeoLocation 및 WhoIs 데이터는 선택한 IOC(손상 지표)가 발견된 조사를 위해 더 많은 컨텍스트를 제공할 수 있습니다.

예를 들어 GeoLocation 데이터를 사용하여 지표에 대한 *조직* 또는 *국가* 및 WhoIs 데이터와 같은 세부 정보를 찾아 *등록자* 및 *레코드 만들기* 데이터와 같은 데이터를 찾습니다.

Microsoft Sentinel로 가져온 손상의 각 지표에 대한 **위협 인텔리전스** 창에서 GeoLocation 및 WhoIs 데이터를 볼 수 있습니다. 지표에 대한 세부 정보는 사용 가능한 지리적 위치 및 WhoIs 데이터를 포함하여 오른쪽에 표시됩니다.

다음은 그 예입니다.

:::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="GeoLocation 및 WhoIs 데이터를 포함한 표시기 세부 정보의 스크린샷." lightbox="media/whats-new/geolocation-whois-ti.png":::

> [!TIP]
> 지리적 위치 및 WhoIs 정보는 Microsoft 위협 인텔리전스 서비스에서 제공하며, API를 통해 액세스할 수도 있습니다. 자세한 내용은 [API를 통해 지리적 위치 데이터로 엔터티 보강을](geolocation-data-api.md)참조하세요.
>

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 위협 인텔리전스 이해](understand-threat-intelligence.md)
- [위협 인텔리전스 통합](threat-intelligence-integration.md)
- [Microsoft Sentinel에서 위협 지표 작업](work-with-threat-indicators.md)
- [위협 인텔리전스 플랫폼 연결](connect-threat-intelligence-tip.md)

### <a name="use-notebooks-with-azure-synapse-analytics-in-microsoft-sentinel-public-preview"></a>Microsoft Sentinel에서 Azure Synapse Analytics Notebook 사용(공개 미리 보기)

이제 Microsoft Sentinel은 대규모 보안 분석 시나리오에 대한 Azure Synapse Jupyter Notebook을 통합합니다.

지금까지 Microsoft Sentinel의 Jupyter Notebook은 Azure Machine Learning 통합되었습니다. 이 기능은 Notebook, 인기 있는 오픈 소스 기계 학습 도구 키트 및 TensorFlow와 같은 라이브러리뿐만 아니라 고유한 사용자 지정 모델을 보안 워크플로에 통합하려는 사용자를 지원합니다.

새 Azure Synapse 통합은 다음과 같은 추가 분석 마력을 제공합니다.

- **비용** 최적화, 완전 관리형 Azure Synapse Apache Spark 컴퓨팅 풀을 사용하는 빅 데이터 분석 보안

- **비용 효율적인 Data Lake 액세스를** 통해 Azure Blob Storage 기반으로 구축된 빅 데이터 분석 전용 기능 집합인 Azure Data Lake Storage Gen2를 통해 기록 데이터에 대한 분석을 빌드할 수 있습니다.

- 데이터 원본을 여러 원본 및 형식의 보안 작업 워크플로에 **유연하게 통합할** 수 있습니다.

- Python과 함께 Spark 프레임워크를 사용하기 위한 **Python 기반 API인 PySpark를** 사용하면 Python에 이미 익숙한 경우 새 프로그래밍 언어를 배울 필요가 줄어듭니다.

이 통합을 지원하기 위해 Microsoft Sentinel에서 직접 Azure Synapse 작업 영역을 만들고 시작하는 기능을 추가했습니다. 또한 Azure Synapse 환경을 구성하고, Log Analytics에서 Azure Data Lake Storage 연속 데이터 내보내기 파이프라인을 설정하고, 대규모로 해당 데이터를 헌팅하는 과정을 안내하는 새로운 샘플 Notebook을 추가했습니다.

자세한 내용은 [notebooks와 Azure Synapse 통합을](notebooks-with-synapse.md)참조하세요.

### <a name="enhanced-notebooks-area-in-microsoft-sentinel"></a>Microsoft Sentinel의 향상된 Notebook 영역

이제 Microsoft Sentinel의 **Notebooks** 영역에는 **Notebook에** 대한 기본 정보를 찾을 수 있는 개요 탭과 표시되는 각 **Notebook의 유형을** 나타내는 **템플릿** 탭의 새 Notebook 유형 열이 있습니다. 예를 들어 Notebook에는 **시작,** **구성**, **헌팅** 및 **Synapse** 형식이 있을 수 있습니다.

다음은 그 예입니다.

:::image type="content" source="media/whats-new/notebooks-synapse.png" alt-text="Notebook 페이지의 새 Azure Synapse 기능 스크린샷." lightbox="media/whats-new/notebooks-synapse.png":::

자세한 내용은 [Jupyter Notebook을 사용하여 보안 위협 헌트 를](notebooks.md)참조하세요.

### <a name="microsoft-sentinel-renaming"></a>Microsoft Sentinel 이름 바꾸기

2021년 11월부터 Azure Sentinel 이름이 Microsoft Sentinel로 바뀌고 포털, 설명서 및 기타 리소스에 예정된 업데이트가 병렬로 표시됩니다.

이 문서의 이전 항목과 [Sentinel의 새로운](whats-new-archive.md) 기능에 대한 이전 보관 파일은 해당 기능이 새로 제공되었을 때 서비스 이름처럼 *Azure* Sentinel이라는 이름을 계속 사용합니다.

자세한 내용은 최근 [보안 향상에 대한 블로그를 참조하세요.](https://aka.ms/secblg11)

### <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel"></a>Azure Sentinel Azure Key Vault honeytoken 배포 및 모니터링

새로운 **Azure Sentinel Deception** 솔루션은 선택된 Azure Key Vault에 *honeytoken이라는* 디코이키 및 비밀을 배포하는 데 도움을 줌으로써 키 자격 증명 모음에서 악의적인 활동을 감시하는 데 도움이 됩니다.

배포되면 honeytoken 키와 비밀을 포함하는 액세스 또는 작업은 Azure Sentinel 조사할 수 있는 인시던트 생성합니다.

실제로 Honeytoken 키와 비밀을 사용할 이유가 없으므로 작업 영역의 유사한 활동은 악의적일 수 있으며 조사해야 합니다.

**Azure Sentinel Deception** 솔루션에는 대규모 또는 한 번에 하나씩 허니 토큰을 배포하는 데 도움이 되는 통합 문서, 생성된 Honeytoken을 추적하는 감시 목록 및 필요에 따라 인시던트 생성을 위한 분석 규칙이 포함되어 있습니다.

자세한 내용은 [Azure Sentinel(공개 미리 보기)를 Azure Key Vault honeytoken 배포 및 모니터링을](monitor-key-vault-honeytokens.md)참조하세요.

## <a name="october-2021"></a>2021년 10월

- [GA에서 Azure Monitor 에이전트를 사용하는 Windows 보안 이벤트 커넥터](#windows-security-events-connector-using-azure-monitor-agent-now-in-ga)
- [defender for Office 365 이벤트는 이제 Microsoft 365 Defender 커넥터에서 사용할 수 있습니다(공개 미리 보기).](#defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview)
- [플레이북 템플릿 및 갤러리 사용 가능(공개 미리 보기)](#playbook-templates-and-gallery-now-available-public-preview)
- [예약된 분석 규칙에 대한 템플릿 버전 관리(공개 미리 보기)](#manage-template-versions-for-your-scheduled-analytics-rules-public-preview)
- [DHCP 정규화 스키마(공개 미리 보기)](#dhcp-normalization-schema-public-preview)

### <a name="windows-security-events-connector-using-azure-monitor-agent-now-in-ga"></a>GA에서 Azure Monitor 에이전트를 사용하는 Windows 보안 이벤트 커넥터

이제 Azure Monitor 에이전트를 기반으로 하는 새 버전의 Windows 보안 Events 커넥터가 일반 제공됩니다. 자세한 내용은 [Windows 서버에 대한 커넥트 참조하여 보안 이벤트를 수집합니다.](connect-windows-security-events.md?tabs=AMA)

### <a name="defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview"></a>defender for Office 365 이벤트는 이제 Microsoft 365 Defender 커넥터에서 사용할 수 있습니다(공개 미리 보기).

엔드포인트용 Microsoft Defender의 이벤트 외에도 이제 [Microsoft 365 Defender 커넥터](connect-microsoft-365-defender.md)를 통해 [Office 365 위해 Microsoft Defender에서](/microsoft-365/security/office-365-security/overview) 원시 [고급 헌팅 이벤트를](/microsoft-365/security/defender/advanced-hunting-overview) 수신할 수 있습니다. [자세히 알아보기](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

### <a name="playbook-templates-and-gallery-now-available-public-preview"></a>플레이북 템플릿 및 갤러리 사용 가능(공개 미리 보기)

플레이북 템플릿은 요구 사항에 맞게 사용자 지정할 수 있는 미리 빌드되고 테스트되고 즉시 사용할 수 있는 워크플로입니다. 템플릿은 플레이북을 처음부터 개발할 때 모범 사례에 대한 참조로 사용하거나 새로운 자동화 시나리오에 대한 영감을 제공할 수도 있습니다.

플레이북 템플릿은 Sentinel 커뮤니티, ISV(독립 소프트웨어 공급업체) 및 Microsoft 자체 전문가에 의해 개발되었으며, **플레이북 템플릿** 탭(Automation 아래), [Azure Sentinel 솔루션의](sentinel-solutions.md)일부로 또는 [Azure Sentinel GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)찾을 수 있습니다. 

자세한 내용은 [기본 제공 템플릿에서 플레이북 만들기 및 사용자 지정을 참조하세요.](use-playbook-templates.md)

### <a name="manage-template-versions-for-your-scheduled-analytics-rules-public-preview"></a>예약된 분석 규칙에 대한 템플릿 버전 관리(공개 미리 보기)

[기본 제공 Azure Sentinel 규칙 템플릿에서](detect-threats-built-in.md)분석 규칙을 만들 때 템플릿의 복사본을 효과적으로 만듭니다. 이 시점을 지나면 활성 규칙이 원래 템플릿에 적용된 변경 내용과 일치하도록 동적으로 ***업데이트되지 않습니다.***

그러나 템플릿에서 만든 규칙은  두 가지 이점을 제공하는 템플릿을 기억합니다.

- 템플릿에서 규칙을 만들 때(또는 그 이후 언제든지) 규칙을 변경한 경우 언제든지 규칙을 원래 버전(템플릿의 복사본)으로 되돌릴 수 있습니다.

- 템플릿이 업데이트되면 알림을 받을 수 있으며, 규칙을 새 버전의 템플릿으로 업데이트하거나 그대로 둘 수 있습니다.

[이러한 작업을 관리하는 방법](manage-analytics-rule-templates.md)및 유의해야 할 사항을 알아봅니다. 이러한 절차는 템플릿에서 만든 [예약된](detect-threats-built-in.md#scheduled) 분석 규칙에 적용됩니다.

### <a name="dhcp-normalization-schema-public-preview"></a>DHCP 정규화 스키마(공개 미리 보기)

ASIM(Advanced SIEM Information Model)은 이제 DHCP 정규화 스키마를 지원합니다. 이 스키마는 DHCP 서버에서 보고하는 이벤트를 설명하는 데 사용되며 Azure Sentinel 소스와 관계 없는 분석을 사용하도록 설정하는 데 사용됩니다. 

DHCP 정규화 스키마에 설명된 이벤트에는 클라이언트 시스템에서 임대된 DHCP IP 주소에 대한 요청 제공 및 부여된 임대로 DNS 서버 업데이트가 포함됩니다.

자세한 내용은 다음을 참조하세요.

- [DHCP 정규화 스키마 참조 Azure Sentinel(공개 미리 보기)](dhcp-normalization-schema.md)
- [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)

## <a name="september-2021"></a>2021년 9월

- [문서의 새로운 항목: 데이터 커넥터 스케일링 설명서](#new-in-docs-scaling-data-connector-documentation)
- [Azure Storage 계정 커넥터 변경 내용](#azure-storage-account-connector-changes)

### <a name="new-in-docs-scaling-data-connector-documentation"></a>문서의 새로운 항목: 데이터 커넥터 스케일링 설명서

Azure Sentinel용 기본 제공 데이터 커넥터를 점점 더 많이 계속 추가하면서 이 스케일링을 반영하도록 데이터 커넥터 설명서를 다시 구성했습니다.

대부분 데이터 커넥터의 경우 개별 커넥터를 설명하는 전체 문서를 일련의 일반 절차와 현재 지원되는 모든 커넥터의 전체 참조로 바꾸었습니다.

관련 일반 절차에 대한 참조와 필요한 추가 정보, 구성을 포함하여 커넥터에 관한 자세한 내용은 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md)를 확인하세요.

자세한 내용은 다음을 참조하세요.

- **개념 정보**: [데이터 원본 연결](connect-data-sources.md)

- **일반 방법 문서**:

   - [Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md)
   - [데이터 원본을 Azure 센티널 데이터 수집기 API에 커넥트 하 여 데이터 수집](connect-rest-api-template.md)
   - [디바이스 또는 어플라이언스에서 Azure Sentinel로 CEF 형식 로그 가져오기](connect-common-event-format.md)
   - [Syslog를 사용하여 Linux 기반 소스에서 데이터 수집](connect-syslog.md)
   - [Log Analytics 에이전트를 사용하여 Azure Sentinel에 사용자 지정 로그 형식의 데이터 수집](connect-custom-logs.md)
   - [Azure Functions를 사용하여 Azure Sentinel에 데이터 원본 연결](connect-azure-functions-template.md)
   - [Azure Sentinel 사용자 지정 커넥터를 만들기 위한 리소스](create-custom-connector.md)

### <a name="azure-storage-account-connector-changes"></a>Azure Storage 계정 커넥터 변경 내용

Azure Storage 계정 리소스 구성 자체 내에서 변경된 내용으로 인해 커넥터도 다시 구성해야 합니다.
스토리지 계정(부모) 리소스에는 파일, 테이블, 큐 및 Blob과 같은 각 스토리지 유형에 대한 다른(자식) 리소스가 있습니다.

스토리지 계정에 대한 진단을 구성할 때 다음을 차례로 선택하고 구성해야 합니다.
- 상위 계정 리소스, **트랜잭션** 메트릭 내보내기
- 모든 로그 및 메트릭을 내보내는 각 하위 스토리지 유형 리소스(위 표 참조)

실제로 리소스를 정의한 스토리지 유형만 표시됩니다.

:::image type="content" source="media/whats-new/storage-diagnostics.png" alt-text="Azure Storage 진단 구성의 스크린샷":::

## <a name="august-2021"></a>2021년 8월

- [고급 인시던트 검색(퍼블릭 미리 보기)](#advanced-incident-search-public-preview)
- [랜섬웨어에 대한 퓨전 검색(퍼블릭 미리 보기)](#fusion-detection-for-ransomware-public-preview)
- [UEBA 데이터용 관심 목록 템플릿](#watchlist-templates-for-ueba-data-public-preview)
- [파일 이벤트 정규화 스키마(퍼블릭 미리 보기)](#file-event-normalization-schema-public-preview)
- [문서의 새로운 내용: 모범 사례 지침](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>고급 인시던트 검색(퍼블릭 미리 보기)

기본적으로 인시던트 검색은 **인시던트 ID**, **제목**, **태그**, **소유자**, **제품 이름** 값에서만 실행됩니다. 이제 Azure Sentinel에서 경고 세부 정보, 설명, 엔터티, 전술 등을 포함하여 더 많은 데이터를 검색할 수 있는 [고급 검색 옵션](investigate-cases.md#search-for-incidents)을 제공합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="인시던트 페이지 고급 검색 옵션 스크린샷":::

자세한 내용은 [인시던트 검색](investigate-cases.md#search-for-incidents)을 참조하세요.

### <a name="fusion-detection-for-ransomware-public-preview"></a>랜섬웨어에 대한 퓨전 검색(퍼블릭 미리 보기)

이제 Azure Sentinel에서 가능한 랜섬웨어 활동에 대한 새로운 퓨전 검색을 제공하여 **랜섬웨어 활동과 관련된 여러 경고가 검색됨** 이라는 인시던트를 생성합니다.

특정 시간 프레임 동안 발생하고 공격의 실행 및 방어 회피 스테이지와 연결된 경우 랜섬웨어 활동과 관련된 경고에 대해 인시던트가 생성됩니다. 인시던트에 나열된 경고를 사용하여 공격자가 호스트/디바이스를 손상시키고 검색을 회피하기 위해 사용할 수 있는 기술을 분석할 수 있습니다.

지원되는 데이터 커넥터는 다음과 같습니다.

- [Azure Defender(Azure Security Center)](connect-defender-for-cloud.md)
- [엔드포인트에 대한 Microsoft Defender](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Azure Sentinel 예약된 분석 규칙](detect-threats-built-in.md#scheduled).

자세한 내용은 [랜섬웨어 활동과 관련된 여러 경고가 검색됨](fusion.md#fusion-for-ransomware)을 참조하세요.

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>UEBA 데이터용 관심 목록 템플릿(퍼블릭 미리 보기)

이제 Azure Sentinel에서 사용자 환경에 맞게 사용자 지정하고 조사 중에 사용할 수 있는 UEBA 데이터용 기본 제공 관심 목록 템플릿을 제공합니다.

UEBA 관심 목록이 데이터로 채워진 후에는 해당 데이터와 분석 규칙 간 상관 관계를 지정하고, 인사이트 같은 엔터티 페이지와 조사 그래프에 표시하고, VIP 또는 중요한 사용자 추적 등의 사용자 지정 용도를 만들 수 있습니다.

현재 관심 목록 템플릿은 다음과 같습니다.

- **VIP 사용자**. 조직에 큰 영향을 주는 직원의 사용자 계정 목록입니다.
- **해고된 직원**. 해고되었거나 해고될 직원의 사용자 계정 목록입니다.
- **서비스 계정**. 서비스 계정과 해당 소유자 목록입니다.
- **ID 상관 관계**. 동일한 사람에게 속한 관련 사용자 계정 목록입니다.
- **높은 가치 자산**. 조직에서 중요한 가치가 있는 디바이스, 리소스 또는 기타 자산 목록입니다.
- **네트워크 매핑**. IP 서브넷과 해당 조직 컨텍스트 목록입니다.

자세한 내용은 [템플릿을 사용하여 새 관심 목록 만들기](watchlists.md#create-a-new-watchlist-using-a-template-public-preview) 및 [기본 제공 관심 목록 스키마](watchlist-schemas.md)를 참조하세요.



### <a name="file-event-normalization-schema-public-preview"></a>파일 이벤트 정규화 스키마(퍼블릭 미리 보기)

이제 ASIM(Azure Sentinel 정보 모델)에서 파일이나 문서 만들기, 수정 또는 삭제와 같은 파일 작업을 설명하는 데 사용되는 파일 이벤트 정규화 스키마를 지원합니다. 파일 이벤트는 운영 체제, 파일 스토리지 시스템(예: Azure Files) 및 문서 관리 시스템(예: Microsoft SharePoint)에서 보고됩니다.

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 파일 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](file-event-normalization-schema.md)
- [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>문서의 새로운 내용: 모범 사례 지침

고객과 지원 팀의 여러 요청에 대한 응답으로 설명서에 일련의 모범 사례 지침을 추가했습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel을 배포하기 위한 필수 조건](prerequisites.md)
- [Azure Sentinel 모범 사례](best-practices.md)
- [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)
- [Azure Sentinel 작업 영역 아키텍처 디자인](design-your-workspace-architecture.md)
- [Azure Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md)
- [데이터 수집 모범 사례](best-practices-data.md)

> [!TIP]
> 관련 개념 및 방법 문서에서 설명서에 추가된 자세한 지침을 확인할 수 있습니다. 자세한 내용은 [모범 사례 참조](best-practices.md#best-practice-references)를 참조 하세요.
>

## <a name="july-2021"></a>2021년 7월

- [Microsoft 위협 인텔리전스 일치 분석(퍼블릭 미리 보기)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Azure Sentinel IdentityInfo 테이블과 함께 Azure AD 데이터 사용(퍼블릭 미리 보기)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [API를 통해 지리적 위치 데이터로 엔터티 보강(퍼블릭 미리 보기)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [ADX 리소스 간 쿼리 지원(퍼블릭 미리 보기)](#support-for-adx-cross-resource-queries-public-preview)
- [관심 목록 일반 공급](#watchlists-are-in-general-availability)
- [더 많은 지역에서 데이터 보존 지원](#support-for-data-residency-in-more-geos)
- [Azure Defender 커넥터의 양방향 동기화(퍼블릭 미리 보기)](#bidirectional-sync-in-azure-defender-connector-public-preview)

### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Microsoft 위협 인텔리전스 일치 분석(퍼블릭 미리 보기)

이제 Azure Sentinel에서 Microsoft가 생성한 위협 인텔리전스 데이터를 로그와 대조하는 기본 제공 **Microsoft 위협 인텔리전스 일치 분석** 규칙을 제공합니다. 이 규칙은 검색된 로그 컨텍스트에 따라 적절한 심각도로 고충실도의 경고와 인시던트를 생성합니다. 일치 항목이 검색되면 Azure Sentinel 위협 인텔리전스 리포지토리에도 표시기가 게시됩니다.

**Microsoft 위협 인텔리전스 일치 분석** 규칙은 현재 다음 로그 원본에서 일치하는 도메인 표시기를 검색합니다.

- [CEF](connect-common-event-format.md)
- [DNS](./data-connectors-reference.md#windows-dns-server-preview)
- [Syslog](connect-syslog.md)

자세한 내용은 [일치 분석을 사용하여 위협 탐지(퍼블릭 미리 보기)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview)를 참조하세요.

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Azure Sentinel IdentityInfo 테이블과 함께 Azure AD 데이터 사용(퍼블릭 미리 보기)

공격자가 조직의 사용자 및 서비스 계정을 사용하는 경우가 많기 때문에 조사 과정에서 사용자 ID와 권한을 비롯한 사용자 계정 데이터가 분석가에게 매우 중요합니다.

이제 Azure Sentinel 작업 영역에서 [UEBA를 사용하도록 설정](enable-entity-behavior-analytics.md)하면 Azure AD 데이터가 Log Analytics의 새 **IdentityInfo** 테이블에도 동기화됩니다. Azure AD와 **IdentifyInfo** 테이블 간 동기화는 사용자 메타데이터, 그룹 정보, 각 사용자에게 할당된 Azure AD 역할을 포함하는 사용자 프로필 데이터 스냅샷을 만듭니다.

**IdentityInfo** 테이블은 조사 중에 사용될 뿐 아니라 가양성을 줄이기 위해 분석 규칙을 조직에 맞게 미세 조정하는 경우에도 사용됩니다.

자세한 내용은 UEBA 보강 참조의 [IdentityInfo 테이블](ueba-enrichments.md#identityinfo-table-public-preview) 및 [UEBA 데이터를 사용하여 가양성 분석](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)을 참조하세요.

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>API를 통해 지리적 위치 데이터로 엔터티 보강(퍼블릭 미리 보기)

이제 Azure Sentinel에서 지리적 위치 정보를 사용하여 데이터를 보강하는 API를 제공합니다. 그런 다음, 지리적 위치 데이터를 사용하여 보안 인시던트를 분석하고 조사할 수 있습니다.

자세한 내용은 [REST API를 통해 지리적 위치 데이터를 사용하여 Azure Sentinel에서 엔터티 보강(퍼블릭 미리 보기)](geolocation-data-api.md)과 [Azure Sentinel에서 엔터티를 사용하여 데이터 분류 및 분석](entities.md)을 참조하세요.


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>ADX 리소스 간 쿼리 지원(퍼블릭 미리 보기)

이제 Azure Sentinel의 헌팅 환경에서 [ADX 리소스 간 쿼리](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer)를 지원합니다.
 
Log Analytics가 Azure Sentinel을 사용한 분석을 위한 기본 데이터 스토리지 위치로 계속 사용되지만 비용, 보존 기간 또는 기타 요인으로 인해 ADX가 데이터를 저장하는 데 필요한 경우가 있습니다. 이 기능을 사용하면 고객이 더 광범위한 데이터 세트를 헌팅하고 헌팅 쿼리, [Livestream](livestream.md), Log Analytics 검색 페이지를 비롯한 [Azure Sentinel 헌팅 환경](hunting.md)에서 결과를 볼 수 있습니다.

ADX 클러스터에 저장된 데이터를 쿼리하려면 adx() 함수를 사용하여 ADX 클러스터, 데이터베이스 이름, 원하는 테이블을 지정합니다. 그런 다음, 다른 테이블처럼 출력을 쿼리할 수 있습니다. 자세한 내용은 위에 링크된 페이지를 참조하세요.




### <a name="watchlists-are-in-general-availability"></a>관심 목록 일반 공급

이제 [관심 목록](watchlists.md) 기능이 출시되었습니다. 관심 목록을 사용하여 비즈니스 데이터로 경고를 보강하고, 액세스 이벤트를 확인할 허용 목록이나 차단 목록을 만들고, 위협을 조사하고, 경고 피로를 줄일 수 있습니다.

### <a name="support-for-data-residency-in-more-geos"></a>더 많은 지역에서 데이터 보존 지원

이제 Azure Sentinel이

브라질, 노르웨이, 남아프리카 공화국, 대한민국, 독일, UAE(아랍에미리트), 스위스 등의 추가 지역에서 전체 데이터 보존을 지원합니다.

데이터 보존에 대한 자세한 내용은 [지원되는 지역의 전체 목록](quickstart-onboard.md#geographical-availability-and-data-residency)을 참조하세요.

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Azure Defender 커넥터의 양방향 동기화(퍼블릭 미리 보기)

이제 Azure Defender 커넥터가 Defender와 Azure Sentinel 간에 경고 상태의 양방향 동기화를 지원합니다. Defender 경고가 포함된 Sentinel 인시던트를 닫으면 Defender 포털에서도 해당 경고가 자동으로 닫힙니다.

[업데이트된 Azure Defender 커넥터에 대한 자세한 설명](connect-defender-for-cloud.md)을 참조하세요.

## <a name="june-2021"></a>2021년 6월

- [정규화 및 Azure Sentinel 정보 모델 업그레이드](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [업데이트된 서비스 간 커넥터](#updated-service-to-service-connectors)
- [분석 규칙 내보내기 및 가져오기(퍼블릭 미리 보기)](#export-and-import-analytics-rules-public-preview)
- [경고 보강: 경고 세부 정보(퍼블릭 미리 보기)](#alert-enrichment-alert-details-public-preview)
- [플레이북에 대한 추가 도움말](#more-help-for-playbooks)
- [새 설명서 재구성](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>정규화 및 Azure Sentinel 정보 모델 업그레이드

Azure Sentinel 정보 모델을 사용하면 원본에 관계없이 콘텐츠를 사용하고 만들 수 있으므로 Azure Sentinel 작업 영역의 데이터 분석이 간소화됩니다.

이번 달 업데이트에서는 정규화 설명서를 개선하여 새로운 수준의 세부 정보와 전체 DNS, 프로세스 이벤트, 인증 정규화 스키마를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)(업데이트됨)
- [Azure Sentinel 인증 정규화 스키마 참조(퍼블릭 미리 보기)](authentication-normalization-schema.md)(신규)
- [Azure Sentinel 데이터 정규화 스키마 참조](./network-normalization-schema.md)
- [Azure Sentinel DNS 정규화 스키마 참조(퍼블릭 미리 보기)](dns-normalization-schema.md)(신규)
- [Azure Sentinel 프로세스 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](process-events-normalization-schema.md)(신규)
- [Azure Sentinel 레지스트리 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](registry-event-normalization-schema.md)(신규)


### <a name="updated-service-to-service-connectors"></a>업데이트된 서비스 간 커넥터

가장 많이 사용되는 두 가지 커넥터가 주요 업그레이드의 수혜자가 되었습니다.

- [Windows 보안 이벤트 커넥터(퍼블릭 미리 보기)](connect-windows-security-events.md)는 이제 새 AMA(Azure Monitor 에이전트)를 기반으로 하므로 수집할 데이터를 훨씬 더 유연하게 선택하고 최소 비용으로 가시성을 최대화할 수 있습니다.

- [Azure 활동 로그 커넥터](./data-connectors-reference.md#azure-activity)는 이제 진단 설정 파이프라인을 기반으로 하므로 더 완벽한 데이터가 제공되고 수집 지연이 훨씬 감소하며 성능과 안정성이 향상됩니다.

업그레이드가 자동으로 수행되지는 않습니다. 해당 커넥터의 사용자는 새 버전을 사용하도록 설정하는 것이 좋습니다.

### <a name="export-and-import-analytics-rules-public-preview"></a>분석 규칙 내보내기 및 가져오기(퍼블릭 미리 보기)

이제 Azure Sentinel 배포를 코드로 관리하고 제어하는 과정의 일환으로, 분석 규칙을 JSON 형식의 ARM(Azure Resource Manager) 템플릿 파일로 내보내고 해당 파일에서 규칙을 가져올 수 있습니다. **예약됨** 뿐만 아니라 모든 유형의 [분석 규칙](detect-threats-built-in.md)을 ARM 템플릿으로 내보낼 수 있습니다. 템플릿 파일에는 쿼리부터 할당된 MITRE ATT&CK 전술에 이르기까지 규칙의 모든 정보가 포함됩니다.

자세한 내용은 [ARM 템플릿에서 분석 규칙 내보내기 및 가져오기](import-export-analytics-rules.md)를 참조하세요.

### <a name="alert-enrichment-alert-details-public-preview"></a>경고 보강: 경고 세부 정보(퍼블릭 미리 보기)

이제 엔터티 매핑과 사용자 지정 세부 정보로 경고 콘텐츠를 보강하는 것뿐 아니라 특정 콘텐츠에 따라 경고와 인시던트가 제공 및 표시되는 방식을 사용자 지정할 수도 있습니다. 다른 경고 보강 기능과 마찬가지로 이 기능도 [분석 규칙 마법사](detect-threats-custom.md)에서 구성할 수 있습니다.

자세한 내용은 [Azure Sentinel에서 경고 세부 정보 사용자 지정](customize-alert-details.md)을 참조하세요.


### <a name="more-help-for-playbooks"></a>플레이북에 대한 추가 도움말

두 개의 새 문서는 플레이북 생성 및 사용을 시작하거나 익히는 데 도움이 될 수 있습니다.
- [Azure Sentinel에 플레이북 인증](authenticate-playbooks-to-sentinel.md)은 Logic Apps 기반 플레이북이 Azure Sentinel의 정보에 연결하고 액세스하는 데 사용하는 다양한 인증 방법과 각 인증 방법을 사용해야 하는 경우를 이해하는 데 도움이 됩니다.
- [플레이북에서 트리거 및 작업 사용](playbook-triggers-actions.md)에서는 **인시던트 트리거** 와 **경고 트리거** 의 차이점과 각 트리거를 사용해야 하는 경우를 설명하고, [사용자 지정 세부 정보](playbook-triggers-actions.md#work-with-custom-details)의 정보에 액세스하는 방법을 포함하여 인시던트에 대한 응답으로 플레이북에서 수행할 수 있는 다양한 작업 중 몇 가지를 보여 줍니다.

플레이북 설명서는 다중 테넌트 MSSP 시나리오에 대해서도 명시적으로 다룹니다.

### <a name="new-documentation-reorganization"></a>새 설명서 재구성

이번 달에는 일반적인 고객 경험을 따르는 직관적인 범주로 [Azure Sentinel 설명서](index.yml)를 재구성했습니다. 필터링된 문서 검색과 업데이트된 방문 페이지를 사용하여 Azure Sentinel 문서를 탐색할 수 있습니다.

:::image type="content" source="media/whats-new/new-docs.png" alt-text="새 Azure Sentinel 설명서 재구성" lightbox="media/whats-new/new-docs.png":::


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)
