---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: fa4d3974a016fc57624c2c51d9aaf703583f8764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556929"
---
Data Box 실행 중인 소프트웨어 버전에 따라 디바이스의 PowerShell 인터페이스에 연결하기 위해 다른 단계를 수행해야 할 수 있습니다. 이러한 모든 경우의 첫 번째 단계는 Data Box 실행되는 소프트웨어 버전을 식별하는 것입니다.

### <a name="identify-software-version-of-the-device"></a>디바이스의 소프트웨어 버전 식별

> [!NOTE]
> 버전 번호는 v 4.9 이상부터 로컬 UI에 표시됩니다. 로컬 UI에서 여기에 설명된 대로 버전 번호가 표시되지 않으면 Microsoft 지원 문의하여 버전 번호를 식별한 다음, 단계에 따라 디바이스의 PowerShell 인터페이스에 연결합니다.

다음 방법 중 하나를 사용하여 디바이스에서 실행되는 소프트웨어 버전과 디바이스 일련 번호를 식별합니다.

 - **로그인** 페이지로 이동합니다. 소프트웨어 버전이 이 페이지에 표시됩니다.
 
    ![의 로컬 웹 UI에 있는 로그인 페이지의 일련 번호 및 소프트웨어 버전Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-sign-in-local-ui.png)

 - 로컬 웹 UI에서 **도움말** 아이콘을 선택합니다. **도움말** 창에 소프트웨어 버전 및 디바이스 일련 번호가 표시됩니다. 디바이스 일련 번호와 소프트웨어 버전을 모두 기록해 둡다.
 
    ![의 로컬 웹 UI에 있는 도움말 창의 일련 번호 및 소프트웨어 버전Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-help-pane-local-ui.png)



다음 단계는 Data Box 실행 중인 소프트웨어 버전에 따라 결정됩니다.
 
### <a name="v41-and-later"></a>[v4.1 이상](#tab/c)

이러한 소프트웨어 버전의 경우 먼저 Data Box 액세스하는 호스트에 인증서를 설치한 다음 디바이스의 PowerShell 인터페이스에 연결해야 합니다. 인증서를 설치할 때 디바이스가 배송되는 기본 인증서를 사용하거나 사용자 고유의 인증서를 가져올 수 있습니다. 각 경우에 수행되는 특정 단계는 약간 다릅니다.


### <a name="step-1-install-certificate-on-host-or-client"></a>1단계: 호스트 또는 클라이언트에 인증서 설치

#### <a name="use-default-certificates"></a>기본 인증서 사용 

Data Box 기본 인증서(디바이스와 함께 제공)를 사용하는 경우 다음 단계를 수행합니다.

1. Azure Portal Data Box 주문 리소스로 이동합니다. **일반 &gt; 디바이스 세부 정보** 로 이동합니다. 디바이스 탭에 **대한 API 액세스에서** **다운로드를** 선택합니다.

    ![Azure Portal Data Box 인증서 다운로드](media/data-box-connect-powershell-interface/download-certificate-data-box-portal.png)

1. Data Box 연결하는 호스트에 인증서를 복사합니다. Windows 호스트를 사용하는 경우 탐색기를 엽니다. 호스트에 복사한 인증서 파일을 마우스 오른쪽 단추로 클릭하고 **인증서 설치를** 선택합니다. 

1. **인증서 가져오기 마법사** 를 통해 설치 단계를 완료합니다. 자세한 단계는 [클라이언트로 인증서 가져오기를 참조하세요.](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client) 

1. 디바이스 IP 주소를 디바이스의 FQDN에 매핑하는 호스트 파일에 항목을 추가합니다. 항목의 형식은 다음과 같습니다. 

    `<Device IP address>  <Device serial number>.microsoftdatabox.com`

    이전 단계의 디바이스 일련 번호를 제공합니다. 

    ![로컬 UI에서 Data Box 일련 번호 받기](media/data-box-connect-powershell-interface/get-device-serial-number-portal.png)
 

#### <a name="use-your-own-certificates"></a>사용자 고유의 인증서 사용 

Data Box 자체 인증서를 가져오는 경우 다음 단계를 수행합니다.
 
