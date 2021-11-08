---
title: 비 Azure 머신을 Microsoft Defender for Cloud에 연결
description: 비 Azure 머신을 Microsoft Defender for Cloud에 연결하는 방법 알아보기
author: memildin
ms.author: memildin
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 390a628640389b2d2b89afbf36b4aab41f1eb115
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422314"
---
# <a name="connect-your-non-azure-machines-to-microsoft-defender-for-cloud"></a>비 Azure 머신을 Microsoft Defender for Cloud에 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud는 비 Azure 컴퓨터의 보안 태세를 모니터링할 수 있지만, 그러려면 먼저 Azure에 연결해야 합니다.

다음과 같은 방법으로 비 Azure 컴퓨터를 연결할 수 있습니다.

- Azure Arc 지원 서버 사용(**권장**)
- Azure Portal의 Defender for Cloud 페이지에서(**시작** 및 **인벤토리**)

이 페이지에서 이러한 각 방법에 대해 설명합니다.

> [!TIP]
> 다른 클라우드 공급자의 머신을 연결하는 경우 [AWS 계정 연결](quickstart-onboard-aws.md) 또는 [GCP 계정 연결](quickstart-onboard-gcp.md)을 참조하세요.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Azure Arc를 사용하여 비 Azure 머신 추가

비 Azure 머신을 Defender for Cloud에 추가하려면 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하는 것이 좋습니다.

Azure Arc 지원 서버가 있는 머신은 Azure 리소스가 되며, 여기에 Log Analytics 에이전트를 설치하면 Defender for Cloud에 다른 Azure 리소스와 같은 권장 사항과 함께 나타납니다.

