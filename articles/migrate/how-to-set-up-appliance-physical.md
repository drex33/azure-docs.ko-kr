---
title: 물리적 서버용 Azure Migrate 어플라이언스 설정
description: 물리적 서버의 검색 및 평가를 위해 Azure Migrate 어플라이언스를 설정하는 방법에 대해 알아봅니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 7dd11143e3852d17787de5e20ebe53290f5af96f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528491"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>물리적 서버용 어플라이언스 설정

이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용하여 물리적 서버를 평가하는 경우 Azure Migrate 어플라이언스를 설정하는 방법을 설명합니다.

Azure Migrate 어플라이언스는 Azure Migrate: 검색 및 평가에서 다음을 수행하는 데 사용하는 간단한 어플라이언스입니다.

- 온-프레미스 서버를 검색합니다.
- 검색된 서버에 대한 메타데이터 및 성능 데이터를 Azure Migrate: 검색 및 평가에 보냅니다.

Azure Migrate 어플라이언스에 대해 [자세히 알아봅니다](migrate-appliance.md).


## <a name="appliance-deployment-steps"></a>어플라이언스 배포 단계

어플라이언스를 설정하려면 다음을 수행합니다.

1. 포털에서 어플라이언스 이름을 제공하고, 프로젝트 키를 생성합니다.
2. Azure Portal에서 Azure Migrate 설치 프로그램 스크립트가 포함된 압축 파일을 다운로드합니다.
3. 압축 파일의 콘텐츠를 추출합니다. 관리자 권한으로 PowerShell 콘솔을 시작합니다.
4. PowerShell 스크립트를 실행하여 어플라이언스 구성 관리자를 시작합니다.
5. 어플라이언스를 처음으로 구성하고, 프로젝트 키를 사용하여 어플라이언스를 프로젝트에 등록합니다.

### <a name="generate-the-project-key"></a>프로젝트 키 생성

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 선택합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **물리적 또는 기타(AWS, GCP, Xen 등)** 를 선택합니다.
3. **1: 프로젝트 키 생성** 에서 물리적 또는 가상 서버를 검색하도록 설정할 Azure Migrate 어플라이언스의 이름을 입력합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성** 을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [서버 검색] 페이지를 닫지 마세요.
1. Azure 리소스가 성공적으로 만들어지면 **프로젝트 키** 가 생성됩니다.
1. 어플라이언스를 구성하는 동안 어플라이언스 등록을 완료하는 데 필요하므로 키를 복사합니다.

   ![키 생성 선택](./media/tutorial-assess-physical/generate-key-physical-1.png)

### <a name="download-the-installer-script"></a>설치 프로그램 스크립트 다운로드

**2: Azure Migrate 어플라이언스 다운로드** 에서 **다운로드** 를 클릭합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **다운로드** | **해시 값**
    --- | ---
    [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140334) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> 동일한 스크립트를 사용하여 Azure 퍼블릭 또는 Azure Government 클라우드용 물리적 어플라이언스를 설정할 수 있습니다.

### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate 설치 프로그램 스크립트 실행

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1** 이라는 스크립트를 실행합니다.

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. 시나리오, 클라우드, 연결 옵션 중에서 선택하여 원하는 구성으로 어플라이언스를 배포합니다. 예를 들어 아래와 같이 선택하면 **Azure 퍼블릭 클라우드** 에서 **기본 ‘(퍼블릭 엔드포인트)’ 연결** 이 있는 Azure Migrate 프로젝트에 대한 **물리적 서버**‘(또는 AWS, GCP, Xen 등의 다른 클라우드에서 실행되는 서버)’를 검색하고 평가하도록 어플라이언스가 설정됩니다. 

    :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-1.png" alt-text="원하는 구성으로 어플라이언스를 설정하는 방법을 보여주는 스크린샷.":::

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

### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

1. 어플라이언스에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*): 44368**)을 엽니다.

   또는 바탕 화면에서 앱 바로 가기를 클릭하여 앱을 열 수 있습니다.
2. **사용 조건** 에 동의하고 타사 정보를 읽습니다.
1. 웹앱 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
    - **연결**: 앱에서 서버가 인터넷에 액세스할 수 있는지 확인합니다. 서버에서 프록시를 사용하는 경우:
        - **프록시 설정** 을 클릭하여 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 대기 포트를 지정합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
        - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
    - **시간 동기화**: 시간이 확인됩니다. 서버 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate: 검색 및 평가에서 어플라이언스에 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스에서 실행되는 구성 요소의 상태와 버전을 확인할 수 있습니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **Azure Migrate: 검색 및 평가 > 검색 > 기존 어플라이언스 관리** 로 차례로 이동하여 키 생성 시 제공한 어플라이언스 이름을 선택하고, 해당 키를 복사합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.

    ![디바이스 코드를 보여주는 모달](./media/tutorial-discover-vmware/device-code.png)

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 디바이스 코드를 붙여넣고 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 로그인 탭을 실수로 로그인하지 않고 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 로그인 단추를 다시 사용하도록 설정해야 합니다.
1. 성공적으로 로그인한 후 어플라이언스 구성 관리자를 사용하여 이전 탭으로 돌아갑니다.
4. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 [권한](./tutorial-discover-physical.md)이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기** 를 클릭하여 등록 세부 정보를 확인할 수 있습니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

