---
title: Azure Sentinel에서 기본 제공 분석 규칙을 사용하여 위협 감지 | Microsoft Docs
description: 의심스러운 상황이 발생할 때 사용자에게 알리는 기본 제공 템플릿을 기준으로 하는 기본 제공 위협 검색 규칙을 사용하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2021
ms.author: yelevin
ms.openlocfilehash: bcb84b0beba762342c6ecf8b8f3be81cbcaf24d8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535462"
---
# <a name="detect-threats-out-of-the-box"></a>곧바로 위협 탐지

Azure Sentinel에 [데이터 원본을 연결](quickstart-onboard.md)한 후에는 의심스러운 상황이 발생하면 알림이 표시되어야 합니다. 바로 이때문에 Azure Sentinel에서는 위협 검색 규칙을 만드는 데 도움이 되는 기본 제공 템플릿을 제공하고 있습니다.

Microsoft의 보안 전문가와 분석가 팀은 알려진 위협, 일반적인 공격 벡터, 의심스러운 활동 에스컬레이션 체인을 바탕으로 규칙 템플릿을 설계했습니다. 이러한 템플릿에서 생성된 규칙은 사용자 환경에서 의심스러운 활동을 자동으로 검색합니다. 많은 템플릿을 사용자 지정하여 활동을 검색하거나, 필요하다면 필터를 지정할 수 있습니다. 이러한 규칙을 통해 생성되는 경고는 사용자 환경에서 할당하고 조사할 수 있는 인시던트를 만듭니다.

이 문서는 Azure Sentinel을 사용하여 위협을 탐지하는 방법을 이해하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 기본 제공 위협 검색 사용
> * 위협 응답 자동화

## <a name="view-built-in-detections"></a>기본 제공 검색 보기

Azure Sentinel의 모든 분석 규칙 및 검색을 보려면 **분석** > **규칙 템플릿** 으로 이동하세요. 이 탭에는 Azure Sentinel 기본 제공 규칙이 모두 포함됩니다.

:::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="기본 제공 탐지를 사용하여 Azure Sentinel로 위협 요소 찾기":::

기본 제공 검색에는 다음이 포함됩니다.

