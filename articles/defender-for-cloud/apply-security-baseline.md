---
title: Azure 보안 기준 및 클라우드 용 Microsoft Defender를 사용 하 여 Windows 및 Linux OS 강화
description: Microsoft Defender for Cloud가 게스트 구성을 사용 하 여 OS 강화를 Azure 보안 벤치 마크의 지침과 비교 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 3ac0b2745ac7abe91bc9f1a122e98842ce0e4f0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529340"
---
# <a name="apply-azure-security-baselines-to-machines"></a>컴퓨터에 Azure 보안 기준 적용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

컴퓨터의 공격 노출 영역을 줄이고 알려진 위험을 방지 하려면 OS (운영 체제)를 최대한 안전 하 게 구성 하는 것이 중요 합니다.

Azure 보안 벤치 마크에는 [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) 및 [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md)에 대 한 보안 기준 문서를 초래한 OS 강화에 대 한 지침이 있습니다.

이 문서에 설명 된 보안 권장 사항을 사용 하 여 사용자 환경의 컴퓨터를 평가 하 고 다음을 수행 합니다.

- 보안 구성의 간격 식별
- 이러한 격차를 해결 하는 방법 알아보기

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|Free|
|필수 조건:|컴퓨터는 (1) 작업 그룹의 구성원 이어야 합니다. (2)에는 게스트 구성 확장이 있고 (3) 시스템 할당 관리 id가 있으며 (4) 지원 되는 OS를 실행 중 이어야 합니다.<br>• Windows Server 2012, 2012r2, 2016 또는 2019<br>• Ubuntu 14.04, 16.04, 17.04, 18.04 또는 20.04<br>• Debian 7, 8, 9 또는 10<br>• CentOS 7 또는 8<br>• Red Hat Enterprise Linux (RHEL) 7 또는 8<br>• Oracle Linux 7 또는 8<br>• SUSE Linux Enterprise Server 12|
|필요한 역할 및 권한:|게스트 구성 확장 및 해당 필수 구성 요소를 설치 하려면 관련 컴퓨터에 **쓰기** 권한이 있어야 합니다.<br>권장 사항을 **확인** 하 고 OS 기준 데이터를 탐색 하려면 구독 수준에서 **읽기** 권한이 필요 합니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가 (Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>강화 권장 사항은 무엇 인가요?

Microsoft Defender for Cloud에는 환경의 Windows 및 Linux 컴퓨터 구성이 Azure 보안 기준 구성을 충족 하는지 여부를 확인 하는 두 가지 권장 사항이 포함 되어 있습니다.

- **Windows** 컴퓨터의 경우 [Windows 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) 에서 제공). 구성을 [Windows 보안 기준과](../governance/policy/samples/guest-configuration-baseline-windows.md)비교 합니다.
- Linux **컴퓨터의 경우 linux** [컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) 에서 제공). 구성을 [linux 보안 기준과](../governance/policy/samples/guest-configuration-baseline-linux.md)비교 합니다.

이러한 권장 사항은 Azure Policy의 게스트 구성 기능을 사용 하 여 컴퓨터의 OS 구성을 [Azure 보안 벤치 마크](/security/benchmark/azure/overview)에 정의 된 기준선과 비교 합니다.

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>구독의 컴퓨터와 OS 보안 기준을 비교 합니다.

컴퓨터를 OS 보안 기준과 비교 하려면:
 
