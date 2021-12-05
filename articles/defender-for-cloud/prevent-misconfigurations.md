---
title: 클라우드용 Microsoft Defender를 사용하여 잘못된 구성을 방지하는 방법
description: 권장 사항 세부 정보 페이지에서 클라우드용 Defender의 '적용' 및 '거부' 옵션을 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1e3a3b790ba9d07c03d3053ba34267d94c77933f
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133571226"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>적용/거부 권장 사항을 사용하여 구성 오류 방지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

보안 구성 오류는 보안 인시던트의 주요 원인입니다. Defender for Cloud는 특정 권장 사항과 관련하여 새 리소스의 잘못된 구성을 *방지하는* 데 도움이 될 수 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.
- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.

이는 선택한 보안 권장 사항에 대한 리소스 세부 정보 페이지의 맨 위에 있습니다([거부/적용 옵션 관련 권장 사항](#recommendations-with-denyenforce-options)참조).

## <a name="prevent-resource-creation"></a>리소스 생성 방지

1. 새 리소스가 충족해야 하는 권장 사항을 열고 페이지 상단에서 **거부** 단추를 선택합니다.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-deny-button.png" alt-text="거부 단추가 강조 표시된 권장 사항 페이지":::

    구성 창이 열리고 범위 옵션이 나열됩니다. 

1. 관련 구독 또는 관리 그룹을 선택하여 범위를 설정합니다.

    > [!TIP]
    > 행의 끝에 있는 세 개의 점을 사용하여 단일 구독을 변경하거나 확인란을 사용하여 여러 구독 또는 그룹을 선택한 후 **거부로 변경** 을 선택할 수 있습니다.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy 거부 범위 설정.":::


## <a name="enforce-a-secure-configuration"></a>보안 구성 적용

1. 새 리소스가 충족하지 않는 경우 템플릿 배포를 배포할 권장 사항을 열고 페이지 상단에 있는 **적용** 단추를 선택합니다.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-button.png" alt-text="적용 단추가 강조 표시된 권장 사항 페이지.":::

    구성 창이 열리고 모든 정책 구성 옵션이 나타납니다. 

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-config.png" alt-text="구성 옵션 적용.":::

1. 범위, 할당 이름, 기타 관련 옵션을 설정합니다.

1. **검토 + 만들기** 를 선택합니다.

## <a name="recommendations-with-denyenforce-options"></a>거부/적용 옵션 권장 사항

해당 권장 사항은 **deny** 옵션과 함께 사용할 수 있습니다.

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

해당 권장 사항은 **적용** 옵션과 함께 사용할 수 있습니다.

- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- Azure Arc 지원 Kubernetes 클러스터에는 Microsoft Defender for Cloud 확장이 설치되어 있어야 합니다.
- Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.
- microsoft Defender for App Service 사용하도록 설정해야 합니다.
- 컨테이너 레지스트리용 Microsoft Defender를 사용하도록 설정해야 합니다.
- DNS용 Microsoft Defender를 사용하도록 설정해야 합니다.
- microsoft Defender for Key Vault 사용하도록 설정해야 합니다.
- Kubernetes용 Microsoft Defender를 사용하도록 설정해야 합니다.
- microsoft Defender for Resource Manager 사용하도록 설정해야 합니다.
- 서버용 Microsoft Defender를 사용하도록 설정해야 합니다.
- Azure SQL Database 서버용 Microsoft Defender를 사용하도록 설정해야 합니다.
- 컴퓨터의 SQL 서버용 Microsoft Defender를 사용하도록 설정해야 합니다.
- 보호되지 않는 Azure SQL 서버에 대해 microsoft Defender for SQL 사용하도록 설정해야 합니다.
- microsoft Defender for Storage 사용하도록 설정해야 합니다.
- 클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.
- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.
- Logic Apps의 진단 로그를 사용하도록 설정해야 함
- Search Service의 진단 로그를 사용하도록 설정해야 함
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.
