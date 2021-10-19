---
title: Azure Lab Services를 사용하여 윤리적 해킹 랩 설정 | Microsoft Docs
description: 윤리적 해킹 교육을 위해 Azure Lab Services를 사용하여 랩을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 649646e560906c499cacda13c7745be3f2f3f758
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176912"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>윤리적 해킹 클래스를 교육하기 위한 랩 설정

이 문서에서는 윤리적 해킹의 포렌식 측면에 초점을 맞춘 클래스를 설정하는 방법을 보여줍니다. 윤리적 해킹 커뮤니티에서 사용하는 방법인 침투 테스트는 누군가가 악의적인 공격자가 악용할 수 있는 취약성을 입증하기 위해 시스템 또는 네트워크에 대한 액세스 권한을 얻으려고 할 때 발생합니다.

윤리적 해킹 클래스에서 학생은 취약점을 방어하기 위한 최신 기술을 배울 수 있습니다. 각 학생에게는 두 개의 중첩된 가상 머신이 있는 Windows Server 호스트 가상 머신이 있습니다. 즉 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 있는 가상 머신과 [Kali Linux](https://www.kali.org/) 이미지가 있는 가상 머신이 있습니다. Metasploitable 가상 머신은 악용 목적을 위해 사용되며 Kali 가상 머신은 법정 작업을 실행하는 데 필요한 도구에 대한 액세스 권한을 제공합니다.

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 클래스룸 랩을 만드는 방법에 대해 설명합니다. 두 번째 섹션에서는 중첩된 가상화와 필요한 도구 및 이미지를 사용하여 템플릿 컴퓨터를 만드는 방법에 대해 설명합니다. 이 경우 이미지를 호스팅하기 위해 Hyper-V를 사용하도록 설정한 컴퓨터의 Metasploitable 이미지 및 Kali Linux 이미지가 사용됩니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수도 있고 기존 계정을 사용할 수도 있습니다. 새 랩 계정 만들기에 대한 자습서는 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요.

[이 자습서](tutorial-setup-classroom-lab.md)에 따라 새 랩을 만들고 다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간(중첩된 가상화) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>템플릿 머신

템플릿 머신을 만든 후에는 머신을 시작하고 연결하여 다음 세 개의 주요 작업을 완료합니다.

1. 중첩된 가상화를 위해 컴퓨터를 설정합니다. 그러면 Hyper-V와 같은 적절한 창 기능을 모두 사용할 수 있으며, Hyper-V 이미지가 서로 및 인터넷과 통신할 수 있도록 네트워킹을 설정할 수 있습니다.
2. [Kali](https://www.kali.org/) Linux 이미지를 설정합니다. Kali는 침투 테스트 및 보안 감사 도구가 포함된 Linux 배포판입니다.
3. Metasploitable 이미지를 설정합니다. 이 예제에서는 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 사용됩니다. 이 이미지는 의도적으로 보안 취약성을 갖도록 만들어졌습니다.

이 문서의 나머지 부분에서는 위의 작업을 완료하기 위한 수동 단계를 다룹니다.  또는 [Hyper-V 스크립트 랩 서비스](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) 및 [윤리적 해킹 스크립트 랩 서비스](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)를 실행할 수 있습니다.

### <a name="prepare-template-machine-for-nested-virtualization"></a>중첩된 가상화를 위한 템플릿 컴퓨터 준비하기

중첩된 가상화를 위해 템플릿 가상 컴퓨터를 준비하려면 [중첩된 가상화를 사용하도록 설정](how-to-enable-nested-virtualization-template-vm.md)하는 지침을 따르세요.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kali Linux 이미지를 사용하여 중첩된 가상 머신 설정

Kali는 침투 테스트 및 보안 감사 도구가 포함된 Linux 배포판입니다.

1. [Offensive Security Kali Linux VM 이미지](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)에서 이미지를 다운로드합니다.  다운로드 페이지에 표시된 기본 사용자 이름과 암호를 기억하세요.
    1. VMware용 **Kali Linux VMware 64비트 (7z)** 이미지를 다운로드합니다.
    1. .7z 파일을 추출합니다.  7 zip 파일이 없다면 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)에서 다운로드하세요. 나중에 필요하므로 추출된 폴더의 위치를 기억하세요.
1. Hyper-V에서 vhdx 파일을 사용할 수 있도록 추출된 vmdk 파일을 vhdx 파일로 변환합니다. VMware 이미지를 Hyper-V 이미지로 변환하는데 사용할 수 있는 몇 가지 도구가 있습니다.  [StarWind V2V 변환기](https://www.starwindsoftware.com/starwind-v2v-converter)를 사용하겠습니다.  다운로드하려면 [StarWind V2V 변환기 다운로드 페이지](https://www.starwindsoftware.com/starwind-v2v-converter#download)를 참조하세요.
    1. **StarWind V2V 변환기** 를 시작합니다.
    1. **변환할 이미지 위치 선택** 페이지에서 **로컬 파일** 을 선택합니다.  **다음** 을 선택합니다.
    1. **원본 이미지** 페이지에서 **파일 이름** 설정에 대해 이전 단계에서 추출한 Kali Linux vmdk 파일로 이동하여 선택합니다.  파일은 Kali-Linux-{version}-vmware-amd64.vmdk 형식입니다.  **다음** 을 선택합니다.
    1. **대상 이미지 위치 선택** 에서 **로컬 파일** 을 선택합니다.  **다음** 을 선택합니다.
    1. **대상 이미지 형식 선택** 페이지에서 **VHD/VHDX** 를 선택합니다.  **다음** 을 선택합니다.
    1. **VHD/VHDX 이미지 형식 옵션 선택** 페이지에서 **VHDX Growable 이미지** 를 선택합니다.  **다음** 을 선택합니다.
    1. **대상 파일 이름 선택** 페이지에서 기본 파일 이름을 적용합니다.  **변환** 을 선택합니다.
    1. **변환 중** 페이지에서 이미지가 변환될 때까지 기다립니다.  이 작업은 몇 분 정도 걸릴 수 있습니다.  변환이 완료되면 **마침** 을 선택합니다.
1. 새 Hyper-V 가상 컴퓨터를 만듭니다.
    1. **Hyper-V 관리자** 를 엽니다.
    1. **작업** -> **새로 만들기** -> **가상 컴퓨터** 를 선택합니다.
    1. **새 가상 컴퓨터 마법사** 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
    1. **이름 및 위치 지정** 페이지에서 **이름** 에 **Kali-Linux** 를 입력하고 **다음** 을 선택합니다.
    1. **세대 지정** 페이지에서 기본값을 적용하고 **다음** 을 선택합니다.
    1. **메모리 할당** 페이지에서 **시작 메모리** 에 **2048MB** 를 입력하고 **다음** 을 선택합니다.
    1. **네트워킹 구성** 페이지에서 연결을 **연결되지 않음** 으로 유지합니다. 네트워크 어댑터는 나중에 설정합니다.
    1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 을 선택합니다. 이전 단계에서 만든 **Kali-Linux-{version}-vmware-amd64.vmdk** 파일의 위치를 찾고 **다음** 을 선택합니다.
    1. **새 가상 컴퓨터 마법사 완료** 페이지에서 **마침** 을 선택합니다.
    1. 가상 컴퓨터가 만들어지면 Hyper-V 관리자에서 가상 컴퓨터를 선택합니다. 컴퓨터를 아직 켜지 마세요.  
    1. **작업** -> **설정** 을 선택합니다.
    1. **Kali-Linux 설정** 대화 상자에서 **하드웨어 추가** 를 선택합니다.
    1. **레거시 네트워크 어댑터** 를 선택하고 **추가** 를 선택합니다.
    1. **레거시 네트워크 어댑터** 페이지에서 **가상 스위치** 설정에 **LabServicesSwitch** 를 선택하고 **확인** 을 선택합니다. LabServicesSwitch는 **중첩된 가상화를 위한 템플릿 준비** 섹션에서 Hyper-V용 템플릿 컴퓨터 준비할 때 생성되었습니다.
    1. 이제 Kali-Linux 이미지를 사용할 준비가 되었습니다. **Hyper-V 관리자** 에서 **작업** -> **시작** 다음에 **작업** -> **연결** 을 선택하여 가상 컴퓨터에 연결합니다.  기본 사용자 이름은 **kali** 이며 암호는 **kali** 입니다.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Metasploitable 이미지로 중첩된 VM 설정  

Rapid7 Metasploitable 이미지는 의도적으로 보안 취약성을 갖도록 구성되었습니다. 이 이미지를 사용하여 문제를 테스트하고 찾습니다. 다음 지침은 미리 만든 Metasploitable 이미지를 사용하는 방법을 보여줍니다. 그러나 최신 버전의 Metasploitable 이미지가 필요한 경우 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)을 참조하세요.

1. Metasploitable 이미지를 다운로드합니다.
    1. [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)으로 이동합니다. 양식을 작성하여 이미지를 다운로드하고 **제출** 단추를 선택합니다.
    2. **지금 Metasploitable 다운로드** 단추를 선택합니다.
    3. zip 파일이 다운로드되면 zip 파일의 압축을 풀고 Metasploitable.vmdk 파일의 위치를 기억합니다.
1. Hyper-V에서 vhdx 파일을 사용할 수 있도록 추출된 vmdk 파일을 vhdx 파일로 변환합니다. VMware 이미지를 Hyper-V 이미지로 변환하는데 사용할 수 있는 몇 가지 도구가 있습니다.  [StarWind V2V 변환기](https://www.starwindsoftware.com/starwind-v2v-converter)를 다시 사용하겠습니다.  다운로드하려면 [StarWind V2V 변환기 다운로드 페이지](https://www.starwindsoftware.com/starwind-v2v-converter#download)를 참조하세요.
    1. **StarWind V2V 변환기** 를 시작합니다.
    1. **변환할 이미지 위치 선택** 페이지에서 **로컬 파일** 을 선택합니다.  **다음** 을 선택합니다.
    1. **원본 이미지** 페이지에서 **파일 이름** 설정에 대해 이전 단계에서 추출한 Metasploitable.vmdk 파일로 이동하여 선택합니다.  **다음** 을 선택합니다.
    1. **대상 이미지 위치 선택** 에서 **로컬 파일** 을 선택합니다.  **다음** 을 선택합니다.
    1. **대상 이미지 형식 선택** 페이지에서 **VHD/VHDX** 를 선택합니다.  **다음** 을 선택합니다.
    1. **VHD/VHDX 이미지 형식 옵션 선택** 페이지에서 **VHDX Growable 이미지** 를 선택합니다.  **다음** 을 선택합니다.
    1. **대상 파일 이름 선택** 페이지에서 기본 파일 이름을 적용합니다.  **변환** 을 선택합니다.
    1. **변환 중** 페이지에서 이미지가 변환될 때까지 기다립니다.  이 작업은 몇 분 정도 걸릴 수 있습니다.  변환이 완료되면 **마침** 을 선택합니다.
1. 새 Hyper-V 가상 컴퓨터를 만듭니다.
    1. **Hyper-V 관리자** 를 엽니다.
    1. **작업** -> **새로 만들기** -> **가상 컴퓨터** 를 선택합니다.
    1. **새 가상 컴퓨터 마법사** 의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.
    1. **이름 및 위치 지정** 페이지에서 **이름** 에 **Metasploitable** 를 입력하고 **다음** 을 선택합니다.

        ![새 VM 이미지 마법사](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. **세대 지정** 페이지에서 기본값을 적용하고 **다음** 을 선택합니다.
    1. **메모리 할당** 페이지에서 **시작 메모리** 에 **512MB** 를 입력하고 **다음** 을 선택합니다.

        ![메모리 할당 페이지](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. **네트워킹 구성** 페이지에서 연결을 **연결되지 않음** 으로 유지합니다. 네트워크 어댑터는 나중에 설정합니다.
    1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 을 선택합니다. 이전 단계에서 만든 **metasploitable.vhdx** 파일의 위치를 찾고 **다음** 을 선택합니다.

        ![가상 네트워크 디스크 연결 페이지](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. **새 가상 컴퓨터 마법사 완료** 페이지에서 **마침** 을 선택합니다.
    1. 가상 컴퓨터가 만들어지면 Hyper-V 관리자에서 가상 컴퓨터를 선택합니다. 컴퓨터를 아직 켜지 마세요.  
    1. **작업** -> **설정** 을 선택합니다.
    1. **Metasploitable 설정** 대화 상자에서 **하드웨어 추가** 를 선택합니다.
    1. **레거시 네트워크 어댑터** 를 선택하고 **추가** 를 선택합니다.

        ![네트워크 어댑터 페이지](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. **레거시 네트워크 어댑터** 페이지에서 **가상 스위치** 설정에 **LabServicesSwitch** 를 선택하고 **확인** 을 선택합니다. LabServicesSwitch는 **중첩된 가상화를 위한 템플릿 준비** 섹션에서 Hyper-V용 템플릿 컴퓨터 준비할 때 생성되었습니다.

        ![레거시 네트워크 어댑터 페이지](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. 이제 Metasploitable 이미지를 사용할 준비가 되었습니다. **Hyper-V 관리자** 에서 **작업** -> **시작** 다음에 **작업** -> **연결** 을 선택하여 가상 컴퓨터에 연결합니다.  기본 사용자 이름은 **msfadmin** 이며 암호는 **msfadmin** 입니다.

이제 템플릿이 업데이트되었으며 템플릿에는 윤리적 해킹 침투 테스트 클래스에 필요한 이미지, 침투 테스트를 수행하는 도구 이미지 및 발견할 보안 취약성이 있는 다른 이미지가 있습니다. 이제 템플릿 이미지를 클래스에 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 랩에 템플릿을 게시합니다.

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
