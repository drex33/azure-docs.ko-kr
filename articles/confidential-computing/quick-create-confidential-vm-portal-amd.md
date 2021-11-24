---
title: Azure Portal에서 Azure AMD 기반 기밀 VM 만들기(미리 보기)
description: Azure Marketplace 이미지를 사용하여 Azure Portal에서 AMD 기반의 기밀 VM(기밀 가상 머신)을 신속하게 만드는 방법을 알아봅니다.
author: RunCai
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/15/2021
ms.author: RunCai
ms.openlocfilehash: 61807b25b117706aa3e2e4ea1d7ada871cb1d52e
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942646"
---
# <a name="quickstart-create-confidential-vm-on-amd-in-the-azure-portal-preview"></a>빠른 시작: Azure Portal에서 AMD의 기밀 VM 만들기(미리 보기)

> [!IMPORTANT]
> Azure 기밀 컴퓨팅의 기밀 VM(기밀 가상 머신)은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Portal 사용하여 Azure Marketplace 이미지를 기반으로 [기밀 VM](confidential-vm-overview.md)을 신속하게 만들 수 있습니다. AMD에는 AMD SEV-SNP 기술이 적용된 여러 [기밀 VM 옵션](virtual-machine-solutions-amd.md)이 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 평가판 계정은 이 자습서에 사용된 VM에 액세스할 수 없습니다. 한 가지 옵션은 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)을 사용하는 것입니다.
- Linux 기반 기밀 VM을 사용하는 경우 SSH에 사용할 BASH 셸을 사용하거나 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)와 같은 SSH 클라이언트를 설치합니다.

## <a name="create-confidential-vm"></a>기밀 VM 만들기

Azure Marketplace 이미지를 사용하여 Azure Portal에서 기밀 VM을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **가상 머신** 을 선택하거나 검색합니다.

1. **가상 머신** 페이지에서 **만들기** &gt; **가상 머신** 을 선택합니다.