|규칙 유형  |설명  |
|---------|---------|
|**Microsoft 보안**     |  Microsoft 보안 템플릿은 다른 Microsoft 보안 솔루션에서 생성된 경고에서 실시간으로 Azure Sentinel 인시던트를 자동으로 만듭니다. Microsoft 보안 규칙을 템플릿으로 사용하여 유사한 논리로 새 규칙을 만들 수 있습니다. <br><br>보안 규칙에 대한 자세한 내용은 [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)을 참조하세요.       |
|**Fusion**     | Fusion 기술을 기준으로 하는 Azure Sentinel의 고급 다단계 공격 감지는 여러 제품에서 발생하는 여러 가지 낮은 충실도 경고 및 이벤트를 높은 충실도 및 실행 가능한 인시던트에 상호 연관 지을 수 있는 스케일링 가능한 머신 러닝 알고리즘을 사용합니다. Fusion은 기본적으로 사용하도록 설정되어 있습니다. 논리는 숨겨져 있어서 사용자 지정할 수 없으므로 이 템플릿을 사용하여 규칙을 하나만 만들 수 있습니다. <br><br>또한 Fusion 엔진은 [예약된 분석 규칙](#scheduled)에 의해 생성된 경고를 다른 시스템의 경고와 상호 연관시키며, 결과적으로는 충실도가 높은 인시던트를 생성할 수 있습니다.      |
|**ML(기계 학습)동작 분석**     |    ML 동작 분석 템플릿은 독점적인 Microsoft 기계 학습 알고리즘을 기준으로 하므로 작동 방법 및 실행 시기에 대한 내부 논리를 볼 수 없습니다. <br><br>논리는 숨겨져 있어서 사용자 지정할 수 없으므로 이 형식의 각 템플릿을 사용하여 규칙을 하나만 만들 수 있습니다.|
|<a name="anomaly"></a>**변칙**     |    변칙 규칙 템플릿은 SOC-ML(기계 학습)을 사용하여 특정 유형의 비정상적인 동작을 감지합니다. 각 규칙에는 분석 중인 동작에 적합한 고유한 매개 변수와 임계값이 있습니다. <br><br>해당 규칙 구성은 변경하거나 미세 조정할 수 없지만 규칙을 복제하고, 복제본을 변경 및 미세 조정할 수 있습니다. 이 경우 **플라이트** 모드에서 복제본을 실행하고 **프로덕션** 모드에서 원본을 동시에 실행합니다. 그런 다음 결과를 비교하고 원하는 대로 미세 조정하면 복제본을 **프로덕션** 으로 전환합니다. <br><br>자세한 내용은 [SOC-ML 변칙을 사용하여 Azure Sentinel에서 위협 검색](soc-ml-anomalies.md) 및 [Azure Sentinel에서 변칙 검색 분석 규칙 사용](work-with-anomaly-rules.md)을 참조하세요.     |
| <a name="scheduled"></a>**예약됨**    |    예약된 분석 규칙은 Microsoft 보안 전문가가 작성한 기본 제공 쿼리를 기준으로 합니다. 쿼리 논리를 보고 변경할 수 있습니다. 예약된 규칙 템플릿을 사용하고 쿼리 논리 및 일정 설정을 사용자 지정하여 새 규칙을 만들 수 있습니다. <br><br>몇 가지 새로운 예약된 분석 규칙 템플릿은 Fusion 엔진에서 다른 시스템의 경고와 상관 관계가 있는 경고를 생성하여 충실도가 높은 인시던트를 생성합니다. 자세한 내용은 [고급 다단계 공격 탐지](fusion.md#configure-scheduled-analytics-rules-for-fusion-detections)를 참조하세요.<br><br>**팁**: 규칙 예약 옵션에는 규칙을 사용하도록 설정할 때 시작되는 시계와 함께 지정된 분, 시간 또는 일 수마다 규칙을 실행하도록 구성하는 것이 포함됩니다. <br><br>규칙이 제때에 새로운 인시던트 스택을 가져올 수 있도록 새 분석 규칙이나 편집된 분석 규칙을 사용하는 경우를 염두에 두는 것이 좋습니다. 예를 들어 SOC 분석가가 근무일을 시작할 때와 동기화하여 규칙을 실행하고 그때 규칙을 사용하도록 설정할 수 있습니다.|
| | |

> [!IMPORTANT]
>  - Fusion 규칙 템플릿의 일부 검색은 현재 **미리 보기** 로 제공됩니다. 미리 보기로 제공되는 검색을 확인하려면 [Azure Sentinel에서 고급 다단계 공격 감지](fusion.md)를 참조하세요.
>
> -  변칙 규칙 템플릿은 현재 **미리 보기** 로 제공됩니다.
>
> - 머신 러닝 동작 분석 규칙 템플릿은 현재 **미리 보기** 상태입니다. ML 동작 분석 템플릿을 기준으로 하는 규칙을 만들고 사용하도록 설정하여 머신 러닝 엔진과 모델로 처리하는 데 필요한 대로 **Azure Sentinel 작업 영역의 지리적 위치 외부에서 수집한 데이터를 복사하기 위한 Microsoft 권한을 부여** 합니다.
>
> 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="use-built-in-analytics-rules"></a>기본 제공 분석 규칙 사용

이 프로시저에서는 기본 제공 분석 규칙 템플릿을 사용하는 방법을 설명합니다.

**기본 제공 분석 규칙을 사용** 하려면 다음을 수행합니다.

1. Azure Sentinel > **분석** > **규칙 템플릿** 페이지에서 템플릿 이름을 선택하고, 세부 정보 창에서 **규칙 만들기** 단추를 선택하여 해당 템플릿을 기반으로 새 활성 규칙을 만듭니다. 

    각 템플릿에는 필요한 데이터 원본 목록이 있습니다. 템플릿을 열면 데이터 원본의 가용성이 자동으로 검사됩니다. 가용성 문제가 있는 경우 **규칙 만들기** 단추가 사용하지 않도록 설정되거나 해당 효과에 대한 경고가 표시될 수 있습니다.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="검색 규칙 미리 보기 패널":::

1. **규칙 만들기** 를 선택하면 선택한 템플릿에 따라 규칙 만들기 마법사가 열립니다. 모든 세부 정보가 자동으로 채워지며, **예약** 또는 **Microsoft 보안** 템플릿을 사용하여 논리 및 기타 규칙 설정을 사용자 지정하여 특정 요구에 더 적합하게 만들 수 있습니다. 이 프로세스를 반복하여 기본 제공 템플릿을 기준으로 하는 추가 규칙을 만들 수 있습니다. 규칙 만들기 마법사의 단계를 끝까지 완료한 후에는 템플릿을 기준으로 하는 규칙 만들기가 완료됩니다. **활성 규칙** 탭에 새 규칙이 표시됩니다.

    규칙 만들기 마법사에서 규칙을 사용자 지정하는 방법에 대한 자세한 내용은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.

> [!TIP]
> - 환경을 완전한 보안하려면 **연결된 데이터 원본과 연결된 모든 규칙 사용** 을 설정해야 합니다. 분석 규칙을 사용하도록 설정하는 가장 효율적인 방법은 모든 관련 규칙을 나열하는 데이터 커넥터 페이지에서 직접 사용하는 것입니다. 자세한 내용은 [데이터 원본 연결](connect-data-sources.md)을 참조하세요.
> 
> - 추가 작업이 필요하지만 **[API](/rest/api/securityinsights/) 및 [PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)** 을 통해 Azure Sentinel에 규칙을 푸시할 수도 있습니다. 
> 
>     API 또는 PowerShell을 사용하는 경우 규칙을 사용하도록 설정하기 전에 먼저 규칙을 JSON으로 내보내야 합니다. API 또는 PowerShell은 각 인스턴스에서 동일한 설정을 사용하여 Azure Sentinel의 여러 인스턴스에서 규칙을 사용으로 설정할 때 유용할 수 있습니다.
> 
## <a name="export-rules-to-an-arm-template"></a>ARM 템플릿으로 규칙 내보내기

규칙을 관리하고 코드로 배포하려는 경우 [ARM(Azure Resource Manager) 템플릿으로 규칙을 쉽게 내보낼](import-export-analytics-rules.md) 수 있습니다. 사용자 인터페이스에서 규칙을 보고 편집하기 위해 템플릿 파일에서 규칙을 가져올 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- 사용자 지정 규칙을 만들려면 기존 규칙을 템플릿 또는 참조로 사용합니다. 기존 규칙을 기준으로 사용하면 필요한 내용을 변경하기 전에 대부분의 논리를 작성하는 데 도움이 됩니다. 자세한 내용은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.

- 위협에 대한 응답을 자동화하는 방법에 대해 알아보려면 [Azure Sentinel에서 자동화된 위협 응답 설정](tutorial-respond-threats-playbook.md)을 참조하세요.
