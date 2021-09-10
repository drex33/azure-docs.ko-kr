---
title: 에이전트 없는 VMware을 마이그레이션하기 위한 Azure Migrate 스케일 아웃 어플라이언스 설정
description: Hyper-V VM을 마이그레이션하기 위해 Azure Migrate 스케일 아웃 어플라이언스를 설정하는 방법에 대해 알아봅니다.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5c8858e50707209b47eb61d554a8e4f7313c92c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566526"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Azure로 에이전트 없는 VMware 가상 머신의 마이그레이션 확장

이 문서는 VMware VM을 마이그레이션하기 위한 Azure Migrate 서버 마이그레이션 도구의 에이전트 없는 방법을 통해 스케일 아웃 어플라이언스를 사용하여 많은 수의 VMware VM(가상 머신)을 Azure로 마이그레이션하는 방법을 이해하는 데 도움을 줍니다.

에이전트 없는 VMware 가상 머신 마이그레이션 방법을 사용하면 다음을 수행할 수 있습니다.

- 하나의 Azure Migrate 어플라이언스를 사용해 단일 vCenter 서버에서 동시에 최대 300대의 VM을 복제합니다.
- 두 번째 마이그레이션용 스케일 아웃 어플라이언스를 배포하여 단일 vCenter 서버에서 동시에 최대 500대의 VM을 복제합니다.

이 아티클에서는 다음 방법을 설명합니다.

- 에이전트 없는 VMware 가상 머신 마이그레이션용 스케일 아웃 어플라이언스 추가
- 스케일 아웃 어플라이언스를 사용하여 동시에 최대 500대의 VM을 마이그레이션합니다.

##  <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 단계를 수행해야 합니다.

- Azure Migrate 프로젝트 만들기
- Azure Migrate 어플라이언스(기본 어플라이언스)를 배포하고 vCenter 서버에서 관리하는 VMware 가상 머신의 검색을 완료합니다.
- 마이그레이션할 한 대 이상의 가상 머신에 대한 복제를 구성합니다.
> [!IMPORTANT]
> 마이그레이션용 스케일 아웃 어플라이스를 추가하려면 프로젝트에 복제 가상 머신이 한 대 이상 있어야 합니다.

위의 단계를 수행하는 방법에 대해 알아보려면 [에이전트 없는 마이그레이션 방법으로 VMware 가상 머신을 Azure로 마이그레이션](./tutorial-migrate-vmware.md)자습서를 검토하세요.

## <a name="deploy-a-scale-out-appliance"></a>스케일 아웃 어플라이언스 배포

스케일 아웃 어플라이언스를 추가하려면 아래에 설명된 단계를 수행합니다.

1. **검색** > **컴퓨터가 가상화되었습니까?** 를 클릭합니다. 
1. **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다.
1. 다음 단계에서 에이전트 없는 복제를 선택합니다.
1. 어플라이언스 유형 선택 메뉴에서 **기존 기본 어플라이언스 스케일 아웃** 을 선택합니다.
1. 스케일 아웃하려는 기본 어플라이언스(검색을 수행한 어플라이언스)를 선택합니다.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="스케일 아웃 온보딩용 컴퓨터 검색 페이지":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure Migrate 프로젝트 키 생성

1. **Azure Migrate 프로젝트 키 생성** 에서 스케일 아웃 어플라이언스용 접미사 이름을 제공합니다. 접미사는 영숫자 문자만 포함할 수 있으며 길이는 14자로 제한됩니다.
2. **키 생성** 을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [검색] 페이지를 닫지 마세요.
3. 생성된 키를 복사합니다. 스케일 아웃 어플라이언스 등록을 완료하려면 나중에 키가 필요합니다.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. 스케일 아웃 어플라이언스용 설치 관리자 다운로드

