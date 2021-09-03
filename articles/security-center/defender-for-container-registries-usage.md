---
title: 컨테이너 레지스트리용 Azure Defender를 사용하는 방법
description: 컨테이너 레지스트리용 Azure Defender를 사용하여 Linux 호스트형 레지스트리에서 Linux 이미지를 검색하는 방법에 대해 알아봅니다
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 195e47ce7165a02c5bb7126521432d448e2199a9
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238809"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>컨테이너 레지스트리용 Azure Defender를 사용하여 이미지에서 취약성 검사

이 페이지에서는 기본 제공 취약성 스캐너를 사용하여 Azure Resource Manager 기반 Azure Container Registry에 저장된 컨테이너 이미지를 스캔하는 방법을 설명합니다.

**컨테이너 레지스트리용 Azure Defender** 를 사용하도록 설정하면 레지스트리로 푸시하는 모든 이미지가 즉시 스캔됩니다. 또한 지난 30일 내에 끌어온 이미지도 검색됩니다. 

스캐너가 Security Center에 취약성을 보고하면 Security Center는 결과 및 관련 정보를 권장 사항으로 제공합니다. 또한 결과에는 수정 단계, 관련 CVE, CVSS 점수 등의 관련 정보가 포함됩니다. 하나 이상의 구독 또는 특정 레지스트리에 대해 식별된 취약성을 볼 수 있습니다.

> [!TIP]
> CI/CD GitHub 워크플로에서 이미지를 빌드할 때 컨테이너 이미지의 취약성을 검사할 수도 있습니다. 자세한 정보는 [CI/CD 워크플로에서 취약한 컨테이너 이미지 식별](defender-for-container-registries-cicd.md)을 참조하세요.


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Azure 컨테이너 레지스트리의 이미지 취약성 식별 

Azure Resource Manager 기반 Azure Container Registry에 저장된 이미지의 취약성 검색을 사용하려면 다음을 수행합니다.

1. 구독에 대한 **컨테이너 레지스트리용 Azure Defender** 를 사용하도록 설정합니다. 이제 Security Center는 레지스트리의 이미지를 스캔할 준비가 되었습니다.

    >[!NOTE]
    > 이 기능은 이미지별로 요금이 청구됩니다.

1. 이미지 검색은 모든 푸시 또는 가져오기에서 트리거되고, 지난 30일 내에 이미지를 끌어온 경우에 발생합니다. 

    검색이 완료될 때(일반적으로 약 2분 후, 최대 15분이 될 수 있음) Security Center 권장 사항으로 제공 됩니다.

