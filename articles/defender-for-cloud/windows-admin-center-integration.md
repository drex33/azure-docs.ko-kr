---
title: Microsoft Defender for Cloud를 사용하여 Windows 관리 센터 서버를 보호하는 방법
description: 이 문서에서는 Microsoft Defender for Cloud를 Windows 관리 센터와 통합하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 89ce8f764dddb45be24bb2b5f58a1b1be4fabd5a
ms.sourcegitcommit: 4298f71f502c70d601a7c58b28fc7bca62be3595
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133133605"
---
# <a name="protect-windows-admin-center-resources-with-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud를 사용하여 Windows 관리 센터 리소스 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center는 Windows Server용 관리 도구입니다. 시스템 관리자가 가장 일반적으로 사용되는 대다수 관리 도구에 액세스할 수 있는 단일 위치입니다. Windows 관리 센터 내에서 온-프레미스 서버를 Microsoft Defender for Cloud에 직접 온보딩할 수 있습니다. 그런 다음, Windows Admin Center 환경의 보안 권장 사항과 경고의 요약을 볼 수 있습니다.

> [!NOTE]
> Azure 구독 및 연결된 Log Analytics 작업 영역은 모두 Windows Admin Center 통합을 사용하도록 설정하기 위해 클라우드용 Microsoft Defender의 향상된 보안 기능을 사용하도록 설정해야 합니다.
> 이전에 구독 및 작업 영역에서 사용하지 않은 경우 향상된 보안 기능은 처음 30일 동안 무료입니다. 현지 통화 또는 지역의 가격 책정 세부 정보는 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/security-center/)참조하세요.
>

Windows Admin Center에서 Microsoft Defender for Cloud로 서버를 성공적으로 온보딩한 경우 다음을 수행할 수 있습니다.

* Windows 관리 센터의 Defender for Cloud 확장 내에서 보안 경고 및 권장 사항 보기
* 보안 태세를 확인하고 Azure Portal(또는 API를 통해) Defender for Cloud에서 Windows 관리 센터 관리 서버에 대한 자세한 추가 정보를 검색합니다.

이러한 두 도구를 결합하여 Defender for Cloud는 리소스에 관계없이 모든 보안 정보를 볼 수 있는 단일 창이 됩니다. Windows 관리 센터 관리 온-프레미스 서버, VM 및 추가 PaaS 워크로드 보호.

## <a name="onboard-windows-admin-center-managed-servers-into-defender-for-cloud"></a>Windows 관리 센터 관리 서버를 Defender for Cloud에 온보딩

1. Windows 관리 센터에서 서버 중 하나를 선택하고 **도구** 창에서 클라우드용 Microsoft Defender 확장을 선택합니다.

    ![Windows 관리 센터의 클라우드용 Microsoft Defender 확장.](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 서버가 Defender for Cloud에 이미 온보딩된 경우 설정 창이 표시되지 않습니다.

1. **Azure에 로그인 및 설정** 을 클릭합니다.
    ![Windows 관리 센터 확장을 Defender for Cloud에 온보딩합니다.](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 지침에 따라 서버를 Defender for Cloud에 연결합니다. 필요한 세부 정보를 입력하고 확인한 후 Defender for Cloud는 다음을 모두 true로 유지하기 위해 필요한 구성을 변경합니다.
    * Azure Gateway가 등록되어 있습니다.
    * 서버에 보고할 작업 영역과 연결된 구독이 있습니다.
    * Defender for Cloud의 Log Analytics 솔루션은 작업 영역에서 사용하도록 설정됩니다. 이 솔루션은 이 작업 영역에 보고하는 *모든* 서버 및 가상 머신에 대한 클라우드용 Microsoft Defender 기능을 제공합니다.
    * 서버용 Microsoft Defender는 구독에서 사용하도록 설정됩니다.
    * Log Analytics 에이전트가 서버에 설치되고 선택한 작업 영역에 보고하도록 구성됩니다. 서버가 이미 다른 작업 영역에 보고하는 경우 새로 선택한 작업 영역에도 보고하도록 구성됩니다.

    > [!NOTE]
    > 온보딩 후 권장 사항이 표시되는 데 다소 시간이 걸릴 수 있습니다. 실제로 서버 작업에 따라 ‘모든’ 경고가 수신되지 않을 수 있습니다. 경고가 올바르게 작동하는지 테스트하는 테스트 경고를 생성하려면 [경고 유효성 검사 절차](alert-validation.md)의 지침을 따릅니다.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows Admin Center에서 보안 권장 사항 및 경고 보기

온보딩되면 Windows 관리 센터의 Microsoft Defender for Cloud 영역에서 직접 경고 및 권장 사항을 볼 수 있습니다. 권장 사항이나 경고를 클릭하여 Azure Portal에서 확인합니다. 여기에서 추가 정보를 확인하고 문제를 수정하는 방법을 알아봅니다.

[![Defender for Cloud 권장 사항 및 경고는 Windows 관리 센터에 표시되어 있습니다.](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-defender-for-cloud"></a>Defender for Cloud에서 Windows 관리 센터 관리 서버에 대한 보안 권장 사항 및 경고 보기
클라우드용 Microsoft Defender에서:

* 모든 Windows Admin Center 서버의 보안 권장 사항을 보려면 [자산 인벤토리](asset-inventory.md)를 열고 조사하려는 머신 유형으로 필터링합니다. **VM 및 컴퓨터** 탭을 선택합니다.

* 모든 Windows Admin Center 서버의 보안 경고를 보려면 **보안 경고** 를 엽니다. **필터** 를 클릭하고 “비 Azure”**만** 선택되어 있는지 확인합니다.

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Windows Admin Center 관리형 서버에 대한 보안 경고 필터링" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::