1. 클라우드의 포털 용 Defender 페이지에서 **권장 사항** 페이지를 엽니다. 
1. 관련 권장 사항을 선택 합니다.
    - **Windows** 컴퓨터의 경우 [Windows 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동 됨)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) .
    - Linux **컴퓨터의** 경우 [linux 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) .

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="컴퓨터의 OS 구성을 관련 Azure 보안 기준과 비교 하기 위한 두 가지 권장 사항입니다." lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. 권장 사항 세부 정보 페이지에서 다음을 확인할 수 있습니다.
    1. 영향을 받는 리소스입니다.
    1. 특정 보안 검사에 실패 했습니다.

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Windows 컴퓨터의 기본 구성에서 취약점에 대 한 Windows 권장 사항에 대 한 권장 사항 세부 정보 페이지" lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. 특정 찾기에 대 한 자세한 내용을 보려면 해당 파일을 선택 합니다.

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="지정 된 보안 기준에 대 한 OS 구성의 게스트 구성 비교에서 특정 찾기에 대 한 자세한 내용을 Learning 합니다." lightbox="media/apply-security-baseline/finding-details.png":::

1. 기타 조사 가능성:

    - 평가 된 컴퓨터 목록을 보려면 **영향을 받는 리소스** 를 엽니다.
    - 하나의 컴퓨터에 대 한 검색 결과 목록을 보려면 **비정상 리소스** 탭에서 컴퓨터를 선택 합니다. 해당 컴퓨터에 대 한 검색 내용만 나열 하는 페이지가 열립니다.


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>FAQ-보안 기준에 따라 OS 강화

- [보안 구성 권장 사항에 대 한 필수 구성 요소를 배포 어떻게 할까요??](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [컴퓨터가 적용 되지 않는 것으로 표시 되는 이유는 무엇 인가요?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>보안 구성 권장 사항에 대 한 필수 구성 요소를 배포 어떻게 할까요??

해당 필수 구성 요소를 사용 하 여 게스트 구성 확장을 배포 하려면 다음을 수행 합니다.

- 선택한 컴퓨터의 경우 보안 **모범 사례 구현** 보안 제어에서 **컴퓨터에 게스트 구성 확장을 설치 해야** 합니다. 보안 권장 사항을 따릅니다.

- 규모에 따라 정책 이니셔티브 **배포 필수 구성 요소를 할당 하 여 가상 머신에서 게스트 구성 정책을 사용 하도록 설정** 합니다.


### <a name="why-is-a-machine-shown-as-not-applicable"></a>컴퓨터가 적용 되지 않는 것으로 표시 되는 이유는 무엇 인가요?

**해당 없음** 탭의 리소스 목록에는 **이유** 열이 있습니다. 몇 가지 일반적인 이유는 다음과 같습니다.

| 이유                                                            | 세부 정보                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **컴퓨터에서 검색 데이터를 사용할 수 없습니다.**                         | Azure 리소스 Graph에이 컴퓨터에 대 한 준수 결과가 없습니다. 모든 준수 결과는 게스트 구성 확장을 통해 Azure 리소스 Graph에 기록 됩니다. [Azure Policy 게스트 구성-샘플 ARG 쿼리에서](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration)샘플 쿼리를 사용 하 여 Azure 리소스 Graph에서 데이터를 확인할 수 있습니다.|
| **게스트 구성 확장이 컴퓨터에 설치 되어 있지 않습니다.** | Azure 보안 기준 준수를 평가 하기 위한 필수 요소인 게스트 구성 확장이 컴퓨터에 없습니다.                               |
| **시스템에서 관리 되는 id가 컴퓨터에 구성 되어 있지 않습니다.**      | 시스템 할당 관리 id가 컴퓨터에 배포 되어야 합니다.                                                                                                           |
| **권장 사항은 정책에서 사용 하지 않도록 설정 됩니다.**                      | OS 기준을 평가 하는 정책 정의는 관련 컴퓨터를 포함 하는 범위에서 사용 하지 않도록 설정 됩니다.                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>다음 단계
이 문서에서는 클라우드의 게스트 구성 권장 사항에 대해 Defender를 사용 하 여 OS의 강화를 Azure 보안 기준과 비교 하는 방법을 배웠습니다.

이러한 구성 설정에 대 한 자세한 내용은 다음을 참조 하세요.

- [Windows 보안 기준](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Linux 보안 기준](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Azure Security Benchmark](/security/benchmark/azure/overview)