1. [아래의 설명대로 결과를 보고 교정합니다](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>기타 컨테이너 레지스트리의 이미지 취약성 식별 

1. ACR 도구를 사용하여 Docker Hub 또는 Microsoft Container Registry에서 레지스트리에 이미지를 가져옵니다.  가져오기가 완료되면 Azure Defender에서 가져온 이미지를 검색합니다. 

    자세한 정보는 [컨테이너 이미지를 컨테이너 레지스트리로 가져오기](../container-registry/container-registry-import-images.md)를 참조하세요.

    검색이 완료될 때(일반적으로 약 2분 후, 최대 15분이 될 수 있음) Security Center 권장 사항으로 제공 됩니다.

1. [아래의 설명대로 결과를 보고 교정합니다](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>검색 결과 보기 및 교정

1. 결과를 보려면 **권장 사항** 페이지로 이동합니다. 문제가 발견되면 **Azure Container Registry 이미지를 재구성해야 한다는** 권장 사항을 확인할 수 있습니다

    ![문제를 수정하기 위한 권장 사항](media/monitor-container-security/acr-finding.png)

1. 권장 사항을 선택합니다. 

    추가 정보를 포함하는 권장 사항 세부 정보 페이지가 열립니다. 이 정보에는 취약한 이미지를 포함 하는 레지스트리 목록("영향을 받는 리소스") 및 수정 단계가 포함됩니다. 

1. 특정 레지스트리를 선택하여 취약한 리포지토리가 있는 리포지토리를 확인합니다.

    ![레지스트리 선택](media/monitor-container-security/acr-finding-select-registry.png)

    영향을 받는 리포지토리 목록과 함께 레지스트리 세부 정보 페이지가 열립니다.

1. 특정 리포지토리를 선택하여 취약한 이미지가 있는 리포지토리를 확인합니다.

    ![리포지토리를 선택합니다.](media/monitor-container-security/acr-finding-select-repository.png)

    리포지토리 세부 정보가 열립니다. 결과의 심각도 평가와 함께 취약한 이미지가 나열됩니다.

1. 취약성을 확인할 이미지를 선택합니다.

    ![이미지 선택](media/monitor-container-security/acr-finding-select-image.png)

    선택한 이미지에 대한 결과 목록이 열립니다.

    ![결과 목록](media/monitor-container-security/acr-findings.png)

1. 찾기에 대해 자세히 알아보려면 찾기를 선택합니다. 

    결과 세부 정보 창이 열립니다.

    [![결과 세부 정보 창](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    이 창에는 문제에 대한 자세한 설명과 위협을 완화하는 데 도움이 되는 외부 리소스에 대한 링크가 포함되어 있습니다.

1. 이 창의 수정 섹션에 있는 단계를 따릅니다.

1. 보안 문제를 교정하는 데 필요한 단계를 수행한 경우 레지스트리의 이미지를 바꿉니다.

    1. 업데이트된 이미지를 푸시합니다. 그러면 스캔아 트리거됩니다. 
    
    1. 권장 사항 페이지에서 "Azure Container Registry 이미지의 취약성을 교정해야 합니다" 권장 사항을 확인합니다. 
    
        권장 사항이 계속 표시되고 처리한 이미지가 취약한 이미지 목록에 계속 표시되는 경우 수정 단계를 다시 확인합니다.

    1. 업데이트된 이미지가 푸시되고, 검사되었으며, 권장 사항에 더 이상 표시되지 않는 경우 레지스트리에서 "이전" 취약한 이미지를 삭제합니다.


## <a name="disable-specific-findings-preview"></a>특정 결과 사용 안 함(미리 보기)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

결과가 사용 안 함 규칙에 정의한 조건과 일치하면 검색 결과 목록에 표시되지 않습니다. 일반적인 시나리오는 다음이 포함됩니다.

- 심각도가 보통 이하인 결과를 사용하지 않도록 설정
- 패치 가능하지 않은 검색 결과를 사용하지 않도록 설정
- CVSS 점수가 6.5 미만인 결과를 사용하지 않도록 설정
- 보안 확인 또는 범주에 특정 텍스트가 포함된 결과를 사용하지 않도록 설정(예: "RedHat", "CentOS Security Update for sudo")

> [!IMPORTANT]
> 규칙을 만들려면 Azure Policy에서 정책을 편집할 수 있는 사용 권한이 필요합니다.
>
> [Azure Policy에서 Azure RBAC 사용 권한](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)에 대해 자세히 알아보세요.

다음 조건 중 하나를 사용할 수 있습니다. 

- ID 찾기 
- 범주
- 보안 확인 
- CVSS v3 점수
- 심각도 
- 패치 가능한 상태 

규칙을 만들려면 다음을 수행합니다.

1. **Azure Container Registry 이미지의 취약성을 교정해야 합니다** 권장 사항 세부 정보 페이지에서 **규칙 사용 안 함** 을 선택합니다.
1. 관련 범위를 선택합니다.
1. 조건을 정의하십시오.
1. **규칙 적용** 을 선택합니다.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="레지스트리에서 VA 결과에 대한 사용 안 함 규칙 만들기":::

1. 규칙을 보거나 재정의하거나 삭제하려면 다음을 수행합니다. 
    1. **사용 안 함** 을 선택합니다.
    1. 범위 목록에서 활성 규칙이 있는 구독은 **규칙 적용** 됨으로 표시됩니다.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="기존 규칙 수정 또는 삭제":::
    1. 규칙을 보거나 삭제하려면 줄임표 메뉴("...")를 선택합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Defender](azure-defender.md)에 대해 자세히 알아보세요.