**Azure Migrate 어플라이언스 다운로드** 에서 **다운로드** 를 클릭합니다. PowerShell 설치 관리자 스크립트를 다운로드하여 필수 하드웨어 구성(32GB RAM, vCPU 8개, 약 80GB의 디스크 저장소 및 직접 또는 프록시를 통한 인터넷 액세스)으로 Windows Server 2016을 실행하는 기존 서버에 스케일 아웃 어플라이언스를 배포해야 합니다.

:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="스케일 아웃 어플라이언스용 스크립트 다운로드":::

> [!TIP]
> 다음 단계를 사용하여 다운로드한 zip 파일에 대한 체크섬의 유효성을 검사할 수 있습니다.
>
> 1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
> 2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. 컴퓨팅된 해시 값이 b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2 문자열과 일치하지 않는 경우 포털에서 최신 버전의 스케일 아웃 어플라이언스 설치 관리자를 다운로드합니다.

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure Migrate 설치 프로그램 스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 시나리오, 클라우드, 구성, 연결 옵션 중에서 선택하여 원하는 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure 퍼블릭 클라우드** 에서 **기본(‘퍼블릭 엔드포인트’) 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 VMware 환경에서 실행되는 서버에서 동시 복제를 시작하도록 **스케일 아웃 어플라이언스** 가 설정됩니다.

    :::image type="content" source="./media/how-to-scale-out-for-migration/script-vmware-scaleout-inline.png" alt-text="스케일 아웃 어플라이언스를 설정하는 방법을 보여 주는 스크린샷" lightbox="./media/how-to-scale-out-for-migration/script-vmware-scaleout-expanded.png":::

6. 설치 프로그램 스크립트는 다음을 수행합니다.

    - 동시 서버 복제를 더 많이 수행하도록 게이트웨이 에이전트 및 어플라이언스 구성 관리자를 설치합니다.
    - Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
    - IIS 재작성 모듈을 다운로드하여 설치합니다.
    - Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
    - 지정된 경로에 다음 파일을 만듭니다.
        - **구성 파일**: %Programdata%\Microsoft Azure\Config
        - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

> [!NOTE]
> 문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그에 액세스할 수 있습니다.


### <a name="4-configure-the-appliance"></a>4. 어플라이언스 구성

