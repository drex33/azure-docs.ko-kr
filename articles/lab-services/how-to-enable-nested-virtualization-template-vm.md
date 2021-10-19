---
title: Azure Lab Services의 템플릿 VM에서 중첩된 가상화 사용 | Microsoft Docs
description: 이 문서에서는 Azure Lab Services의 템플릿 머신에서 중첩된 가상화를 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0e730dc71150a5e201e4dcda84f9c6ed61ec2f90
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180403"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services의 템플릿 가상 머신에서 중첩된 가상화 사용

현재 Azure Lab Services를 사용하면 랩에서 하나의 템플릿 가상 머신을 설정하고 각 사용자가 단일 복사본을 사용하도록 할 수 있습니다. 네트워킹, 보안 또는 IT 수업을 가르치는 교육자인 경우 각 학생에게 네트워크를 통해 여러 가상 머신이 서로 통신할 수 있는 환경을 제공해야 할 수 있습니다.

중첩된 가상화를 사용하면 랩의 템플릿 가상 머신 내에 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시하면 랩의 각 사용자에게 여러 개의 VM이 설정된 가상 머신이 제공됩니다.  이 문서에서는 Azure Lab Services의 템플릿 머신에서 중첩된 가상화를 설정하는 방법을 다룹니다.

## <a name="what-is-nested-virtualization"></a>중첩된 가상화란?

중첩된 가상화를 사용하면 가상 머신 내에 가상 머신을 만들 수 있습니다. 중첩된 가상화는 Hyper-V를 통해 수행되며 Windows VM에서만 사용할 수 있습니다.

중첩된 가상화에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure의 중첩된 가상화](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM에서 중첩된 가상화를 사용하는 방법](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

## <a name="considerations"></a>고려 사항

중첩된 가상화를 사용하여 랩을 설정하기 전에 다음 사항을 고려해야 합니다.

- 새 랩을 만들 때 가상 머신 크기로 **중간(중첩된 가상화)** 또는 **대형(중첩된 가상화)** 크기를 선택합니다. 이러한 가상 머신 크기가 중첩된 가상화를 지원합니다.
- 호스트 및 클라이언트 가상 머신 모두에 우수한 성능을 제공할 크기를 선택합니다.  가상화를 사용할 때 선택하는 크기는 머신 한 대만이 아니라 호스트는 물론 동시에 실행되는 모든 Hyper-V 머신에 적합해야 합니다.
- 클라이언트 가상 머신은 Azure 가상 네트워크의 Azure 리소스(예: DNS 서버)에 액세스할 수 없습니다.
- 호스트 가상 머신을 사용하려면 클라이언트 머신에서 인터넷에 연결할 수 있게 설정해야 합니다.
- 클라이언트 가상 머신은 독립 머신으로 사용이 허가됩니다. Microsoft 운영 체제 및 제품의 라이선스에 대한 자세한 내용은 [Microsoft 라이선스](https://www.microsoft.com/licensing/default)를 참조하세요. 템플릿 머신을 설정하기 전에 사용 중인 다른 소프트웨어에 대한 라이선스 규약을 확인합니다.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>템플릿 VM에서 중첩된 가상화 사용

이 문서에서는 랩 계정과 랩을 이미 만들었다고 가정합니다.  새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요. 랩을 만드는 방법에 대한 자세한 내용은 [클래스룸 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조하세요.

>[!IMPORTANT]
>새 랩을 만들 때 가상 머신 크기로 **대형(중첩된 가상화)** 또는 **중간(중첩된 가상화)** 을 선택합니다.  그렇지 않으면 중첩된 가상화가 작동하지 않습니다.  

템플릿 머신에 연결하려면 [클래스룸 템플릿 만들기 및 관리](how-to-create-manage-template.md)를 참조하세요.

중첩된 가상화를 사용하려면 다음과 같은 몇 가지 작업을 수행해야 합니다.  

- **Hyper-V 역할 사용 설정**. Lab Services 가상 머신에서 Hyper-V 가상 머신을 만들고 실행하려면 Hyper-V 역할을 사용 설정해야 합니다.
- **DHCP 사용 설정**.  Lab Services 가상 머신에서 DHCP 역할을 사용 설정하면 Hyper-V 가상 머신에 자동으로 IP 주소가 할당될 수 있습니다.
- **Hyper-V VM의 NAT 만들기**.  NAT 네트워크는 Hyper-V 가상 머신에서 인터넷에 액세스할 수 있도록 설정됩니다.  Hyper-V 가상 머신은 서로 통신할 수 있습니다.

>[!NOTE]
>Lab Services VM에서 만든 NAT 네트워크를 통해 Hyper-V VM은 인터넷과 동일한 Lab Services VM의 다른 Hyper-V VM에 액세스할 수 있습니다.  Hyper-V VM은 Azure 가상 네트워크의 Azure 리소스(예: DNS 서버)에 액세스할 수 없습니다.

스크립트나 Windows 도구를 사용하여 위에 나열된 작업을 수행할 수 있습니다.  자세한 내용은 아래 섹션을 참조하세요.

### <a name="using-script-to-enable-nested-virtualization"></a>스크립트로 중첩된 가상화 사용 설정

Windows Server 2016 또는 Windows Server 2019에서 중첩된 가상화를 위한 자동 설정을 사용하려면 [스크립트를 사용하여 Azure Lab Services의 템플릿 가상 머신에서 중첩된 가상화 사용 설정](how-to-enable-nested-virtualization-template-vm-using-script.md)을 참조하세요. [Lab Services Hyper-V 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)를 사용하여 Hyper-V 역할을 설치합니다.  이 스크립트는 Hyper-V 가상 머신이 인터넷에 액세스할 수 있도록 네트워킹도 설정합니다.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Windows 도구로 중첩된 가상화 사용 설정

Windows 역할과 관리 도구를 사용한 Windows Server 2016 또는 Windows Server 2019의 중첩된 가상화 설정은 [Azure Lab Services의 템플릿 가상 머신에서 수동으로 중첩된 가상화 사용 설정](how-to-enable-nested-virtualization-template-vm-ui.md)을 참조하세요.  지침에서 Hyper-V 가상 머신이 인터넷에 액세스할 수 있도록 네트워킹을 설정하는 방법도 다룹니다.