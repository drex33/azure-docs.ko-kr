---
title: 정규화 및 ASIM(Azure Sentinel 정보 모델) | Microsoft Docs
description: 이 문서에서는 Azure Sentinel이 ASIM(Azure Sentinel 정보 모델)을 사용하여 다양한 원본의 데이터를 정규화하는 방법을 설명합니다.
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
ms.date: 06/15/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9bb09b6a9b65e0e10b2d7d2d1b02e29dde3e85fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009228"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>정규화 및 ASIM(Azure Sentinel 정보 모델)(퍼블릭 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel은 많은 원본에서 데이터를 수집합니다. 다양한 데이터 형식과 테이블을 함께 사용하려면 각 데이터 형식을 이해하고 각 형식 또는 스키마의 분석 규칙, 통합 문서, 헌팅 쿼리 등에 관한 고유한 데이터 세트를 작성하고 사용해야 합니다.


경우에 따라 데이터 형식이 방화벽 디바이스와 같은 공통 요소를 공유하는 경우에도 별도의 규칙, 통합 문서, 쿼리가 필요합니다. 조사 및 헌팅 중에 다양한 데이터 형식 간에 상관 관계를 지정하는 것은 어려울 수도 있습니다.

이 문서에서는 여러 형식의 데이터를 처리하는 문제에 대한 솔루션을 제공하는 ASIM(Azure Sentinel 정보 모델)의 개요를 제공합니다.

