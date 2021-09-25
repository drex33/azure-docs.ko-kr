---
title: Azure의 SAP HANA(대규모 인스턴스)에 SMT 서버를 설정하는 방법 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 SMT 서버를 설정하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9350b6d2a8b593e224da817bcbc3142605276248
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599129"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux용 SMT 서버 설정

이 문서에서는 BareMetal Infrastructure라고도 하는 Azure의 SAP HANA(대규모 인스턴스)에 대해 SMT 서버를 설정하는 단계를 안내합니다.

SAP HANA 대규모 인스턴스는 인터넷에 직접 연결되지 않습니다. 따라서 운영 체제 공급자에 이러한 단위를 등록하고 업데이트를 다운로드하고 적용하는 것은 간단하지 않습니다. SUSE Linux용 솔루션은 Azure VM(가상 머신)에 SMT 서버를 설정하는 것입니다. HLI(HANA(대규모 인스턴스))에 연결된 Azure 가상 네트워크에서 가상 머신을 호스트합니다. SMT 서버가 설치되어 있는 HANA 큰 인스턴스는 업데이트를 등록하고 다운로드할 수 있습니다. 

SUSE에 관한 자세한 내용은 [SLES 12 SP2용 구독 관리 도구](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

HANA(대규모 인스턴스)용 SMT 서버를 설치하려면 먼저 다음이 필요합니다.

- HANA(대규모 인스턴스) ExpressRoute 회로에 연결된 Azure 가상 네트워크.
- 조직과 연결된 SUSE 계정. 조직에는 유효한 SUSE 구독이 있어야 합니다.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Azure 가상 머신에 SMT 서버 설치

1. 먼저 [SUSE 고객 센터](https://scc.suse.com/)에 로그인합니다. **조직** > **조직 자격 증명** 으로 이동합니다. 이 섹션에서는 SMT 서버를 설정하는 데 필요한 자격 증명을 찾아야 합니다.

2. Azure 가상 네트워크에 SUSE Linux VM을 설치합니다. 가상 머신을 배포하려면 Azure의 SLES 12 SP2 갤러리 이미지를 사용하세요(BYOS SUSE 이미지 선택). 배포 과정에서 DNS 이름을 정의하지 않고 고정 IP 주소를 사용하지 않습니다.

    ![SMT 서버에 대한 가상 머신 배포의 스크린샷](./media/hana-installation/image3_vm_deployment.png)

    배포된 가상 머신은 Azure 가상 네트워크에서 내부 IP 주소(10.34.1.4)를 사용합니다. 가상 머신 이름은 *smtserver* 입니다. 설치 후 HANA(대규모 인스턴스)에 대한 연결을 확인합니다. 이름 확인을 구성하는 방법에 따라 Azure 가상 머신의 etc/hosts에 HANA(대규모 인스턴스)의 확인을 구성해야 할 수 있습니다. 

3. 가상 머신에 디스크를 추가합니다. 이 디스크를 사용하여 업데이트를 저장합니다. 부팅 디스크 자체가 너무 작을 수 있습니다. 여기서 디스크는 다음 스크린샷처럼 /srv/www/htdocs에 탑재됩니다. 100GB 디스크로 충분합니다.

    ![PuTTy 창에 추가된 디스크를 보여 주는 스크린샷.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

4. HANA 큰 인스턴스에 로그인합니다. /etc/hosts를 유지 관리합니다. 네트워크를 통해 SMT 서버를 실행할 Azure 가상 머신에 연결할 수 있는지 확인합니다.

5. SMT 서버를 실행할 Azure 가상 머신에 로그인합니다. putty를 사용하여 가상 머신에 로그인하는 경우 bash 창에서 다음 명령을 순서대로 실행합니다.

    ```
    cd ~
    echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
    ```

6. Bash를 다시 시작하여 설정을 활성화합니다. 그런 다음 YAST를 시작합니다.

7. SUSE 사이트에 VM(smtserver)을 연결합니다.

    ```
    smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
    Registered SLES_SAP 12.2 x86_64
    To server: https://scc.suse.com
    Using E-Mail: email address
    Successfully registered system.
    ```
    
8. 가상 머신이 SUSE 사이트에 연결되면 SMT 패키지를 설치합니다. 다음 putty 명령을 사용하여 SMT 패키지를 설치합니다.

    ```
    smtserver:~ # zypper in smt
    Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
    Loading repository data...
    Reading installed packages...
    Resolving package dependencies...
    ```
    
    YAST 도구를 사용하여 SMT 패키지를 설치할 수도 있습니다. YAST에서 **소프트웨어 유지 관리** 로 이동하여 SMT를 검색합니다. **SMT** 를 선택하면 자동으로 yast2-smt로 전환됩니다.

    [![YAST의 SMT 스크린샷](./media/hana-installation/image5_smt_in_yast.PNG)](./media/hana-installation/image5_smt_in_yast.PNG#lightbox)

    smtserver에 설치를 위한 선택 사항을 수락합니다. 


9. 설치가 완료되면 SMT 서버 구성으로 이동합니다. 앞에서 검색한 SUSE 고객 센터의 조직 자격 증명을 입력합니다. 또한 Azure 가상 머신 호스트 이름을 SMT 서버 URL로 입력합니다. 이 예제에서는 https:\//smtserver입니다.

    [![SMT 서버 구성의 스크린샷](./media/hana-installation/image6_configuration_of_smtserver1.png)](./media/hana-installation/image6_configuration_of_smtserver1.png#lightbox)

10. 이제 SUSE 고객 센터에 대한 연결이 작동하는지 여부를 테스트합니다. 다음 스크린샷에서 표시된 것처럼 이 예제에서는 연결이 작동되었습니다.

    [![SUSE 고객 센터에 대한 연결을 테스트하는 스크린샷](./media/hana-installation/image7_test_connect.png)](./media/hana-installation/image7_test_connect.png#lightbox)

11. SMT 설정이 시작되면 데이터베이스 암호를 제공합니다. 새로 설치하는 것이므로 다음 스크린샷에 표시된 대로 해당 암호를 정의해야 합니다.

    [![데이터베이스의 암호를 정의하는 스크린샷](./media/hana-installation/image8_define_db_passwd.PNG)](./media/hana-installation/image8_define_db_passwd.PNG#lightbox)

12. 인증서 만들기

    [![SMT 서버에 대한 인증서를 만드는 스크린샷](./media/hana-installation/image9_certificate_creation.PNG)](./media/hana-installation/image9_certificate_creation.PNG#lightbox)

    구성의 끝에 동기화 확인을 실행하려면 몇 분 정도 걸릴 수 있습니다. SMT 서버를 설치하고 구성한 후에 탑재 지점인 /srv/www/htdocs/ 아래에 디렉터리 리포지토리가 있어야 합니다. 리포지토리 아래에 몇 가지 하위Directories도 있습니다. 

13. 다음 명령으로 SMT 서버 및 관련 서비스를 다시 시작합니다.

    ```
    rcsmt restart
    systemctl restart smt.service
    systemctl restart apache2
    ```

## <a name="download-packages-onto-the-smt-server"></a>SMT 서버에 패키지 다운로드

1. 모든 서비스가 다시 시작된 후 YAST를 사용하여 SMT Management에서 적절한 패키지를 선택합니다. 패키지 선택은 HANA 큰 인스턴스 서버의 운영 체제 이미지에 따라 달라집니다. 패키지 선택은 SLES 릴리스 또는 SMT 서버를 실행하는 가상 머신 버전에 따라 달라지지 않습니다. 다음 스크린샷은 선택 화면에 대한 예를 보여줍니다.

    [![패키지를 선택하는 스크린샷](./media/hana-installation/image10_select_packages.PNG)](./media/hana-installation/image10_select_packages.PNG#lightbox)

2. 설정한 SMT 서버에서 선택한 패키지의 초기 복사본을 시작합니다. 이 복사본은 셸에서 smt-mirror 명령을 사용하여 트리거됩니다.

   [![SMT 서버에 패키지를 다운로드하는 스크린샷](./media/hana-installation/image11_download_packages.PNG)](./media/hana-installation/image11_download_packages.PNG#lightbox)

    패키지는 탑재 지점 /srv/www/htdocs에서 만든 디렉터리에 복사해야 합니다. 이 프로세스는 선택하는 패키지 수에 따라 한 시간 이상 걸릴 수 있습니다. 이 프로세스를 마치면 SMT 클라이언트 설정으로 이동합니다. 

## <a name="set-up-the-smt-client-on-hana-large-instances"></a>HANA(대규모 인스턴스)에 SMT 클라이언트 설정

이 경우 클라이언트는 HANA(대규모 인스턴스)입니다. SMT 서버 설정이 clientSetup4SMT.sh 스크립트를 Azure 가상 머신에 복사했습니다. 

해당 스크립트를 SMT 서버에 연결할 HANA(대규모 인스턴스)로 복사합니다. -h 옵션을 사용하여 스크립트를 시작하고 매개 변수로 SMT 서버의 이름을 지정합니다. 이 예제에서 해당 이름은 *smtserver* 입니다.

[![SMT 클라이언트를 구성하는 스크린샷](./media/hana-installation/image12_configure_client.PNG)](./media/hana-installation/image12_configure_client.PNG#lightbox)

클라이언트가 서버에서 인증서를 로드할 수 있습니다. 그러나 이 예제에서는 다음 스크린샷과 같이 등록이 실패합니다.

[![클라이언트 등록 실패의 스크린샷](./media/hana-installation/image13_registration_failed.PNG)](./media/hana-installation/image13_registration_failed.PNG#lightbox)

등록이 실패하면 [SUSE 지원 문서](https://www.suse.com/de-de/support/kb/doc/?id=7006024)를 참조하여 문서에 설명된 단계를 실행합니다.

> [!IMPORTANT] 
> 서버 이름의 경우 정규화된 도메인 이름은 사용하지 않고 가상 머신 이름을 제공합니다(이 경우 *smtserver* 입니다). 
    
이 단계를 실행한 후 HANA(대규모 인스턴스)에서 다음 명령을 실행합니다.
    
```
SUSEConnect –cleanup
```

> [!Note] 
> 해당 단계를 수행한 후 몇 분 정도 기다립니다. clientSetup4SMT.sh를 즉시 실행하는 경우 오류가 발생할 수 있습니다.

SUSE 문서의 단계에 따라 수정해야 하는 문제를 찾으면 HANA(대규모 인스턴스)에서 clientSetup4SMT.sh를 다시 시작합니다. 이제 성공적으로 완료됩니다.

[![클라이언트 등록 성공의 스크린샷](./media/hana-installation/image14_finish_client_config.PNG)](./media/hana-installation/image14_finish_client_config.PNG#lightbox)

Azure VM에 설치된 SMT 서버에 연결할 HLI의 SMT 클라이언트를 구성했습니다. 이제 “zypper up” 또는 “zypper in”을 사용하여 HANA(대규모 인스턴스)에 OS 업데이트를 설치하거나 다른 패키지를 설치합니다. 이전에 다운로드한 업데이트만 SMT 서버에 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure의 SAP HANA(대규모 인스턴스)를 Azure Virtual Machines로 마이그레이션하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션](hana-large-instance-virtual-machine-migration.md)
