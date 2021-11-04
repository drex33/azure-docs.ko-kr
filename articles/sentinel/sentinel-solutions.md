---
title: Azure Sentinel 콘텐츠 및 솔루션 정보 | Microsoft Docs
description: 이 문서에서는 고객이 데이터 커넥터와 함께 패키지된 데이터 분석 도구를 찾는 데 사용할 수 있는 Azure Sentinel 콘텐츠 및 솔루션에 대해 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38508b591d79e0dee910468f595fa144353574c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064072"
---
# <a name="about-azure-sentinel-content-and-solutions"></a>Azure Sentinel 콘텐츠 및 솔루션 정보

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Azure Sentinel **콘텐츠 허브** 및 솔루션은 모두 개별 솔루션 패키지와 마찬가지로 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel *콘텐츠는* 고객이 데이터를 수집, 모니터링, 경고, 헌팅, 조사, 대응하고 Azure Sentinel 다양한 제품, 플랫폼 및 서비스와 연결할 수 있도록 하는 SIEM(보안 정보 및 이벤트 관리) 콘텐츠입니다.

Azure Sentinel 콘텐츠에는 다음 형식이 포함됩니다.

- **[데이터 커넥터는](connect-data-sources.md)** 여러 원본에서 Azure Sentinel 로그 수집을 제공합니다.
- **[파서가](normalization-about-parsers.md)** [ASIM](normalization.md) 형식으로 로그 서식/변환을 제공하여 다양한 Azure Sentinel 콘텐츠 형식 및 시나리오에서 사용을 지원합니다.
- **[통합 문서에서는](get-visibility.md)** Azure Sentinel 데이터로 모니터링, 시각화 및 대화형 작업을 제공하여 사용자에게 의미 있는 인사이트를 강조 표시합니다.
- **[분석 규칙은](detect-threats-built-in.md)** 인시던트 통해 관련 SOC 작업을 가리키는 경고를 제공합니다.
- **[헌팅 쿼리는](hunting.md)** SOC 팀에서 Azure Sentinel 위협을 사전에 헌팅하는 데 사용됩니다.
- **[Notebook을 통해](notebooks.md)** SOC 팀은 Jupyter 및 Azure Notebooks 고급 헌팅 기능을 사용할 수 있습니다.
- **[감시 목록은](watchlists.md)** 향상된 위협 탐지 및 경고 감소에 대한 *특정* 데이터 수집을 지원합니다.
- **[플레이북 및 Azure Logic Apps 사용자 지정 커넥터는](automate-responses-with-playbooks.md)** Azure Sentinel 자동 조사, 수정 및 응답 시나리오에 대한 기능을 제공합니다.

Azure Sentinel *솔루션은* Azure Sentinel 엔드투엔드 제품, 도메인 또는 산업 수직 시나리오를 충족하는 Azure Sentinel 콘텐츠 또는 Azure Sentinel API 통합 패키지입니다.

