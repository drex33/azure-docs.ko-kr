---
title: Azure Security Center의 Endpoint Protection 권장 사항
description: 엔드포인트 보호 솔루션을 검색하고 정상으로 식별하는 방법입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561231"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure Security Center의 Endpoint Protection 평가 및 권장 사항

Azure Security Center는 [지원되는](security-center-services.md#endpoint-supported) 버전의 Endpoint Protection 솔루션의 성능 상태 평가를 제공합니다. 이 문서에서는 Security Center에서 다음 두 가지 권장 사항을 생성하는 시나리오를 설명합니다.

* **가상 머신에 엔드포인트 보호 솔루션 설치**
* **머신의 엔드포인트 보호 상태 문제 해결**

## <a name="windows-defender"></a>Windows Defender

* [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus)가 실행되고 결과가 **AMServiceEnabled: False** 인 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.

* [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus)가 실행되고 다음 상황이 발생하는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.

  * 다음 속성은 모두 false입니다.

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 다음 속성 중 하나 또는 둘 다가 7 이상인 경우:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* **SCEPMpModule("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** 을 가져오고 **Get-MProtComputerStatus** 를 실행한 결과가 **AMServiceEnabled = false** 인 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.

* **Get-MprotComputerStatus** 가 실행되고 다음 상황이 발생하는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.

  * 다음 속성 중 하나 이상이 false인 경우:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 다음 서명 업데이트 중 하나 또는 둘 다가 7보다 크거나 같은 경우:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 다음 검사가 충족되지 않는 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** 가 있음
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** 가 있음
    - **dsa_query.cmd** 파일은 설치 폴더에 있습니다.
    - **dsa_query.cmd** 를 실행하면 **Component.AM.mode: on - Trend Micro Deep Security Agent detected** 가 표시됩니다.

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
다음 검사가 충족되지 않는 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.

- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

또는

- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

다음 검사가 충족되지 않는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.

- Symantec 버전 >= 12 확인: 레지스트리 위치: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**
- 실시간 보호 상태 확인: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**
- 서명 업데이트 상태 확인: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**
- 전체 검사 상태 확인: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**
- Symantec 12에 대한 서명 버전의 서명 버전 번호 경로 찾기: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 
- Symantec 14의 서명 버전 경로: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

레지스트리 경로:
- **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection for Windows

다음 검사가 충족되지 않는 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.

- **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists
- **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

다음 검사가 충족되지 않는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.

- McAfee 버전: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- 서명 버전 찾기: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**
- 서명 날짜 찾기: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**
- 검사 날짜 찾기: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security for Linux Threat Prevention 

다음 검사가 충족되지 않는 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.

- **/opt/isec/ens/threatprevention/bin/isecav** 파일이 있음
- **"/opt/isec/ens/threatprevention/bin/isecav --version"** 출력: **McAfee 이름 = McAfee Endpoint Security for Linux Threat Prevention 및 McAfee 버전 >= 10**

다음 검사가 충족되지 않는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** 는 **빠른 검사, 전체 검사** 와 두 검사 모두 <= 7일을 반환합니다.
- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** 는 **DAT 및 엔진 업데이트 시간** 과 두 항목 모두 <= 7일을 반환합니다.
- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** 는 **액세스 검사 시** 상태를 반환합니다.

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus for Linux 

다음 검사가 충족되지 않는 경우 Security Center에서는 **“가상 머신에 엔드포인트 보호 솔루션을 설치”** 하는 것이 좋습니다.
- **/opt/sophos-av/bin/savdstatus** 파일이 있거나 사용자 지정 위치 **"readlink $(which savscan)"** 검색
- **"/opt/sophos-av/bin/savdstatus --version"** 은 Sophos 이름 = **Sophos Anti-Virus 및 Sophos 버전 >= 9** 를 반환합니다.

다음 검사가 충족되지 않는 경우 Security Center에서는 **“머신에서 엔드포인트 보호 성능 상태 문제를 해결”** 하는 것이 좋습니다.
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scheduled scan .\* completed" | tail -1"** 은 값을 반환합니다.
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished"** | tail -1"은 값을 반환합니다.
- **"/opt/sophos-av/bin/savdstatus --lastupdate"** 는 lastUpdate를 반환하며 이는 <= 7일이어야 합니다. 
- **"/opt/sophos-av/bin/savdstatus -v"** 는 **“액세스 검사 시가 실행 중임”** 과 같습니다. 
- **"/opt/sophos-av/bin/savconfig get LiveProtection"** 은 사용을 반환합니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

Microsoft Antimalware 방지 확장 로그는 **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log** 에서 사용할 수 있습니다.

### <a name="support"></a>지원

도움이 더 필요하면 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의하세요. 또는 Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.