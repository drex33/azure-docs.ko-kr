---
title: 공유 IP 주소 이해
description: Azure DevTest Labs 공유 IP 주소를 사용하여 랩 VM에 액세스하는 데 필요한 공용 IP 주소를 최소화하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 06aac18fb7016a7eb5bee938a4d9988719f86a2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056953"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 IP 주소 이해

Azure DevTest Labs VM(가상 머신)은 랩 VM에 액세스하는 데 필요한 공용 IP 수를 최소화하기 위해 공용 IP 주소를 공유할 수 있습니다.  이 문서에서는 공유 IP의 작동 방식과 공유 IP 주소를 구성하는 방법을 설명합니다.

## <a name="shared-ip-settings"></a>공유 IP 설정

하나 이상의 서브넷이 있을 수 있는 가상 네트워크에 DevTest Labs 랩을 만듭니다. 기본 서브넷에는 **공유 공용 IP 사용이** **예** 로 설정되어 있습니다.  이 구성은 전체 서브넷에 하나의 공용 IP 주소를 만듭니다. 이 서브넷의 모든 VM은 기본적으로 공유 IP를 사용하는 것으로 설정됩니다.

가상 네트워크 및 서브넷 구성에 대한 자세한 내용은 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조하세요.

![랩 서브넷 페이지의 공유 I P 설정을 보여 주는 스크린샷.](media/devtest-lab-shared-ip/lab-subnet.png)

기존 랩의 경우 랩의 왼쪽 탐색에서 **구성 및 정책을** 선택한 다음 외부 리소스 아래에서 **가상 네트워크를** 선택하여 이 옵션을 선택하거나 설정할 수 **있습니다.** 목록에서 가상 네트워크를 선택하여 해당 서브넷에 대한 공유 IP 설정을 확인합니다.

설정을 변경하려면 목록에서 서브넷을 선택한 다음 공유 **공용 IP 사용을** **예** 또는 **아니요로** 변경합니다.

VM을 만들 때 **IP 주소** 옆에 있는 고급 설정 페이지에서 이 **설정에** 액세스할 수 있습니다.

![새 VM을 만들 때 고급 설정 공유 I P 설정을 보여 주는 스크린샷](media/devtest-lab-shared-ip/new-vm.png)

- **공유:** **공유로** 만든 모든 VM은 동일한 리소스 그룹으로 이동합니다. 리소스 그룹에는 리소스 그룹의 모든 VM에서 사용하는 하나의 할당된 IP 주소가 있습니다.
- **공용:** 모든 공용 VM에는 자체 IP 주소 및 리소스 그룹이 있습니다.
- **프라이빗:** 모든 개인 VM은 개인 IP 주소를 사용합니다. RDP(원격 데스크톱 프로토콜)를 사용하여 인터넷에서 이러한 VM에 연결할 수 없습니다.

서브넷에 공유 IP가 있는 VM을 추가하면 DevTest Labs에서 자동으로 VM을 부하 분산에 추가하고 공용 IP 주소에 TCP 포트 번호를 VM에 할당합니다. 포트 번호는 VM의 SSH(보안 셸) 포트로 전달됩니다.

## <a name="use-a-shared-ip"></a>공유 IP 사용

- **Windows 사용자:** VM **개요** 페이지에서 **커넥트** 단추를 선택하여 미리 구성된 RDP 파일을 다운로드하고 VM에 액세스합니다.

- **Linux 사용자:** SSH(보안 셸)는 IP 주소 또는 정규화된 도메인 이름, 콜론, 포트 번호를 사용하여 VM에 연결합니다. 예를 들어 다음 스크린샷은 의 SSH 연결 주소를 `contosolab21000000000000.westus3.cloudapp.azure.com:65013` 보여줍니다.

  ![VM 개요 페이지의 R D P 및 SS H 연결 옵션을 보여 주는 스크린샷.](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>다음 단계

- [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)
- [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)
