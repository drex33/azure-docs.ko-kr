---
title: Azure Site Recovery에서 설치의 재해 복구를 위한 Mobility Service 자동화
description: Azure Site Recovery를 사용하여 VMware/물리적 서버 재해 복구를 위해 Mobility Service를 자동으로 설치하는 방법입니다.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: how-to
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: fc9ba0bc4586983048e9e39f96b3e11d27c06053
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253542"
---
# <a name="automate-mobility-service-installation"></a>Mobility Service 설치 자동화

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)에서 Mobility Service 에이전트의 설치 및 업데이트를 자동화하는 방법을 설명합니다.

온-프레미스 VMware VM과 물리적 서버의 재해 복구를 위해 Azure에 Site Recovery를 배포할 때 복제하려는 각 머신에 Mobility Service 에이전트를 설치합니다. Mobility Service는 머신에 기록된 데이터를 캡처하여 복제를 위해 이를 Site Recovery 프로세스 서버에 전달합니다. 다음과 같은 몇 가지 방법으로 Mobility Service를 배포할 수 있습니다.

- **강제 설치**: Azure Portal에서 머신에 대한 복제를 사용하도록 설정하는 경우 Site Recovery가 Mobility Service 에이전트를 설치하도록 합니다.
- **수동 설치**: 각 머신에 Mobility Service를 수동으로 설치합니다. 강제 설치와 수동 설치에 대해 [자세히 알아보세요](vmware-physical-mobility-service-overview.md).
- **자동 배포**: Microsoft Endpoint Configuration Manager와 같은 소프트웨어 배포 도구 또는 JetPatch와 같은 타사 도구를 사용하여 설치를 자동화합니다.

자동 설치 및 업데이트는 다음과 같은 경우 솔루션이 될 수 있습니다.

- 조직에서 보호된 서버에 강제 설치를 허용하지 않는 경우
- 회사 정책에 따라 암호를 주기적으로 변경해야 하는 경우. 강제 설치에 암호를 지정해야 합니다.
- 보안 정책이 특정 머신에 대한 방화벽 예외 추가를 허용하지 않는 경우
- 호스팅 서비스 공급자 역할을 하는데 Site Recovery를 사용하여 강제 설치에 필요한 고객 머신 자격 증명을 제공하지 않으려는 경우
- 에이전트 설치를 많은 서버로 동시에 스케일링해야 하는 경우
- 계획된 유지 관리 기간에 설치 및 업그레이드를 예약하려는 경우

## <a name="prerequisites"></a>사전 요구 사항

설치를 자동화하려면 다음 항목이 필요합니다.

- [Configuration Manager](/configmgr/) 또는 [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/) 등과 같은 배포된 소프트웨어 설치 솔루션
- VMware 재해 복구 또는 [물리적 서버](physical-azure-disaster-recovery.md) 재해 복구를 위해 [Azure](tutorial-prepare-azure.md)와 [온-프레미스](vmware-azure-tutorial-prepare-on-premises.md)에 마련된 배포 필수 구성 요소. 재해 복구를 위한 [지원 요구 사항](vmware-physical-azure-support-matrix.md)을 검토하세요.

## <a name="prepare-for-automated-deployment"></a>자동화된 배포 준비

다음 표에는 Mobility Service 배포 자동화를 위한 도구 및 프로세스가 요약되어 있습니다.

