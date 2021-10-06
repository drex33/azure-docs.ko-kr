---
title: Azure Arc 지원 서버를 사용하여 하이브리드 머신 연결
description: Azure Arc 지원 서버를 사용하여 하이브리드 머신을 연결하고 등록하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: fc645f930fe37f8a3eca10d1647d40eb9c7a2280
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807644"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>빠른 시작: Azure Arc 지원 서버를 사용하여 하이브리드 머신 연결

[Azure Arc 지원 서버](../overview.md)를 사용하면 온-프레미스, 에지, 다중 클라우드 환경에서 호스트되는 Windows 머신과 Linux 머신을 관리하고 제어할 수 있습니다. 이 빠른 시작에서는 Azure Arc 지원 서버를 통한 관리를 위해 Azure 외부에 호스트된 Windows 머신이나 Linux 머신에서 Connected Machine 에이전트를 배포하고 구성합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* Azure Arc 지원 서버 하이브리드 Connected Machine 에이전트를 배포하려면 머신에 에이전트를 설치하고 구성하기 위한 관리자 권한이 있어야 합니다. Linux에서는 루트 계정을 사용하고 Windows에서는 로컬 관리자 그룹의 멤버인 계정을 사용합니다.

* 시작하기 전에 에이전트 [필수 구성 요소](../agent-overview.md#prerequisites)를 검토하고 다음을 확인해야 합니다.

    * 대상 머신에서 지원되는 [운영 체제](../agent-overview.md#supported-operating-systems)가 실행되고 있어야 합니다.

    * 계정에는 [필요한 Azure 역할](../agent-overview.md#required-permissions)에 대한 할당이 부여됩니다.

    * 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버를 통해 연결되는 경우 [나열된](../agent-overview.md#networking-configuration) URL이 차단되지 않는지 확인합니다.

    * Azure Arc 지원 서버는 [여기](../overview.md#supported-regions)에 지정된 지역만 지원합니다.

> [!WARNING]
> Linux 호스트 이름 또는 Windows 컴퓨터 이름은 이름에 예약된 단어나 상표 중 하나를 사용할 수 없습니다. 그렇지 않으면 Azure에 연결된 컴퓨터를 등록하려고 하면 실패합니다. 예약된 단어 목록은 [예약된 리소스 이름 오류 해결](../../../azure-resource-manager/templates/error-reserved-resource-name.md)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure 리소스 공급자 등록

Azure Arc 지원 서버는 이 서비스를 사용하기 위해 구독의 다음 Azure 리소스 공급자를 사용합니다.

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

다음 명령을 사용하여 등록합니다.

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>설치 스크립트 생성

다운로드, 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음을 수행합니다.

1. **모든 서비스** 를 클릭한 다음, **서버 - Azure Arc** 를 검색하고 선택하여 Azure Portal에서 Azure Arc 서비스를 시작합니다.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="모든 서비스에서 Azure Arc 지원 서버 검색" border="false":::

1. **서버 - Azure Arc** 페이지에서 왼쪽 위에 있는 **추가** 를 선택합니다.

1. **메서드 선택** 페이지에서 **대화형 스크립트를 사용하여 서버 추가** 타일을 선택한 다음, **스크립트 생성** 을 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

1. **필수 구성 요소** 페이지에서 정보를 검토한 후, **다음: 리소스 세부 정보** 를 선택합니다.

1. **리소스 세부 정보** 페이지에서 다음을 제공합니다.

    1. **리소스 그룹** 드롭다운 목록에서 머신을 관리할 리소스 그룹을 선택합니다.
    1. **지역** 드롭다운 목록에서 서버 메타데이터를 저장할 Azure 지역을 선택합니다.
    1. **운영 체제** 드롭다운 목록에서 스크립트가 실행되도록 구성할 운영 체제를 선택합니다.
    1. 머신이 인터넷에 연결하기 위해 프록시 서버를 통해 통신하는 경우 프록시 IP 주소 또는 머신에서 프록시 서버와 통신하는 데 사용할 이름과 포트 번호를 지정합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.
    1. 완료되면 **다음: 태그** 를 선택합니다.

1. **태그** 페이지에서 제안된 기본 **실제 위치 태그** 를 검토하고 값을 입력하거나 표준을 지원하는 **사용자 지정 태그** 를 하나 이상 지정합니다.

1. 완료되면 **다음: 스크립트 다운로드 및 실행** 을 선택합니다.

1. **스크립트 다운로드 및 실행** 페이지에서 요약 정보를 검토한 다음, **다운로드** 를 선택합니다. 그래도 변경해야 하는 경우 **이전** 을 선택합니다.

## <a name="install-the-agent-using-the-script"></a>스크립트를 사용하여 에이전트 설치

### <a name="windows-agent"></a>Windows 에이전트

1. 서버에 로그인합니다.

1. 관리자 권한 64비트 PowerShell 명령 프롬프트를 엽니다.

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.ps1` 스크립트를 실행하여 서버에서 실행합니다.

### <a name="linux-agent"></a>Linux 에이전트

1. Azure와 직접 통신할 수 있는 대상 머신에 Linux 에이전트를 설치하려면 다음 명령을 실행합니다.

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * 대상 머신이 프록시 서버를 통해 통신하는 경우 다음 명령을 실행합니다.

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 지원 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="머신 연결 성공" border="false":::

## <a name="next-steps"></a>다음 단계

Linux 또는 Windows 하이브리드 머신을 사용하도록 설정하고 서비스에 성공적으로 연결되었으므로 Azure의 규정 준수를 이해하도록 Azure Policy를 사용할 수 있습니다.

Log Analytics 에이전트가 설치되어 있지 않은 Azure Arc 지원 서버 사용 머신을 확인하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [비준수 리소스를 식별하는 정책 할당 만들기](tutorial-assign-policy-portal.md)
