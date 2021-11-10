---
title: Azure Chaos Studio에서 에이전트 기반 오류를 사용하는 실험 만들기
description: 에이전트 기반 오류를 사용하는 실험 만들기 및 비정상 상태 에이전트 구성
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 90abee8e5d776b1426e306fe915f5e19a9b1716c
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158421"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-to-add-cpu-pressure-to-a-linux-vm"></a>에이전트 기반 오류를 사용하여 Linux VM에 CPU 압력을 추가하는 비정상 오류 실험 만들기

비정상 상황에서는 비정상 상황에서 이러한 오류를 발생시켜 애플리케이션이 오류에 대해 복원력이 있는지 확인할 수 있습니다. 이 가이드에서는 Chaos 실험 및 Azure Chaos Studio를 사용하여 Linux 가상 머신에서 높은 CPU 이벤트를 발생시킬 것입니다. 이 실험을 실행하면 리소스가 부족해지는 애플리케이션을 방어하는 데 도움이 될 수 있습니다.

이러한 동일한 단계를 사용하여 에이전트 기반 오류에 대한 실험을 설정하고 실행할 수 있습니다. **에이전트 기반** 오류는 계측할 필요 없이 Azure 리소스에 대해 직접 실행되는 서비스 직접 오류와 달리 비정상 상태 에이전트를 설치하고 설치해야 합니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Linux 가상 머신. 가상 머신이 없는 경우 다음 [단계에 따라 을 만들](../virtual-machines/linux/quick-create-portal.md)수 있습니다.
- [가상 머신에 SSH를](../virtual-machines/ssh-keys-portal.md) 허용하는 네트워크 설정
- **대상 가상 머신 또는 가상 머신 확장 집합 에 할당된** 사용자 할당 관리 ID입니다. 사용자 할당 관리 ID가 없는 경우 다음 [단계에 따라 만들](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 수 있습니다.


## <a name="enable-chaos-studio-on-your-virtual-machine"></a>가상 머신에서 Chaos Studio 사용

Chaos Studio는 해당 가상 머신이 Chaos Studio에 먼저 온보딩되지 않은 한 가상 머신에 오류를 삽입할 수 없습니다. 리소스에 [대상 및 기능을](chaos-studio-targets-capabilities.md) 만든 다음, Chaos 에이전트를 설치하여 가상 머신을 Chaos Studio에 온보딩합니다. 가상 머신에는 두 가지 대상 유형이 있습니다. 하나는 서비스 직접 오류를 사용하도록 설정하고(에이전트가 필요하지 않은 경우) 에이전트 기반 오류를 사용하도록 설정하는 유형입니다(에이전트 설치 필요). chaos 에이전트는 게스트 운영 체제에 오류를 삽입할 수 있는 [가상 머신 확장으로 가상 머신에](../virtual-machines/extensions/overview.md) 설치된 애플리케이션입니다.

### <a name="install-stress-ng"></a>stress-ng 설치

Linux용 Chaos Studio 에이전트에는 가상 머신에서 다양한 스트레스 이벤트를 일으킬 수 있는 오픈 소스 애플리케이션인 stress-ng가 필요합니다. [Linux 가상 머신에 연결하고](../virtual-machines/ssh-keys-portal.md) 패키지 관리자에 대한 적절한 설치 명령을 실행하여 stress-ng를 설치할 수 있습니다. 예를 들면 다음과 같습니다.

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

또는

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-capabilities-and-agent"></a>비정상대화 대상, 기능 및 에이전트 사용

> [!IMPORTANT]
> 아래 단계를 완료하기 전에 [사용자 할당 관리 ID를 만들고](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 대상 가상 머신 또는 가상 머신 확장 집합에 할당해야 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 검색 표시줄에서 **Chaos Studio(미리 보기)를** 검색합니다.
3. **대상을** 클릭하고 가상 머신으로 이동합니다.
![Azure Portal 대상 보기](images/tutorial-agent-based-targets.png)
4. 가상 머신 옆의 확인란을 선택하고 **대상 사용을** 클릭한 다음 드롭다운 메뉴에서 에이전트 기반 **대상 사용을** 클릭합니다.
![Azure Portal 대상 사용](images/tutorial-agent-based-targets-enable.png)
5. Chaos 에이전트를 인증하고 필요에 따라 애플리케이션 Insights 실험 이벤트 및 에이전트 로그를 볼 수 있도록 설정하는 데 사용할 **관리 ID를** 선택합니다.
![관리 ID 선택](images/tutorial-agent-based-targets-enable-options.png)
6. **검토 + 사용을** 클릭한 다음 **사용을** 클릭합니다.
![에이전트 기반 대상 사용 검토](images/tutorial-agent-based-targets-enable-review.png)
7. 몇 분 후에 선택한 리소스가 사용하도록 설정되었음을 나타내는 알림이 표시됩니다. Azure Portal 가상 머신에 사용자 할당 ID를 추가하고, 에이전트 대상 및 기능을 사용하도록 설정하고, chaos 에이전트를 가상 머신 확장으로 설치합니다.
![대상을 사용하도록 설정됨을 보여주는 알림](images/tutorial-agent-based-targets-enable-confirm.png)

이제 Linux 가상 머신을 Chaos Studio에 성공적으로 온보딩했습니다. **대상** 보기에서 이 리소스에 사용하도록 설정된 기능을 관리할 수도 있습니다. 리소스 옆에 있는 **작업 관리** 링크를 클릭하면 해당 리소스에 대해 사용하도록 설정된 기능이 표시됩니다.

## <a name="create-an-experiment"></a>실험 만들기
이제 가상 머신을 온보딩하면 실험을 만들 수 있습니다. 비정상 상황에서는 대상 리소스에 대해 수행할 작업을 순차적으로 실행되는 단계와 병렬로 실행되는 분기로 구성하여 정의합니다.

1. Chaos Studio 탐색에서 **실험** 탭을 클릭합니다. 이 보기에서는 모든 비정실적 실험을 보고 관리할 수 있습니다. Azure Portal **실험** 
 ![ 추가 보기를 클릭합니다.](images/tutorial-agent-based-add.png)
2. 비정상 상황에서 실험을 배포하려는 **구독,** **리소스 그룹** 및 **위치를** 입력합니다. 실험에 **이름** 을 지정합니다. **다음: 실험 디자이너 >** 기본 실험 세부 
 ![ 정보 추가를 클릭합니다.](images/tutorial-agent-based-add-basics.png)
3. 이제 Chaos Studio 실험 디자이너에 있습니다. 실험 디자이너를 사용하면 단계, 분기 및 오류를 추가하여 실험을 빌드할 수 있습니다. **단계** 및 **분기에** 친숙한 이름을 지정한 다음 **오류 추가를** 클릭합니다.
![실험 디자이너](images/tutorial-agent-based-add-designer.png)
4. 드롭다운에서 **CPU 압력을** 선택한 다음, 가중을 적용할 시간(분)으로 **기간을** 입력하고 적용할 CPU 압력의 양으로 **pressureLevel을** 입력합니다. **virtualMachineScaleSetInstances를 비워 둡니다.** **다음: 대상 리소스 >** 
 ![ 오류 속성을 클릭합니다.](images/tutorial-agent-based-add-fault.png)
5. 가상 머신을 선택하고 **다음** 
 ![ 대상 추가를 클릭합니다.](images/tutorial-agent-based-add-targets.png)
6. 실험이 올바른지 확인한 다음 **검토 + 만들기, 만들기를** 차례로 **클릭합니다.** 
 ![ 실험 검토 및 만들기](images/tutorial-agent-based-add-review.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>가상 머신에 실험 권한 부여
Chaos 실험을 만들 때 Chaos Studio는 대상 리소스에 대해 오류를 실행하는 시스템 할당 관리 ID를 만듭니다. 실험을 성공적으로 실행하려면 이 ID에 대상 리소스에 [대한 적절한 권한이](chaos-studio-fault-providers.md) 부여되어야 합니다.

1. 가상 머신으로 이동하여 **액세스 제어(IAM)** 를 클릭합니다.
![가상 머신 개요 페이지](images/tutorial-agent-based-access-resource.png)
2. **추가를** 클릭한 다음 **역할 할당 추가를** 클릭합니다.
![액세스 제어 개요](images/tutorial-agent-based-access-iam.png)
3. **읽기** 프로그램을 검색하고 역할을 선택합니다. **다음** 
 ![ Virtual Machine 기여자 역할 할당을 클릭합니다.](images/tutorial-agent-based-access-role.png)
4. **멤버 선택을** 클릭하고 실험 이름을 검색합니다. 실험을 선택하고 **선택을** 클릭합니다. 동일한 테넌트에서 동일한 이름을 가진 여러 실험이 있는 경우 임의의 문자가 추가되어 실험 이름이 잘립니다.
![역할에 실험 추가](images/tutorial-agent-based-access-experiment.png)
5. **검토 + 할당을** 클릭한 다음 **검토 + 할당을** 클릭합니다.

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인하려면 별도의 브라우저 탭에서 가상 머신의 CPU 압력으로 [Azure Monitor 메트릭 차트를](../azure-monitor/essentials/tutorial-metrics-explorer.md) 여는 것이 좋습니다.

1. **실험** 보기에서 실험을 클릭하고 **시작을** 클릭한 다음 **확인을** 클릭합니다.
![실험 시작](images/tutorial-agent-based-start.png)
2. **상태가** **실행 중으로** 변경되면 **기록** 아래의 최신 실행에 대한 **세부 정보를** 클릭하여 실행 중인 실험에 대한 세부 정보를 확인합니다.

## <a name="next-steps"></a>다음 단계
이제 에이전트 기반 실험을 실행했으므로 다음을 수행할 준비가 되었습니다.
- [서비스 직접 오류를 사용하는 실험 만들기](chaos-studio-tutorial-service-direct.md)
- [실험 관리](chaos-studio-run-experiment.md)
