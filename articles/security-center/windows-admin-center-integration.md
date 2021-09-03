---
title: Azure Security Center를 사용하여 Windows Admin Center 서버를 보호하는 방법
description: 이 문서에서는 Windows Admin Center와 Azure Security Center를 통합하는 방법을 설명합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: f0a6c62145f5e8c94462a33fe446c595a7e2109e
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236808"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Security Center를 사용하여 Windows Admin Center 리소스 보호

Windows Admin Center는 Windows Server용 관리 도구입니다. 시스템 관리자가 가장 일반적으로 사용되는 대다수 관리 도구에 액세스할 수 있는 단일 위치입니다. Windows Admin Center 내에서 온-프레미스 서버를 Azure Security Center에 직접 온보딩할 수 있습니다. 그런 다음, Windows Admin Center 환경의 보안 권장 사항과 경고의 요약을 볼 수 있습니다.

> [!NOTE]
> Azure 구독과 연결된 Log Analytics 작업 영역에서는 둘 다 Windows Admin Center 통합을 사용하기 위해 Azure Defender를 사용하도록 설정해야 합니다.
> Azure Defender는 구독 및 작업 영역에서 이전에 사용하지 않은 경우 처음 30일 동안 무료입니다. 선택한 통화와 지역에 따른 가격 정보는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.
>

Windows Admin Center에서 Azure Security Center로 서버를 성공적으로 온보딩한 경우 다음을 수행할 수 있습니다.

* Windows Admin Center의 Security Center 확장 내에서 보안 경고와 권장 사항을 확인합니다.
* 보안 상태를 확인하고 Azure Portal 또는 API를 통해 Security Center에서 Windows Admin Center 관리형 서버의 추가 세부 정보를 검색합니다.

이 두 가지 도구를 결합하면 리소스가 무엇이든 Security Center에서 모든 보안 정보를 한눈에 볼 수 있어 Windows Admin Center 관리형 온-프레미스 서버, VM, 추가 PaaS 워크로드를 보호할 수 있습니다.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Windows Admin Center 관리형 서버를 Security Center에 온보딩

1. Windows Admin Center에서 서버 중 하나를 선택하고 **도구** 창에서 Azure Security Center 확장을 선택합니다.

    ![Windows Admin Center의 Azure Security Center 확장](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 서버가 이미 Security Center에 온보딩된 경우 설정 창이 표시되지 않습니다.

1. **Azure에 로그인 및 설정** 을 클릭합니다.
    ![Windows Admin Center 확장을 Azure Security Center에 온보딩](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 지침에 따라 서버를 Security Center에 연결합니다. 필요한 세부 정보를 입력하고 확인한 후 Security Center에서는 필요한 구성을 변경하여 다음에 모두 해당하는지 확인합니다.
    * Azure Gateway가 등록되어 있습니다.
    * 서버에 보고할 작업 영역과 연결된 구독이 있습니다.
    * Security Center의 Log Analytics 솔루션이 작업 영역에서 사용됩니다. 이 솔루션은 해당 작업 영역에 보고하는 ‘모든’ 서버와 가상 머신에 대한 Azure Defender 기능을 제공합니다.
    * 서버에 대한 Azure Defender가 구독에서 사용됩니다.
    * Log Analytics 에이전트가 서버에 설치되고 선택한 작업 영역에 보고하도록 구성됩니다. 서버가 이미 다른 작업 영역에 보고하는 경우 새로 선택한 작업 영역에도 보고하도록 구성됩니다.

    > [!NOTE]
    > 온보딩 후 권장 사항이 표시되는 데 다소 시간이 걸릴 수 있습니다. 실제로 서버 작업에 따라 ‘모든’ 경고가 수신되지 않을 수 있습니다. 경고가 올바르게 작동하는지 테스트하는 테스트 경고를 생성하려면 [경고 유효성 검사 절차](security-center-alert-validation.md)의 지침을 따릅니다.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows Admin Center에서 보안 권장 사항 및 경고 보기

온보딩되면 Windows Admin Center의 Azure Security Center 영역에서 직접 경고와 권장 사항을 볼 수 있습니다. 권장 사항이나 경고를 클릭하여 Azure Portal에서 확인합니다. 여기에서 추가 정보를 확인하고 문제를 수정하는 방법을 알아봅니다.

[![Windows Admin Center에 표시된 Security Center 권장 사항 및 경고](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Security Center에서 Windows Admin Center 관리형 서버에 대한 보안 권장 사항 및 경고 보기
Azure Security Center에서:

* 모든 Windows Admin Center 서버의 보안 권장 사항을 보려면 [자산 인벤토리](asset-inventory.md)를 열고 조사하려는 머신 유형으로 필터링합니다. **VM 및 컴퓨터** 탭을 선택합니다.

* 모든 Windows Admin Center 서버의 보안 경고를 보려면 **보안 경고** 를 엽니다. **필터** 를 클릭하고 “비 Azure”**만** 선택되어 있는지 확인합니다.

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Windows Admin Center 관리형 서버에 대한 보안 경고 필터링" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::