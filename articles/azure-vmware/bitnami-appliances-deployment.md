---
title: Bitnami 가상 어플라이언스 배포
description: Azure VMware Solution 프라이빗 클라우드에 배포하기 위해 Bitnami에서 탑재한 가상 어플라이언스에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634283"
---
# <a name="bitnami-appliance-deployment"></a>Bitnami 어플라이언스 배포

VMware의 Bitnami는 턴키 가상 어플라이언스의 풍부한 카탈로그를 제공합니다. 가장 일반적인 오픈 소스 소프트웨어 프로젝트를 포함하여 [VMware Marketplace에서](https://marketplace.cloud.vmware.com/)사용할 수 있는 Bitnami에 의해 vSphere 호환 어플라이언스를 배포할 수 있습니다.

이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 Bitnami가 패키지한 다음 가상 어플라이언스를 설치하고 구성하는 방법을 알아봅니다.

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>필수 구성 요소

- Azure VMware Solution [프라이빗 클라우드는 최소 3개의 노드로 배포됩니다.](deploy-azure-vmware-solution.md)

- [네트워크 계획 검사 목록](tutorial-network-checklist.md)에 설명된 대로 구성된 네트워킹입니다.



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>1단계: Bitnami 가상 어플라이언스 OVA/OVF 파일 다운로드


1. [VMware Marketplace로](https://marketplace.cloud.vmware.com/) 이동하여 Azure VMware Solution 프라이빗 클라우드에 설치하려는 가상 어플라이언스를 다운로드합니다.

   - [Bitnami에서 패키지된 LAMP 가상 어플라이언스](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. 버전을 선택하고 **다운로드를** 선택한 다음 EULA 라이선스에 동의합니다. 

   >[!NOTE]
   >가상 머신에서 파일에 액세스할 수 있는지 확인합니다.



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>2단계. 프라이빗 클라우드의 로컬 vCenter에 액세스

1. [Azure Portal](https://portal.azure.com)로그인하고 프라이빗 클라우드를 선택한 다음 ID **관리**  >  를 선택합니다.

1. vCenter URL, 사용자 이름 및 암호를 복사합니다. VM(가상 머신)에 액세스하는 데 사용합니다. 

1. **개요를** 선택하고 VM을 선택한 다음 RDP를 통해 연결합니다. 연결에 대한 도움이 필요한 경우 자세한 내용은 [가상 머신에 연결을](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) 참조하세요.

1. VM에서 브라우저를 열고 vCenter URL로 이동합니다. 

1. `cloudadmin@vsphere.local`이전에 복사한 사용자 자격 증명으로 로그인합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="VMware vSphere 로그인 페이지를 보여 주는 스크린샷" border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>3단계: vCenter에 Bitnami OVA/OVF 파일 설치

1. LAMP 가상 어플라이언스 설치할 클러스터를 마우스 오른쪽 단추로 클릭하고 **OVF 템플릿 배포를** 선택합니다.

1. **로컬 파일을** 선택하고 이전에 다운로드한 OVF 파일로 이동합니다. 그런 후 **다음** 을 선택합니다.

1. 데이터 센터를 선택하고 가상 어플라이언스 VM의 이름을 제공합니다(예: **bitnami-lampstack).** 그런 후 **다음** 을 선택합니다.

1. VM을 실행할 컴퓨팅 리소스로 ESXi 호스트를 선택한 후 **다음을** 선택합니다.

1. 세부 정보를 검토하고 **다음을** 선택합니다.

1. 사용권 계약에 동의하고 **다음** 을 선택합니다.

1. VM에 대한 스토리지를 선택하고 **다음을** 선택합니다.

1. VM의 대상 네트워크를 선택하고 **다음을** 선택합니다.

1. VM 및 네트워킹 속성과 같은 템플릿을 사용자 지정하는 데 필요한 정보를 제공합니다. 그런 후 **다음** 을 선택합니다.  

1. 구성 설정을 검토한 **다음, 마침을** 선택합니다.

1. 작업 **콘솔에서** OVF 템플릿 배포의 상태가 성공적으로 완료되었는지 확인합니다.

1. 설치가 완료되면 **작업** 에서 **전원** 켜기를 선택하여 어플라이언스를 켭니다. 

1. vCenter 콘솔에서 **웹 콘솔 시작** 을 선택하고 Bitnami 가상 어플라이언스로 로그인합니다. [Bitnami 가상 어플라이언스 지원 설명서에서](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/) 기본 사용자 이름 및 암호를 확인하세요.

   >[!NOTE]
   >기본 암호를 더 안전한 암호로 변경할 수 있습니다. 자세한 내용은 ...



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>4단계. 가상 어플라이언스로 고정 IP 할당

이 단계에서는 *bootproto* 및 *onboot* 매개 변수를 수정하고 고정 IP 주소를 Bitnami 가상 어플라이언스로 할당합니다. 

1. 네트워크 구성 파일을 검색합니다. 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. etc *\/ \/ sysconfig \/ network-scripts \/ ifcfg-ens160* 파일을 편집하고 부팅 매개 변수를 수정합니다. 그런 다음 고정 IP, netmask 및 게이트웨이 주소를 추가합니다.

   - bootproto=static

   - onboot=yes


1. **ifcfg-ens160** 파일의 변경 내용을 보고 확인합니다.

   ```bash
   cat ifcfg-ens160  
   ```

1. 네트워킹 서비스를 다시 시작합니다. 이렇게 하면 네트워킹 서비스가 먼저 중지된 다음 IP 구성이 적용됩니다. 

   ```bash
   sudo systemctl restart network
   ```

1. 게이트웨이 IP 주소를 Ping하여 네트워크에 대한 구성 및 VM 연결을 확인합니다.

1. 기본 경로 0.0.0.0이 나열되어 있는지 확인합니다.

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>5단계. 가상 어플라이언스로 SSH 액세스 사용

이 단계에서는 원격 액세스 제어를 위해 가상 어플라이언스에서 SSH를 사용하도록 설정합니다. SSH 서비스는 기본적으로 사용하지 않도록 설정되어 있습니다. 또한 PuTTy를 사용하여 호스트 콘솔에 연결합니다.

1. SSH 서비스를 사용하도록 설정하고 시작합니다.

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. etc *\/ \/ ssh \/ sshd_config* 파일을 편집하여 암호 인증을 변경합니다.

   ```bash
   PasswordAuthentication yes
   ```

1. **sshd_config** 파일의 변경 내용을 보고 확인합니다.

   ```bash
   sudo cat sshd_config
   ```

1. 파일의 변경 내용을 다시 로드합니다. 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. PuTTY를 열고 **SSH** 옵션을 선택하고 포트에 호스트 이름 및 **22를* 제공합니다. 그런 다음, **열기** 를 선택합니다. 

1. 가상 어플라이언스 콘솔 프롬프트에서 Bitnami 사용자 이름 및 암호를 입력하여 호스트에 연결합니다. 