> [!TIP]
> 또한 [ASIM 웨비나](https://www.youtube.com/watch?v=WoGD-JeC7ng)를 시청하거나 [웨비나 슬라이드](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)를 검토하세요. 자세한 내용은 [다음 단계](#next-steps)를 참조하세요.
>

> [!IMPORTANT]
> ASIM은 현재 미리 보기 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="common-asim-usage"></a>일반적인 ASIM 사용

ASIM(Azure Sentinel 정보 모델)은 다음 기능을 제공하여 균일하고 정규화된 보기에서 다양한 원본을 처리할 수 있는 원활한 환경을 제공합니다.

- **원본 간 검색**. 정규화된 분석 규칙은 원본, 온-프레미스, 클라우드에서 작동하며 무차별 암호 대입(brute force)과 같은 공격이나 OKTA, AWS, Azure를 비롯한 시스템 간 불가능한 이동을 탐지합니다.

- **원본 중립적 콘텐츠**. ASIM을 사용하는 기본 제공 및 사용자 지정 콘텐츠의 적용 범위는 콘텐츠가 생성된 후 원본이 추가된 경우에도 ASIM을 지원하는 모든 원본으로 자동으로 확장됩니다. 예를 들어, 프로세스 이벤트 분석은 엔드포인트용 Microsoft Defender, Windows 이벤트, Sysmon과 같이 고객이 데이터를 가져오는 데 사용할 수 있는 모든 원본을 지원합니다.

- 기본 제공 분석에서 **사용자 지정 원본에 대한 지원**

- **사용 편의성** 분석가가 ASIM을 학습한 후에는 필드 이름이 항상 동일하기 때문에 쿼리 작성이 훨씬 더 간단합니다.

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM 및 오픈 소스 보안 이벤트 메타데이터

Azure Sentinel 정보 모델은 [OSSEM(오픈 소스 보안 이벤트 메타데이터)](https://ossemproject.com/intro.html) 일반 정보 모델에 맞게 조정되므로 정규화된 테이블에서 예측 가능한 엔터티 상관 관계를 허용합니다.

OSSEM은 다양한 데이터 원본 및 운영 체제에서 보안 이벤트 로그의 설명서 및 표준화에 주로 중점을 둔 커뮤니티 주도 프로젝트입니다. 또한 이 프로젝트는 보안 분석가가 다양한 데이터 원본에서 데이터를 쿼리하고 분석할 수 있도록 데이터 정규화 절차 중에 데이터 엔지니어에게 사용할 수 있는 CIM(Common Information Model)을 제공합니다.

자세한 내용은 [OSSEM 참조 설명서](https://ossemproject.com/cdm/guidelines/entity_structure.html)를 참조하세요.

## <a name="asim-components"></a>ASIM 구성 요소

다음 이미지는 정규화되지 않은 데이터를 정규화된 콘텐츠로 변환하고 Azure Sentinel에서 사용할 수 있는 방법을 보여 줍니다. 예를 들어, 사용자 지정 제품별 정규화가 아닌 테이블로 시작하고 파서와 정규화 스키마를 사용하여 해당 테이블을 정규화된 데이터로 변환할 수 있습니다. Microsoft 및 사용자 지정 분석, 규칙, 통합 문서, 쿼리 등에서 둘 다 정규화된 데이터를 사용합니다.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Azure Sentinel에서 정규화되지 않은 데이터를 정규화된 데이터로 변환 흐름 및 사용":::

Azure Sentinel 정보 모델에는 다음 구성 요소가 포함됩니다.

|구성 요소  |설명  |
|---------|---------|
|**정규화된 스키마**     |   통합 기능을 빌드할 때 사용할 수 있는 예측 가능한 이벤트 유형의 표준 세트를 포함합니다. <br><br>각 스키마는 이벤트, 정규화된 열 명명 규칙, 필드 값의 표준 형식을 나타내는 필드를 정의합니다. <br><br> ASIM은 현재 다음 스키마를 정의합니다.<br> - [네트워크 세션](./network-normalization-schema.md)<br> - [DNS 작업](dns-normalization-schema.md)<br> - [프로세스 이벤트](process-events-normalization-schema.md)<br> - [인증 이벤트](authentication-normalization-schema.md)<br> - [레지스트리 이벤트](registry-event-normalization-schema.md)<br> - [파일 활동](file-event-normalization-schema.md)  <br><br>자세한 내용은 [Azure Sentinel 정보 모델 스키마](normalization-about-schemas.md)를 참조하세요.  |
|**파서**     |  [KQL 함수](/azure/data-explorer/kusto/query/functions/user-defined-functions)를 사용하여 기존 데이터를 정규화된 스키마에 매핑합니다. <br><br>[Azure Sentinel GitHub Parsers 폴더](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers)에서 Microsoft에서 개발한 정규화 파서를 배포합니다. 정규화된 파서는 **ASim***으로 시작하는 하위 폴더에 있습니다.  <br><br>자세한 내용은 [Azure Sentinel 정보 모델 파서](normalization-about-parsers.md)를 참조하세요.     |
|**각 정규화된 스키마의 콘텐츠**     |    분석 규칙, 통합 문서, 헌팅 쿼리 등이 포함됩니다. 각 정규화된 스키마의 콘텐츠는 원본별 콘텐츠를 만들지 않고도 모든 정규화된 데이터에서 작동합니다. <br><br>자세한 내용은 [Azure Sentinel 정보 모델 콘텐츠](normalization-content.md)를 참조하세요.   |
| | |

### <a name="asim-terminology"></a>ASIM 용어

Azure Sentinel 정보 모델은 다음 용어를 사용합니다.

|용어  |설명  |
|---------|---------|
|**보고 디바이스**     |   Azure Sentinel로 레코드를 보내는 시스템입니다. 이 시스템은 전송되는 레코드의 주체 시스템이 아닐 수 있습니다.      |
|**레코드**     |보고 디바이스에서 전송되는 데이터의 단위입니다. 레코드는 `log`, `event` 또는 `alert`라고도 하지만 다른 형식일 수도 있습니다.         |
|**콘텐츠** 또는 **콘텐츠 항목**     |Azure Sentinel에서 사용할 수 있는 다른 사용자 지정 가능한 아티팩트 또는 사용자가 만든 아티팩트입니다. 해당 아티팩트에는 분석 규칙, 헌팅 쿼리, 통합 문서 등이 포함됩니다. 콘텐츠 항목은 이러한 아티팩트 중 하나입니다.|
| | |

<br>

## <a name="getting-started-with-asim"></a>ASIM 시작

ASIM 사용을 시작하려면 다음을 수행합니다.

1. [Azure 센티널 GitHub 리포지토리에서](https://aka.ms/AzSentinelASim)모든 asim 파서를 신속 하 게 배포 합니다.

1. ASIM을 사용하는 분석 규칙 템플릿을 활성화합니다. 자세한 내용은 [ASIM(Azure Sentinel 정보 모델) 콘텐츠 목록](normalization-content.md#builtin)을 참조하세요.

1. 다음 방법을 사용하여 작업 영역에서 ASIM을 사용합니다.

    - Azure Sentinel **로그** 페이지에서 KQL로 로그를 쿼리하는 경우 Azure Sentinel GitHub 리포지토리의 ASIM 헌팅 쿼리를 사용합니다. 자세한 내용은 [ASIM(Azure Sentinel 정보 모델) 콘텐츠 목록](normalization-content.md#builtin)을 참조하세요.

    - ASIM을 사용하여 고유한 분석 규칙을 작성하거나 [기존 규칙을 변환](normalization-content.md#builtin)합니다.

    - 사용자 지정 원본의 [파서를 작성](normalization-about-parsers.md)하고 관련 원본 중립적 파서에 [추가](normalization-about-parsers.md#include)하여 기본 제공 분석을 사용하기 위해 사용자 지정 데이터를 사용하도록 설정합니다.

## <a name="next-steps"></a><a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 정규화와 Azure Sentinel 정보 모델의 정규화 개요를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [ASIM 웨비나](https://www.youtube.com/watch?v=WoGD-JeC7ng)를 시청하거나 [슬라이드](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)를 검토
- [Azure Sentinel 정보 모델 스키마](normalization-about-schemas.md)
- [Azure Sentinel 정보 모델 파서](normalization-about-parsers.md)
- [Azure Sentinel 정보 모델 콘텐츠](normalization-content.md)