> [!TIP]
> 사용자 고유의 요구에 맞게 즉시 사용 가능 콘텐츠를 사용자 지정하거나 커뮤니티의 다른 사용자와 공유할 수 있는 콘텐츠로 고유한 솔루션을 만들 수 있습니다. 자세한 내용은 솔루션 작성 및 게시를 위한 Azure Sentinel 솔루션 [빌드 가이드를](https://aka.ms/sentinelsolutionsbuildguide) 참조하세요.
>
## <a name="discover-and-manage-azure-sentinel-content"></a>Azure Sentinel 콘텐츠 검색 및 관리

Azure Sentinel **콘텐츠 허브를** 사용하여 기본 제공(기본 제공) 콘텐츠를 중앙에서 검색하고 설치합니다.

Azure Sentinel Content Hub는 Azure Sentinel 제품 내 검색 기능, 단일 단계 배포 및 엔드 투 엔드 제품, 도메인 및/또는 수직적인 첫 번째 솔루션 및 콘텐츠의 사용을 제공합니다.

- 콘텐츠 **허브에서** [범주](#azure-sentinel-out-of-the-box-content-and-solution-categories) 및 기타 매개 변수를 필터링하거나 강력한 텍스트 검색을 사용하여 조직의 요구에 가장 적합한 콘텐츠를 찾습니다. 일부 콘텐츠는 Microsoft에서 유지 관리하고 다른 콘텐츠는 파트너 또는 커뮤니티에서 유지 관리하기 때문에 콘텐츠 **허브는** 각 콘텐츠 부분에 적용되는 [지원 모델을](#azure-sentinel-out-of-the-box-content-and-solution-support-models) 나타냅니다.

    Azure Sentinel **콘텐츠 허브를** [통해,](sentinel-solutions-deploy.md#install-or-update-a-solution) 그리고 **리포지토리** 페이지를 통해 사용자 지정 콘텐츠에 대한 업데이트를 관리합니다.

- 사용자 고유의 요구에 맞는 콘텐츠를 사용자 지정하거나 분석 규칙, 헌팅 쿼리, Notebook, 통합 문서 등을 비롯한 사용자 지정 콘텐츠를 만듭니다. Azure Sentinel [리포지토리](ci-cd.md) 페이지를 통해 [Azure Sentinel API](/rest/api/securityinsights/)또는 사용자 고유의 소스 제어 리포지토리를 통해 Azure Sentinel 작업 영역에서 직접 사용자 지정 콘텐츠를 관리합니다.

### <a name="why-content-hub-and-solutions"></a>콘텐츠 허브 및 솔루션이 있는 이유

Azure Sentinel *솔루션은* 하나 이상의 도메인 또는 수직 시나리오에 대한 엔드 투 엔드 제품 값을 제공하는 패키지된 콘텐츠 또는 통합입니다.

솔루션 환경은 솔루션의 검색 기능 및 배포를 위한 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 통해 구동됩니다. 자세한 내용은 솔루션 작성 및 게시를 위한 Azure Sentinel 솔루션 [빌드 가이드를](https://aka.ms/sentinelsolutionsbuildguide) 참조하세요.

Azure Sentinel 솔루션은 Azure Sentinel에서 제품 내 검색 가능성, 단일 단계 배포 및 엔드투엔드 제품, 도메인 및/또는 수직 시나리오 구현을 제공합니다. 이 환경은 솔루션의 검색 기능, 배포 및 사용 및 솔루션 작성 및 게시를 위해 에서 구동됩니다.

- **패키지된 콘텐츠는** 데이터 커넥터, 통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리, 감시 목록, 파서 등과 같은 하나 이상의 Azure Sentinel 콘텐츠의 컬렉션입니다.

- **통합에는** Azure와 기존 고객 애플리케이션 간의 통합을 지원하거나 해당 애플리케이션에서 Azure Sentinel 데이터, 쿼리 등을 마이그레이션하는 Azure Sentinel 또는 Azure Log Analytics API를 사용하여 빌드된 서비스 또는 도구가 포함됩니다.

또한 솔루션을 사용하여 콘텐츠가 즉시 사용할 준비가 된 단일 단계에서 즉시 사용할 수 있는 콘텐츠 패키지를 설치할 수도 있습니다. 공급자와 파트너는 솔루션을 사용하여 결합된 제품, 도메인 또는 수직 가치를 제공하여 투자를 제품화할 수 있습니다.

콘텐츠 허브를 사용하여 시나리오 기반 방식으로 솔루션 및 기본적으로 사용할 수 있는 콘텐츠를 중앙에서 검색하고 배포합니다.

자세한 내용은 다음을 참조하세요.

- [기본적으로 Azure Sentinel 콘텐츠 및 솔루션을 중앙에서 검색 및 배포](sentinel-solutions-deploy.md)
- [콘텐츠 허브 카탈로그 Azure Sentinel](sentinel-solutions-catalog.md)

## <a name="azure-sentinel-out-of-the-box-content-and-solution-categories"></a>Azure Sentinel 콘텐츠 및 솔루션 범주

Azure Sentinel 첫 번째 콘텐츠는 다음 범주 중 하나 이상에 적용할 수 있습니다. 콘텐츠 **허브에서** 표시할 콘텐츠를 변경하기 위해 보려는 범주를 선택합니다.

### <a name="domain-categories"></a>도메인 범주

| 범주의 이름입니다.  | 설명 |
| ---------- | ----------------------- |
| **애플리케이션**  | 웹, 서버 기반, SaaS, 데이터베이스, 통신 또는 생산성 워크로드          |
| **클라우드 공급자**  | 클라우드 서비스|
| **호환성**   | 규정 준수 제품, 서비스 및 프로토콜  |
| **DevOps**       | 개발 운영 도구 및 서비스    |
| **ID**     | ID 서비스 공급자 및 통합     |
| **IoT(사물 인터넷)**    | IoT, OT 디바이스 및 인프라, 산업 제어 서비스                   |
| **IT 운영**| IT를 관리하는 제품 및 서비스   |
| **마이그레이션**    | 마이그레이션 사용 제품, 서비스 및               |
| **네트워킹**   | 네트워크 제품, 서비스 및 도구    |
| **플랫폼**     | 제네릭 또는 프레임워크 구성 요소, 클라우드 인프라 및 플랫폼 Azure Sentinel|
| **보안 - 기타**   | 다른 명확한 범주가 없는 기타 보안 제품 및 서비스           |
| **보안 - 위협 인텔리전스**  | 위협 인텔리전스 플랫폼, 피드, 제품 및 서비스        |
| **보안 - 위협 방지**   | 위협 방지, 메일 보호, XDR 및 엔드포인트 보호 제품 및 서비스     |
| **보안 – 0일 취약성**   | 0일 취약성 공격(예: [0일](/azure/security/fundamentals/recover-from-identity-compromise) 취약성 공격)에 대한 전문 솔루션 |
| **보안 – AUTOMATION(SOAR)**   | 보안 자동화, SOAR(보안 운영 및 자동화된 대응), 보안 운영 및 인시던트 대응 제품 및 서비스.   |
| **보안 – 클라우드 보안** | CASB(Cloud Access Service Broker), CWPP(클라우드 워크로드 보호 플랫폼), CSPM(클라우드 보안 태세 관리 및 기타 Cloud Security 제품 및 서비스 |
| **보안 – Information Protection**   | 정보 보호 및 문서 보호 제품 및 서비스|
| **보안 – 참가자 위협**  | 보안 제품 및 서비스에 대한 내부자 위협 및 UEBA(사용자 및 엔터티 동작 분석)                |
| **보안 – 네트워크**    | 보안 네트워크 디바이스, 방화벽, NDR(네트워크 검색 및 응답), NIDP(네트워크 침입 및 검색 방지) 및 네트워크 패킷 캡처   |
| **보안 – 취약성 관리** | 취약성 관리 제품 및 서비스                    |
| **스토리지**      | 파일 저장소 및 파일 공유 제품 및 서비스                 |
| **학습 및 자습서**  | 자산 학습, 자습서 및 온보딩 |
| **사용자 동작(UEBA)**    | 사용자 동작 분석 제품 및 서비스|
| | |

### <a name="industry-vertical-categories"></a>업계 수직 범주

| 범주의 이름입니다.  | 설명 |
| ---------- | ----------------------- |
| **항공학**  | 인증 업계에 특정된 제품, 서비스 및 콘텐츠 |
| **Education**    | 교육 산업 관련 제품, 서비스 및 콘텐츠   |
| **재무**      | 재무 산업과 특정한 제품, 서비스 및 콘텐츠     |
| **의료**   | 의료 산업 관련 제품, 서비스 및 콘텐츠  |
| **제조업** | 제조 산업과 관련된 제품, 서비스 및 콘텐츠 |
| **정품**       | 소매업에 특정한 제품, 서비스 및 콘텐츠       |
| | |

## <a name="azure-sentinel-out-of-the-box-content-and-solution-support-models"></a>기본 콘텐츠 및 솔루션 지원 모델 Azure Sentinel

Microsoft와 다른 조직은 모두 Azure Sentinel 콘텐츠와 솔루션을 작성합니다. 각 콘텐츠 또는 솔루션의 각 부분에는 다음 지원 유형 중 하나가 있습니다.

| 지원 모델  | 설명 |
| ---------- | ----------------------- |
| **Microsoft 지원**| 적용 대상: <br>- Microsoft가 데이터 공급자이며 관련되고 작성자인 콘텐츠/솔루션입니다. <br> - Microsoft가 작성하지 않은 데이터 원본에 대한 일부 Microsoft 작성 콘텐츠/솔루션입니다. <br><br>    Microsoft는 Microsoft Azure 지원 계획에 따라 이 지원 모델의 콘텐츠/솔루션을 지원하고 유지 [관리합니다.](https://azure.microsoft.com/support/options/#overview) <br>파트너 또는 Community Microsoft 이외의 다른 당사자가 작성한 콘텐츠/솔루션을 지원합니다.|
|**파트너 지원** | Microsoft 이외의 당사자가 작성한 콘텐츠/솔루션에 적용됩니다.  <br><br>   파트너 회사는 이러한 콘텐츠/솔루션 부분에 대한 지원 또는 유지 관리를 제공합니다. 파트너 회사는 독립 소프트웨어 공급업체, MSP/MSSP(관리 서비스 공급자), SI(시스템 통합자) 또는 선택한 콘텐츠/솔루션에 대한 Azure Sentinel 페이지에 연락처 정보가 제공되는 모든 조직일 수 있습니다.<br><br>    파트너 지원 솔루션과 관련된 문제는 지정된 지원 담당자에게 문의하세요.|
|**커뮤니티 지원** |Azure Sentinel 지원 및 유지 관리를 위한 연락처가 나열되지 않은 Microsoft 또는 파트너 개발자가 작성한 콘텐츠/솔루션에 적용됩니다.<br><br>    이러한 솔루션에 대한 질문이나 문제는 Azure Sentinel GitHub [커뮤니티에](https://aka.ms/threathunters)문제를 [제출하세요.](https://github.com/Azure/Azure-Sentinel/issues/new/choose) |
| | |


## <a name="next-steps"></a>다음 단계

Azure Sentinel 콘텐츠에 대해 알아보고 나면 Azure Sentinel 작업 영역에서 콘텐츠 및 솔루션 관리를 시작합니다.

Azure Sentinel **콘텐츠 허브에서** 솔루션을 검색하고 설치합니다. 자세한 내용은 다음을 참조하세요.

- [중앙에서 기본적으로 사용 가능한 콘텐츠 및 솔루션 검색 및 배포(공개 미리 보기)](sentinel-solutions-deploy.md)
- [콘텐츠 허브 카탈로그 Azure Sentinel](sentinel-solutions-catalog.md)
- [Azure Sentinel 데이터 커넥터](connect-data-sources.md)
- [Azure Sentinel 데이터 커넥터 찾기](data-connectors-reference.md)
