---
title: Azure 보안 기준 및 Microsoft Defender for Cloud를 사용하여 Windows 및 Linux OS 강화
description: Microsoft Defender for Cloud가 게스트 구성을 사용하여 OS 강화를 Azure Security Benchmark의 지침과 비교하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/04/2021
ms.author: memildin
ms.openlocfilehash: 2516d95ddf8a74ce11239666b647ee2a666422dd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453215"
---
# <a name="apply-azure-security-baselines-to-machines"></a>컴퓨터에 Azure 보안 기준 적용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

머신의 공격 노출을 줄이고 알려진 위험을 방지하려면 OS(운영 체제)를 최대한 안전하게 구성하는 것이 중요합니다.

Azure Security Benchmark에는 OS 강화에 대한 지침이 있으며, 이로 인해 [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) 및 [Linux에](../governance/policy/samples/guest-configuration-baseline-linux.md)대한 보안 기준 문서가 작성되었습니다.

이 문서에 설명된 보안 권장 사항을 사용하여 사용자 환경의 머신을 평가하고 다음을 수행합니다.

- 보안 구성의 간격 식별
- 이러한 차이를 해결하는 방법 알아보기

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|Free|
|필수 조건:|머신은 (1) 작업 그룹의 구성원이어야 하고, (2)에는 게스트 구성 확장이 있어야 하고, (3)에는 시스템 할당 관리 ID가 있어야 하며, (4) 지원되는 OS를 실행해야 합니다.<br>• Windows Server 2012, 2012r2, 2016 또는 2019<br>• Ubuntu 14.04, 16.04, 17.04, 18.04 또는 20.04<br>• Debian 7, 8, 9 또는 10<br>• CentOS 7 또는 8<br>• RHEL(Red Hat Enterprise Linux) 7 또는 8<br>• Oracle Linux 7 또는 8<br>• SUSE Linux Enterprise Server 12|
|필요한 역할 및 권한:|게스트 구성 확장 및 해당 필수 구성을 설치하려면 관련 머신에 대한 **쓰기** 권한이 필요합니다.<br>권장 사항을 **보고** OS 기준 데이터를 탐색하려면 구독 수준에서 **읽기** 권한이 필요합니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>강화 권장 사항은 무엇인가요?

클라우드용 Microsoft Defender에는 사용자 환경에서 Windows 및 Linux 머신의 구성이 Azure 보안 기준 구성을 충족하는지 확인하는 두 가지 권장 사항이 포함되어 있습니다.

- **Windows** 컴퓨터의 경우 [Windows 컴퓨터의 보안 구성 취약성을 수정해야 합니다(게스트 구성에서 구동)는](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) 구성을 [Windows 보안 기준과](../governance/policy/samples/guest-configuration-baseline-windows.md)비교합니다.
- **Linux 머신의** 경우 [Linux 머신의 보안 구성 취약성을 수정해야 합니다(게스트 구성에서 구동)는](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) 구성을 [Linux 보안 기준](../governance/policy/samples/guest-configuration-baseline-linux.md)과 비교합니다.

이러한 권장 사항은 Azure Policy 게스트 구성 기능을 사용하여 컴퓨터의 OS 구성을 Azure Security [Benchmark](/security/benchmark/azure/overview)에 정의된 기준과 비교합니다.

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>구독의 머신을 OS 보안 기준과 비교

컴퓨터를 OS 보안 기준과 비교하려면 다음을 수행합니다.
 
