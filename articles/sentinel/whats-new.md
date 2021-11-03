---
title: Azure Sentinel의 새로운 기능
description: 이 문서에서는 지난 몇 달간 Azure Sentinel의 새로운 기능을 설명합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 95753a40c60e6b191b768fa3ac9cae3c5e9cb466
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433273"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel의 새로운 기능

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에는 Azure Sentinel에 추가된 최신 기능과 Azure Sentinel에서 향상된 사용자 환경을 제공하는 관련 서비스의 새로운 기능이 나열되어 있습니다.

6개월 이상된 항목을 찾으려는 경우 [Azure Sentinel의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다. 이전에 제공된 기능에 대한 자세한 내용은 [기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)를 참조하세요.

> [!IMPORTANT]
> 언급된 기능은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Microsoft 위협 헌팅 팀에서는 [Azure Sentinel 커뮤니티](https://github.com/Azure/Azure-Sentinel)에 쿼리, 플레이북, 통합 문서, Notebook을 제공하고 있으며, 여기에는 여러분의 팀이 조정하여 사용할 수 있는 특정한 [헌팅 쿼리](https://github.com/Azure/Azure-Sentinel)가 포함됩니다.
>
> 여러분도 참여할 수 있습니다! [Azure Sentinel 위협 헌터 GitHub 커뮤니티](https://github.com/Azure/Azure-Sentinel/wiki)에 참여하세요.
>

## <a name="november-2021"></a>2021년 11월

### <a name="windows-forwarded-events-connector-now-available-public-preview"></a>Windows 이제 전달 된 이벤트 커넥터 사용 가능 (공개 미리 보기)

이제 새 데이터 커넥터 덕분에 Windows 이벤트 수집/Windows 이벤트 전달 (WEC/wef)을 사용 하 여 Azure 센티널 작업 영역에 연결 된 Windows 서버에서 이벤트 로그를 스트리밍할 수 있습니다. 커넥터는 새 Azure Monitor 에이전트 (AMA)를 사용 하 여 레거시 Log Analytics 에이전트 (MMA 라고도 함)에 비해 여러 가지 이점을 제공 합니다.
- **확장성:** wec (Windows 이벤트 수집)를 사용 하도록 설정한 경우에는 wec 컴퓨터에 Azure Monitor 에이전트 (AMA)를 설치 하 여 단일 연결 지점을 사용 하는 여러 서버에서 로그를 수집할 수 있습니다.

- **속도:** AMA는 5K EPS의 향상 된 속도로 데이터를 전송할 수 있으므로 데이터를 빠르게 새로 고칠 수 있습니다.

- **효율성:** AMA를 사용 하면 복잡 한 DCR (데이터 수집 규칙)를 디자인 하 여 원본에서 로그를 필터링 하 고 작업 영역에 스트리밍할 정확한 이벤트를 선택할 수 있습니다. Ecr은 원치 않는 이벤트를 종료 하 여 네트워크 트래픽 및 수집 비용을 낮춥니다.

- **검사:** WEC/wef를 사용 하면 레거시 (온-프레미스 및 실제) 서버에서의 Windows 이벤트 로그를 수집 하 고, 에이전트를 설치 하지 않아도 되는 도메인 컨트롤러와 같은 높은 사용량 또는 중요 한 컴퓨터 에서도 이벤트 로그를 수집할 수 있습니다. 

데이터 정규화를 완벽 하 게 지원 하기 위해 설치 된 [ASIM (Azure 센티널 Information Model)](normalization.md) 파서에서이 커넥터를 사용 하는 것이 좋습니다.

[전달 된 이벤트 Windows 커넥터](data-connectors-reference.md#windows-forwarded-events-preview)에 대해 자세히 알아보세요.

### <a name="near-real-time-nrt-threat-detection-rules-now-available-public-preview"></a>거의 실시간 (NRT) 위협 검색 규칙 사용 가능 (공개 미리 보기)

보안 위협에 직면 하는 경우 시간과 속도는 기본적으로입니다. 이를 신속 하 게 분석 하 고 대응할 수 있도록 구체화 될 때 위협을 인식 해야 합니다. Azure 센티널의 거의 실시간 (NRT) 분석 규칙은 온-프레미스 SIEM 및 특정 시나리오에서 응답 시간을 단축 하는 기능에 더 빠르게 위협 감지 기능을 제공 합니다.

Azure 센티널의 [거의 실시간 분석 규칙](detect-threats-built-in.md#nrt) 은 기본 제공 되는 최신 위협 검색 기능을 제공 합니다. 이 유형의 규칙은 1 분 간격으로 쿼리를 실행 하 여 응답성이 높은 응답성으로 설계 되었습니다.

[Nrt 규칙](near-real-time-rules.md) 및 [사용 방법](create-nrt-rules.md)에 대해 자세히 알아보세요.

### <a name="fusion-engine-now-detects-emerging-and-unknown-threats-public-preview"></a>이제 Fusion 엔진은 새롭게 알려지지 않은 위협 (공개 미리 보기)을 검색 합니다.

[미리 정의 된 시나리오](fusion-scenario-reference.md)를 기반으로 공격을 검색 하는 것 외에도, Azure 센티널의 ML 기반 Fusion 엔진은 확장 된 ML 분석을 적용 하 고 광범위 한 비정상 신호의 범위를 상호 연결 하 여 경고를 피로 하는 방식으로 사용자 환경에서 새로 발생 하 고 알 수 없는 위협을 찾는 데 도움이 될 수 있습니다.

Fusion 엔진의 ML 알고리즘은 기존 공격 으로부터 지속적으로 배우고 보안 분석가가 생각 하는 방법에 따라 분석을 적용 합니다. 따라서 사용자 환경 전체에서 kill 체인을 통해 수백만 개의 비정상 동작에서 이전에 검색 되지 않은 위협을 발견할 수 있으므로 공격자의 한 단계를 진행 하는 데 도움이 됩니다.

[새로운 위협에 대 한 Fusion](fusion.md#fusion-for-emerging-threats)에 대해 자세히 알아보세요.

또한 이제 증가 된 기능을 반영 하 여 [Fusion 분석 규칙을 더 쉽게 구성할 수](configure-fusion-rules.md)있습니다.

### <a name="get-fine-tuning-recommendations-for-your-analytics-rules-public-preview"></a>분석 규칙에 대 한 미세 조정 권장 사항 가져오기 (공개 미리 보기)

SIEM의 위협 검색 규칙을 미세 조정 하는 것은 위협 검색 범위를 극대화 하 고 가양성을 최소화 하는 것이 어려울 수 있습니다. Azure 센티널은 기계 학습을 사용 하 여 데이터 원본에서 수십억 개의 신호를 분석 하 고 시간에 따른 인시던트에 대 한 응답을 분석 하 고, 튜닝 오버 헤드를 크게 낮출 수 있는 조치 가능한 권장 사항 및 통찰력을 제공 하 고, 실제 위협에 대 한 검색 및 대응에 집중할 수 있도록 기계 학습을 통해이 프로세스를 간소화 하 고 간소화 합니다.

이제 [튜닝 권장 사항 및 통찰력](detection-tuning.md) 이 분석 규칙에 기본 제공 됩니다. 

### <a name="free-trial-updates"></a>무료 평가판 업데이트

Azure 센티널의 무료 평가판은 처음 31 일 동안 추가 비용 없이 신규 또는 기존 Log Analytics 작업 영역을 계속 지원 합니다.
현재 무료 평가판 환경에서 다음 업데이트를 포함 하 고 있습니다.

- **새 Log Analytics 작업 영역** 에서는 처음 31 일 동안 최대 10gb/일의 로그 데이터를 무료로 수집할 수 있습니다. 새 작업 영역에는 3 일 미만의 작업 영역이 포함 됩니다.

   Log Analytics 데이터 수집 및 Azure 센티널 요금은 모두 31 일의 평가 기간 동안 면제 받으려면 됩니다. 이 무료 평가판에는 Azure 테 넌 트 당 20 개의 작업 영역 제한이 적용 됩니다.


- **기존 Log Analytics 작업 영역** 에서 추가 비용 없이 Azure 센티널을 사용 하도록 설정할 수 있습니다. 기존 작업 영역에는 3 일 이상 전에 만든 작업 영역이 포함 됩니다.

   31 일의 평가 기간 동안에는 Azure 센티널 요금은 면제 받으려면 됩니다.

이러한 한도를 초과 하는 사용량에 대해서는 [Azure 센티널 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel) 페이지에 나열 된 가격 책정에 따라 요금이 청구 됩니다. [Automation](automation-in-azure-sentinel.md) 의 추가 기능과 관련 된 요금은 무료 평가판을 사용 하는 동안에도 계속 [적용 됩니다.](bring-your-own-ml.md)

> [!TIP]
> 무료 평가판을 사용 하는 경우 Azure 센티널의 **무료 평가판 탭 > 뉴스 & 가이드** 에서 cost management, 교육 등에 대 한 리소스를 찾아보세요. 이 탭에는 무료 평가판 날짜 및 만료 될 때까지 남은 일 수에 대 한 세부 정보도 표시 됩니다.
>

자세한 내용은 [Azure 센티널에 대 한 비용 계획 및 관리](azure-sentinel-billing.md)를 참조 하세요.

### <a name="content-hub-and-new-solutions-public-preview"></a>콘텐츠 허브 및 새 솔루션 (공개 미리 보기)

이제 azure 센티널은 azure 센티널 작업 영역에 대 한 Azure 센티널 기본 제공 콘텐츠 및 솔루션을 찾아서 배포 하는 중앙 위치인 **콘텐츠 허브** 를 제공 합니다. 콘텐츠 형식, 지원 모델, 범주 등을 필터링 하 여 필요한 콘텐츠를 찾거나 강력한 텍스트 검색을 사용 합니다.

**콘텐츠 관리** 에서 **콘텐츠 허브** 를 선택 합니다. 오른쪽에 대 한 자세한 내용을 보려면 솔루션을 선택 하 고 **설치** 를 클릭 하 여 작업 영역에 설치 합니다.

:::image type="content" source="media/whats-new/solutions-list.png" alt-text="새 Azure 센티널 콘텐츠 허브의 스크린샷" lightbox="media/whats-new/solutions-list.png":::

다음 목록에는 콘텐츠 허브에 추가 된 새로운 기본 제공 솔루션이 포함 되어 있습니다.

:::row:::
   :::column span="":::
      - Azure 센티널 학습 랩
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
      - GCP Id 및 액세스 관리
      - False
      - FireEye NX
      - 플레어 시스템 불꽃놀이
      - Forescout
      - Fortinet Fortigate
      - Imperva 클라우드 FAW
   :::column-end:::
   :::column span="":::
      - 참가자 위험 관리
      - IronNet 사이버 보안 다리미 방어
      - Lookout
      - McAfee 네트워크 보안 플랫폼
      - 클라우드 용 Microsoft MITRE AT&T&접시 헤드 솔루션
      - Palo Alto PAN-OS
   :::column-end:::
   :::column span="":::
      - Rapid7 Nexpose/정보 VM
      - ReversingLabs
      - RSA SecurID
      - Semperis
      - Tenable 스캐너 사용
      - Vectra 스트림
      - 제로 트러스트
   :::column-end:::
:::row-end:::

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 솔루션 정보](sentinel-solutions.md)
- [Azure Sentinel 솔루션 검색 및 배포](sentinel-solutions-deploy.md)
- [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)

### <a name="enable-continuous-deployment-from-your-content-repositories-public-preview"></a>콘텐츠 리포지토리에서 연속 배포 사용 (공개 미리 보기)

새 Azure 센티널 **리포지토리** 페이지는 Azure Portal에서 관리 하는 대신 GitHub 또는 Azure DevOps 리포지토리에서 사용자 지정 콘텐츠를 관리 하 고 배포 하는 기능을 제공 합니다. 이 기능은 Azure 센티널 작업 영역에서 콘텐츠를 관리 하 고 배포 하기 위한 보다 간소화 되 고 자동화 된 접근 방식을 도입 합니다.

Azure 센티널 외부에서 유지 관리 하기 위해 외부 리포지토리에 사용자 지정 콘텐츠를 저장 하는 경우 이제 해당 리포지토리를 Azure 센티널 작업 영역에 연결할 수 있습니다. 리포지토리에서 추가, 만들기 또는 편집 하는 콘텐츠는 Azure 센티널 작업 영역에 자동으로 배포 되며, **분석**, **구하기** 또는 **통합 문서** 페이지와 같은 다양 한 azure 센티널 갤러리에서 표시 됩니다.

자세한 내용은 [리포지토리에서 사용자 지정 콘텐츠 배포](ci-cd.md)를 참조 하세요.

### <a name="enriched-threat-intelligence-with-geolocation-and-whois-data-public-preview"></a>지리적 위치 및 WhoIs 데이터를 사용 하 여 위협 인텔리전스 보강 (공개 미리 보기)

이제 데이터 커넥터 및 논리 앱 플레이 북을 통해 Azure 센티널로 이동 하거나 Azure 센티널에서 만드는 모든 위협 인텔리전스 데이터는 지리적 위치 및 WhoIs 정보를 사용 하 여 자동으로 보강 됩니다.

지리적 위치 및 WhoIs 데이터는 선택한 IOC (손상 된 표시기)가 발견 된 조사에 더 많은 컨텍스트를 제공할 수 있습니다.

예를 들어, 지리적 위치 데이터를 사용 하 여 표시기에 대 한 *조직* 또는 *국가* 와 같은 세부 정보를 찾고 WhoIs 데이터를 사용 하 여 *등록자* 및 *레코드 생성* 데이터 같은 데이터를 찾을 수 있습니다

Azure 센티널로 가져온 각 손상 표시기에 대해 **위협 인텔리전스** 창에서 지리적 위치 및 WhoIs 데이터를 볼 수 있습니다. 표시기에 대 한 세부 정보는 사용 가능한 모든 지리적 위치 및 WhoIs 데이터를 포함 하 여 오른쪽에 표시 됩니다.

예를 들어:

:::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="지리적 위치 및 WhoIs 데이터를 포함 하는 표시기 정보의 스크린샷" lightbox="media/whats-new/geolocation-whois-ti.png":::

> [!TIP]
> 지리적 위치 및 WhoIs 정보는 API를 통해 액세스할 수도 있는 Microsoft 위협 인텔리전스 서비스에서 제공 됩니다. 자세한 내용은 [API를 통해 지리적 위치 데이터로 엔터티 보강](geolocation-data-api.md)을 참조 하세요.
>

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel의 위협 인텔리전스 이해](understand-threat-intelligence.md)
- [위협 인텔리전스 통합](threat-intelligence-integration.md)
- [Azure Sentinel에서 위협 지표 작업](work-with-threat-indicators.md)
- [위협 인텔리전스 플랫폼 연결](connect-threat-intelligence-tip.md)

### <a name="use-notebooks-with-azure-synapse-analytics-in-azure-sentinel-public-preview"></a>Azure 센티널에서 Azure Synapse Analytics와 함께 노트북 사용 (공개 미리 보기)

이제 azure 센티널은 대규모 보안 분석 시나리오를 위해 Jupyter 노트북을 Azure Synapse와 통합 합니다.

지금까지 Azure 센티널의 Jupyter 노트북은 Azure Machine Learning와 통합 되었습니다. 이 기능은 전자 필기장, 인기 있는 오픈 소스 기계 학습 도구 키트 및 TensorFlow와 같은 라이브러리 및 자체 사용자 지정 모델을 보안 워크플로에 통합 하려는 사용자를 지원 합니다.

새 Azure Synapse 통합은 다음과 같은 추가 분석의 마 력을 제공 합니다.

- 비용 최적화, 완전히 관리 되는 Azure Synapse Apache Spark compute 풀을 사용 하는 **보안 빅 데이터 분석**.

- **비용 효율적인 Data Lake** Azure Blob Storage을 기반으로 하는 빅 데이터 분석 전용 기능 집합인 Azure Data Lake Storage Gen2를 통해 기록 데이터에 대 한 빌드 분석에 액세스할 수 있습니다.

- 여러 원본 및 형식의 보안 작업 워크플로에 **데이터 원본을 통합 하기 위한 유연성** .

- Python과 함께 Spark 프레임 워크를 사용 하는 **python 기반 API 인 PySpark** 를 사용 하면 python에 이미 익숙한 경우 새 프로그래밍 언어를 익히는 필요성이 줄어듭니다.

이러한 통합을 지원 하기 위해 azure 센티널에서 직접 Azure Synapse 작업 영역을 만들고 시작 하는 기능을 추가 했습니다. 또한 Azure Synapse 환경을 구성 하는 과정을 안내 하 고, Log Analytics에서 Azure Data Lake Storage으로 연속 데이터 내보내기 파이프라인을 설정 하 고, 대규모로 해당 데이터를 검색할 수 있는 새로운 샘플 노트북을 추가 했습니다.

자세한 내용은 [Azure Synapse와 노트북 통합](notebooks-with-synapse.md)을 참조 하세요.

### <a name="enhanced-notebooks-area-in-azure-sentinel"></a>Azure 센티널의 향상 된 노트북 영역

이제 Azure 센티널의 **전자 필기장** 영역에는 **개요** 탭이 있습니다. 여기에서 전자 필기장에 대 한 기본 정보를 찾을 수 있으며 **템플릿** 탭에서 새 **노트북 유형** 열을 통해 표시 되는 각 노트북의 유형을 나타낼 수 있습니다. 예를 들어, 노트북에는 **시작**, **구성**, **구하기** 및 **Synapse** 의 유형이 있을 수 있습니다.

예를 들어:

:::image type="content" source="media/whats-new/notebooks-synapse.png" alt-text="전자 필기장 페이지에서 새 Azure Synapse 기능의 스크린샷" lightbox="media/whats-new/notebooks-synapse.png":::

자세한 내용은 [Jupyter 노트북을 사용 하 여 보안 위협 구하기](notebooks.md)를 참조 하세요.


### <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel"></a>Azure 센티널을 사용 하 여 Azure Key Vault honeytokens 배포 및 모니터링

새 **Azure 센티널 사기** 솔루션을 사용 하 여 키 자격 증명 모음에서 악의적인 활동을 감시 하 고, 선택 된 azure key vault에 *허니* 토큰 이라는 위장 키와 비밀을 배포할 수 있습니다.

배포 되 면 허니 토큰 키와 암호를 사용 하는 모든 액세스 또는 작업은 Azure 센티널에서 조사할 수 있는 인시던트를 생성 합니다.

실제로 허니 토큰 키와 비밀을 사용 하는 이유는 없으므로 작업 영역에서 유사한 활동은 악성이 될 수 있으므로 조사 해야 합니다.

**Azure 센티널 사기** 솔루션에는 honeytokens를 한 번에 하나씩 배포 하는 데 도움이 되는 통합 문서가 포함 되어 있습니다. watchlists는 생성 된 honeytokens를 추적 하 고 필요한 경우 인시던트를 생성 하는 분석 규칙을 포함 합니다.

자세한 내용은 [Azure 센티널을 사용 하 여 Azure Key Vault Honeytokens 배포 및 모니터링 (공개 미리 보기)](monitor-key-vault-honeytokens.md)을 참조 하세요.


## <a name="october-2021"></a>2021년 10월

- [Windows 보안 현재 GA에서 Azure Monitor 에이전트를 사용 하는 이벤트 커넥터](#windows-security-events-connector-using-azure-monitor-agent-now-in-ga)
- [Defender for Office 365 이벤트를 Microsoft 365 Defender 커넥터 (공개 미리 보기)에서 사용할 수 있습니다.](#defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview)
- [플레이 북 템플릿 및 갤러리 사용 가능 (공개 미리 보기)](#playbook-templates-and-gallery-now-available-public-preview)
- [예약 된 분석 규칙에 대 한 템플릿 버전 관리 (공개 미리 보기)](#manage-template-versions-for-your-scheduled-analytics-rules-public-preview)
- [DHCP 정규화 스키마 (공개 미리 보기)](#dhcp-normalization-schema-public-preview)

### <a name="windows-security-events-connector-using-azure-monitor-agent-now-in-ga"></a>Windows 보안 현재 GA에서 Azure Monitor 에이전트를 사용 하는 이벤트 커넥터

이제 Azure Monitor 에이전트를 기반으로 하는 Windows 보안 Events 커넥터의 새 버전이 출시 되었습니다. 자세한 내용은 [Windows 서버를 커넥트 하 여 보안 이벤트 수집](connect-windows-security-events.md?tabs=AMA) 을 참조 하세요.

### <a name="defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview"></a>Defender for Office 365 이벤트를 Microsoft 365 Defender 커넥터 (공개 미리 보기)에서 사용할 수 있습니다.

끝점에 대 한 microsoft defender 외에도 이제 [Microsoft 365 Defender 커넥터](connect-microsoft-365-defender.md)를 통해 [Office 365에 대해 microsoft defender](/microsoft-365/security/office-365-security/overview) 에서 원시 [고급 구하기 이벤트](/microsoft-365/security/defender/advanced-hunting-overview) 를 수집할 수 있습니다. [자세히 알아보기](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).
### <a name="playbook-templates-and-gallery-now-available-public-preview"></a>플레이 북 템플릿 및 갤러리 사용 가능 (공개 미리 보기)

플레이 북 템플릿은 요구 사항에 맞게 사용자 지정할 수 있는 미리 작성 되 고 테스트 되 고 사용이 가능한 워크플로입니다. 템플릿은 처음부터 플레이 북을 개발할 때 모범 사례에 대 한 참조 나 새로운 자동화 시나리오의 역할을 할 수도 있습니다.

플레이 북 템플릿은 azure 센티널 커뮤니티, isv (독립 소프트웨어 공급 업체) 및 Microsoft 고유의 전문가에 의해 개발 되었으며, **플레이 북 템플릿** 탭 ( **Automation** 아래), [**azure 센티널 솔루션**](sentinel-solutions.md)의 일부로 또는 [**azure 센티널 GitHub 리포지토리에서**](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)찾을 수 있습니다. 

자세한 내용은 [기본 제공 템플릿에서 플레이 북 만들기 및 사용자 지정](use-playbook-templates.md)을 참조 하세요.

### <a name="manage-template-versions-for-your-scheduled-analytics-rules-public-preview"></a>예약 된 분석 규칙에 대 한 템플릿 버전 관리 (공개 미리 보기)

[기본 제공 Azure 센티널 규칙 템플릿에서](detect-threats-built-in.md)분석 규칙을 만들 때 템플릿의 복사본을 효과적으로 만듭니다. 그 전에는 활성 규칙이 원래 템플릿에 적용 되는 변경 내용과 일치 하도록 동적으로 업데이트 ***되지 않습니다*** .

그러나 템플릿에서 생성 된 규칙은 제공 된 ***템플릿을 명심 하*** 여 다음과 같은 두 가지 이점을 누릴 수 있습니다.

- 템플릿에서 만들 때 (또는 그 후 언제 든 지) 규칙을 변경한 경우 항상 규칙을 다시 원래 버전 (템플릿의 복사본)으로 되돌릴 수 있습니다.

- 템플릿이 업데이트 되 면 알림 메시지를 받을 수 있으며, 규칙을 새 버전의 템플릿으로 업데이트 하거나 그대로 둘 수 있습니다.

[이러한 작업을 관리 하는 방법과](manage-analytics-rule-templates.md)기억해 야 할 사항에 대해 알아보세요. 이러한 절차는 템플릿에서 만든 모든 [예약 된](detect-threats-built-in.md#scheduled) 분석 규칙에 적용 됩니다.

### <a name="dhcp-normalization-schema-public-preview"></a>DHCP 정규화 스키마 (공개 미리 보기)

Azure 센티널 Information Model (ASIM)은 이제 dhcp 정규화 스키마를 지원 합니다 .이 스키마는 DHCP 서버에서 보고 하는 이벤트를 설명 하는 데 사용 되며, Azure 센티널에서 원본에 관계 없는 분석을 사용 하도록 하는 데 사용 됩니다. 

DHCP 정규화 스키마에 설명 된 이벤트에는 클라이언트 시스템에서 임대 된 DHCP IP 주소에 대 한 요청 처리와 임대가 부여 된 DNS 서버 업데이트가 포함 됩니다.

자세한 내용은 다음을 참조하세요.

- [Azure 센티널 DHCP 정규화 스키마 참조 (공개 미리 보기)](dhcp-normalization-schema.md)
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

- [Azure Defender(Azure Security Center)](connect-azure-security-center.md)
- [엔드포인트에 대한 Microsoft Defender](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
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
> 관련 개념 및 방법 문서에서 설명서에 추가된 자세한 지침을 확인할 수 있습니다. 자세한 내용은 [모범 사례 참조를 참조하세요.](best-practices.md#best-practice-references)
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

자세한 내용은 [REST API를 통해 지리적 위치 데이터를 사용하여 Azure Sentinel에서 엔터티 보강(퍼블릭 미리 보기)](geolocation-data-api.md)과 [Azure Sentinel에서 엔터티를 사용하여 데이터 분류 및 분석](entities-in-azure-sentinel.md)을 참조하세요.


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

[업데이트된 Azure Defender 커넥터에 대한 자세한 설명](connect-azure-security-center.md)을 참조하세요.

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


## <a name="may-2021"></a>2021년 5월

- [Azure Sentinel PowerShell 모듈](#azure-sentinel-powershell-module)
- [경고 그룹화 개선 사항](#alert-grouping-enhancements)
- [Azure Sentinel 솔루션(공개 미리 보기)](#azure-sentinel-solutions-public-preview)
- [SAP 솔루션에 대한 지속적인 위협 모니터링(공개 미리 보기)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [위협 인텔리전스 통합(공개 미리 보기)](#threat-intelligence-integrations-public-preview)
- [예약된 경고에 대한 Fusion(공개 미리 보기)](#fusion-over-scheduled-alerts-public-preview)
- [SOC-ML 변칙(공개 미리 보기)](#soc-ml-anomalies-public-preview)
- [IP 엔터티 페이지(공개 미리 보기)](#ip-entity-page-public-preview)
- [활동 사용자 지정(공개 미리 보기)](#activity-customization-public-preview)
- [헌팅 대시보드(공개 미리 보기)](#hunting-dashboard-public-preview)
- [인시던트 팀 - Microsoft Teams에서 협업(공개 미리 보기)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [제로 트러스트(TIC3.0) 통합 문서](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Azure Sentinel PowerShell 모듈

일상적인 운영 작업을 자동화하는 공식 Azure Sentinel PowerShell 모듈이 GA로 릴리스되었습니다!

[PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.SecurityInsights/)에서 다운로드할 수 있습니다.

자세한 내용은 PowerShell 설명서 [Az.SecurityInsights](/powershell/module/az.securityinsights/)를 참조하세요.

### <a name="alert-grouping-enhancements"></a>경고 그룹화 개선 사항

이제 특정 엔터티 유형과 일치하는 경우뿐만 아니라 구성된 엔터티에 대한 특정 경고 이름, 심각도 또는 기타 사용자 지정 세부 정보가 일치하는 경우 경고를 단일 인시던트로 그룹화하도록 분석 규칙을 구성할 수 있습니다. 

분석 규칙 마법사의 **인시던트 설정** 탭에서 경고 그룹화를 켜도록 선택한 다음, **선택한 엔터티 유형 및 세부 정보가 일치하는 경우 경고를 단일 인시던트로 그룹화** 옵션을 선택합니다. 

그런 다음, 일치시킬 엔터티 유형 및 관련 세부 정보를 선택합니다.

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="엔터티 세부 정보를 일치시켜 경고를 그룹화합니다.":::

자세한 내용은 [경고 그룹화](detect-threats-custom.md#alert-grouping)를 참조하세요.

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel 솔루션(공개 미리 보기)

이제 Azure Sentinel은 **패키지된 콘텐츠** [솔루션](sentinel-solutions-catalog.md)을 제공합니다. 여기에는 하나 이상의 데이터 커넥터, 통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리, 파서, 감시 목록 및 Azure Sentinel용 기타 구성 요소의 조합이 포함됩니다.

솔루션은 향상된 제품 내 검색 기능, 단일 단계 배포 및 엔드투엔드 제품 시나리오를 제공합니다. 자세한 내용은 [중앙에서 기본 제공 콘텐츠 및 솔루션 검색 및 배포](sentinel-solutions-deploy.md)를 참조 하세요.

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>SAP 솔루션에 대한 지속적인 위협 모니터링(공개 미리 보기)

이제 Azure Sentinel 솔루션에 **SAP용 지속적인 위협 모니터링** 이 포함되어 비즈니스 및 애플리케이션 계층 내에서 정교한 위협이 있는지 SAP 시스템을 모니터링할 수 있습니다.

SAP 데이터 커넥터는 전체 SAP 시스템 환경에서 다양한 14개의 애플리케이션 로그를 스트리밍하고 NetWeaver RFC 호출을 통해 ABAP(Advanced Business Application Programming)에서, OSSAP 제어 인터페이스를 통해 파일 스토리지 데이터에서 로그를 수집합니다. SAP 데이터 커넥터는 SAP 기본 인프라를 모니터링하는 Azure Sentinel 기능을 추가합니다.

Azure Sentinel로 SAP 로그를 수집하려면 SAP 환경에 Azure Sentinel SAP 데이터 커넥터가 설치되어 있어야 합니다. SAP 데이터 커넥터를 배포한 후에는 다양한 SAP 솔루션 보안 콘텐츠를 배포하여 조직의 SAP 환경을 원활하게 파악하고 관련 보안 작업 기능을 향상합니다.

자세한 내용은 [자습서: SAP에 대한 Azure Sentinel 솔루션 배포(공개 미리 보기)](sap-deploy-solution.md)를 참조하세요.

### <a name="threat-intelligence-integrations-public-preview"></a>위협 인텔리전스 통합(공개 미리 보기)

Azure Sentinel은 [위협 인텔리전스 피드를 사용하여](./understand-threat-intelligence.md) 알려진 위협을 감지하고 우선 순위를 지정하는 보안 분석가의 기능을 향상시키는 몇 가지 다른 방법을 제공합니다.

사용 가능한 여러 새로운 TIP(통합 위협 인텔리전스 플랫폼) 제품 중 하나를 사용하고, TAXII 서버에 연결하여 STIX 호환 위협 인텔리전스 원본을 활용할 수 있으며, [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator)와 직접 통신할 수 있는 사용자 지정 솔루션을 사용할 수도 있습니다.

직접 조사 및 대응 작업에 도움이 될 수 있는 TI 정보로 인시던트 보강을 위해 플레이북에서 위협 인텔리전스 원본에 연결할 수도 있습니다.

자세한 내용은 [Azure Sentinel로 위협 인텔리전스 통합](threat-intelligence-integration.md)을 참조하세요.

### <a name="fusion-over-scheduled-alerts-public-preview"></a>예약된 경고에 대한 Fusion(공개 미리 보기)

**Fusion** 기계 학습 상관 관계 엔진은 이제 다른 데이터 원본에서 가져온 경고 외에 해당 상관 관계에 있는 [예약된 분석 규칙](detect-threats-custom.md) 세트에서 생성된 경고를 사용하여 다단계 공격을 검색할 수 있습니다.

자세한 내용은 [Azure Sentinel의 고급 다단계 공격 감지](fusion.md)를 참조하세요.

### <a name="soc-ml-anomalies-public-preview"></a>SOC-ML 변칙(공개 미리 보기)

Azure Sentinel의 SOC-ML 기계 학습 기반 변칙은 검색을 피할 수 있는 비정상적인 동작을 식별할 수 있습니다.

SOC-ML은 바로 작동하도록 배치할 수 있는 분석 규칙 템플릿을 사용합니다. 변칙은 악의적이거나 심지어 의심스러운 행동을 의미하지는 않지만 검색, 조사 및 위협 헌팅의 충실도를 개선하는 데 사용할 수 있습니다.

자세한 내용은 [SOC-ML 변칙을 사용하여 Azure Sentinel에서 위협 탐지](soc-ml-anomalies.md)를 참조하세요.

### <a name="ip-entity-page-public-preview"></a>IP 엔터티 페이지(공개 미리 보기)

이제 Azure Sentinel은 IP 주소 엔터티를 지원하며 새 IP 엔터티 페이지에서 IP 엔터티 정보를 볼 수 있습니다.

사용자 및 호스트 엔터티 페이지와 마찬가지로 IP 페이지에는 IP에 대한 일반 정보, IP가 속한 것으로 확인되는 활동 목록 등이 포함되어 있어 보안 인시던트 조사를 향상시키기 위해 더욱 풍부한 정보 저장소를 제공합니다.

자세한 내용은 [엔터티 페이지](identify-threats-with-entity-behavior-analytics.md#entity-pages)를 참조하세요.

### <a name="activity-customization-public-preview"></a>활동 사용자 지정(공개 미리 보기)

엔터티 페이지와 관련하여, 이제 엔터티에 대해 새로운 사용자 정의 활동을 만들 수 있습니다. 이 활동은 지금까지 본 기본 제공 활동과 함께 해당 엔터티 페이지에 추적 및 표시됩니다.

자세한 내용은 [엔터티 페이지 타임라인에서 작업 사용자 지정](customize-entity-activities.md)을 참조하세요.

### <a name="hunting-dashboard-public-preview"></a>헌팅 대시보드(공개 미리 보기)

**헌팅** 블레이드가 새로 고쳐졌습니다. 새 대시보드를 사용하면 한 번의 클릭으로 모든 쿼리 또는 선택한 하위 집합을 실행할 수 있습니다.

24시간 동안의 결과 수, 급증 또는 결과 수 변경을 확인하여 헌팅을 시작할 위치를 식별합니다. 즐겨찾기, 데이터 원본, MITRE ATT&CK 전술 또는 기술, 결과 또는 결과 델타별로 정렬하고 필터링할 수도 있습니다. 필요한 데이터 원본이 아직 연결되지 않은 쿼리를 보고 이러한 쿼리를 사용하도록 설정하는 방법에 대한 권장 사항을 확인합니다.

자세한 내용은 [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)을 참조하세요.

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Azure Sentinel 인시던트 팀 - Microsoft Teams에서 협업(공개 미리 보기)

이제 Azure Sentinel은 Microsoft Teams와의 직접 통합을 지원하므로 조직 전체 및 외부 이해 관계자와 원활하게 협업할 수 있습니다.

Azure Sentinel의 인시던트에서 직접 중앙 통신 및 조정에 사용할 새 *인시던트 팀* 을 만듭니다.

인시던트 팀은 심각도가 높으면서 계속 진행 중인 인시던트의 전용 회의 브리지로 사용할 때 특히 유용합니다. 통신 및 협업을 위해 이미 Microsoft Teams를 사용하는 조직은 Azure Sentinel 통합을 사용하여 보안 데이터를 대화 및 일상적인 작업으로 직접 가져올 수 있습니다.

Microsoft Teams에서 새로운 팀의 **인시던트 페이지** 탭에는 항상 Azure Sentinel에서 업데이트된 최신 데이터가 표시되어 팀이 가장 관련성이 높은 데이터를 바로 사용할 수 있습니다.

[ ![Microsoft Teams의 인시던트 페이지.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

자세한 내용은 [Microsoft Teams에서 협업(공개 미리 보기)](collaborate-in-microsoft-teams.md)을 참조하세요.

### <a name="zero-trust-tic30-workbook"></a>제로 트러스트(TIC3.0) 통합 문서

새로운 Azure Sentinel 제로 트러스트(TIC3.0) 통합 문서에서는 [TIC(신뢰할 수 있는 인터넷 연결)](https://www.cisa.gov/trusted-internet-connections) 프레임워크에 교차 안내된 [제로 트러스트](/security/zero-trust/) 원칙의 자동화된 시각화를 제공합니다.

규정 준수는 단순히 연간 요구사항이 아니며, 조직은 시간 경과에 따라 구성을 철저하게 모니터링해야 합니다. Azure Sentinel의 제로 트러스트 통합 문서에서는 Azure, Office 365, Teams, Intune, Windows Virtual Desktop 등에서 다양한 Microsoft 보안 제품을 사용합니다.

[ ![제로 트러스트 통합 문서.](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**제로 트러스트 통합 문서**:

- 구현자, SecOps 분석가, 평가자, 보안 및 규정 준수 의사 결정자, MSSP 등이 클라우드 워크로드의 보안 태세에 대한 상황을 인식할 수 있도록 지원합니다.
- TIC 3.0 보안 기능에 맞춰 조정된 75개 이상의 제어 카드와 탐색용 GUI 버튼을 선택할 수 있습니다.
- 자동화, AI, 기계 학습, 쿼리/경고 생성, 시각화, 맞춤형 권장 사항 및 해당 설명서 참조를 통해 인력을 보강하도록 설계되었습니다.

자세한 내용은 [데이터 시각화 및 모니터링](monitor-your-data.md)을 참조하세요.

## <a name="april-2021"></a>2021년 4월

- [Azure Policy 기반 데이터 커넥터](#azure-policy-based-data-connectors)
- [인시던트 타임라인(공개 미리 보기)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Azure Policy 기반 데이터 커넥터

Azure Policy를 사용하여 Azure Sentinel에 수집하려는 로그가 있는 특정 유형의 모든(현재 및 미래) 리소스에 공통 진단 로그 설정 세트를 적용할 수 있습니다.

데이터 수집 구성 작업에 [AzurePolicy](../governance/policy/overview.md)의 기능을 적용하기 위한 노력을 계속하여 공개 미리 보기에 릴리스된 [Azure Storage 계정](./data-connectors-reference.md#azure-storage-account) 리소스에 대해 다른 Azure Policy 향상 데이터 수집기를 제공하고 있습니다.

또한 [Azure Key Vault](./data-connectors-reference.md#azure-key-vault) 및 [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks)를 위한 두 개의 미리 보기 커넥터가 이제 GA(일반 공급)로 릴리스되어 [Azure SQL Databases](./data-connectors-reference.md#azure-sql-databases) 커넥터에 조인되었습니다.

### <a name="incident-timeline-public-preview"></a>인시던트 타임라인(공개 미리 보기)

인시던트 세부 정보 페이지의 첫 번째 탭은 이제 **타임라인** 으로, 인시던트에서 경고 및 책갈피의 타임라인을 표시합니다. 인시던트의 타임라인을 통해 인시던트를 더 잘 이해하고 관련 경고 및 책갈피에서 공격자 활동의 타임라인을 다시 구성할 수 있습니다.

- 인시던트 컨텍스트를 벗어나지 않고 타임라인에서 항목을 선택하여 세부 정보를 확인합니다.
- 타임라인 콘텐츠를 필터링하여 경고나 책갈피만 표시하거나 특정 심각도 또는 MITRE 전술의 항목을 표시합니다.
- **시스템 경고 ID** 링크를 선택하여 전체 레코드를 보거나 **이벤트** 링크를 선택하여 **로그** 영역에서 관련 이벤트를 확인할 수 있습니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="인시던트 타임라인 탭":::

자세한 내용은 [자습서: Azure Sentinel을 사용하여 인시던트 조사](investigate-cases.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)
