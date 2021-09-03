---
title: Azure Lab Services를 사용하여 VirtualBox에서 윤리적 해킹 랩 설정 | Microsoft Docs
description: VirtualBox에서 윤리적 해킹 교육을 위해 Azure Lab Services를 사용하여 랩을 설정하는 방법을 알아봅니다.
ms.topic: article
ms.date: 06/11/2021
ms.openlocfilehash: 49db4562a7d541a4c3409a5956a67e3bd0aadef4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113040792"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>VirtualBox를 사용하여 윤리적 해킹 수업을 가르치는 랩 설정

이 문서에서는 윤리적 해킹의 포렌식 측면에 초점을 맞춘 클래스를 설정하는 방법을 보여줍니다. 윤리적 해킹 커뮤니티에서 사용하는 방법인 침투 테스트는 누군가가 악의적인 공격자가 악용할 수 있는 취약성을 입증하기 위해 시스템 또는 네트워크에 대한 액세스 권한을 얻으려고 할 때 발생합니다.

윤리적 해킹 클래스에서 학생은 취약점을 방어하기 위한 최신 기술을 배울 수 있습니다. 각 학생은 3개의 중첩된 가상 머신([시드](https://seedsecuritylabs.org/lab_env.html) 이미지가 있는 가상 머신 2개와 [Kali Linux](https://www.kali.org/) 이미지가 있는 가상 머신)이 포함된 호스트 가상 머신을 사용합니다. Seed 가상 머신은 악용 목적을 위해 사용되며 Kali 가상 머신은 법정 작업을 실행하는 데 필요한 도구에 대한 액세스 권한을 제공합니다.

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 클래스룸 랩을 만드는 방법에 대해 설명합니다. 두 번째 섹션에서는 중첩된 가상화와 필요한 도구 및 이미지를 사용하여 템플릿 컴퓨터를 만드는 방법에 대해 설명합니다. 이 경우 이미지를 호스팅하기 위해 [VirtualBox](https://www.virtualbox.org/)가 사용하도록 설정된 컴퓨터에 두 개의 시드 이미지와 한 개의 Kali Linux 이미지가 있습니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수도 있고 기존 계정을 사용할 수도 있습니다. 새 랩 계정 만들기에 대한 자습서는 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요.

[이 자습서](tutorial-setup-classroom-lab.md)에 따라 새 랩을 만들고 다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간(중첩된 가상화) | Windows Server 2019 Datacenter |
| 중간(중첩된 가상화) | Windows 10 |

## <a name="template-machine"></a>템플릿 머신

템플릿 머신을 만든 후에는 머신을 시작하고 연결하여 다음 세 개의 주요 작업을 완료합니다.

1. 중첩된 가상 머신에 [VirtualBox](https://www.virtualbox.org/)를 사용하도록 컴퓨터를 설정합니다.
2. [Kali](https://www.kali.org/) Linux 이미지를 설정합니다. Kali는 침투 테스트 및 보안 감사 도구가 포함된 Linux 배포판입니다.
3. 시드 이미지를 설정합니다. 이 예에서는 [시드](https://seedsecuritylabs.org/lab_env.html) 이미지가 사용됩니다. 이 이미지는 보안 교육을 위해 특별히 만들어졌습니다.

이 문서의 나머지 부분에서는 위의 작업을 완료하기 위한 수동 단계를 다룹니다.

### <a name="installing-virtualbox"></a>VirtualBox 설치

1. Windows 호스트 옵션을 선택하여 [VirtualBox 플랫폼 패키지](https://www.virtualbox.org/wiki/Downloads)를 다운로드합니다.
2. 설치 실행 파일을 실행하고 기본 옵션을 사용하여 설치를 완료합니다.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kali Linux 이미지를 사용하여 중첩된 가상 머신 설정

Kali는 침투 테스트 및 보안 감사 도구가 포함된 Linux 배포판입니다.

1. [Kali Linux VM VirtualBox 이미지](https://www.kali.org/get-kali/#kali-virtual-machines)에서 ova 이미지를 다운로드합니다.  32비트 버전을 권장하며 64비트 버전은 오류가 있습니다.  다운로드 페이지에 표시된 기본 사용자 이름과 암호를 기억하세요.
2. VirtualBox 관리자를 열고 [.ova 이미지를 가져옵니다](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html).  계속하려면 Kali 라이선스 계약을 검토하고 수락해야 합니다.

>[!Note]
>- Kali VM의 VirtualBox 기본 램은 2기가(2048)이며, 필요에 따라 램을 최소 4기가(4096) 이상으로 늘리는 것이 좋습니다.  이는 VM에서 학생이 변경할 수 있습니다.  VirtualBox 내에서 RAM 크기를 변경해도 랩의 VM 크기는 변경되지 않습니다.
>- 기본적으로 하드 디스크는 80기가 제한으로 설정되지만 동적으로 할당됩니다.  랩 서비스 컴퓨터는 128기가의 하드 드라이브 공간으로 제한되어 있으므로 이 디스크 크기를 초과하지 않도록 주의합니다.
>- Kali 이미지에는 [Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads)이 필요하거나 USB 탭에서 USB 컨트롤러를 1.0으로 설정하는 USB 2.0 사용이 있습니다.

### <a name="setup-seed-lab-images"></a>시드 랩 이미지 설정

1. [시드 랩 VM 이미지](https://seedsecuritylabs.org/labsetup.html)를 다운로드하고 추출합니다.
2. 지침에 따라 [VirtualBox에서 VM을 만듭니다.](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)
   여러 시드 VM이 각 컴퓨터에 대해 .iso의 복사본을 만들어야 하는 경우 다른 컴퓨터에 동일한 .iso를 사용하면 제대로 작동하지 않습니다.

>[!IMPORTANT] 
>템플릿을 게시하기 전에 중첩된 모든 가상 머신의 전원이 꺼져 있는지 확인합니다.  전원이 켜진 상태로 두면 가상 시스템 손상을 포함하여 예기치 않은 부작용이 발생했습니다.

## <a name="cost"></a>비용  

이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다.

학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은

학생 25명 \* (20 + 10)시간 \* 55 랩 단위 \* 0.01USD/시간 = 412.50USD

>[!IMPORTANT]
>예상 비용은 예제 용도로만 사용됩니다. 가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="conclusion"></a>결론

이 문서에서는 윤리적 해킹 클래스에 대한 랩을 만드는 단계를 안내합니다. 여기에는 침투 테스트를 위해 호스트 가상 컴퓨터에 두 개의 가상 컴퓨터 생성하기 위해 중첩된 가상화를 설정하는 단계가 포함됩니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)