또한 Azure Arc 지원 서버는 컴퓨터에서 게스트 구성 정책을 사용하도록 설정하고 다른 Azure 서비스를 사용하여 배포를 간소화하는 옵션 등과 같은 향상된 기능을 제공합니다. 이점에 대한 개요는 [지원되는 클라우드 작업](../azure-arc/servers/overview.md#supported-cloud-operations)을 참조하세요.

> [!NOTE]
> Log Analytics 에이전트를 배포하기 위한 Defender for Cloud의 자동 배포 도구는 Azure Arc를 실행하는 머신을 지원하지 않습니다. Azure Arc를 사용하여 머신을 연결한 경우 관련 Defender for Cloud 권장 사항을 사용하여 에이전트를 배포하고 Defender for Cloud에서 제공하는 전체 보호 기능을 사용합니다.
>
> - [Linux 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/720a3e77-0b9a-4fa9-98b6-ddf0fd7e32c1)
> - [Windows 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/27ac71b1-75c5-41c2-adc2-858f5db45b08)

[Azure Arc 지원 서버](../azure-arc/servers/overview.md)에 대해 자세히 알아보세요.

**Azure Arc를 배포하려면 다음을 수행합니다.**

- 단일 머신의 경우 [빠른 시작: Azure Arc 사용 서버를 사용하여 하이브리드 머신 연결](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)의 지침을 따르세요.
- 여러 컴퓨터를 대규모로 Azure Arc 지원 서버에 연결하려면 [Azure에 하이브리드 컴퓨터를 대규모로 연결](../azure-arc/servers/onboard-service-principal.md)을 참조하세요.

> [!TIP]
> AWS(Amazon Web Services)에서 실행되는 머신을 온보딩하는 경우 Defender for Cloud의 AWS용 커넥터에서 Azure Arc 배포를 투명하게 자동으로 처리합니다. [Microsoft Defender for Cloud에 AWS 계정 연결](quickstart-onboard-aws.md)에서 자세히 알아봅니다.

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Azure Portal에서 비 Azure 컴퓨터 추가

1. Defender for Cloud의 메뉴에서 **시작** 페이지를 엽니다.
1. **시작하기** 탭을 선택합니다.
1. **비Azure 서버 추가** 에서 **구성** 을 선택합니다.

    :::image type="content" source="./media/quickstart-onboard-machines/onboarding-get-started-tab.png" alt-text="[시작] 페이지의 [시작] 탭." lightbox="./media/quickstart-onboard-machines/onboarding-get-started-tab.png":::

    > [!TIP]
    > **인벤토리** 페이지의 **비 Azure 서버 추가** 단추에서 [컴퓨터 추가]를 열 수도 있습니다.
    > 
    > :::image type="content" source="./media/quickstart-onboard-machines/onboard-inventory.png" alt-text="자산 인벤토리 페이지에서 비 Azure 머신 추가.":::

    Log Analytics 작업 영역 목록이 표시됩니다. 이 목록에는 자동 프로비저닝을 사용하는 경우 Defender for Cloud에서 자동으로 생성되는 기본 작업 영역이 포함됩니다(해당하는 경우). 이 작업 영역이나 사용할 다른 작업 영역을 선택합니다.

    컴퓨터를 기존 작업 영역에 추가하거나 새 작업 영역을 만들 수 있습니다.

1. 선택 사항으로 새 작업 영역을 만들려면 **새 작업 영역 만들기** 를 선택합니다.

1. 작업 영역 목록에서 관련 작업 영역에 대한 **서버 추가** 를 선택합니다.
    **에이전트 관리** 페이지가 표시됩니다.

    이 페이지에서, 온보딩하려는 컴퓨터의 종류에 따라 아래의 관련 절차를 선택합니다.

    - [Azure Stack Hub VM 온보딩](#onboard-your-azure-stack-hub-vms)
    - [Linux 컴퓨터 온보딩](#onboard-your-linux-machines)
    - [Windows 컴퓨터 온보딩](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Azure Stack Hub VM 온보딩

Azure Stack Hub VM을 추가하려면 **에이전트 관리** 페이지의 정보가 필요하며, Azure Stack Hub 인스턴스에서 실행되는 가상 머신에 **Azure Monitor, Update 및 Configuration Management** 가상 머신 확장을 구성해야 합니다.

1. **에이전트 관리** 페이지에서 **작업 영역 ID** 및 **기본 키** 를 복사하여 메모장에 붙여넣습니다.
1. **Azure Stack Hub** 포털에 로그인하여 **가상 머신** 페이지를 엽니다.
1. Defender for Cloud로 보호하려는 가상 머신을 선택합니다.
    >[!TIP]
    > Azure Stack Hub에서 가상 머신을 만드는 방법에 대한 자세한 내용은 [Windows 가상 머신용 빠른 시작](/azure-stack/user/azure-stack-quick-windows-portal) 또는 [Linux 가상 머신용 빠른 시작](/azure-stack/user/azure-stack-quick-linux-portal)을 참조하세요.
1. **확장** 을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.
1. **추가** 탭을 선택합니다. **새 리소스** 메뉴에 사용 가능한 가상 머신 확장 목록이 표시됩니다.
1. **Azure Monitor, Update 및 Configuration Management** 확장을 선택하고 **만들기** 를 선택합니다. **확장 설치** 구성 페이지가 열립니다.
    >[!NOTE]
    > **Azure Monitor, Update 및 Configuration Management** 확장이 Marketplace에 나열되지 않으면 사용할 수 있도록 Azure Stack Hub 운영자에게 연락하세요.
1. **확장 설치** 구성 페이지에서, 이전 단계에서 메모장에 복사해 둔 **작업 영역 ID** 와 **작업 영역 키(기본 키)** 를 붙여넣습니다.
1. 구성이 완료되면 **확인** 을 선택합니다. 확장의 상태가 **프로비전 성공** 으로 표시됩니다. Defender for Cloud에 가상 머신이 나타날 때까지 최대 1시간이 걸릴 수 있습니다.

### <a name="onboard-your-linux-machines"></a>Linux 컴퓨터 온보딩

Linux 컴퓨터를 추가하려면 **에이전트 관리** 페이지의 WGET 명령이 필요합니다.

1. **에이전트 관리** 페이지에서 **WGET** 명령을 메모장에 복사합니다. Linux 컴퓨터에서 액세스할 수 있는 위치에 이 파일을 저장합니다.
1. Linux 컴퓨터에서 WGET 명령을 사용하여 이 파일을 엽니다. 모든 콘텐츠를 선택하고 복사하여 터미널 콘솔에 붙여넣습니다.
1. 설치가 완료되면 *pgrep* 명령을 실행하여 *omsagent* 가 설치되었는지 확인할 수 있습니다. 이 명령은 *omsagent* PID를 반환합니다.
    에이전트의 로그는 */var/opt/microsoft/omsagent/\<workspace id>/log/* 에서 찾을 수 있습니다. 새 Linux 컴퓨터가 Defender for Cloud에 나타날 때까지 최대 30분이 걸릴 수 있습니다.

### <a name="onboard-your-windows-machines"></a>Windows 컴퓨터 온보딩

Windows 컴퓨터를 추가하려면 **에이전트 관리** 페이지의 정보가 필요하며, 적절한 에이전트 파일(32/64비트)을 다운로드해야 합니다.
1. 사용하는 컴퓨터 프로세서 유형에 해당하는 **Windows 에이전트 다운로드** 링크를 선택하여 설치 파일을 다운로드합니다.
1. **에이전트 관리** 페이지에서 **작업 영역 ID** 및 **기본 키** 를 복사하여 메모장에 붙여넣습니다.
1. 다운로드한 설치 파일을 대상 컴퓨터에 복사하고 실행합니다.
1. 설치 마법사의 지시를 따릅니다(**다음**, **동의**, **다음**, **다음**).
    1. **Azure Log Analytics** 페이지에서, 앞에서 메모장에 복사해 둔 **작업 영역 ID** 와 **작업 영역 키(기본 키)** 를 붙여넣습니다.
    1. 컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 **Azure Cloud** 드롭다운 목록에서 **Azure 미국 정부** 를 선택합니다.
    1. 컴퓨터가 프록시 서버를 통해 Log Analytics 서비스와 통신해야 하는 경우 **고급** 을 선택하고 프록시 서버의 URL 및 포트 번호를 제공합니다.
    1. 구성 설정을 모두 입력했으면 **다음** 을 선택합니다.
    1. **설치 준비 완료** 페이지에서 적용할 설정을 검토하고 **설치** 를 선택합니다.
    1. **구성이 완료되었습니다** 페이지에서 **마침** 을 선택합니다.

완료되면 **제어판** 에 **Microsoft Monitoring Agent** 가 나타납니다. 구성을 검토하고 에이전트에 연결되었는지 확인할 수 있습니다.

에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)을 참조하세요.

::: zone-end

## <a name="verifying"></a>확인 중

축하합니다! 이제 Azure 및 비 Azure 컴퓨터를 한 장소에서 모두 볼 수 있습니다. [자산 인벤토리 페이지](asset-inventory.md)를 열고 관련 리소스 종류로 필터링합니다. 이러한 아이콘은 다음과 같은 종류를 구분합니다.

  ![비 Azure 머신에 대한 ASC 아이콘.](./media/quickstart-onboard-machines/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

  ![Azure 머신에 대한 ASC 아이콘.](./media/quickstart-onboard-machines/security-center-monitoring-icon2.png) Azure VM

  ![Azure Arc 서버에 대한 ASC 아이콘.](./media/quickstart-onboard-machines/arc-enabled-machine-icon.png) Azure Arc 지원 서버

## <a name="next-steps"></a>다음 단계

이 페이지에서는 비 Azure 머신을 Microsoft Defender for Cloud에 추가하는 방법을 보여줍니다. 상태를 모니터링하려면 다음 페이지에 설명된 대로 인벤토리 도구를 사용하세요.

- [자산 인벤토리로 리소스 탐색 및 관리](asset-inventory.md)
