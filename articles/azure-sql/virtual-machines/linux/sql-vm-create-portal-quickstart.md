---
title: '빠른 시작: Azure에서 Linux SQL Server VM 만들기'
description: 이 자습서는 Azure Portal에서 Linux SQL Server 2017 가상 머신을 만드는 방법을 보여줍니다.
services: virtual-machines-sql
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.workload: iaas-sql-server
ms.author: mathoma
ms.custom: mode-portal
ms.openlocfilehash: b28ffa847b7c2057b10c8e5e8d8f8d8fae1ceb21
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361520"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Azure Portal에서 SQL Server를 실행하는 Linux 가상 머신 프로비전
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

이 빠른 시작 자습서에서는 Azure Portal을 사용하여 SQL Server 2017이 설치된 Linux 가상 머신을 만듭니다. 다음을 학습합니다. 


* [갤러리에서 SQL Server를 실행하는 Linux VM 만들기](#create)
* [SSH를 사용하여 새로운 VM에 연결](#connect)
* [SA 암호 변경](#password)
* [원격 연결의 구성](#remote)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>SQL Server가 설치된 Linux VM 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 창에서 **리소스 만들기** 를 선택합니다.

1. **리소스 만들기** 창에서 **컴퓨팅** 을 선택합니다.

1. **추천** 제목 옆에 있는 **모두 보기** 를 선택합니다.

   ![모든 VM 이미지를 봅니다.](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. 검색 상자에 **SQL Server 2019** 를 입력하고 **Enter** 를 선택하여 검색을 시작합니다.

1. **운영 체제** > **Redhat** 을 선택하여 검색 결과를 제한합니다.

    ![SQL Server 2019 VM 이미지의 검색 필터](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. 검색 결과에서 SQL Server 2019 Linux 이미지를 선택합니다. 이 자습서에서는 **RHEL74의 SQL Server 2019** 를 사용합니다.

   > [!TIP]
   > Developer 버전을 사용하면 SQL Server 라이선스의 비용 없이 Enterprise 버전의 기능을 사용하여 테스트하거나 개발할 수 있습니다. Linux VM을 실행하는 비용만을 지불합니다.

1. **만들기** 를 선택합니다. 


### <a name="set-up-your-linux-vm"></a>Linux VM 설정

1. **기본** 탭에서 **구독** 및 **리소스 그룹** 을 선택합니다. 

    ![기본 사항 창](./media/sql-vm-create-portal-quickstart/basics.png)

1. **가상 머신 이름** 에서 새 Linux VM에 사용할 이름을 입력합니다.
1. 그런 다음, 다음 값을 입력하거나 선택합니다.
   * **지역**: 적합한 Azure 지역을 선택합니다.
   * **가용성 옵션**: 앱과 데이터에 가장 적합한 가용성 및 중복성 옵션을 선택합니다.
   * **크기 변경**: 이 옵션을 선택하여 머신 크기를 고르고 완료하면 **선택** 을 누릅니다. VM 머신 크기에 대한 자세한 내용은 [VM 크기](../../../virtual-machines/sizes.md)를 참조하세요.

     ![VM 크기 선택](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > 개발 및 기능 테스트의 경우 **DS2** 이상인 VM 크기를 사용합니다. 성능 테스트에 **DS13** 이상을 사용하는 것이 좋습니다.

   * **인증 형식**: **SSH 공개 키** 를 선택합니다.

     > [!Note]
     > 인증을 위해 SSH 공개 키를 사용할지 아니면 암호를 사용할지 선택할 수 있습니다. SSH는 더 안전합니다. SSH 키를 생성하는 방법에 대한 지침은 [Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기](../../../virtual-machines/linux/mac-create-ssh-keys.md)를 참조하세요.

   * **사용자 이름**: VM에 사용할 관리자 이름을 입력합니다.
   * **SSH 공개 키**: RSA 공개 키를 입력합니다.
   * **공용 인바운드 포트**: **선택한 포트 허용** 을 선택하고 **공용 인바운드 포트 선택** 목록에서 **SSH(22)** 포트를 선택합니다. 이 빠른 시작에서 SQL Server 구성을 연결하고 완료하려면 이 단계가 필요합니다. SQL Server에 원격으로 연결하려면 가상 머신을 만든 후 인터넷을 통한 연결을 위해 Microsoft SQL Server에서 사용하는 기본 포트(1433)로의 트래픽을 수동으로 허용해야 합니다.

     ![인바운드 포트](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. 다음 추가 탭에서 원하는 설정 항목을 변경하거나 기본 설정을 유지합니다.
    * **디스크**
    * **네트워킹**
    * **관리**
    * **게스트 구성**
    * **태그**

1. **검토 + 만들기** 를 선택합니다.
1. **검토 + 만들기** 창에서 **만들기** 를 선택합니다.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Linux VM에 연결

이미 BASH 셸을 사용하는 경우 **ssh** 명령을 사용하여 Azure VM에 연결합니다. 다음 명령에서 VM 사용자 이름 및 IP 주소를 바꿔서 Linux VM에 연결합니다.

```bash
ssh azureadmin@40.55.55.555
```

Azure Portal에서 VM의 IP 주소를 찾을 수 있습니다.

![Azure Portal의 IP 주소](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Windows에서 실행 중이고 BASH 셸이 설치되지 않은 경우 PuTTY와 같은 SSH 클라이언트를 설치합니다.

1. [PuTTY 다운로드 및 설치](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. PuTTY를 실행합니다.

1. PuTTY 구성 화면에서 VM의 공용 IP 주소를 입력합니다.

1. **열기** 를 선택하고 프롬프트에 사용자 이름 및 암호를 입력합니다.

Linux VM에 연결하는 방법에 대한 자세한 내용은 [포털을 사용하여 Azure에서 Linux VM 만들기](../../../virtual-machines/linux/quick-create-portal.md)를 참조하세요.

> [!NOTE]
> 레지스트리에서 캐시되지 않은 서버의 호스트 키에 대한 PuTTY 보안 경고가 표시되는 경우 다음 옵션 중에서 선택합니다. 이 호스트를 신뢰하는 경우 **예** 를 선택하여 PuTTy의 캐시에 키를 추가하여 연결을 계속합니다. 한 번만 연결을 수행하려는 경우 캐시에 키를 추가하지 않고 **아니요** 를 선택합니다. 이 호스트를 신뢰하지 않는 경우 **취소** 를 선택하여 연결을 중단합니다.

## <a name="change-the-sa-password"></a><a id="password"></a> SA 암호 변경

새 가상 머신은 임의의 SA 암호를 사용하여 SQL Server를 설치합니다. SA 로그인을 사용하여 SQL Server에 연결하려면 먼저 이 암호를 다시 설정해야 합니다.

1. Linux VM에 연결한 후에 새 명령을 터미널을 엽니다.

1. 다음 명령을 사용하여 SA 암호를 변경합니다.

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   메시지가 표시되면 새 SA 암호와 암호 확인을 입력합니다.

1. SQL Server 서비스를 다시 시작합니다.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>경로에 도구 추가(선택 사항)

SQL Server 명령줄 도구 패키지를 포함하여 여러 SQL Server [패키지](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)를 기본적으로 설치합니다. 도구 패키지에는 **sqlcmd** 및 **bcp** 도구가 포함됩니다. 편의 상 선택적으로 도구 경로인 `/opt/mssql-tools/bin/`을 **PATH** 환경 변수에 추가할 수 있습니다.

1. 다음 명령을 실행하여 로그인 세션과 대화형/비로그인 세션에 대한 **PATH** 를 수정합니다.

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> 원격 연결의 구성

Azure VM의 SQL Server에 원격으로 연결해야 할 경우 네트워크 보안 그룹에서 인바운드 규칙을 구성해야 합니다. 규칙은 SQL Server를 수신하는 포트의 트래픽을 허용합니다(기본값 1433). 다음 단계에서는 이 단계에서 Azure Portal을 사용하는 방법을 보여줍니다.

> [!TIP]
> 프로비전하는 동안 설정에서 인바운드 포트 **MS SQL(1433)** 을 선택한 경우 사용자를 대신하여 이러한 설정이 지정됩니다. 방화벽 구성 방법에 대한 그 다음 섹션으로 넘어가도 됩니다.

1. 포털에서 **가상 머신** 를 선택한 다음 SQL Server VM을 선택합니다.
1. 왼쪽 탐색 메뉴의 **설정** 에서 **네트워킹** 을 선택합니다.
1. 네트워킹 창의 **인바운드 포트 규칙** 에서 **인바운드 포트 추가** 를 선택합니다.

   ![인바운드 포트 규칙](./media/sql-vm-create-portal-quickstart/networking.png)

1. **서비스** 목록에서 **MS SQL** 을 선택합니다.

    ![MS SQL 보안 그룹 규칙](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. **확인** 을 클릭하여 VM에 대한 규칙을 저장합니다.

### <a name="open-the-firewall-on-rhel"></a>RHEL에서 방화벽 열기

이 자습서에서는 사용자가 RHEL(Red Hat Enterprise Linux) VM을 만들도록 지시했습니다. 또한 RHEL VM에 원격으로 연결하려는 경우 Linux 방화벽에서 1433 포트를 열어야 합니다.

1. RHEL VM에 [연결](#connect)합니다.

1. BASH 셸에서 다음 명령을 실행합니다.

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>다음 단계

이제 Azure에 SQL Server 2017 가상 머신이 설치되었으므로 로컬로 **sqlcmd** 와 연결하여 Transact-SQL 쿼리를 실행할 수 있습니다.

원격 SQL Server 연결에 Azure VM을 구성한 경우 원격으로 연결할 수 있어야 합니다. Windows에서 원격으로 Linux의 SQL Server에 연결하는 방법의 예제는 [Windows의 SSMS를 사용하여 Linux의 SQL Server에 연결](/sql/linux/sql-server-linux-develop-use-ssms)을 참조하세요. Visual Studio Code와 연결하려면 [Visual Studio Code를 사용하여 SQL Server에 Transact-SQL 스크립트 만들기 및 실행](/sql/linux/sql-server-linux-develop-use-vscode)을 참조하세요.

Linux의 SQL Server에 대한 일반적인 정보는 [Linux의 SQL Server 2017 개요](/sql/linux/sql-server-linux-overview)를 참조하세요. SQL Server 2017 Linux 가상 머신에 대한 자세한 내용은 [Azure의 SQL Server 2017 Linux 가상 머신 개요](sql-server-on-linux-vm-what-is-iaas-overview.md)를 참조하세요.
