---
title: Azure Purview 오픈 소스 도구 및 유틸리티에 대해 알아보기
description: 이 자습서에서는 Azure Purview에서 사용할 수 있는 다양한 도구 및 유틸리티를 나열하고 사용법에 대해 설명합니다.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/10/2021
ms.openlocfilehash: 8c87adc48a9a717a6ca9796b6d3bb2ec780220f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007641"
---
# <a name="azure-purview-open-source-tools-and-utilities"></a>Azure Purview 오픈 소스 도구 및 유틸리티

이 문서에는 Azure Purview 서비스를 몇 분 만에 빠르게 시작하는 데 도움이 되는 몇 가지 오픈 소스 도구 및 유틸리티(명령줄, Python 및 PowerShell 인터페이스)가 나와 있습니다. 이러한 도구는 Azure Purview 제품 그룹과 오픈 소스 커뮤니티의 공동 노력으로 작성되고 개발되었습니다. 이러한 도구의 목적은 Purview의 학습, 시작, 정기적인 사용 및 장기 채택을 간결하고 매우 빠르게 만드는 것입니다.

### <a name="intended-audience"></a>대상 그룹

- 고객, 개발자, ISV, 파트너, 에반젤리스트 및 마니아를 포함한 Azure Purview 커뮤니티 