1. Defender for Cloud의 포털 페이지에서 **권장 사항** 페이지를 엽니다. 
1. 관련 권장 사항을 선택합니다.
    - **Windows** 컴퓨터의 경우 Windows 컴퓨터의 보안 구성 취약성을 [수정해야 합니다(게스트 구성으로 구동).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
    - **Linux 머신의** 경우 [Linux 머신의 보안 구성 취약성을 수정해야 합니다(게스트 구성에서 구동).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="머신의 OS 구성을 관련 Azure 보안 기준과 비교하기 위한 두 가지 권장 사항입니다." lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. 권장 사항 세부 정보 페이지에서 다음을 확인할 수 있습니다.
    1. 영향을 받는 리소스입니다.
    1. 실패한 특정 보안 검사입니다.

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Windows 머신의 기준 구성에서 취약성에 대한 Windows 권장 사항의 권장 사항 세부 정보 페이지입니다." lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. 특정 찾기에 대해 자세히 알아보려면 선택합니다.

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="OS 구성과 정의된 보안 기준의 게스트 구성 비교에서 구체적인 결과를 Learning." lightbox="media/apply-security-baseline/finding-details.png":::

1. 기타 조사 가능성:

    - 평가된 컴퓨터 목록을 보려면 영향을 받는 **리소스 를 엽니다.**
    - 한 컴퓨터에 대한 결과 목록을 보려면 **비정상 리소스** 탭에서 컴퓨터를 선택합니다. 페이지가 열리고 해당 머신에 대한 결과만 나열됩니다.


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>FAQ - 보안 기준에 따라 OS 강화

- [보안 구성 권장 사항에 대한 필수 구성 어떻게 할까요? 배포해야 합니까?](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [컴퓨터가 해당되지 않는 것으로 표시된 이유는 무엇인가요?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>보안 구성 권장 사항에 대한 필수 구성 어떻게 할까요? 배포해야 합니까?

필수 구성 조건으로 게스트 구성 확장을 배포하려면 다음을 수행합니다.

- 선택한 컴퓨터의 경우 보안 모범 사례 구현 보안 컨트롤에서 **컴퓨터에 게스트 구성 확장을 설치해야 한다는** 보안 권장 **사항을** 따릅니다.

- 대규모로 정책 이니셔티브 **배포 필수 구성을 할당하여 가상 머신에서 게스트 구성 정책을 사용하도록 설정합니다.**


### <a name="why-is-a-machine-shown-as-not-applicable"></a>컴퓨터가 해당되지 않는 것으로 표시된 이유는 무엇인가요?

**해당하지 않음** 탭의 리소스 **목록에는 이유** 열이 포함됩니다. 몇 가지 일반적인 이유는 다음과 같습니다.

| 이유                                                            | 세부 정보                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **컴퓨터에서 사용할 수 있는 검사 데이터가 없음**                         | Azure Resource Graph 이 머신에 대한 규정 준수 결과가 없습니다. 모든 준수 결과는 게스트 구성 확장에서 Graph Azure Resource에 기록됩니다. Azure Policy 게스트 구성 - [샘플 ARG](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration)쿼리의 샘플 쿼리를 사용하여 Azure Resource Graph 데이터를 확인할 수 있습니다.|
| **게스트 구성 확장이 컴퓨터에 설치되어 있지 않습니다.** | 컴퓨터에 게스트 구성 확장이 없습니다. 이 확장은 Azure 보안 기준 준수를 평가하기 위한 필수 조건입니다.                               |
| **시스템 관리 ID가 컴퓨터에 구성되지 않음**      | 시스템이 할당한 관리 ID를 컴퓨터에 배포해야 합니다.                                                                                                           |
| **권장 사항은 정책에서 사용하지 않도록 설정됩니다.**                      | OS 기준을 평가하는 정책 정의는 관련 머신을 포함하는 범위에서 사용하지 않도록 설정됩니다.                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>다음 단계
이 문서에서는 Defender for Cloud의 게스트 구성 권장 사항을 사용하여 OS 강화를 Azure 보안 기준과 비교하는 방법을 배웠습니다.

이러한 구성 설정에 대한 자세한 내용은 다음을 참조하세요.

- [Windows 보안 기준](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Linux 보안 기준](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Azure Security Benchmark](/security/benchmark/azure/overview)