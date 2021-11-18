---
title: 애플리케이션 설치 템플릿 생성
description: Azure에서 SAP 배포 자동화 프레임워크와 함께 사용할 SAP 애플리케이션 템플릿을 생성하는 방법입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 7d9ade08ce2e07c6a93866ea137eca558e9d0c19
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730456"
---
# <a name="generate-sap-application-templates-for-automation"></a>자동화를 위한 SAP 애플리케이션 템플릿 생성

[Azure의 SAP 배포 자동화 프레임워크는](automation-deployment-framework.md) BOM(제품 구성표)을 사용하여 SAP 애플리케이션을 정의합니다. 사용자 지정 BOM을 사용하여 시스템을 배포하려면 무인 SAP 설치에 사용되는 ini-files에 대한 템플릿도 만들어야 합니다. 이 가이드에서는 SAP/S4 배포를 위한 애플리케이션 템플릿을 만드는 방법을 설명합니다. 프로세스는 다른 SAP 애플리케이션에서도 동일합니다.

## <a name="prerequisites"></a>필수 구성 요소

- SAP 설치 미디어 및 관련 파일을 아직 다운로드하지 않은 경우 [다운로드하고 준비합니다.](automation-bom-get-files.md) [다운로드한 SAPCAR 유틸리티 파일의 이름을](automation-bom-get-files.md#acquire-media) 사용할 수 있는지 확인합니다.
- 아직 수행하지 않은 경우 [BOM을 준비합니다.](automation-bom-prepare.md) 만든 BOM 파일을 사용할 수 있는지 확인합니다.
- Azure 구독 Azure 구독이 없는 경우 체험 [계정 을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 사용하려는 데이터베이스를 사용할 수 있는 권한이 있는 SAP 계정
- 필요에 따라 스토리지 계정에서 SAP 미디어를 전송하는 데 사용할 VM(가상 머신)을 Azure 내에 만듭니다. 이 메서드는 전송 속도를 향상시킵니다. VM과 대상 SAP VM 간에 연결이 있는지 확인합니다. 예를 들어 SSH 키가 준비되어 있는지 확인합니다.

## <a name="check-media-and-tools"></a>미디어 및 도구 확인

SAP 애플리케이션 템플릿을 생성하기 전에 모든 필수 설치 미디어 및 도구가 있는지 확인합니다.

1. 루트 사용자로 대상 VM에 로그인합니다.

1. 루트 사용자 암호를 알려진 값으로 변경합니다. 이 암호는 나중에 SAP SWPM(Software Provisioning Manager)에 연결하는 데 사용합니다.

1. 임시 디렉터리를 만들고 변경합니다.

    ```bash
    mkdir /tmp/workdir; cd $_
    ```

1. SAP 애플리케이션 템플릿에 대한 임시 디렉터리인지 확인합니다.

    ```bash
    mkdir /tmp/app_template/
    ```

1. SAPCAR 유틸리티에 대한 사용 권한을 변경하여 이 파일을 실행 가능하게 만듭니다. `<SAPCAR>.EXE`를 다운로드한 파일의 이름으로 대체합니다. 예들 들어 `SAPCAR_1311-80000935.EXE`입니다.

    ```bash
    chmod +x /usr/sap/install/download_basket/<SAPCAR>.EXE
    ```

1. SWPM에 대한 설치 폴더가 있는지 확인합니다.

    ```bash
    mkdir -p /usr/sap/install/SWPM
    ```

1. SAPCAR 유틸리티를 사용하여 SWPM 설치 파일을 추출합니다.

```bash
/usr/sap/install/download_basket/SAPCAR_1311-80000935.EXE -xf /usr/sap/install/SWPM20SP07_0-80003424.SAR -R /usr/sap/install/SWPM/
```

매개 변수 파일을 통해 무인 SAP 설치를 수행할 수 있습니다. 이러한 파일은 SWPM 설치 관리자에 필요한 모든 매개 변수를 전달합니다. 

> [!NOTE]
> 매개 변수 파일을 생성하려면 수동 설치를 부분적으로 수행해야 합니다. 이유에 대한 자세한 내용은 [SAP NOTE 2230669.](https://launchpad.support.sap.com/#/notes/2230669)

## <a name="generate-ascs-parameter-file"></a>ASCS 매개 변수 파일 생성

ASCS에 대한 무인 설치 매개 변수 파일을 생성하려면 다음을 수행합니다.

1. CLI(명령줄 인터페이스)를 통해 루트 사용자로 VM에 로그인합니다.

1. 명령을 `hostname` 실행하여 설치를 실행 중인 VM의 호스트 이름을 확인합니다. 고유한 호스트 이름(여기서 `<example-vm-hostname>` 은 예제 출력에 있음)과 GUI의 전체 URL을 모두 기록해 둡다.
   

1. [VM에 필요한 모든 미디어 및 도구가 설치되어 있는지 확인합니다.](#check-media-and-tools)

1. 다음과 같이 SWPM을 시작합니다. 

    1. `<target-VM-hostname>`를 이전에 가져온 호스트 이름으로 대체합니다.

    1. `<XML-stack-file-path>`을 만든 XML 스택 파일 경로로 대체합니다. 예들 들어 `/usr/sap/install/config/MP_STACK_S4_2020_v001.xml`입니다.

    ```bash
    /usr/sap/install/SWPM/sapinst                      \
    SAPINST_XML_FILE=<XML-stack-file-path>.xml         \
    SAPINST_USE_HOSTNAME=<target-VM-hostname>
    SAPINST_START_GUISERVER=true \
    SAPINST_STOP_AFTER_DIALOG_PHASE=true 
    
    ```

    ```output
    Connecting to the ASCS VM to launch
    ********************************************************************************
    Open your browser and paste the following URL address to access the GUI
    https://<example-VM-hostname>.internal.cloudapp.net:4237/sapinst/docs/index.html
    Logon users: [root]
    ********************************************************************************
    ```


1. 브라우저를 열고 이전에 가져온 GUI의 URL을 방문합니다.

    1. 보안 위험 경고를 수락합니다.
    
    1. 시스템의 루트 사용자 자격 증명으로 인증합니다.
    
1. 드롭다운 메뉴에서 **SAP S/4HANA Server 2020** &gt; **SAP HANA 데이터베이스** &gt; **설치** 애플리케이션 &gt; **서버 ABAP** 분산 시스템 &gt;  &gt; **ASCS 인스턴스를** 선택합니다.
        
1. **매개 변수 모드에서** **사용자 지정** 을 선택합니다. 그다음에 **다음** 을 선택합니다.

1. SAP 시스템 설정을 구성합니다.

    1. SAP 시스템 식별자가 인지 `{SID}` 확인합니다.
    
    1. SAP 탑재 디렉터리 값이 인지 `/sapmnt` 확인합니다.
    
    1. **다음** 을 선택합니다.

1. FQDN(정규화된 도메인 이름) 설정을 구성합니다. 

    1. FQDN 값이 자동으로 채워지도록 합니다.
    
    1. SAP 시스템에 **대해 FQDN 설정을** 사용하도록 설정해야 합니다.
    
    1. **다음** 을 선택합니다.

1. 이 ASCS 인스턴스를 만드는 동안에만 사용하는 기본 암호를 설정합니다. 암호에는 영숫자 문자와 특수 문자 , , 및 만 사용할 `#` `$` 수 `@` `_` 있습니다. 첫 번째 문자로 숫자 또는 밑밑을 사용할 수도 없습니다.

    1. 기본 암호를 입력합니다.

    1. 기본 암호를 확인합니다.

    1. **다음** 을 선택합니다. 

1. 더 많은 관리자 설정을 구성합니다. 다른 암호 필드는 설정한 기본 암호에 따라 미리 채워집니다.

    1. 관리자 OS 사용자(여기서> `<sid>adm` SID)의 식별자를 `<sid` 로 `2000` 설정합니다.

    1. SAP 시스템( )의 식별자를 `sapsys` 로 `2000` 설정합니다.

    1. **다음** 을 선택합니다.

1. SAPEXE 커널 파일 경로를 묻는 메시지가 표시되면 `/usr/sap/install/download_basket` 를 입력한 후 **다음을** 선택합니다.

1. 패키지 상태가 사용 **가능인지** 확인한 후 **다음을** 선택합니다.

1. SAP 호스트 에이전트 설치 파일 상태가 **사용 가능인지** 확인한 후 **다음을** 선택합니다.

1. SAP 관리자 OS 사용자에 대한 정보를 제공합니다.

    1. 기본 암호에서 상속된 암호를 그대로 둡니다.

    1. OS 사용자 식별자를 `2100` 로 설정합니다.

    1. **다음** 을 선택합니다.

1. 설치 설정을 확인합니다.

    1. 설치의 인스턴스 번호가 올바른지 확인합니다.

    1. 인스턴스의 가상 호스트 이름을 설정해야 합니다.

    1. **다음** 을 선택합니다.

1.  ABAP 메시지 서버 포트 설정을 유지합니다. 이러한 기본 설정은 `3600` 및 `3900` 입니다. 그런 다음, **'다음을** 선택합니다.

1. 설치할 다른 구성 요소를 선택하지 않고 **다음을** 선택합니다.

1. **보안 매개 변수 설정 건너뛰기** 를 사용하도록 설정한 후 **다음을** 선택합니다.

1. 예를 사용하도록 설정하고 **운영 체제 사용자를 정리한** 후 **다음을** 선택합니다.

1. **매개 변수 요약** 에서 아직 아무 것도 수행하지 않습니다.

1. CLI의 임시 SAP 설치 디렉터리에서 설치 구성 파일을 찾습니다. 이 시점에서 파일을 라고 하며, `inifile.params`

    1. `ls /tmp/sapinst_instdir/`를 실행하여 SAP 설치 디렉터리에 있는 파일을 나열합니다.

    1. 파일이 있는 경우 `.lastInstallationLocation` 파일 내용을 보고 나열된 디렉터리를 기록해 둡니다.

    1. 설치 중인 제품의 디렉터리(예: )가 있는 경우 `S4HANA2020` 제품 폴더로 이동합니다. 예를 들어 `cd /tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/ASCS/`를 실행합니다.

1. 브라우저의 SWPM GUI에서 **취소를** 선택합니다. 이제 ASCS의 무인 설치를 수행할 수 있는 템플릿을 빌드하는 데 필요한 ini 파일이 있습니다.

1. 를 복사하고 `inifile.params` 의 이름을 로 바꿉니다. `scs.inifile.params` `/tmp/app_template` `<path-to-INI-file>`를 다음과 같이 INI 파일의 경로로 대체합니다.

    ```bash
    cp <path-to-INI-file>/inifile.params /tmp/app_template/scs.inifile.params
    ```

## <a name="load-database-content"></a>데이터베이스 콘텐츠 로드

시작하기 전에 VM에 다음 설정이 있는지 확인합니다.

- HANA 및 SCS 인스턴스를 설치 하 고 구성 합니다. 이러한 인스턴스는 데이터베이스 콘텐츠 로드를 완료 하기 전에 온라인 상태 여야 합니다.

- `<sid>adm` [Ascs에 대 한 무인 설치 파일을 생성할 때 만든](#generate-ascs-parameter-file) 사용자는 해당 그룹의 구성원 이어야 합니다 `sapinst` .

- 의 사용자 식별자는 `<sid>adm` 의 값과 일치 해야 합니다 `hdblcm` . 이 예에서는 `2000`를 사용합니다.

- SWPM은에 대 한 액세스 권한이 필요 `/sapmnt/<SID>/global/` 합니다. 사용 권한을 구성 하려면를 실행 `chown <sid>adm:sapsys /sapmnt/<SID>/global` 합니다.

### <a name="generate-database-load-template"></a>데이터베이스 로드 템플릿 생성

데이터베이스 콘텐츠를 로드 하기 위한 무인 설치 매개 변수 파일을 생성 하려면:

1. 임시 디렉터리로 설정 하 고 변경 합니다. `<sid>`을 SID로 바꿉니다.
    
    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/db_workdir"; cd $_
    ```

1. SWPM을 시작 하 고 나열 된 URL을 적어둡니다.

    ```bash
    /usr/sap/install/SWPM/sapinst   \
    SAPINST_XML_FILE=/usr/sap/install/config/MP_STACK_S4_2020_v001.xml
    ```

1. 브라우저에서 적어둔 URL을 방문 합니다.

1. 보안 위험 경고를 적용 합니다.
    
1. 시스템의 루트 사용자 자격 증명을 사용 하 여 인증 합니다.

1. 사용자 지정 매개 변수를 사용 하 여 분산 시스템을 만듭니다.

    1. 드롭다운 메뉴에서 **SAP S4/HANA Server 2020** &gt; **SAP HANA 데이터베이스** &gt; **설치** &gt; **응용 프로그램 서버 ABAP** &gt; **분산 시스템** &gt; **데이터베이스 인스턴스** &gt; **분산 시스템** 으로 이동 합니다.

    1. **사용자 지정** 매개 변수 모드를 선택 합니다.

    1. **다음** 을 선택합니다.

1. ASCS 설치에서 만드는 프로필 디렉터리의 경로를 확인 합니다. 예를 들어 `/usr/sap/<SID>/SYS/profile` 여기서 `<SID>` 는 SID입니다. 그다음에 **다음** 을 선택합니다.

1. ascs 인스턴스에 대 한 ABAP 메시지 서버 포트를 입력 합니다. 포트 번호는 이며 `36<InstanceNumber>` , 여기서 `<InstanceNumber>` 는 HANA 인스턴스 번호입니다. 예를 들어 0 개의 인스턴스가 있는 경우 `3600` 는 포트 번호입니다. 그다음에 **다음** 을 선택합니다.

1. 데이터베이스 콘텐츠를 설치 하는 동안 사용할 주 암호를 입력 합니다. 그다음에 **다음** 을 선택합니다.

1. 사용자의 SID 인 관리자 사용자에 대 한 세부 정보가 `<SID>adm` `SID` 올바른지 확인 합니다. 그다음에 **다음** 을 선택합니다.

1. **SAP HANA Database 테 넌 트** 에 대 한 정보를 입력 합니다. 

    1. **데이터베이스 호스트** 에 HANA 데이터베이스 VM의 호스트 이름을 입력 합니다. 이 호스트 이름을 찾으려면 Azure Portal의 리소스 페이지로 이동 합니다.

    1. **인스턴스 번호** 에 HANA 인스턴스 번호를 입력 합니다. 예들 들어 `00`입니다.

    1. 새 데이터베이스 테 넌 트의 식별자를 입력 합니다. 예들 들어 `S4H`입니다.

    1. 데이터베이스 시스템 관리자에 대해 자동으로 생성 된 암호를 유지 합니다.

    1. **다음** 을 선택합니다.

1. 연결 정보가 올바른지 확인 합니다. 그런 다음 **확인** 을 선택합니다.

1. 시스템 데이터베이스에 대 한 관리자 암호를 입력 합니다. 그다음에 **다음** 을 선택합니다.

1. SAPEXE 커널의 경로를 입력 `/usr/sap/install/download_basket` 합니다. 그다음에 **다음** 을 선택합니다.

1. 사용할 수 있는 파일을 검토 합니다.
    
    1. **다음** 을 선택합니다.

    1. `SAPHOSTAGENT`파일을 사용할 수 있는지 확인 합니다.

    1. **다음** 을 다시 선택합니다.

1. 암호 확인 페이지에서 **다음** 을 선택 합니다.

1. 모든 핵심 HANA 데이터베이스 내보내기 파일을 사용할 수 있는지 검토 합니다. 그다음에 **다음** 을 선택합니다.

1. **데이터베이스 스키마** 에 대해 `SAPHANADB` **다음** 을 선택 합니다.

1. **데이터베이스 연결에 대 한 보안 Storage** 에서 **다음** 을 선택 합니다.

1. **SAP HANA 가져오기 매개 변수** 에서 **다음** 을 선택 합니다.

1. 데이터베이스 VM에 대 한 HANA 데이터베이스 관리자 ()의 암호를 입력 합니다 `<SID>adm` . 그다음에 **다음** 을 선택합니다.

1. **클라이언트 소프트웨어 설치 경로 SAP HANA** 에서 **다음** 을 선택 합니다.

1. SAP HANA 클라이언트 파일을 사용할 수 있는지 확인 하십시오. 그다음에 **다음** 을 선택합니다.

1. **예, 운영 체제 사용자를 정리** 해야 합니다. 그다음에 **다음** 을 선택합니다.

1. **매개 변수 요약** 에서 아직 아무것도 선택 하지 않습니다.

1. CLI를 열고 설치 구성 파일을 찾습니다.

    1. 임시 디렉터리의 파일을 나열 `/tmp/sapinst_instdir/` 합니다.

    1. 설치 구성 파일이 있는지 확인 `inifile.params` 하십시오.

    1. 파일이 있는 경우 `lastInstallationLocation` 파일을 엽니다. 파일 내용에 나열 된 디렉터리를 확인 합니다.

    1. 설치 중인 제품에 대 한 디렉터리 (예:)가 이미 있는 경우 `S4HANA2020` 일치 하는 폴더로 이동 합니다. 예들 들어 `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/HA/ABAP/DB/`입니다.

1. SWPM을 다시 엽니다.

1. **취소** 를 선택합니다. 이제 데이터베이스 콘텐츠를 로드 하는 데 무인 메서드를 사용할 수 있습니다.

1. 다음과 같이 설치 구성 파일을 복사 하 여 이름을 바꿉니다. `<path_to_config_file>`구성 파일의 경로로 대체 합니다.

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/db.inifile.params
    ```

1. `sapinst`SWPM에서 도구의 버전을 확인 합니다.
    
    ```bash
    /usr/sap/install/SWPM/sapinst -version
    ```

1. 버전이 `sapinst` 보다 큰 경우에 `749.0.6` 도 파일을 복사 하 `keydb.xml` 고 `instkey.pkey` [SAP Note 2393060](https://launchpad.support.sap.com/#/notes/2393060)을 따릅니다. `<path_to_config_file>`구성 파일의 경로로 대체 합니다.
    
    ```bash
    cp <path_to_config_file>/{keydb.xml,instkey.pkey} /tmp/app_template/
    ```

## <a name="generate-pas-parameter-file"></a>PAS 매개 변수 파일 생성

PAS에서 사용할 무인 설치 매개 변수 파일을 생성 합니다. 이러한 파일은 모두로 시작 `inifile` 합니다. 

> [!IMPORTANT]
> 이러한 설정 중 일부는 2020 버전의 SAP 제품에서 표시 되지 않을 수 있습니다. 이 경우 단계를 건너뜁니다.

1. CLI를 통해 VM에 커넥트 합니다.

1. [필요한 모든 미디어와 도구가 VM에 설치](#check-media-and-tools)되어 있는지 확인 합니다.

1. 를 만들고 임시 디렉터리를 변경 합니다. `<SID>`을 SID로 바꿉니다.

    ```bash
    sudo install -d -m 0777 <SID>adm -g sapinst "/tmp/pas_workdir"; cd $_
    ```
    
1. 루트 사용자로 노드에 커넥트 합니다. 

1. SWPM에 로그인 합니다. 

    1. SWPM GUI의 URL로 이동 합니다. [ASCS에 대 한 무인 설치 파일을 생성할](#generate-ascs-parameter-file)때이 URL을 얻었습니다.

    1. 보안 경고에 동의 합니다.
    
    1. 시스템의 루트 사용자 자격 증명을 사용 하 여 인증 합니다.

1. 드롭다운 메뉴에서 **SAP S/4hana Server 2020** &gt; **SAP HANA 데이터베이스** &gt; **설치** &gt; **응용 프로그램 서버 ABAP** &gt; **분산 시스템** &gt; **주 응용 프로그램 서버 인스턴스로** 이동 합니다.

1. **매개 변수 설정** 에서 **사용자 지정** 을 선택 합니다. 그다음에 **다음** 을 선택합니다.

1. **프로필 디렉터리가** 또는으로 설정 되어 있는지 확인 `/sapmnt/<SID>/profile/` 합니다 `/usr/sap/<SID>/SYS/profile` . 여기서 `<SID>` 은 SID입니다. 그다음에 **다음** 을 선택합니다.

1. **메시지 서버 포트** 를로 설정 합니다 `36<instance-number>` `<instance-number>` . 여기서은 ascs 인스턴스 번호입니다. 예들 들어 `3600`입니다. 그다음에 **다음** 을 선택합니다.

1. 모든 사용자의 기본 암호를 설정합니다. 그다음에 **다음** 을 선택합니다.

1. 목록이 `below-the-fold-list` 채워지도록 기다립니다. 그다음에 **다음** 을 선택합니다.

1. **SAP 호스트 에이전트를 제공된 SAPHOSTAGENT 버전으로 업그레이드 설정을 사용하지 않도록 설정해야 합니다. SAR 보관**. 그다음에 **다음** 을 선택합니다.

1. SAP HANA 데이터베이스의 인스턴스 번호와 데이터베이스 시스템 관리자 암호를 입력합니다. 그다음에 **다음** 을 선택합니다.

1. **SAP HANA SAP liveCache 구성에서** **다음을** 선택합니다.

1. 의 **데이터베이스 스키마에서** `DBACOCKPIT` **다음을** 선택합니다.

1. 의 **데이터베이스 스키마에서** `SAPHANADB` **다음을** 선택합니다.

1. **데이터베이스 연결에 대한 보안 Storage** **에서 다음을** 선택합니다.

1. PAS 인스턴스 번호 및 인스턴스 호스트가 올바른지 확인합니다. 그다음에 **다음** 을 선택합니다.

1. **메시지 서버 포트 ABAP** 에서 **다음을** 선택합니다.

1. **작업 프로세스 구성에서** **다음을** 선택합니다.

1. **SAP Web Dispatcher에 대한 ICM 사용자 관리에서** **다음을** 선택합니다.

1. **SAP 시스템 OS 수준의 SLD 대상에서** 다음 설정을 구성합니다.

    1. **SLD 대상 없음을** 사용하도록 설정합니다. 그다음에 **다음** 을 선택합니다.

    1. 메시지 **서버 Access Control 목록 을 만들지 않음을** 사용하도록 설정합니다. 그런 다음, **다음을 선택합니다.**

    1. **TMS 실행을** 사용하도록 설정합니다. 

    1. **TMSADM** int Client **000의** 사용자 암호를 기본 암호로 설정합니다. 그다음에 **다음** 을 선택합니다.

    1. **가져오기 ABAP 전송에 대해 아니요를** 사용하도록 설정합니다. 그다음에 **다음** 을 선택합니다.

1. **추가 SAP 시스템 언어에서 다음을** 선택합니다.  

1. **SAP 시스템 DDIC 사용자** 에서 **다음을** 선택합니다.

1. **보안 Storage 키 생성에서** 개별 **키를** 선택해야 합니다. 그다음에 **다음** 을 선택합니다.

1. 경고 화면에서 다음을 수행합니다.

    1. 키 식별자 및 키 값을 복사합니다. 
    
    1. 키 식별자와 키 값을 안전하게 저장합니다.

    1. **다음** 을 선택합니다. 

1. **운영 체제 사용자 정리에서** **예를** 선택합니다. 그다음에 **다음** 을 선택합니다.

1. CLI에서 설치할 임시 디렉터리를 엽니다. 

1. 매개 변수 파일 의 복사본이 있는지 `inifile.params` 확인합니다. 예들 들어 `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/DISTRIBUTED/ABAP/APP1/inifile.params`입니다.

1. SWPM에서 **취소를** 선택합니다. 이제 무인 메서드를 통해 PAS를 설치할 수 있습니다.

1. 다음과 같이 PAS 매개 변수 파일을 복사하고 의 이름을 로 `pas.inifile.params` `/tmp/app_template` 바꿉니다. `<path_to_config_file>`를 매개 변수 파일의 경로로 대체합니다. 

    ```bash
    cp <path_to_config_file>/inifile.params /tmp/app_template/pas.inifile.params
    ```

1. 복사본을 만들고 `pas.inifile.params` 컴퓨터 또는 VM에 다운로드합니다.

## <a name="generate-additional-application-servers-parameter-file"></a>추가 애플리케이션 서버 매개 변수 파일 생성

AAS와 함께 사용할 무인 설치 매개 변수 파일을 생성합니다. 이러한 파일은 모두 로 `inifile` 시작합니다. 

> [!IMPORTANT]
> 2020년 버전의 SAP 제품에서는 이러한 설정 중 일부가 표시되지 않을 수 있습니다. 이 경우 단계를 건너뜁니다.

1. CLI를 통해 AAS VM에 커넥트.

1. [VM에 필요한 모든 미디어 및 도구가 설치되어 있는지 확인합니다.](#check-media-and-tools)

1. 그룹이 있는지 `sapinst` 확인합니다.
    
    ```bash
    groupadd -g 2000 sapinst
    ```

1. 다음과 같이 설치를 위한 임시 디렉터리를 만듭니다. `<sid>`를 SID로 대체합니다.

    ```bash
    sudo install -d -m 0777 <sid>adm -g sapinst "/tmp/aas_workdir"; cd $_
    ```

1. SWPM에 로그인합니다. 

    1. SWPM GUI의 URL로 이동합니다. [ASCS에 대한 무인 설치 파일을 생성할](#generate-ascs-parameter-file)때 이 URL을 받았습니다.

    1. 보안 경고를 수락합니다.
    
    1. 시스템의 루트 사용자 자격 증명으로 인증합니다.

1. 드롭다운 메뉴에서 SAP **S/4HANA Server 2020** &gt; **SAP HANA 데이터베이스** &gt; **설치** 애플리케이션 &gt; 서버는 고가용성 시스템 추가 애플리케이션 서버  &gt;  &gt; **인스턴스를** ABAP.

1. **매개 변수 설정** 에서 **사용자 지정** 을 선택합니다. 그다음에 **다음** 을 선택합니다.

1. **프로필 디렉터리를** 또는 로 설정했는지 확인합니다. `/sapmnt/<SID>/profile/` 여기서 `/usr/sap/<SID>/SYS/profile` `<SID>` 은 SID입니다. 그다음에 **다음** 을 선택합니다.

1. **메시지 서버 포트를** `36<instance-number>` 로 설정합니다. 여기서 `<instance-number>` 은 ASCS 인스턴스 번호입니다. 그다음에 **다음** 을 선택합니다.

1. 모든 사용자의 기본 암호를 설정합니다. 그다음에 **다음** 을 선택합니다.

1. **소프트웨어 패키지 브라우저에서** **Search 디렉터리를** 로 `/usr/sap/install/download_basket` 설정합니다. 그다음에 **다음** 을 선택합니다.

1. 목록이 `below-the-fold-list` 채워지도록 기다립니다. 그다음에 **다음** 을 선택합니다.

1. SAP 호스트 **에이전트를 제공된 SAPHOSTAGENT 버전으로 업그레이드를 사용하도록 설정해야 합니다. SAR 보관**. 그다음에 **다음** 을 선택합니다.

1. SAP HANA 데이터베이스의 인스턴스 번호와 데이터베이스 시스템 관리자 암호를 입력합니다. 그다음에 **다음** 을 선택합니다.

1. **SAP HANA SAP liveCache 구성에서** **다음을** 선택합니다.

1. 의 **데이터베이스 스키마에서** `DBACOCKPIT` **다음을** 선택합니다.

1. 의 **데이터베이스 스키마에서** `SAPHANADB` **다음을** 선택합니다.

1. **데이터베이스 연결에 대한 보안 Storage** **에서 다음을** 선택합니다.

1. AAS 인스턴스 번호와 인스턴스 호스트가 올바른지 확인합니다. 그다음에 **다음** 을 선택합니다.

1. **메시지 서버 포트 ABAP** 에서 **다음을** 선택합니다.

1. **작업 프로세스 구성에서** **다음을** 선택합니다.

1. **SAP Web Dispatcher에 대한 ICM 사용자 관리에서** **다음을** 선택합니다.

1. **SAP 시스템 OS 수준의 SLD 대상 에서** **SLD 대상 없음** 을 사용하도록 설정해야 합니다. 그다음에 **다음** 을 선택합니다.

1. 메시지 **서버 Access Control 목록 을 만들지 않음을** 사용하도록 설정합니다. 그다음에 **다음** 을 선택합니다.

1. **TMS 실행을** 사용하도록 설정합니다.

1. **클라이언트 000의** 사용자 **TMSADM에** 대한 암호를 기본 암호로 설정합니다. 그다음에 **다음** 을 선택합니다.

1. **SPAM/BC 업데이트 보관** 파일을 `/usr/sap/install/config/KD75371.SAR` 로 설정합니다.

1. **가져오기 ABAP 전송을** **아니요로** 설정합니다. 그다음에 **다음** 을 선택합니다.

1. **소프트웨어 업데이트 관리자 화면 준비에서** **SUM 추출*을 사용하도록 설정합니다. SAR 보관**. 그다음에 **다음** 을 선택합니다.

1. **소프트웨어 패키지 브라우저에서** **검색된 패키지** 테이블을 선택합니다. **SUM 2.0의** 개별 패키지 위치가 비어 있는 경우 패키지 경로를 로 `usr/sap/install/config` 설정합니다. 그다음에 **다음** 을 선택합니다.

1. 패키지 위치가 채워지도록 기다립니다. 그다음에 **다음** 을 선택합니다.

1. **추가 SAP 시스템 언어에서 다음을** 선택합니다. 

1. 예를 사용하도록 설정해야 **합니다. 운영 체제 사용자를 정리합니다.** 그다음에 **다음** 을 선택합니다.

1. CLI를 통해 임시 디렉터리에 매개 변수 파일의 복사본이 있는지 확인합니다. 예들 들어 `/tmp/sapinst_instdir/S4HANA2020/CORE/HDB/INSTALL/AS/APPS/inifile.params`입니다.

1. 다음과 같이 파일을 복사하고 의 이름을 로 `aas.inifile.params` `/tmp/app_template` 바꿉니다. `<path_to_inifile>`를 매개 변수 파일의 경로로 대체합니다.

    ```bash
    cp <path_to_inifile>/inifile.params /tmp/app_template/aas.inifile.params
    ```

1. 복사본을 만들고 `aas.inifile.params` 컴퓨터 또는 VM에 다운로드합니다.

1. SWPM에서 **취소를** 선택합니다. 이제 무인 메서드를 통해 AAS 설치를 수행할 수 있습니다.

## <a name="combine-parameter-files"></a>매개 변수 파일 결합

로 끝나는 매개 변수 파일을 `inifile.params` 설치 프로세스를 위한 하나의 파일로 결합할 수 있습니다. 

### <a name="create-combination-file"></a>조합 파일 만들기

모든 매개 변수를 결합하는 파일을 만들려면 다음을 수행합니다.

1. 아직 다운로드하지 않은 경우 만든 각 매개 변수 파일(ASCS, PAS 및 AAS)을 다운로드합니다. 작업 중인 컴퓨터 또는 VM에 이러한 파일이 필요합니다.

1. 각 매개 변수 파일의 백업을 만듭니다.

1. 새 조합 파일을 만듭니다. 사용 하는 SAP 제품에 대 한이 파일의 이름을 지정 합니다. 예들 들어 `S4HANA_2020_ISS_v001.inifile.params`입니다.

1. 편집기에서 ASCS 매개 변수 파일( `scs.inifile.params` )을 엽니다.

1. ASCS 매개 변수 파일의 헤더를 조합 파일에 복사합니다. 예를 들어 다음과 같습니다.

    ```yml
    #########################################################################################################################
    #                                                                                                                       #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                      #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP' #
    #                                                                                                                       #
    #########################################################################################################################
    ```

1. 가지고 있는 각 파일에 대해 `inifile.params` 헤더에서 제품 식별자 줄을 복사합니다. 그런 다음, 제품 식별자를 조합 파일의 헤더에 복사합니다. 예를 들어 다음과 같습니다.

    ```yml
    #############################################################################################################################################
    #                                                                                                                                           #
    # Installation service 'SAP S/4HANA Server 2020 > SAP HANA Database > Installation                                                          #
    #   > Application Server ABAP > Distributed System > ASCS Instance', product id 'NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP'                     #
    #   > Application Server ABAP > Distributed System > Database Instance', product id 'NW_ABAP_DB:S4HANA2020.CORE.HDB.ABAP'                   #
    #   > Application Server ABAP > Distributed System > Primary Application Server Instance', product id 'NW_ABAP_CI:S4HANA2020.CORE.HDB.ABAP' #
    #   > Additional SAP System Instances > Additional Application Server Instance', product id 'NW_DI:S4HANA2020.CORE.HDB.PD'                  #
    #                                                                                                                                           #
    #############################################################################################################################################
    ```

1. `bom.yml`편집기에서 파일을 엽니다.

1. 의 섹션을 `product_ids` 조합 파일에 복사합니다.

1. 있는 각 파일에 대해 `inifile.params` 헤더의 제품 식별자를 의 적절한 부분에 `product_ids` 복사합니다. 예를 들어 ASCS를 에 복사합니다. `scs`

    ```yml
    product_ids:
      scs: "NW_ABAP_ASCS:S4HANA2020.CORE.HDB.ABAP"
      db:  ""
      pas: ""
      aas: ""
      web: ""
    ```

1. 주석으로 달거나 비워 두는 줄을 제거합니다.

1. 조합 파일을 저장합니다.

### <a name="improve-readability"></a>가독성 향상

다음으로, [조합 파일](#create-combination-file)의 가독성을 향상시킵니다.

1. 편집기에서 조합 파일을 엽니다.

1. 머리글에 없는 모든 줄을 정렬합니다.

1. 중복된 줄을 제거합니다.

1. 같음 기호를 모두 맞춥니다. 예를 들어 다음과 같습니다.

    ```yml
    archives.downloadBasket                             = /usr/sap/install/download_basket
    HDB_Schema_Check_Dialogs.schemaName                 = SAPHANADB
    HDB_Schema_Check_Dialogs.schemaPassword             = MyDefaultPassw0rd
    HDB_Userstore.doNotResolveHostnames                 = x00dx0000l09d4
    ```

1. 줄을 접두사로 구분합니다. 예를 들어 `NW_CI_Instance.*` 및 `NW_HDB_DB.*`를 지정합니다.

1. Ansible 변수를 사용하도록 다음 줄을 업데이트합니다.

    1. `archives.downloadBasket                             = {{ download_basket_dir }}`

    1. `HDB_Schema_Check_Dialogs.schemaPassword             = {{ main_password }}`
    
    1. `HDB_Userstore.doNotResolveHostnames                 = {{ hdb_hostname }}`
    
    1. `hostAgent.sapAdmPassword                            = {{ main_password }}`
    
    1. `NW_AS.instanceNumber                                = {{ aas_instance_number }}`
    
    1. `NW_checkMsgServer.abapMSPort                        = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ascsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `NW_CI_Instance.ciInstanceNumber                     = {{ pas_instance_number }}`
    
    1. `NW_CI_Instance.ciMSPort                             = 36{{ scs_instance_number }}`
    
    1. `NW_CI_Instance.ciVirtualHostname                    = {{ pas_hostname }}`
    
    1. `NW_CI_Instance.scsVirtualHostname                   = {{ scs_hostname }}`
    
    1. `NW_DI_Instance.virtualHostname                      = {{ aas_hostname }}`
    
    1. `NW_getFQDN.FQDN                                     = {{ sap_fqdn }}`
    
    1. `NW_GetMasterPassword.masterPwd                      = {{ main_password }}`
    
    1. `NW_GetSidNoProfiles.sid                             = {{ app_sid | upper }}`
    
    1. `NW_HDB_DB.abapSchemaPassword                        = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.dbhost                             = {{ hdb_hostname }}`
    
    1. `NW_HDB_getDBInfo.dbsid                              = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.instanceNumber                     = {{ hdb_instance_number }}`
    
    1. `NW_HDB_getDBInfo.systemDbPassword                   = {{ main_password }}`
    
    1. `NW_HDB_getDBInfo.systemid                           = {{ hdb_sid | upper }}`
    
    1. `NW_HDB_getDBInfo.systemPassword                     = {{ main_password }}`
    
    1. `NW_readProfileDir.profileDir                        = /usr/sap/{{ app_sid | upper }}/SYS/profile`
    
    1. `NW_Recovery_Install_HDB.extractLocation             = /usr/sap/{{ hdb_sid | upper }}/HDB{{ hdb_instance_number }}/backup/data/DB_{{ hdb_sid | upper }}`
    
    1. `NW_Recovery_Install_HDB.sidAdmName                  = {{ hdb_sid | lower }}adm`
    
    1. `NW_Recovery_Install_HDB.sidAdmPassword              = {{ main_password }}`
    
    1. `NW_SAPCrypto.SAPCryptoFile                          = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.instanceNumber                      = {{ scs_instance_number }}`
    
    1. `NW_Unpack.igsExeSar                                 = {{ download_basket_dir }}/igsexe_12-80003187.sar`
    
    1. `NW_Unpack.igsHelperSar                              = {{ download_basket_dir }}/igshelper_17-10010245.sar`
    
    1. `NW_Unpack.sapExeDbSar                               = {{ download_basket_dir }}/SAPEXEDB_300-80004392.SAR`
    
    1. `NW_Unpack.sapExeSar                                 = {{ download_basket_dir }}/SAPEXE_300-80004393.SAR`
    
    1. `NW_SCS_Instance.scsVirtualHostname                  = {{ scs_hostname }}`
    
    1. `nwUsers.sapadmUID                                   = {{ sapadm_uid }}`
    
    1. `nwUsers.sapsysGID                                   = {{ sapsys_gid }}`
    
    1. `nwUsers.sidadmPassword                              = {{ main_password }}`
    
    1. `nwUsers.sidAdmUID                                   = {{ sidadm_uid }}`
    
    1. `storageBasedCopy.hdb.instanceNumber                 = {{ hdb_instance_number }}`
    
    1. `storageBasedCopy.hdb.systemPassword                 = {{ main_password }}`

### <a name="upload-combination-file"></a>업로드 조합 파일

마지막으로 결합된 템플릿 파일을 SAP 라이브러리에 업로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Storage 계정을 선택하거나 **검색합니다.**

1. SAP 라이브러리에 대한 스토리지 계정을 선택합니다.

1. 스토리지 계정 메뉴의 **데이터 스토리지** 아래에서 **컨테이너를 선택합니다.**

1. `sapbits`컨테이너를 선택합니다.

1. 에서 BOM의 제품 폴더로 `sapbits` 이동합니다. 예들 들어 `boms/S4HANA_2020_ISS_v001`입니다.

1. **templates라는** 디렉터리 가 없는 경우 이 디렉터리를 만듭니다.

1. **템플릿** 디렉터리를 엽니다.

1. **업로드** 를 선택합니다.

1. 창에서 **파일 선택을 선택합니다.**

1. 결합된 템플릿 파일을 선택합니다.  예들 들어 `S4HANA_2020_ISS_v001.inifile.params`입니다.

1. **업로드** 를 선택합니다.

## <a name="update-bom-with-templates"></a>템플릿으로 BOM 업데이트

[매개 변수 파일을 결합한](#combine-parameter-files)후 BOM을 새 템플릿 파일로 업데이트합니다.

1. `bom.yml`를 엽니다.

1. 섹션에서 `templates` 새 템플릿 파일 이름을 추가합니다.  예를 들어 다음과 같습니다.

    ```yml
    templates:
      - name:     "S4HANA_2020_ISS_v001 ini file"
        file:     S4HANA_2020_ISS_v001.inifile.params
        override_target_location: "{{ target_media_location }}/config"
    ```

1. 스크립팅된 애플리케이션 BOM 준비를 사용하는 경우 `#` 템플릿 앞에 를 제거합니다.

1. 변경 내용을 저장합니다.

그런 다음, SAP 라이브러리에 새 BOM 파일을 업로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Storage 계정을 선택하거나 **검색합니다.**

1. SAP 라이브러리에 대한 스토리지 계정을 선택합니다.

1. 스토리지 계정 메뉴의 **데이터 스토리지** 아래에서 **컨테이너를 선택합니다.**

1. `sapbits`컨테이너를 선택합니다.

1. 에서 BOM의 제품 폴더로 `sapbits` 이동합니다. 예들 들어 `boms/S4HANA_2020_ISS_v001`입니다.

1. `boms`디렉터리를 엽니다.

1. **업로드** 를 선택합니다.

1. 창에서 **파일 선택을 선택합니다.**

1. 컴퓨터 또는 VM에서 BOM 파일 `bom.yml` 를 선택합니다.

1. 파일이 이미 있는 **경우 덮어쓰기를** 사용하도록 설정해야 합니다.

1. **업로드** 를 선택합니다.