1. **기본 사항** 탭에서 다음 설정을 구성합니다.

    1. **프로젝트 세부 정보** 에서 **구독** 에 대해 [사전 요구 사항](#prerequisites)을 충족하는 Azure 구독을 선택합니다.
    
    1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다. 이름을 입력하고 **확인** 을 선택합니다.

    1. **인스턴스 세부 정보** 에서 **가상 머신 이름** 에 대해 새 VM의 이름을 입력합니다.

    1. **지역** 에 대해 VM을 배포할 Azure 지역을 선택합니다. 

        > [!NOTE]
        > 일부 위치에서는 기밀 VM을 사용할 수 없습니다. 현재 지원되는 위치는 [Azure 지역별 사용 가능한 VM 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조하세요.

    1. **보안 유형** 에 대해 **기밀 가상 머신** 을 선택합니다.

    1. **이미지** 에 대해 VM에 사용할 OS 이미지를 선택합니다. 이 자습서에서는 **Ubuntu 20.04 LTS**, **Windows Server 2019 [Small disk] Data Center** 또는 **Windows Server 2022 [Small disk] Data Center** 를 선택합니다.

        > [!TIP]
        > 필요에 따라 **모든 이미지 보기** 를 선택하여 Azure Marketplace를 엽니다. **보안 유형** &gt; **기밀** 필터를 선택하여 사용 가능한 모든 기밀 VM 이미지를 표시합니다.

    1. [2세대](../virtual-machines/generation-2.md)이미지로 전환합니다. 기밀 VM은 2세대 이미지에서만 실행됩니다. 확인하려면 **이미지** 에서 **VM 생성 구성** 을 선택합니다. **VM 생성 구성** 창에서 **VM 생성** 에 대해 **2세대** 를 선택합니다. 그런 다음, **적용** 을 선택합니다.

    1. **크기** 에 대해 VM 크기를 선택합니다. 자세한 정보는 [지원되는 기밀 VM 제품군](virtual-machine-solutions-amd.md)을 참조하세요.


    1. Linux VM을 만드는 경우 **인증 유형** 에 **SSH 공개 키** 를 선택합니다. SSH 키가 없는 경우 [Linux VM에 대한 SSH 키를 만듭니다](../virtual-machines/linux/mac-create-ssh-keys.md).

    1. **관리자 계정** 에서 **사용자 이름** 에 VM의 관리자 이름을 입력합니다.

    1. 해당하는 경우 **SSH 공개 키** 에 RSA 공개 키를 입력합니다.

    1. **암호** 및 **암호 확인** 에 해당하는 경우 관리자 암호를 입력합니다.

    1. **인바운드 포트 규칙** 아래의 **공용 인바운드 포트** 에서 **선택한 포트 허용** 을 선택합니다.

    1. **인바운드 포트 선택** 의 드롭다운 메뉴에서 인바운드 포트를 선택합니다. Windows VM의 경우 **HTTP(80)** 및 **RDP(3389)** 를 선택합니다. Linux VM에는 **SSH(22)** 및 **HTTP(80)** 를 선택합니다.

        > [!NOTE]
        > 프로덕션 배포에는 RDP/SSH 포트를 허용하지 않는 것이 좋습니다.

1. **디스크** 탭에서 다음 설정을 구성합니다.

    1. 만드는 동안 VM의 OS 디스크를 암호화하려면 **디스크 옵션** 에서 **기밀 컴퓨팅 암호화** 를 사용하도록 설정합니다.

    1. **기밀 컴퓨팅 암호화 유형** 에 대해 사용할 암호화 유형을 선택합니다. 

1. 필요에 따라 **네트워킹**, **관리**, **게스트 구성** 및 **태그** 에서 설정을 변경합니다.

1. **검토 + 만들기** 를 선택하여 구성의 유효성을 검사합니다.

1.  유효성 검사가 완료될 때까지 기다립니다. 필요한 경우 유효성 검사 문제를 해결한 다음, **검토 + 만들기** 를 다시 선택합니다.

1. **검토 + 만들기** 창에서 **만들기** 를 선택합니다.

## <a name="connect-to-confidential-vm"></a>기밀 VM 연결

몇 가지 방법으로 [Windows 기밀 VM](#connect-to-windows-vms) 및 [Linux 기밀 VM](#connect-to-linux-vms)에 연결할 수 있습니다.

### <a name="connect-to-windows-vms"></a>Windows VM에 연결

Windows OS에 기밀 VM을 연결하려면 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](../virtual-machines/windows/connect-logon.md)을 참조하세요.

### <a name="connect-to-linux-vms"></a>Linux VM에 연결

Linux OS에 기밀 VM을 연결하려면 컴퓨터의 OS에 대한 지침을 참조하세요.

시작하기 전에 VM의 공용 IP 주소가 있는지 확인합니다. IP 주소를 찾으려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **가상 머신** 을 선택하거나 검색합니다.

1. **가상 머신** 페이지에서 기밀 VM을 선택합니다.

1. 기밀 VM의 개요 페이지에서 **공용 IP 주소** 를 복사합니다.

    Linux VM 연결에 대한 자세한 정보는 [빠른 시작: Azure Portal에서 Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md)를 참조하세요.

1. PuTTY와 같은 SSH 클라이언트를 엽니다.

1. 기밀 VM의 공용 IP 주소를 입력합니다.

1. VM에 연결합니다. PuTTY에서 **열기** 를 선택합니다.

1. VM 관리자 사용자 이름 및 암호를 입력합니다.

    > [!NOTE]
    > PuTTY를 사용하는 경우 서버의 호스트 키가 레지스트리에 캐시되지 않는다는 보안 경고를 받을 수 있습니다. 이 호스트를 신뢰하는 경우 **예** 를 선택하여 PuTTY의 캐시에 키를 추가하여 연결을 계속합니다. 키를 추가하지 않고 한 번만 연결하려면 **아니요** 를 선택합니다. 호스트를 신뢰하지 않는 경우 **취소** 를 선택하여 연결을 중단합니다.

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작이 완료되면 기밀 VM, 리소스 그룹 및 기타 관련 리소스를 정리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **리소스 그룹** 을 선택하거나 검색합니다.

1. **리소스 그룹** 페이지에서 이 자습서용으로 만든 리소스 그룹을 선택합니다.

1. 리소스 그룹의 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

1. 경고 창에서 리소스 그룹의 이름을 입력하여 삭제를 확인합니다.

1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ARM 템플릿을 사용하여 AMD에서 기밀 VM 만들기](quick-create-confidential-vm-arm-amd.md)