**도구** | **세부 정보** | **지침**
--- | --- | ---
**구성 관리자** | 1. 위에 나열된 [필수 구성 요소](#prerequisites)가 마련되어 있는지 확인합니다. <br/><br/> 2. OVF 템플릿을 사용하여 Site Recovery 구성 서버를 VMware VM으로 배포하기 위해 OVA 파일을 다운로드하는 등 원본 환경을 설정하여 재해 복구를 배포합니다.<br/><br/> 3. 구성 서버를 Site Recovery 서비스에 등록하고, 대상 Azure 환경을 설정하고, 복제 정책을 구성합니다.<br/><br/> 4. 자동화된 Mobility Service 배포의 경우 구성 서버 암호 및 Mobility Service 설치 파일이 포함된 네트워크 공유를 만듭니다.<br/><br/> 5. 설치 또는 업데이트를 포함하는 Configuration Manager 패키지를 만들고 Mobility Service 배포를 준비합니다.<br/><br/> 6. Mobility Service가 설치된 머신에 대해 Azure로의 복제를 사용하도록 설정할 수 있습니다. | [Configuration Manager로 자동화](#automate-with-configuration-manager)
**JetPatch** | 1. 위에 나열된 [필수 구성 요소](#prerequisites)가 마련되어 있는지 확인합니다. <br/><br/> 2. OVF 템플릿을 사용하여 Site Recovery 환경에서 Azure Site Recovery를 위한 JetPatch Agent Manager를 다운로드하여 배포하는 등 원본 환경을 설정하여 재해 복구를 배포할 수 있습니다.<br/><br/> 3. 구성 서버를 Site Recovery에 등록하고, 대상 Azure 환경을 설정하고, 복제 정책을 구성합니다.<br/><br/> 4. 자동화된 배포의 경우 JetPatch Agent Manager 구성을 초기화하고 완료합니다.<br/><br/> 5. JetPatch에서 Mobility Service 에이전트의 배포 및 업그레이드를 자동화하는 Site Recovery 정책을 만들 수 있습니다. <br/><br/> 6. Mobility Service가 설치된 머신에 대해 Azure로의 복제를 사용하도록 설정할 수 있습니다. | [JetPatch Agent Manager로 자동화](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [JetPatch에서 에이전트 설치 문제 해결](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Configuration Manager로 자동화

### <a name="prepare-the-installation-files"></a>설치 파일 준비

1. 다음 필수 구성 요소가 마련되어 있는지 확인합니다.
1. 구성 서버를 실행 중인 머신에서 액세스할 수 있는 보안 네트워크 파일 공유(SMB 공유)를 만듭니다.
1. Configuration Manager에서 Mobility Service를 설치하거나 업데이트하려는 [서버를 범주화](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)합니다. 한 컬렉션에는 모든 Windows 서버, 다른 모든 Linux 서버가 포함되어야 합니다.
1. 네트워크 공유에서 폴더를 만듭니다.

   - Windows 머신에 설치하는 경우 _MobSvcWindows_ 라는 폴더를 만듭니다.
   - Linux 머신에 설치하는 경우 _MobSvcLinux_ 라는 폴더를 만듭니다.

1. 구성 서버 컴퓨터에 로그인합니다.
1. 구성 서버 머신에서 관리 명령 프롬프트를 엽니다.
1. 암호 파일을 생성하려면 다음 명령을 실행합니다.

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. _MobSvc.passphrase_ 파일을 Windows 폴더 및 Linux 폴더에 복사합니다.
1. 설치 파일이 포함된 폴더로 이동하려면 다음 명령을 실행합니다.

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. 설치 파일을 네트워크 공유에 복사합니다.

   - Windows의 경우 _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ 를 _MobSvcWindows_ 에 복사합니다.
   - Linux의 경우 다음 파일을 _MobSvcLinux_ 에 복사합니다.
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. 다음 절차에 설명된 대로 Windows 또는 Linux 폴더에 코드를 복사합니다. 다음을 가정합니다.

   - 구성 서버의 IP 주소는 `192.168.3.121`입니다.
   - 보안 네트워크 파일 공유는 `\\ContosoSecureFS\MobilityServiceInstallers`입니다.

### <a name="copy-code-to-the-windows-folder"></a>Windows 폴더에 코드 복사

다음 코드를 복사합니다.

- _MobSvcWindows_ 폴더의 코드를 _install.bat_ 으로 저장합니다.
- 이 스크립트의 `[CSIP]` 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿉니다.
- 스크립트는 Mobility Service 에이전트의 새로 설치 및 이미 설치된 에이전트의 업데이트를 지원합니다.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Linux 폴더로 코드 복사

다음 코드를 복사합니다.

- _MobSvcLinux_ 폴더의 코드를 _install_linux.sh_ 로 저장합니다.
- 이 스크립트의 `[CSIP]` 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿉니다.
- 스크립트는 Mobility Service 에이전트의 새로 설치 및 이미 설치된 에이전트의 업데이트를 지원합니다.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r Agent -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>패키지 만들기

1. Configuration Manager 콘솔에 로그인하고 **소프트웨어 라이브러리** > **애플리케이션 관리** > **패키지** 로 이동합니다.
1. **패키지** > **패키지 만들기** 를 마우스 오른쪽 단추로 클릭합니다.
1. 이름, 설명, 제조업체, 언어, 버전 등 패키지 세부 정보를 제공합니다.
1. **이 패키지는 원본 파일을 포함함** 을 선택합니다.
1. **찾아보기** 를 클릭하고 관련 설치 프로그램(_MobSvcWindows_ 또는 _MobSvcLinux_)을 포함하는 네트워크 공유 및 폴더를 선택합니다. 그다음에 **다음** 을 선택합니다.

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. **만들려는 프로그램 유형 선택** 페이지에서 **표준 프로그램** > **다음** 을 클릭합니다.

   ![표준 프로그램 옵션을 보여 주는 패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. **이 표준 프로그램에 대한 정보 지정** 페이지에서 다음 값을 지정합니다.

    **매개 변수** | **Windows 값** | **Linux 값**
    --- | --- | ---
    **이름** | Microsoft Azure Mobility Service(Windows) 설치 | Microsoft Azure Mobility Service(Linux) 설치
    **명령줄** | install.bat | ./install_linux.sh
    **프로그램을 실행할 수 있는 조건** | 사용자 로그온 여부 | 사용자 로그온 여부
    **기타 매개 변수** | 기본 설정 사용 | 기본 설정 사용

   ![표준 프로그램에 대해 지정할 수 있는 정보를 보여 주는 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. **이 표준 프로그램의 요구 사항 지정** 에서 다음 작업을 수행합니다.

   - Windows 머신의 경우 **지정된 플랫폼에서만 이 프로그램 실행** 을 선택합니다. 그런 다음 [지원되는 Windows 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines)를 선택하고 **다음** 을 선택합니다.
   - Linux 머신의 경우 **모든 플랫폼에서 이 프로그램 실행** 을 선택합니다. 그런 후 **다음** 을 선택합니다.

1. 마법사를 마칩니다.

### <a name="deploy-the-package"></a>패키지 배포

1. Configuration Manager 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포** 를 선택합니다.

   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. 패키지를 복사할 위치에 배포 지점을 선택합니다. [자세히 알아봅니다](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. 마법사를 완료합니다. 그러면 패키지가 지정된 배포 지점에 복제를 시작합니다.
1. 패키지 배포를 마친 후 패키지 > **배포** 를 마우스 오른쪽 단추로 클릭합니다.

   ![배포 메뉴 옵션을 보여 주는 Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. 이전에 만든 Windows 또는 Linux 디바이스 컬렉션을 선택합니다.
1. **콘텐츠 대상 지정** 페이지에서 **배포 지점** 을 선택합니다.
1. **이 소프트웨어의 배포 방법을 제어하는 설정 지정** 페이지에서 **용도** 를 **필수** 로 설정합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. **이 배포의 일정 지정** 에서 일정을 설정합니다. [자세히 알아봅니다](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Mobility Service는 지정한 일정에 따라 설치됩니다.
   - 불필요한 재부팅을 방지하려면 매월 유지 관리 시간 또는 소프트웨어 업데이트 시간에 패키지 설치를 예약합니다.

1. **배포 지점** 페이지에서 설정을 구성하고 마법사를 완료합니다.
1. Configuration Manager 콘솔에서 배포 진행률을 모니터링합니다. **모니터링** > **배포** >  _\<your package name\>_ 로 이동합니다.

### <a name="uninstall-the-mobility-service"></a>Mobility Service 제거

Mobility Service를 제거하기 위한 Configuration Manager 패키지를 만들 수 있습니다. 예를 들어 다음 스크립트는 Mobility Service를 제거합니다.

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>다음 단계

VM에 대해 [복제 활성화](vmware-azure-enable-replication.md)
