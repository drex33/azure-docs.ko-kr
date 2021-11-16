---
title: Microsoft 센티널 콘텐츠 및 솔루션 정보 | Microsoft Docs
description: 이 문서에서는 고객이 데이터 커넥터와 함께 패키지 된 데이터 분석 도구를 찾는 데 사용할 수 있는 Microsoft 센티널 콘텐츠 및 솔루션을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 940acf043711c7645423a9e52d8c4dabc2592e3f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516926"
---
# <a name="about-microsoft-sentinel-content-and-solutions"></a>Microsoft 센티널 콘텐츠 및 솔루션 정보

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft 센티널 **콘텐츠 허브** 및 솔루션은 현재 **미리 보기** 상태 이며, 모든 개별 솔루션 패키지입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft 센티널 *콘텐츠* 는 고객이 microsoft 센티널에서 다양 한 제품, 플랫폼 및 서비스를 사용 하 여 데이터를 수집 하 고, 모니터링 하 고, 경고 하 고, 조사 하 고, 대응 하 고, 연결할 수 있도록 하는 Siem (보안 정보 및 이벤트 관리) 콘텐츠입니다.

Microsoft 센티널의 콘텐츠에는 다음과 같은 형식이 포함 되어 있습니다.

- **[데이터 커넥터](connect-data-sources.md)** 는 다른 원본에서 Microsoft 센티널로 로그 수집을 제공 합니다.
- **[파서는](normalization-about-parsers.md)** [asim](normalization.md) 형식에 로그 형식/변환을 제공 하 여 다양 한 Microsoft 센티널 콘텐츠 형식 및 시나리오에서 사용을 지원 합니다.
- **[통합 문서](get-visibility.md)** 는 Microsoft 센티널의 데이터와 모니터링, 시각화 및 상호 작용을 제공 하 여 사용자에 게 의미 있는 정보를 강조 표시 합니다.
- **[분석 규칙](detect-threats-built-in.md)** 은 인시던트를 통해 관련 SOC 작업을 가리키는 경고를 제공 합니다.
- **[구하기 쿼리](hunting.md)** 는 SOC 팀에서 Microsoft 센티널의 위협을 사전에 사냥 하는 데 사용 됩니다.
- **[전자 필기장](notebooks.md)** 은 SOC 팀에서 Jupyter 및 Azure Notebooks의 고급 구하기 기능을 사용 하는 데 도움이 됩니다.
- **[Watchlists](watchlists.md)** 는 향상 된 위협 검색 및 축소 된 경고에 대 한 *특정* 데이터의 수집을 지원 피로
- 플레이 **[북 및 Azure Logic Apps 사용자 지정 커넥터](automate-responses-with-playbooks.md)** 는 Microsoft 센티널의 자동화 된 조사, 재구성 및 대응 시나리오에 대 한 기능을 제공 합니다.

Microsoft 센티널 *솔루션* 은 microsoft 센티널에서 종단 간 제품, 도메인 또는 업계 수직 시나리오를 충족 하는 microsoft 센티널 콘텐츠 또는 MICROSOFT 센티널 API 통합의 패키지입니다.