1. Data Box 연결할 호스트 머신에 가져오는 서명 체인 인증서를 설치합니다. 탐색기를 통해 인증서를 열고 파일을 마우스 오른쪽 단추로 클릭하고 **인증서 설치를** 선택합니다. 
1. [클라이언트에 인증서 가져오기](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client) 및 로컬 컴퓨터/루트에 인증서 설치의 단계를 수행합니다. 
1. 디바이스 IP 주소를 디바이스의 FQDN에 매핑하는 항목을 호스트 파일에 추가합니다. 항목의 형식은 다음과 같습니다. 

    `<Device IP address>  <Name>.<DNS domain>` 

    디바이스의 **이름** 및 **DNS 도메인을** 얻으려면 로컬 UI에서 **인증서** 페이지로 이동합니다. 
1. 인증서를 업로드하려면 로컬 UI에서 **인증서** 페이지로 이동합니다. **+ 인증서 추가를** 선택하고 인증서를 제공합니다. [인증서 업로드 참조하세요.](../articles/databox/data-box-bring-your-own-certificates.md#add-certificates-to-device)
 

### <a name="step-2-connect-to-the-powershell-interface"></a>2단계: PowerShell 인터페이스에 커넥트


1. `$Name`매개 변수를 설정합니다.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
    이전 단계의 디바이스에 대한 **이름** 및 **DNS 도메인을** 제공합니다.

1. `TrustedHosts`매개 변수를 사용하여 목록에 Data Box `$Name` 추가합니다. 유형:
 
    ```powershell
    Set-Item wsman:\localhost\Client\TrustedHosts $Name 
    ```

1. Azure Portal 로컬 UI에 대한 암호를 얻습니다. 암호를 포함하는 보안 문자열을 만듭니다.

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
   <!--

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("<ipv4_address of databox>\StorSimpleAdmin",$Pwd) 
    ```-->

1. 연결을 설정합니다.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL  
    ```
    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\Users\Administrator> winrm quickconfig
    WinRM service is already running on this machine.
    WinRM is already set up for remote management on this computer.
    PS C:\Users\Administrator> $Name = "by506b4b5d0790.microsoftdatabox.com"
    PS C:\Users\Administrator> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\Users\Administrator> Enter-PSSession -ComputerName $Name -Credential ~\StorSimpleAdmin -ConfigurationName Minishell -UseSSL
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [by506b4b5d0790.microsoftdatabox.com]: PS>
    ```

### <a name="skip-certificate-validation"></a>인증서 유효성 검사 건너뛰기

인증서를 사용하지 않는 경우(인증서를 사용하는 것을 권장함) 세션 옵션 `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`을 사용하여 인증서 유효성 검사를 건너뛸 수 있습니다.

인증서 유효성 검사를 건너뛰려면 다음 단계를 수행합니다.

1. `$Name`매개 변수를 설정합니다.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
 
1. PowerShell 세션을 열 때 세션 옵션을 사용합니다.

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    ```
1. 연결을 설정합니다.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL -SessionOption $sessOptions 
    ```

### <a name="v30-to-v40"></a>[v3.0에서 v4.0으로](#tab/b)

1. **관리자 권한으로 실행을** 사용하여 PowerShell 세션을 엽니다. 
1. IP 주소를 사용하여 목록에 Data Box `TrustedHosts` 추가합니다. 유형:

    ```powershell
    Set-Item Wsman:\localhost\Client\TrustedHosts <IPv4_address for your Data Box> 
    ``` 

1. Azure Portal Data Box 주문 리소스에 대한 암호를 얻습니다. 

1. 일반 텍스트로 제공된 암호를 보안 문자열로 변환합니다. 유형:

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
1. 세션에 대한 사용자 이름과 암호를 제공하고 자격 증명 개체를 만듭니다. 기본 사용자 이름은 `StorSimpleAdmin` 이며 이전 단계에서 얻은 암호입니다.

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    ``` 
1. 디바이스로 세션을 시작합니다.

    ```powershell
    Enter-PSSession -Computer <IPv4_address for your Data Box> -ConfigurationName Minishell -Credential $Cred 
    ```

    출력의 예제는 다음과 같습니다.
    
    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    
    PS C:\WINDOWS\system32> Set-Item wsman:\localhost\Client\TrustedHosts "10.128.45.52"
    
    WinRM Security Configuration.
    This command modifies the TrustedHosts list for the WinRM client. The computers in the
    TrustedHosts list might not be authenticated. The client might send credential information to
     these computers. Are you sure that you want to modify this list?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    PS C:\WINDOWS\system32> $Pwd = ConvertTo-SecureString "Password1" -AsPlainText -Force
    PS C:\WINDOWS\system32> $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    PS C:\WINDOWS\system32> Enter-PSSession -Computer "10.128.45.52" -ConfigurationName Minishell -Credential $Cred
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [10.128.45.52]: PS>
    ```




  