- Azure Purview 카탈로그는 [Apache Atlas](https://atlas.apache.org/)를 기반으로 하며 Apache Atlas API에 대한 전체 지원을 확장합니다. Apache Atlas 커뮤니티, 마니아 및 개발자가 Azure Purview를 기반으로 하여 전적으로 빌드하고 전파하는 것을 환영합니다.

### <a name="purview-customer-journey-stages"></a>Purview 고객 경험 단계

- *Purview 학습자*: Azure Purview 서비스를 처음 시작하고 다중 클라우드 통합 데이터 거버넌스 솔루션의 작동 방식을 이해하고 탐구하려는 학습자입니다. 학습자 섹션에는 데이터 거버넌스 시장의 다른 경쟁 솔루션과 Purview를 비교 및 ​​대조하고 장기간 사용하기 위해 채택하기 전에 사용해 보려는 사용자가 포함됩니다.

- *Purview 혁신가*: 적극적으로 기존 및 최신 기능을 이해하고, 아이디어화하고, Purview에서 예정된 기능을 개념화하는 혁신가입니다. 고객을 위한 솔루션을 빌드하고 개발하는 데 능숙하며, 차세대 최첨단 데이터 거버넌스 제품에 대한 미래 지향적 아이디어를 가지고 있습니다.

- *Purview 마니아/에반젤리스트*: 학습자와 혁신가가 결합된 마니아입니다. Purview에 대한 확고한 이해와 지식을 발전시켰으며, 이에 따라 Purview의 채택에 대해 낙관적입니다. Purview를 서비스로 전파하고 전 세계의 여러 다른 Purview 사용자와 잠재 고객을 교육하는 데 도움이 될 수 있습니다.

- *Purview 채택자*: Purview를 시작하고 탐색한 후 마이그레이션하여 Purview를 몇 개월 이상 원활하게 사용하고 있는 채택자입니다.

- *Purview 장기 일반 사용자*: Purview를 1년 넘게 사용해 왔으며 이제 Azure Portal 및 Purview Studio에서 최신 고급 Purview 사용 사례를 자신감 있고 편안하게 사용할 수 있는 장기 사용자입니다. 또한 Purview REST API와 Purview API를 통해 지원되는 추가 사용 사례에 대한 거의 완벽한 지식과 인식을 가지고 있습니다.


## <a name="azure-purview-open-source-tools-and-utilities-list"></a>Azure Purview 오픈 소스 도구 및 유틸리티 목록

1. [Purview-API-via-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/README.md) 

    - **권장 고객 경험 단계**: *학습자, 혁신가, 마니아, 채택자, 장기 일반 사용자*
    - **설명**: 이 유틸리티는 [Azure Purview REST API 참조](/rest/api/purview/) Microsoft Docs의 전체 세트를 기반으로 하며 이를 다루고 있습니다. [PowerShell 갤러리에서 다운로드 및 설치](https://aka.ms/purview-api-ps)하세요. 빠르고 사용하기 쉬운 PowerShell 인터페이스를 통해 문서화된 모든 Purview REST API를 실행할 수 있습니다. 장기적인 일반 사용을 위해 명령줄 및 스크립팅된 메서드를 통해 Purview API를 사용하고 자동화합니다. 이는 대량 작업을 자동화된 방식, 일괄 처리 모드 또는 예약된 cron 작업으로 수행하려는 고객을 위한 대안이며, Azure Portal 및 Purview Studio를 사용하는 GUI 메서드와는 대조적입니다. 자세한 설명서, 샘플 사용 가이드, 자가 진단 및 예제는 [GitHub:Azure-Purview-API-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell)에서 사용할 수 있습니다.

1. [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)

    - **권장 고객 경험 단계**: *학습자, 혁신가, 마니아*
    - **설명**: Purview 계정의 초기 설정을 수행하는 PowerShell 스크립트입니다. 여러 개의 새 Purview 계정을 5분 이내에 설정하려는 사용자에게 유용합니다!

1. [Purview 랩](https://aka.ms/purviewlab)

    - **권장 고객 경험 단계**: *학습자, 혁신가, 마니아*
    - **설명**: Purview의 수많은 기능을 소개하고 Purview에 대해 최상의 가능한 이해를 개발하기 위해 각 단계를 직접 실행하는 실용적이고 실습적인 접근 방식으로 개념을 학습하는 데 도움이 되는 실습 랩입니다.

1. [Purview CLI](https://aka.ms/purviewcli)

    - **권장 고객 경험 단계**: *혁신가, 마니아, 채택자, 장기 일반 사용자*
    - **설명**: [Purview-API-via-PowerShell](https://aka.ms/purview-api-ps)과 비슷하지만 기능이 PowerShell 기반 프레임워크보다 더 제한적이거나 더 적은 Purview API를 실행하는 Python 기반 도구입니다.

1. [Purview 데모](https://aka.ms/pvdemo)

    - **권장 고객 경험 단계**: *학습자, 혁신가, 마니아*
    - **설명**: 새 Azure Purview 계정을 하나의 명령으로만 빠르고 안전하게 자동으로 설정하고 배포하는 ARM(Azure Resource Manager) 템플릿 기반 도구입니다. Azure SQL Database, Azure Data Lake Storage Gen2 계정, Azure Data Factory, Azure Synapse Analytics 작업 영역과 같은 몇 가지 미리 구성된 데이터 원본을 추가로 배포하는 추가 기능인 [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)와 비슷합니다.

1. [PyApacheAtlas: Atlas API를 사용하는 Azure Purview와 Apache Atlas 간의 인터페이스](https://github.com/wjohnson/pyapacheatlas)

    - **권장 고객 경험 단계**: *혁신가, 마니아, 채택자, 장기 일반 사용자*
    - **설명**: Azure Purview 및 Apache Atlas API에서 작동하는 Python 패키지입니다. 클래스 및 Excel 템플릿의 Pythonic 세트에서 대량 로드, 사용자 지정 계보 등을 지원합니다. 이 패키지는 프로그래밍 방식 상호 작용과 하위 코드 업로드용 Excel 템플릿을 지원합니다.

1. [Purview EventHub 알림 판독기](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/purview_atlas_eventhub_sample.py)

    - **권장 고객 경험 단계**: *혁신가, 마니아, 채택자, 장기 일반 사용자*
    - **설명**: 이 도구는 Purview의 EventHub를 읽고, EventHub에서 실시간 Kafka 알림을 Atlas 알림(https://atlas.apache.org/2.0.0/Notifications.html) 형식으로 catch하는 방법을 보여 줍니다. 또한 검사 중에 라이브로 검색되는 엔터티 및 자산의 CSV Excel 시트와 Purview에서 생성하는 기타 관심 알림을 즉시 생성합니다.


## <a name="feedback-and-disclaimer"></a>피드백 및 고지 사항

어떤 도구도 해당 효율성 또는 기능 보장을 확인하는 보증을 확인하는 Microsoft의 명시적 보증을 제공하지 않습니다. 이러한 도구는 악의적인 활동 또는 바이러스가 없는 것으로 인증되었으며 개인 데이터 또는 중요한 데이터를 수집하지 않도록 보장됩니다.

사용 중에 제기되는 효율성 및 기능에 대한 피드백 또는 질문은 해당 GitHub 리포지토리에 언급된 연락처 세부 정보에서 해당 도구 소유자 및 작성자에게 문의하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
