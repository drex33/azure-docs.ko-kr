---
title: 클라우드 위협 인텔리전스 보고서에 대 한 Microsoft Defender | Microsoft Docs
description: 이 페이지에서는 보안 경고에 대 한 자세한 정보를 찾기 위해 조사 중에 Microsoft Defender를 사용 하 여 클라우드 위협 인텔리전스 보고서를 사용할 수 있습니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2021
ms.author: memildin
ms.openlocfilehash: 190cc14274b8e96434ab82fe7ff1df486d1b608b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472089"
---
# <a name="microsoft-defender-for-cloud-threat-intelligence-report"></a>클라우드 위협 인텔리전스 보고서에 대 한 Microsoft Defender

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 페이지에서는 클라우드의 위협 인텔리전스 보고서에 대 한 Microsoft Defender가 보안 경고를 트리거한 위협에 대해 자세히 알아보는 데 도움이 되는 방법을 설명 합니다.

## <a name="what-is-a-threat-intelligence-report"></a>위협 인텔리전스 보고서란?

클라우드의 위협 방지를 위한 Defender는 Azure 리소스, 네트워크 및 연결 된 파트너 솔루션의 보안 정보를 모니터링 하는 방식으로 작동 합니다. 위협을 식별하도록 종종 여러 소스의 정보를 상호 연결하는 이 정보를 분석합니다. 자세한 내용은 [Cloud For Cloud에서 위협 검색 및 위협에 대응 하는 방법](alerts-overview.md#detect-threats)을 참조 하세요.

Defender for Cloud는 위협을 식별 하는 경우 업데이트 제안을 포함 하 여 이벤트에 대 한 자세한 정보를 포함 하는 [보안 경고](managing-and-responding-alerts.md)를 트리거합니다. 인시던트 대응 팀이 위협을 조사 하 고 해결 하는 데 도움이 되도록 Defender for Cloud는 검색 된 위협에 대 한 정보가 포함 된 위협 인텔리전스 보고서를 제공 합니다. 보고서에는 다음과 같은 정보가 포함됩니다.

* 공격자의 ID 또는 연결(이 정보가 제공되는 경우)
* 공격자의 목표
* 현재 및 과거 공격 캠페인(이 정보가 제공되는 경우)
* 공격자의 전술, 도구 및 프로시저
* URL, 파일 해시 등 관련 IoC(보안 침해 지표)
* 피해자 유형(Azure 리소스가 위험한 상태에 있는지 결정하는 데 도움이 되는 산업적 유행 및 지리적 유행)
* 마이그레이션 및 수정 정보

> [!NOTE]
> 세부 수준이 맬웨어 활동 및 유행에 기반하기 때문에 특정 보고서의 정보 량은 다양하게 변합니다.

Defender for Cloud에는 공격에 따라 달라질 수 있는 세 가지 유형의 위협 보고서가 있습니다. 제공되는 보고서는 다음과 같습니다.

* **활동 그룹 보고서**: 공격자와 이들의 목표 및 전술에 대해 자세히 설명합니다.
* **캠페인 보고서**: 특정 공격 캠페인의 세부 정보에 중점을 둡니다.
* **위협 요약 보고서**: 이전 두 보고서의 모든 항목을 포함하고 있습니다.

해당 유형의 정보는 공격의 소스, 공격자의 동기, 문제를 완화하기 위한 조치를 파악하기 위해 지속적인 조사를 진행하는 인시던트 대응 프로세스에서 유용합니다.

## <a name="how-to-access-the-threat-intelligence-report"></a>어떻게 위협 인텔리전스 보고서에 액세스하는가?

1. 클라우드 용 Defender의 메뉴에서 **보안 경고** 페이지를 엽니다.
1. 경고를 선택합니다. 

    경고에 대한 자세한 내용이 포함된 경고 정보 페이지가 열립니다. 다음은 **랜섬웨어 지표에 감지된** 경고의 세부 정보 페이지입니다.

    [![랜섬웨어 지표에 감지된 경고의 세부 정보 페이지.](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. 보고서 링크를 선택하면 기본 브라우저에서 PDF가 열립니다.

    [![잠재적으로 안전하지 않은 작업 경고 세부 정보 페이지.](media/threat-intelligence-reports/threat-intelligence-report.png)](media/threat-intelligence-reports/threat-intelligence-report.png#lightbox)

    필요에 따라 PDF 보고서를 다운로드할 수 있습니다. 

    >[!TIP]
    > 각 보안 경고에 제공되는 정보 량은 경고 유형에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

이 페이지에서는 보안 경고를 조사할 때 위협 인텔리전스 보고서를 여는 방법을 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

* [Microsoft Defender For Cloud의 보안 경고를 관리 하 고 대응](managing-and-responding-alerts.md)합니다. 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Microsoft Defender for Cloud에서 보안 인시던트 처리](incidents.md)