> [!TIP]
> 자신의 요구 사항에 맞게 기본 콘텐츠를 사용자 지정 하거나 콘텐츠를 사용 하 여 커뮤니티의 다른 사용자와 공유할 수 있는 고유한 솔루션을 만들 수 있습니다. 자세한 내용은 [Microsoft 센티널 solutions](https://aka.ms/sentinelsolutionsbuildguide) 솔루션 제작 및 게시를 위한 빌드 가이드를 참조 하세요.
>
## <a name="discover-and-manage-microsoft-sentinel-content"></a>Microsoft 센티널 콘텐츠 검색 및 관리

Microsoft 센티널 **콘텐츠 허브** 를 사용 하 여 기본 제공 콘텐츠 (기본 제공)를 중앙에서 검색 하 고 설치할 수 있습니다.

Microsoft 센티널 콘텐츠 허브는 제품 내 검색 기능, 단일 단계 배포를 제공 하 고, Microsoft 센티널의 수직 기본 제공 솔루션 및 콘텐츠를 제공 합니다.

- **콘텐츠 허브** 에서 [범주](#microsoft-sentinel-out-of-the-box-content-and-solution-categories) 및 기타 매개 변수를 기준으로 필터링 하거나 강력한 텍스트 검색을 사용 하 여 조직의 요구 사항에 가장 적합 한 콘텐츠를 찾습니다. 콘텐츠 **허브** 는 일부 콘텐츠가 Microsoft에서 유지 관리 되 고 다른 콘텐츠가 파트너 또는 커뮤니티에서 유지 관리 되므로 콘텐츠 허브는 각 콘텐츠 조각에 적용 되는 [지원 모델](#microsoft-sentinel-out-of-the-box-content-and-solution-support-models) 도 나타냅니다.

    Microsoft 센티널 **콘텐츠 허브** 를 통해 기본 [콘텐츠에 대 한 업데이트](sentinel-solutions-deploy.md#install-or-update-a-solution) 를 관리 하 고, **리포지토리** 페이지를 통해 사용자 지정 콘텐츠에 대 한 업데이트를 관리 합니다.

- 사용자 요구에 맞게 기본 콘텐츠를 사용자 지정 하거나 분석 규칙, 구하기 쿼리, 노트북, 통합 문서 등을 비롯 한 사용자 지정 콘텐츠를 만듭니다. Microsoft 센티널 작업 영역에서 microsoft 센티널 [API](/rest/api/securityinsights/)를 통하거나 Microsoft 센티널 [리포지토리](ci-cd.md) 페이지를 통해 사용자 고유의 소스 제어 리포지토리에서 직접 사용자 지정 콘텐츠를 관리 합니다.

### <a name="why-content-hub-and-solutions"></a>콘텐츠 허브 및 솔루션은 무엇 인가요?

Microsoft 센티널 *솔루션* 은 하나 이상의 도메인 또는 수직 시나리오에 대 한 종단 간 제품 값을 제공 하는 패키지 된 콘텐츠 또는 통합입니다.

솔루션의 검색 기능 및 배포에 대 한 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) 는 솔루션 환경을 제공 합니다. 자세한 내용은 솔루션 제작 및 게시에 대 한 [Microsoft 센티널 솔루션 빌드 가이드](https://aka.ms/sentinelsolutionsbuildguide) 를 참조 하세요.

Microsoft 센티널 솔루션은 제품 내 검색 기능, 단일 단계 배포 및 Microsoft 센티널에서 종단 간 제품, 도메인 및/또는 수직 시나리오를 제공 합니다. 이 환경은 솔루션의 검색 기능, 배포 및 활성화, 솔루션의 작성 및 게시에 대 한 기능을 제공 합니다.

- **패키지 된 콘텐츠** 는 데이터 커넥터, 통합 문서, 분석 규칙, 플레이 북, 사냥 쿼리, watchlists, 파서 등과 같은 하나 이상의 Microsoft 센티널 콘텐츠 모음입니다.

- **통합** 에는 azure와 기존 고객 응용 프로그램 간의 통합을 지 원하는 microsoft 센티널 또는 Azure Log Analytics api를 사용 하 여 빌드된 서비스 또는 도구, 해당 응용 프로그램에서 microsoft 센티널로 데이터, 쿼리 등을 마이그레이션하는 작업이 포함 됩니다.

또한 솔루션을 사용 하 여 즉시 콘텐츠를 즉시 사용할 수 있는 단일 단계로 기본 콘텐츠 패키지를 설치할 수 있습니다. 공급자 및 파트너는 결합 된 제품, 도메인 또는 수직 가치를 제공 하 여 투자를 productize 솔루션을 사용할 수 있습니다.

콘텐츠 허브를 사용 하 여 중앙에서 솔루션을 검색 하 고 배포 하 고 시나리오 기반 방식으로 기본 콘텐츠를 배포 합니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널 기본 콘텐츠 및 솔루션을 중앙에서 검색 및 배포](sentinel-solutions-deploy.md)
- [Microsoft 센티널 콘텐츠 허브 카탈로그](sentinel-solutions-catalog.md)

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-categories"></a>Microsoft 센티널 기본 콘텐츠 및 솔루션 범주

Microsoft 센티널 기본 콘텐츠는 다음 범주 중 하나 이상으로 적용할 수 있습니다. **콘텐츠 허브** 에서 표시 되는 내용을 변경 하기 위해 보려는 범주를 선택 합니다.

### <a name="domain-categories"></a>도메인 범주

| 범주의 이름입니다.  | Description |
| ---------- | ----------------------- |
| **애플리케이션**  | 웹, 서버 기반, SaaS, 데이터베이스, 통신 또는 생산성 워크 로드          |
| **클라우드 공급자**  | 클라우드 서비스|
| **호환성**   | 규정 준수 제품, 서비스 및 프로토콜  |
| **DevOps**       | 개발 운영 도구 및 서비스    |
| **ID**     | Id 서비스 공급자 및 통합     |
| **IoT(사물 인터넷)**    | IoT, OT 장치 및 인프라, 산업용 제어 서비스                   |
| **IT 운영**| IT를 관리 하는 제품 및 서비스   |
| **마이그레이션**    | 마이그레이션 활성화 제품, 서비스 및               |
| **네트워킹**   | 네트워크 제품, 서비스 및 도구    |
| **플랫폼**     | Microsoft 센티널 generic 또는 framework 구성 요소, 클라우드 인프라 및 플랫폼|
| **보안-기타**   | 다른 명확한 범주가 없는 기타 보안 제품 및 서비스           |
| **보안 위협 인텔리전스**  | 위협 인텔리전스 플랫폼, 피드, 제품 및 서비스        |
| **보안 위협 방지**   | 위협 방지, 메일 보호 및 XDR 및 endpoint protection 제품 및 서비스     |
| **보안 – 0 일 취약성**   | [Nobelium](/azure/security/fundamentals/recover-from-identity-compromise) 와 같은 제로 취약성 공격의 특수 솔루션 |
| **보안-자동화 (대화 충성도)**   | 보안 자동화, 대화 충성도 (보안 작업 및 자동화 된 응답), 보안 작업 및 인시던트 대응 제품 및 서비스입니다.   |
| **보안 – 클라우드 보안** | CASB (클라우드 액세스 Service Broker), CWPP (클라우드 워크 로드 보호 플랫폼), CWPP (클라우드 보안 상태 관리 및 기타 클라우드 보안 제품 및 서비스) |
| **보안 – Information Protection**   | 정보 보호 및 문서 보호 제품 및 서비스|
| **보안-참가자 위협**  | 보안 제품 및 서비스에 대 한 참가자 위협 및 사용자 및 엔터티 동작 분석 (UEBA)                |
| **보안 – 네트워크**    | 보안 네트워크 장치, 방화벽, NDR (네트워크 검색 및 응답), NIDP (네트워크 침입 및 검색 방지) 및 네트워크 패킷 캡처   |
| **보안 – 취약성 관리** | 취약성 관리 제품 및 서비스                    |
| **스토리지**      | 파일 저장소 및 파일 공유 제품 및 서비스                 |
| **교육 및 자습서**  | 교육, 자습서 및 온 보 딩 자산 |
| **사용자 동작 (UEBA)**    | 사용자 동작 분석 제품 및 서비스|
| | |

### <a name="industry-vertical-categories"></a>산업 수직 범주

| 범주의 이름입니다.  | Description |
| ---------- | ----------------------- |
| **항공학**  | Aeronautics 업계와 관련 된 제품, 서비스 및 콘텐츠 |
| **Education**    | 제품, 서비스 및 교육 업계 관련 콘텐츠   |
| **재무**      | 제품, 서비스 및 재무 업계 전용 콘텐츠     |
| **의료**   | 의료 업계와 관련 된 제품, 서비스 및 콘텐츠  |
| **제조업** | 제조 업계에 맞는 제품, 서비스 및 콘텐츠 |
| **정품**       | 제품, 서비스 및 소매 업계 전용 콘텐츠       |
| | |

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-support-models"></a>Microsoft 센티널 기본 콘텐츠 및 솔루션 지원 모델

Microsoft와 다른 조직은 모두 Microsoft의 기본 콘텐츠와 솔루션을 제작 합니다. 기본 콘텐츠 또는 솔루션의 각 부분에는 다음 지원 유형 중 하나가 있습니다.

| 지원 모델  | 설명 |
| ---------- | ----------------------- |
| **Microsoft 지원**| 적용 대상: <br>-Microsoft가 데이터 공급자 (해당 하는 경우), 작성자 인 콘텐츠/솔루션. <br> -Microsoft 이외의 데이터 원본에 대 한 Microsoft에서 작성 한 콘텐츠/솔루션입니다. <br><br>    Microsoft는 [Microsoft Azure 지원 계획](https://azure.microsoft.com/support/options/#overview)에 따라이 지원 모델에서 콘텐츠/솔루션을 지원 하 고 유지 관리 합니다. <br>파트너 또는 Community Microsoft가 아닌 모든 당사자가 작성 한 콘텐츠/솔루션을 지원 합니다.|
|**파트너 지원** | Microsoft 이외의 파티에서 작성 한 콘텐츠/솔루션에 적용 됩니다.  <br><br>   파트너 회사는 이러한 콘텐츠/솔루션에 대 한 지원 또는 유지 관리를 제공 합니다. 파트너 회사는 독립 소프트웨어 공급 업체, 관리 서비스 공급자 (MSP/MSSP), SI (시스템 통합자) 또는 선택한 콘텐츠/솔루션에 대 한 Microsoft 센티널 페이지에서 연락처 정보를 제공 하는 조직 일 수 있습니다.<br><br>    파트너 지원 솔루션에 대 한 모든 문제는 지정 된 지원 담당자에 게 문의 하세요.|
|**커뮤니티 지원** |Microsoft 센티널의 지원 및 유지 관리를 위해 나열 된 연락처가 없는 Microsoft 또는 파트너 개발자가 작성 한 콘텐츠/솔루션에 적용 됩니다.<br><br>    이러한 솔루션에 대 한 질문이 나 문제를 해결 하려면 [Microsoft 센티널 GitHub 커뮤니티](https://aka.ms/threathunters)에서 [문제](https://github.com/Azure/Azure-Sentinel/issues/new/choose) 를 해결 하세요. |
| | |

## <a name="next-steps"></a>다음 단계

Microsoft 센티널 콘텐츠에 대 한 정보를 살펴본 후에는 Microsoft 센티널 작업 영역에서 콘텐츠 및 솔루션 관리를 시작 합니다.

Microsoft 센티널 **콘텐츠 허브** 에서 솔루션을 검색 하 고 설치 합니다. 자세한 내용은 다음을 참조하세요.

- [중앙에서 제공 하는 콘텐츠 및 솔루션 검색 및 배포 (공개 미리 보기)](sentinel-solutions-deploy.md)
- [Microsoft 센티널 콘텐츠 허브 카탈로그](sentinel-solutions-catalog.md)
- [Microsoft 센티널 데이터 커넥터](connect-data-sources.md)
- [Microsoft 센티널 데이터 커넥터 찾기](data-connectors-reference.md)
