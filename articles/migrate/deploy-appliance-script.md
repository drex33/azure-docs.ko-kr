---
title: 스크립트를 사용하여 Azure Migrate 어플라이언스 설정
description: 스크립트를 사용하여 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
ms.topic: how-to
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: bcd455590e804ad337f25afbd38d729f03ef3dc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567048"
---
# <a name="set-up-an-appliance-with-a-script"></a>스크립트를 사용하여 어플라이언스 설정

다음 문서에 따라 PowerShell 스크립트를 사용하여 [Azure Migrate 어플라이언스](./migrate-appliance-architecture.md)를 배포합니다.
- VMware 환경에서 실행되는 서버 검색, 평가 및 에이전트 없는 복제
- Hyper-V 환경에서 실행되는 서버를 검색하고 평가합니다.

스크립트를 사용하거나 Azure Portal에서 다운로드한 템플릿(OVA/VHD)을 사용하여 VMware 및 Hyper-V에 서버용 어플라이언스를 배포할 수 있습니다. 다운로드한 템플릿을 사용하여 어플라이언스를 만들 수 없는 경우 스크립트를 사용하는 것이 유용할 수 있습니다.

- 템플릿을 사용하려면 [VMware](./tutorial-discover-vmware.md) 및 [Hyper-V](./tutorial-discover-hyper-v.md)에 대한 자습서를 따르세요.
- 물리적 서버용 어플라이언스를 설정하려면 스크립트만 사용할 수 있습니다. [이 문서](how-to-set-up-appliance-physical.md)를 따르세요.
- Azure Government 클라우드에서 어플라이언스를 설정하려면 스크립트만 사용하면 됩니다. [이 문서](deploy-appliance-script-government.md)를 따르세요.

## <a name="prerequisites"></a>필수 구성 요소

스크립트를 사용하여 VMware 또는 Hyper-V 환경의 기존 서버에 Azure Migrate 어플라이언스를 배포할 수 있습니다.

- 어플라이언스를 호스트하는 서버는 다음 하드웨어 및 OS 요구 사항을 충족해야 합니다.

시나리오 | 요구 사항
--- | ---
VMware | 32GB의 메모리, 8개의 vCPU, 약 80GB의 디스크 스토리지가 탑재된 Windows Server 2016
Hyper-V | 16GB 메모리, 8개의 vCPU, 약 80GB의 디스크 스토리지가 탑재된 Windows Server 2016

- 서버에는 외부 가상 스위치도 필요합니다. 고정 또는 동적 IP 주소가 필요합니다. 
- 어플라이언스를 배포하기 전에 [VMware](migrate-appliance.md#appliance---vmware) 및 [Hyper-V](migrate-appliance.md#appliance---hyper-v)에 대한 자세한 어플라이언스 요구 사항을 검토하세요.
- Azure Migrate 어플라이언스가 이미 설정된 서버에서 스크립트를 실행하는 경우 기존 구성을 정리하고 원하는 구성의 새 어플라이언스를 설정하도록 선택할 수 있습니다. 스크립트를 실행하면 아래와 같은 알림이 표시됩니다.

    ![원하는 구성으로 어플라이언스 설정](./media/deploy-appliance-script/script-reconfigure-appliance.png)

## <a name="set-up-the-appliance-for-vmware"></a>VMware용 어플라이언스 설정

1. 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2116601)에서 AzureMigrateInstaller.zip이라는 Zip 파일을 다운로드합니다.
1. 어플라이언스를 배포하려는 서버에서 콘텐츠를 추출합니다.
1. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
1. 어플라이언스를 설정하고 처음으로 구성합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2116601) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> 동일한 스크립트를 사용하여 Azure 공용 클라우드 또는 Azure Government 클라우드용 VMware 어플라이언스를 설정할 수 있습니다.

### <a name="run-the-script"></a>스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure 공용 클라우드** 에서 **기본 _(퍼블릭 엔드포인트)_ 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 **VMware 환경에서 실행되는 서버** 를 검색, 평가 및 마이그레이션하도록 어플라이언스가 설정됩니다.

    :::image type="content" source="./media/deploy-appliance-script/script-vmware-default-inline.png" alt-text="원하는 구성으로 VMware 어플라이언스를 설정하는 방법을 보여주는 스크린샷" lightbox="./media/deploy-appliance-script/script-vmware-default-expanded.png":::

6. 설치 프로그램 스크립트는 다음을 수행합니다.

 - 에이전트 및 웹 애플리케이션을 설치합니다.
 - Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
 - IIS 재작성 모듈을 다운로드하여 설치합니다.
 - Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
 - 지정된 경로에 다음 파일을 만듭니다.
    - **구성 파일**: %Programdata%\Microsoft Azure\Config
    - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

> [!NOTE]
> 문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그에 액세스할 수 있습니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V용 어플라이언스 설정

1. 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2116657)에서 AzureMigrateInstaller.zip이라는 Zip 파일을 다운로드합니다.
1. 어플라이언스를 배포하려는 서버에서 콘텐츠를 추출합니다.
1. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
1. 어플라이언스를 설정하고 처음으로 구성합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2116657) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> 동일한 스크립트를 사용하여 Azure 공용 클라우드 또는 Azure Government 클라우드용 Hyper-V 어플라이언스를 설정할 수 있습니다.

### <a name="run-the-script"></a>스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure 공용 클라우드** 에서 **기본 _(퍼블릭 엔드포인트)_ 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 **Hyper-V 환경에서 실행되는 서버** 를 검색 및 평가하도록 어플라이언스가 설정됩니다.

    :::image type="content" source="./media/deploy-appliance-script/script-hyperv-default-inline.png" alt-text="원하는 구성으로 Hyper-V 어플라이언스를 설정하는 방법을 보여주는 스크린샷" lightbox="./media/deploy-appliance-script/script-hyperv-default-expanded.png":::

6. 설치 프로그램 스크립트는 다음을 수행합니다.

    - 에이전트 및 웹 애플리케이션을 설치합니다.
    - Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
    - IIS 재작성 모듈을 다운로드하여 설치합니다.
    - Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
    - 지정된 경로에 다음 파일을 만듭니다.
        - **구성 파일**: %Programdata%\Microsoft Azure\Config
        - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

> [!NOTE]
> 문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그에 액세스할 수 있습니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성해야 하며 프로젝트에 등록해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)용 어플라이언스를 설정합니다.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)용 어플라이언스를 설정합니다.