이제 어플라이언스에서 검색할 물리적 서버에 연결하여 검색을 시작합니다.

1. **1단계: Windows 및 Linux 물리적 또는 가상 서버 검색을 위한 자격 증명 제공** 에서 **자격 증명 추가** 를 클릭합니다.
1. Windows 서버의 경우 원본 유형을 **Windows Server** 로 선택하고, 자격 증명에 친숙한 이름을 지정하고, 사용자 이름 및 암호를 추가합니다. **Save** 를 클릭합니다.
1. Linux 서버에 암호 기반 인증을 사용하는 경우 원본 유형을 **Linux Server(암호 기반)** 로 선택하고, 자격 증명에 친숙한 이름을 지정하고, 사용자 이름 및 암호를 추가합니다. **Save** 를 클릭합니다.
1. Linux 서버에 SSH 키 기반 인증을 사용하는 경우 원본 유형을 **Linux Server(SSH 키 기반)** 로 선택하고, 자격 증명에 친숙한 이름을 지정하고, 사용자 이름을 추가하고, SSH 프라이빗 키 파일을 찾아서 선택할 수 있습니다. **Save** 를 클릭합니다.

    - Azure Migrate는 RSA, DSA, ECDSA 및 ed25519 알고리즘을 사용하여 ssh-keygen 명령에 의해 생성된 SSH 프라이빗 키를 지원합니다.
    - 현재 Azure Migrate는 암호 기반 SSH 키를 지원하지 않습니다. 암호 없이 SSH 키를 사용합니다.
    - 현재 Azure Migrate는 PuTTY에서 생성된 SSH 프라이빗 키 파일을 지원하지 않습니다.
    - Azure Migrate는 아래와 같이 SSH 프라이빗 키 파일의 OpenSSH 형식을 지원합니다.
    
    ![SSH 프라이빗 키 지원 형식](./media/tutorial-discover-physical/key-format.png)
1. 여러 자격 증명을 한 번에 추가하려면 **추가** 를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다. 물리적 서버 검색에 여러 자격 증명이 지원됩니다.
1. **2단계: 물리적 또는 가상 서버 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 서버 **IP 주소/FQDN** 을 지정하고 서버에 연결할 자격 증명의 식별 이름을 지정합니다.
1. 한 번에 하나씩 **단일 항목을 추가** 하거나 한꺼번에 **여러 항목을 추가** 할 수 있습니다. 또한 **CSV 가져오기** 를 통해 서버 세부 정보를 제공하는 옵션도 있습니다.

    ![검색 원본을 추가하는 옵션](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - **단일 항목 추가** 를 선택하는 경우 OS 유형을 선택하고, 자격 증명의 식별 이름을 지정하고, 서버 **IP 주소/FQDN** 을 추가하고 **저장** 을 클릭합니다.
    - **여러 항목 추가** 를 선택하는 경우 텍스트 상자에 자격 증명의 식별 이름과 서버 **IP 주소/FQDN** 을 지정하여 여러 레코드를 한 번에 추가할 수 있습니다. 추가된 레코드를 확인**하고 **저장** 을 클릭합니다.
    - **CSV가져오기** 를 선택하는 경우 _(기본적으로 선택됨)_ CSV 템플릿 파일을 다운로드하고 서버 **IP 주소/FQDN** 및 자격 증명 식별 이름으로 파일을 채울 수 있습니다. 그런 다음, 파일을 어플라이언스로 가져와 파일의 레코드를 **확인** 하고 **저장** 을 클릭합니다.

1. 저장을 클릭하면 어플라이언스가 추가된 서버에 대한 연결의 유효성을 검사하고 각 서버에 대한 테이블에 **유효성 검사 상태** 를 표시합니다.
    - 서버에 대한 유효성 검사가 실패하면 테이블의 상태 열에서 **유효성 검사 실패** 를 클릭하여 오류를 검토합니다. 문제를 해결하고, 유효성을 다시 검사합니다.
    - 서버를 제거하려면 **삭제** 를 클릭합니다.
1. 검색을 시작하기 전에 언제든지 서버에 대한 연결의 **유효성을 다시 검사** 할 수 있습니다.
1. **검색 시작** 을 클릭하여 유효성 검사에 성공한 서버의 검색을 시작합니다. 검색이 성공적으로 시작되었으면 테이블의 각 서버에 대한 검색 상태를 확인할 수 있습니다.


그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 서버당 약 2분이 걸립니다.

## <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색이 완료되면 서버가 포털에 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - Windows, Linux 및 SQL Server** > **Azure Migrate: 검색 및 평가** 페이지에서 **검색된 서버** 의 수를 표시하는 아이콘을 클릭합니다.


## <a name="next-steps"></a>다음 단계

Azure Migrate: 검색 및 평가를 사용하여 [물리적 서버 평가](tutorial-assess-physical.md)를 사용해 보세요.
