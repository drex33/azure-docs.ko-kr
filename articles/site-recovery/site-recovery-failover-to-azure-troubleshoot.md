---
title: Azure로 장애 조치(failover) 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure로 장애 조치(failover) 시 일반적인 오류 문제를 해결하는 방법을 설명합니다.
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 5dd2f7a7d5bc580c6846f214c84788bd2067cb0c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441631"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>VMware VM 또는 물리적 머신을 Azure로 장애 조치(failover) 시 발생하는 오류 해결

가상 컴퓨터를 Azure로 장애 조치하는 동안 다음 오류 중 하나가 나타날 수 있습니다. 문제를 해결하려면 각 오류 조건에 대해 설명된 단계를 따르세요.

## <a name="failover-failed-with-error-id-28031"></a>장애 조치 실패(오류 ID 28031)

Site Recovery가 Azure에서 장애 조치된 가상 머신을 만들 수 없습니다. 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 가상 머신을 만드는 데 사용할 수 있는 할당량이 부족합니다. [구독] -> [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](https://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다.

* 동일한 가용성 집합에 다른 크기 제품군의 가상 머신을 장애 조치하려고 합니다. 동일한 가용성 집합의 모든 가상 머신에 대해 동일한 크기 제품군을 선택했는지 확인합니다. 가상 컴퓨터의 **계산** 설정으로 이동 하 여 크기를 변경 하 고 장애 조치 (failover)를 다시 시도 하세요.

* 구독에는 가상 머신의 생성을 방지하는 정책이 있습니다. 가상 머신의 생성을 허용하도록 정책을 변경한 후 장애 조치를 다시 시도합니다.

## <a name="failover-failed-with-error-id-28092"></a>장애 조치 실패(오류 ID 28092)

Site Recovery가 장애 조치된 가상 머신에 대해 네트워크 인터페이스를 만들 수 없습니다. 구독에서 네트워크 인터페이스를 만드는 데 사용할 수 있는 충분한 할당량이 있는지 확인합니다. [구독] -> [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](https://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다. 할당량이 충분하고 일시적인 문제일 수 있다면 작업을 다시 시도합니다. 다시 시도 후에도 문제가 계속되면 이 문서의 끝에 의견을 남겨 주세요.  

## <a name="failover-failed-with-error-id-70038"></a>장애 조치 실패(오류 ID 70038)

Site Recovery가 Azure에서 장애 조치된 클래식 가상 머신을 만들 수 없습니다. 다음 때문에 발생할 수 있습니다.

* 만들 가상 머신에 필요한 가상 네트워크와 같은 리소스 중 하나가 존재하지 않습니다. 가상 컴퓨터의 네트워크 설정 아래에 제공 된 가상 네트워크를 만들거나 설정을 이미 존재 하는 가상 네트워크로 수정한 후 장애 조치 (failover)를 다시 시도 합니다.

## <a name="failover-failed-with-error-id-170010"></a>장애 조치 실패(오류 ID 170010)

Site Recovery가 Azure에서 장애 조치된 가상 머신을 만들 수 없습니다. 하이드레이션의 내부 활동이 온-프레미스 가상 머신에 대해 실패했으므로 발생할 수 있습니다.

Azure의 모든 머신을 표시하려면 Azure 환경에는 부팅 시작 상태의 일부 드라이버 및 자동 시작 상태의 DHCP와 같은 서비스가 필요합니다. 따라서 장애 조치(failover) 시 하이드레이션 작업은 **atapi, intelide, storflt, vmbus 및 storvsc 드라이버** 의 시작 형식을 부팅 시작으로 변환합니다. 또한 DHCP와 같은 몇 가지 서비스의 시작 유형을 자동 시작으로 변환합니다. 이 작업은 특정 환경 문제로 인해 실패할 수 있습니다. 

**Windows 게스트 OS** 의 드라이버 시작 유형을 수동으로 변경하려면 아래 단계를 따릅니다.

1. 비하이드레이션 스크립트를 [다운로드](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1)하여 다음과 같이 실행합니다. 이 스크립트는 VM에 하이드레이션이 필요한지 확인합니다.

    `.\Script-no-hydration.ps1`

    하이드레이션 필요한 경우 다음 결과가 표시됩니다.

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    VM이 비하이드레이션 요구 사항을 충족하는 경우 이 스크립트에는 "이 시스템은 비하이드레이션 요구 사항을 충족합니다"라는 결과가 표시됩니다. 이 경우 모든 드라이버 및 서비스는 Azure에서 필요한 상태에 있으며 VM에서 하이드레이션은 필요 없습니다.

2. VM이 비하이드레이션 요구 사항을 충족하지 않는 경우 다음과 같이 no-hydration-set 스크립트를 실행합니다.

    `.\Script-no-hydration.ps1 -set`
    
    그러면 드라이버의 시작 유형이 변환되고 다음과 같은 결과가 표시됩니다.

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>가상 머신의 연결 단추가 회색으로 표시되어 장애 조치된 가상 머신에 RDP/SSH를 연결할 수 없음

RDP 문제에 대한 자세한 문제 해결 지침은 [여기](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) 설명서를 참조하세요.

SSH 문제에 대한 자세한 문제 해결 지침은 [여기](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection) 설명서를 참조하세요.

Azure에서 장애 조치(failover)된 VM의 **연결** 단추가 회색으로 표시되고 Express 경로 또는 사이트 간 VPN 연결을 통해 Azure에 연결되지 않은 경우에는 다음을 수행합니다.

1. **가상 머신** > **네트워킹** 으로 차례로 이동하고, 필요한 네트워크 인터페이스의 이름을 클릭합니다.  ![네트워크 인터페이스 이름이 선택된 가상 머신의 네트워킹 페이지를 보여 주는 스크린샷](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. **IP 구성** 으로 이동한 다음, 필요한 IP 구성의 이름 필드를 클릭합니다. ![IP 구성 이름이 선택된 네트워크 인터페이스에 대한 IP 구성 페이지를 보여 주는 스크린샷](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. 공용 IP 주소를 사용하도록 설정하려면 **사용** 을 클릭합니다. ![IP 사용](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. **필수 설정 구성** > **새로 만들기** 를 차례로 클릭합니다. ![새로 만들기](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. 공용 주소의 이름을 입력하고, **SKU** 및 **할당** 에 대한 기본 옵션을 선택한 다음, **확인** 을 클릭합니다.
6. 이제 변경 내용을 저장하려면 **저장** 을 클릭합니다.
7. 패널을 닫고, 가상 머신의 **개요** 섹션으로 이동하여 RDP를 연결합니다.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>연결할 수 없음/RDP/SSH - VM 연결 단추 사용 가능

Azure에서 장애 조치(failover)된 VM의 **연결** 단추를 사용할 수 있는 경우(회색으로 표시되지 않음), 가상 머신에서 **부트 진단** 을 검사하고 [이 문서](/troubleshoot/azure/virtual-machines/boot-diagnostics)에 나열된 오류를 확인합니다.

1. 가상 머신이 시작되지 않은 경우 이전의 복구 지점으로 장애 조치를 시도합니다.
2. 가상 머신 내 애플리케이션이 가동되지 않는 경우 애플리케이션 일치 복구 지점으로 장애 조치를 시도합니다.
3. 가상 머신이 도메인에 조인되어 있는 경우 도메인 컨트롤러가 정확하게 작동하는지 확인합니다. 이렇게 하려면 아래 제공된 단계를 따릅니다.

    a. 동일한 네트워크에 새 가상 머신을 만듭니다.

    b.  장애 조치(failover)된 가상 머신이 가동되어야 하는 동일한 도메인에 새 가상 머신이 가입할 수 있는지 확인합니다.

    다. 도메인 컨트롤러가 정확하게 작동하지 **않는** 경우 로컬 관리자 계정을 사용하여 장애 조치(failover)된 가상 머신에 로그인합니다.
4. 사용자 지정 DNS 서버를 사용하는 경우 연결할 수 있는지 확인합니다. 이렇게 하려면 아래 제공된 단계를 따릅니다.

    a. 동일한 네트워크에 새 가상 머신을 만듭니다.

    b. 가상 머신이 사용자 지정 DNS 서버를 사용하여 이름 확인을 수행할 수 있는지 확인합니다.

>[!Note]
>부트 진단 이외의 설정을 사용하도록 설정하려면 장애 조치 전에 Azure VM 에이전트를 가상 머신에 설치해야 합니다.

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>UEFI 기반 컴퓨터를 Azure로 장애 조치한 후 직렬 콘솔을 열 수 없는 경우

RDP를 사용하여 컴퓨터에 연결할 수 있지만 직렬 콘솔을 열 수 없다면 다음 단계에 따라 진행합니다.

* 컴퓨터 OS가 Red Hat 또는 Oracle Linux 7.*/8.0인 경우, 루트 권한으로 장애 조치 Azure VM에서 다음 명령을 실행합니다. 명령이 완료된 후 VM을 다시 부팅합니다.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* 컴퓨터 OS가 CentOS 7.*인 경우, 루트 권한으로 장애 조치 Azure VM에서 다음 명령을 실행합니다. 명령이 완료된 후 VM을 다시 부팅합니다.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>예기치 않은 시스템 종료 메시지(이벤트 ID 6008)

장애 조치(failover) 후 Windows VM을 부팅할 때 복구된 VM에 예기치 않은 시스템 종료 메시지를 수신하는 경우 장애 조치(failover)에 사용된 복구 지점에서 VM 종료 상태가 캡처되지 않았음을 나타냅니다. 이 오류는 VM이 완전히 종료되지 않은 시점으로 복구할 때 발생합니다.

일반적으로 문제를 유발하지는 않으며, 계획되지 않은 장애 조치(failover)의 경우 무시할 수 있습니다. 장애 조치가 계획된 경우, 장애 조치 이전에 VM이 제대로 종료되었는지 확인하고 온-프레미스에서 보류 중인 복제 데이터가 Azure에 전송되는 데 충분한 시간을 제공합니다. 그런 다음, [장애 조치(failover) 화면](site-recovery-failover.md#run-a-failover)의 **최신** 옵션을 사용하여 Azure에서 보류 중인 데이터가 복구 지점으로 처리되어 VM 장애 조치(failover)에 사용되도록 합니다.

## <a name="unable-to-select-the-datastore"></a>데이터 저장소를 선택할 수 없는 경우

이 문제는 장애 조치가 발생한 가상 머신(VM)을 다시 보호하려고 할 때 Azure Portal에서 데이터 저장소를 볼 수 없는 경우에 표시됩니다. 그 이유는 마스터 대상이 Azure Site Recovery에 추가된 vCenters 아래의 가상 머신으로 인식되지 않기 때문입니다.

가상 머신(VM)을 다시 보호하는 방법에 관한 자세한 내용은 [Azure로 장애 조치(failover) 후에 머신을 온-프레미스 사이트로 다시 보호 및 장애 복구(failback)](vmware-azure-reprotect.md)를 참조하세요.

이 문제를 해결하려면

원본 컴퓨터를 관리하는 vCenter에서 마스터 대상을 수동으로 만듭니다. 다음 vCenter의 검색 및 새로 고침 패브릭 작업 후 데이터 저장소를 사용할 수 있습니다.

> [!Note]
> 
> 검색 및 새로 고침 패브릭 작업을 완료하는 데 최대 30분이 걸릴 수 있습니다. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>CS를 사용한 Linux 마스터 대상 등록이 실패하고 TLS 오류 35가 발생 

마스터 대상에서 인증된 프록시를 사용 중이기 때문에 구성 서버에서 Azure Site Recovery 마스터 대상 등록이 실패합니다. 
 
이 오류는 설치 로그에서 다음 문자열로 표시됩니다. 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

이 문제를 해결하려면
 
1. 구성 서버 VM에서 명령 프롬프트를 열고 다음 명령을 사용하여 프록시 설정을 확인합니다.

    cat /etc/environment echo $http_proxy echo $https_proxy 

2. 이전 명령의 출력에 http_proxy 또는 https_proxy 설정이 정의된 것으로 표시될 경우, 다음 방법 중 하나를 사용하여 구성 서버와 마스터 대상 간의 통신 차단을 해제하세요.
   
   - [PsExec 도구](/sysinternals/downloads/psexec)를 다운로드합니다.
   - 도구를 사용하여 시스템 사용자 컨텍스트에 액세스하고 프록시 주소가 구성되었는지 여부를 확인합니다. 
   - 프록시가 구성된 경우, PsExec 도구를 사용하여 시스템 사용자 컨텍스트에서 IE를 엽니다.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - 마스터 대상 서버가 구성 서버와 통신할 수 있는지 확인하려면
  
     - 프록시를 통해 마스터 대상 서버 IP 주소를 사용하지 않도록 Internet Explorer에서 프록시 설정을 수정합니다.   
     또는
     - 마스터 대상 서버에서 프록시를 사용하지 않도록 설정합니다. 


## <a name="next-steps"></a>다음 단계
- [Windows VM에 대한 RDP 연결](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) 문제 해결
- [Linux VM에 대한 SSH 연결](/troubleshoot/azure/virtual-machines/detailed-troubleshoot-ssh-connection) 문제 해결

도움이 더 필요하다면 [Site Recovery에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 쿼리를 게시하거나 이 문서의 끝에 의견을 남기세요. 도움을 드릴 수 있도록 커뮤니티를 운영 중입니다.