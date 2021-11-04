---
title: Azure Government에서 Azure Migrate 어플라이언스 설정
description: Azure Government에서 Azure Migrate 어플라이언스를 설정하는 방법 알아보기
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 9586685bc5fd482bf3e87dcfd6848a08c74dfc4f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509674"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>Azure Government 클라우드에서 어플라이언스 설정

이 문서를 따라 수행할 Azure Government 클라우드에 대한 [Azure Migrate 어플라이언스](./migrate-appliance-architecture.md)를 배포합니다.

- VMware 환경에서 실행되는 서버 검색, 평가 및 에이전트 없는 복제
- Hyper-V 환경에서 실행되는 서버 검색 및 평가
- AWS, GCP, Xen 등의 다른 클라우드에서 실행되는 실제 서버 또는 서버 검색 및 평가

퍼블릭 클라우드에서 어플라이언스를 설정하려면 [이 문서](deploy-appliance-script.md)를 따릅니다.

> [!NOTE]
> 템플릿을 사용하여 어플라이언스를 배포하는 옵션(VMware 또는 Hyper-V 환경에서 실행되는 서버의 경우)은 Azure Government에서 지원되지 않습니다. 설치 관리자 스크립트도 사용해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

스크립트를 사용하여 기존 물리적 또는 가상화된 서버에 Azure Migrate 어플라이언스를 배포할 수 있습니다.

- 어플라이언스 역할을 하는 서버는 Windows Server 2016에서 실행되어야 하며 [VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v) 및 [실제 서버](migrate-appliance.md#appliance---physical)에 대한 다른 요구 사항을 충족해야 합니다.
- Azure Migrate 어플라이언스가 이미 설정된 서버에서 스크립트를 실행하는 경우 기존 구성을 정리하고 원하는 구성의 새 어플라이언스를 설정하도록 선택할 수 있습니다. 스크립트를 실행하면 아래와 같은 알림이 표시됩니다.
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="어플라이언스를 다시 구성하는 방법을 보여 주는 스크린샷":::

## <a name="set-up-the-appliance-for-vmware"></a>VMware용 어플라이언스 설정

1. 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 AzureMigrateInstaller.zip이라는 Zip 파일을 다운로드합니다.
1. 어플라이언스를 배포하려는 서버에서 콘텐츠를 추출합니다.
1. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
1. 어플라이언스를 설정하고 처음으로 구성합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다.
3. 포털에서 어플라이언스 이름을 제공하고, 프로젝트 키를 생성합니다.
3. **다운로드** 를 클릭하여 압축 파일을 다운로드합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029


### <a name="run-the-script"></a>스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.

2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.

3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.

4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure Government 클라우드** 에서 **기본 _(퍼블릭 엔드포인트)_ 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 **VMware 환경에서 실행되는 서버** 를 검색, 평가 및 마이그레이션하도록 어플라이언스가 설정됩니다.

   :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="Vmware에 대해 원하는 구성으로 어플라이언스를 설정하는 방법을 보여 주는 스크린샷" lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. 설치 프로그램 스크립트는 다음을 수행합니다.

   - 에이전트 및 웹 애플리케이션을 설치합니다.
   - Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
   - IIS 재작성 모듈을 다운로드하여 설치합니다.
   - Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
   - 지정된 경로에 다음 파일을 만듭니다.
     - **구성 파일**: %Programdata%\Microsoft Azure\Config
     - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V용 어플라이언스 설정

1. 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 AzureMigrateInstaller.zip이라는 Zip 파일을 다운로드합니다.
1. 어플라이언스를 배포하려는 서버에서 콘텐츠를 추출합니다.
1. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
1. 어플라이언스를 설정하고 처음으로 구성합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2.  **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **예, Hyper-V 사용** 을 선택합니다.
3. 포털에서 어플라이언스 이름을 제공하고, 프로젝트 키를 생성합니다.
3. **다운로드** 를 클릭하여 압축 파일을 다운로드합니다. 

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140424) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="run-the-script"></a>스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.

2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.

3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.

4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure Government 클라우드** 에서 **기본 _(퍼블릭 엔드포인트)_ 연결** 을 사용하는 Azure Migrate 프로젝트에 대해 **Hyper-V 환경에서 실행되는 서버** 를 검색 및 평가하도록 어플라이언스가 설정됩니다.

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="Hyper-V에 대해 원하는 구성으로 어플라이언스를 설정하는 방법을 보여 주는 스크린샷" lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. 설치 프로그램 스크립트는 다음을 수행합니다.

    - 에이전트 및 웹 애플리케이션을 설치합니다.
    - Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
    - IIS 재작성 모듈을 다운로드하여 설치합니다.
    - Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
    - 지정된 경로에 다음 파일을 만듭니다.
    - **구성 파일**: %Programdata%\Microsoft Azure\Config
    - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 성공적으로 실행하면 어플라이언스 구성 관리자가 자동으로 시작됩니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.


## <a name="set-up-the-appliance-for-physical-servers"></a>물리적 서버용 어플라이언스 설정

1. 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 AzureMigrateInstaller.zip이라는 압축된 파일을 다운로드합니다.
1. 어플라이언스를 배포하려는 서버에서 콘텐츠를 추출합니다.
1. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
1. 어플라이언스를 설정하고 처음으로 구성합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **물리적 또는 기타(AWS, GCP, Xen 등)** 를 선택합니다.
3. **다운로드** 를 클릭하여 압축 파일을 다운로드합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140338) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 동일한 스크립트를 사용하여 퍼블릭 또는 프라이빗 엔드포인트 연결로 Azure Government 클라우드용 물리적 어플라이언스를 설정할 수 있습니다.

### <a name="run-the-script"></a>스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.

2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.

3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.

4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

    `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure Government 클라우드** 에서 **기본 _(퍼블릭 엔드포인트)_ 연결** 이 있는 Azure Migrate 프로젝트에 대한 **실제 서버** _(또는 AWS, GCP, Xen 등의 다른 클라우드에서 실행되는 서버)_ 를 검색하고 평가하도록 어플라이언스가 설정됩니다.

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="실제 서버에 대해 원하는 구성으로 어플라이언스를 설정하는 방법을 보여 주는 스크린샷" lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

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

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성해야 하며 프로젝트에 등록해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)용 어플라이언스를 설정합니다.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)용 어플라이언스를 설정합니다.
- [물리적 서버](how-to-set-up-appliance-physical.md)용 어플라이언스를 설정합니다.