시작하기 전에 [이러한 Azure 엔드포인트](migrate-appliance.md#public-cloud-urls)를 스케일 아웃 어플라이언스에서 액세스할 수 있는지 확인합니다.

- 스케일 아웃 어플라이언스 서버에 연결할 수 있는 모든 컴퓨터에서 브라우저를 열고, 어플라이언스 구성 관리자의 URL(**https://*스케일 아웃 어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 구성 관리자의 바로 가기를 사용하여 스케일 아웃 어플라이언스 서버의 바탕 화면에서 구성 관리자를 열 수 있습니다.
- **사용 조건** 에 동의하고 타사 정보를 읽습니다.
- 구성 관리자 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
   - **연결**: 어플라이언스가 서버의 인터넷 연결을 확인합니다. 서버에서 프록시를 사용하는 경우:
     1. **프록시 설정** 을 클릭하여 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 대기 포트를 지정합니다.
     2. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     3. HTTP 프록시만 지원됩니다.
     4. 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인할 수 있습니다.
   - **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우 어플라이언스 구성 관리자 화면의 **설치 지침** 에 나와 있는 대로 VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.


### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **Azure Migrate 프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **서버 평가 > 검색 > 기존 어플라이언스 관리** 로 차례로 이동하여 기본 어플라이언스 이름을 선택하고, 이와 연관된 스케일 아웃 어플라이언스를 찾아 해당 키를 복사합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.

   :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="디바이스 코드를 보여주는 모달":::

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 디바이스 코드를 붙여넣고 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 로그인 탭을 실수로 로그인하지 않고 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 로그인 단추를 다시 사용하도록 설정해야 합니다.
1. 로그인한 후 어플라이언스 구성 관리자를 사용하여 이전 탭으로 돌아갑니다.
1. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 권한이 있는 경우 어플라이언스 등록이 시작됩니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="어플라이언스 구성 관리자의 패널 2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>기본 어플라이언스에서 어플라이언스 구성 가져오기

스케일 아웃 어플라이언스 등록을 완료하려면 **가져오기** 를 클릭하여 기본 어플라이언스에서 필요한 구성 파일을 가져옵니다.

1. **가져오기** 를 클릭하면 기본 어플라이언스에서 필요한 구성 파일을 가져오는 방법에 대한 지침이 포함된 팝업 창이 열립니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="구성 가져오기 모달":::
1. 기본 어플라이언스에 로그인(원격 데스크톱)하고 다음 PowerShell 명령을 실행합니다.

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. 위의 명령을 실행하여 만든 zip 파일을 스케일 아웃 어플라이언스로 복사합니다. zip 파일에는 스케일 아웃 어플라이언스를 등록하는 데 필요한 구성 파일이 포함되어 있습니다.
1. 이전 단계에서 열린 팝업 창에서 복사된 구성 zip 파일의 위치를 선택하고 **저장** 을 클릭합니다.

파일을 가져오면 스케일 아웃 어플라이언스의 등록이 완료되고 마지막으로 성공한 가져오기의 타임스탬프가 표시됩니다. 또한 **세부 정보 보기** 를 클릭하여 등록 세부 정보를 확인할 수 있습니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="스크린샷에는 Azure Migrate 프로젝트를 사용하는 스케일 아웃 어플라이언스 등록 과정이 나와 있습니다.":::

이때 스케일 아웃 어플라이언스에서 vCenter 서버에 연결할 수 있는지 유효성을 다시 검사해야 합니다. **유효성 다시 검사** 를 클릭하여 스케일 아웃 어플라이언스에서 vCenter Server 연결의 유효성을 검사합니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="스크린샷에는 유효성을 검사할 보기 자격 증명과 검색 원본이 나와 있습니다.":::

> [!IMPORTANT]
> 기본 어플라이언스에서 vCenter Server 자격 증명을 편집하는 경우, 최신 구성을 가져오고 진행 중인 복제를 계속하려면 구성 파일을 스케일 아웃 어플라이언스로 다시 가져와야 합니다.<br/> 스케일 아웃 어플라이언스가 더 이상 필요하지 않은 경우, 스케일 아웃 어플라이언스를 사용하지 않도록 설정해야 합니다. 필요하지 않은 경우 스케일 아웃 어플라이언스를 사용하지 않도록 설정하는 방법에 대해 [**자세히 알아보세요**](./common-questions-appliance.md).

## <a name="replicate"></a>복제

1. 스케일 아웃 어플라이언스를 등록한 후 Azure Migrate: 서버 마이그레이션 타일에서 **복제** 를 클릭합니다.

2.  화면의 단계를 따라 더 많은 가상 머신의 복제를 시작합니다. 

이제 스케일 아웃 어플라이언스를 사용하여 500대의 VM을 동시에 복제할 수 있습니다. Azure Portal를 통해 200개의 일괄 처리로 VM을 마이그레이션할 수도 있습니다.

Azure Migrate 서버 마이그레이션 도구는 복제 시 기본 어플라이언스와 스케일 아웃 어플라이언스 간에 가상 머신을 배포합니다. 복제가 완료되면 가상 머신을 마이그레이션할 수 있습니다.

> [!TIP]
> 많은 수의 가상 머신을 마이그레이션하려는 경우 성능을 최적화하려면 200개의 일괄 처리로 가상 머신을 마이그레이션하는 것이 좋습니다.
  
## <a name="next-steps"></a>다음 단계

이 아티클에서는 다음에 대해 알아보았습니다.
- 스케일 아웃 어플라이언스를 구성하는 방법
- 스케일 아웃 어플라이언스를 사용하여 VM을 복제하는 방법


Azure Migrate: 서버 마이그레이션 도구를 사용하여 서버를 Azure로 마이그레이션하는 방법에 대한 [자세한 내용](./tutorial-migrate-vmware.md)
