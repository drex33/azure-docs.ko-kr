---
title: 클라우드 용 Microsoft Defender를 사용 하 여 Windows 관리 센터 서버를 보호 하는 방법
description: 이 문서에서는 Windows 관리 센터와 클라우드 용 Microsoft Defender를 통합 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: fed69e86658ffe15a353aa6a5863c4db32815926
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428409"
---
# <a name="protect-windows-admin-center-resources-with-microsoft-defender-for-cloud"></a>클라우드 용 Microsoft Defender를 사용 하 여 Windows 관리 센터 리소스 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center는 Windows Server용 관리 도구입니다. 시스템 관리자가 가장 일반적으로 사용되는 대다수 관리 도구에 액세스할 수 있는 단일 위치입니다. Windows 관리 센터 내에서 온-프레미스 서버를 클라우드 용 Microsoft Defender에 직접 등록할 수 있습니다. 그런 다음, Windows Admin Center 환경의 보안 권장 사항과 경고의 요약을 볼 수 있습니다.

> [!NOTE]
> Azure 구독 및 연결 된 Log Analytics 작업 영역에는 Windows 관리 센터 통합을 사용 하도록 설정 하기 위해 클라우드 용 Microsoft Defender의 향상 된 보안 기능을 사용 하도록 설정 해야 합니다.
이전에 구독 및 작업 영역에 사용 하지 않은 경우에는 처음 30 일 동안 향상 된 보안 기능을 사용할 수 있습니다. 선택한 통화와 해당 지역에 따라 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.
>

서버를 Windows 관리 센터에서 클라우드 용 Microsoft Defender로 성공적으로 등록 하는 경우 다음을 수행할 수 있습니다.

* Windows Admin Center의 Security Center 확장 내에서 보안 경고와 권장 사항을 확인합니다.
* 보안 상태를 확인 하 고 Windows 관리 센터 관리 서버에 대 한 자세한 정보를 Azure Portal (또는 API를 통해)에서 클라우드 용 Defender로 검색 합니다.

이러한 두 도구를 결합 하 여 Defender for Cloud는 모든 보안 정보를 볼 수 있는 단일 창이 유리 합니다. 즉, 리소스에 대 한 모든 보안 정보를 볼 수 있습니다. 온-프레미스 서버, vm 및 추가 PaaS 워크 로드를 Windows 보호 합니다.

## <a name="onboard-windows-admin-center-managed-servers-into-defender-for-cloud"></a>클라우드 용 Defender에 관리 센터 관리 서버 Windows 등록

1. Windows Admin Center에서 서버 중 하나를 선택하고 **도구** 창에서 Azure Security Center 확장을 선택합니다.

    ![Windows Admin Center의 Azure Security Center 확장](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 서버가 이미 Defender for Cloud로 등록 설정 창이 표시 되지 않습니다.

1. **Azure에 로그인 및 설정** 을 클릭합니다.
    ![클라우드 용 Defender에 Windows 관리 센터 확장을 등록 하 고 있습니다.](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 지침에 따라 서버를 클라우드 용 Defender에 연결 합니다. 필요한 세부 정보를 입력 하 고 확인 한 후에는 Defender for Cloud를 사용 하 여 필요한 구성을 변경 하 여 다음 모두에 해당 하는지 확인 합니다.
    * Azure Gateway가 등록되어 있습니다.
    * 서버에 보고할 작업 영역과 연결된 구독이 있습니다.
    * 클라우드에 대 한 Defender의 Log Analytics 솔루션은 작업 영역에서 사용 하도록 설정 됩니다. 이 솔루션은 해당 작업 영역에 보고하는 ‘모든’ 서버와 가상 머신에 대한 Azure Defender 기능을 제공합니다.
    * 서버에 대 한 Microsoft Defender가 구독에서 사용 됩니다.
    * Log Analytics 에이전트가 서버에 설치되고 선택한 작업 영역에 보고하도록 구성됩니다. 서버가 이미 다른 작업 영역에 보고하는 경우 새로 선택한 작업 영역에도 보고하도록 구성됩니다.

    > [!NOTE]
    > 온보딩 후 권장 사항이 표시되는 데 다소 시간이 걸릴 수 있습니다. 실제로 서버 작업에 따라 ‘모든’ 경고가 수신되지 않을 수 있습니다. 경고가 올바르게 작동하는지 테스트하는 테스트 경고를 생성하려면 [경고 유효성 검사 절차](alert-validation.md)의 지침을 따릅니다.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows Admin Center에서 보안 권장 사항 및 경고 보기

온보딩되면 Windows Admin Center의 Azure Security Center 영역에서 직접 경고와 권장 사항을 볼 수 있습니다. 권장 사항이나 경고를 클릭하여 Azure Portal에서 확인합니다. 여기에서 추가 정보를 확인하고 문제를 수정하는 방법을 알아봅니다.

[![Windows 관리 센터에 표시 되는 클라우드 권장 사항 및 경고에 대 한 Defender입니다.](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-defender-for-cloud"></a>클라우드에 대 한 Defender의 관리 센터 관리 서버 Windows에 대 한 보안 권장 사항 및 경고를 확인 합니다.
클라우드 용 Microsoft Defender에서:

* 모든 Windows Admin Center 서버의 보안 권장 사항을 보려면 [자산 인벤토리](asset-inventory.md)를 열고 조사하려는 머신 유형으로 필터링합니다. **VM 및 컴퓨터** 탭을 선택합니다.

* 모든 Windows Admin Center 서버의 보안 경고를 보려면 **보안 경고** 를 엽니다. **필터** 를 클릭하고 “비 Azure”**만** 선택되어 있는지 확인합니다.

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Windows Admin Center 관리형 서버에 대한 보안 경고 필터링" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::