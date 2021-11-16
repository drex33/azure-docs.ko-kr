---
title: Azure 필요한 상태 구성 확장 버전 기록 작업
description: 이 문서에서는 Azure에서 DSC(Desired State Configuration) 확장에 대한 버전 기록을 사용하는 방법에 대해 설명합니다.
ms.date: 02/17/2021
keywords: dsc, powershell, azure, 확장
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: 3aaadbb6f972838984f4ec01347537f21e244f14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491472"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Azure 필요한 상태 구성 확장 버전 기록 작업

Azure DSC(필요한 상태 구성) VM [확장](../virtual-machines/extensions/dsc-overview.md)은 필요에 따라 Azure, Windows Server 및 Windows PowerShell을 포함하는 WMF(Windows Management Framework)로 제공되는 향상된 기능 및 새로운 기능을 지원하도록 업데이트됩니다.

> [!NOTE]
> DSC 확장을 사용 하도록 설정 하기 전에 최신 버전의 DSC를 미리 보기에서 사용할 수 있다는 것을 알고 Azure Policy [게스트 구성](../governance/policy/concepts/guest-configuration.md)이라는 기능을 통해 관리 합니다. 게스트 구성 서비스는 DSC 확장, Azure Automation 상태 구성 및 가장 일반적으로 요청 되는 기능의 기능을 고객 의견에서 결합 합니다. 게스트 구성에는 [Arc 사용 서버](../azure-arc/servers/overview.md)를 통한 하이브리드 컴퓨터 지원 기능도 포함 되어 있습니다.

이 문서에서는 Azure DSC VM 확장의 각 버전에 대한 정보, 지원하는 환경 및 새 기능 또는 변경 내용에 대한 설명 및 주의 사항을 제공합니다.

## <a name="latest-version"></a>최신 버전

### <a name="version-283"></a>버전 2.83

- **릴리스 날짜:**
  - 2021년 2월
- **OS 지원:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows 클라이언트 7/8.1/10
  - Nano 서버
- **WMF 지원:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 업데이트
  - WMF 4.0
- **환경:**
  - Azure
  - Azure 중국 Vianet 21
  - Azure Government
- **설명:** 해당 릴리스에는 Windows VM 확장을 사용하여 서명되지 않은 이진 파일에 대한 픽스가 포함되어 있습니다.

### <a name="version-280"></a>버전 2.80

- **릴리스 날짜:**
  - 2019년 9월 26일(Azure) | 2020년 7월 6일(Azure 중국 21Vianet) | 2020년 7월 20일(Azure Government)
- **OS 지원:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows 클라이언트 7/8.1/10
  - Nano 서버
- **WMF 지원:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 업데이트
  - WMF 4.0
- **환경:**
  - Azure
  - Azure 중국 Vianet 21
  - Azure Government
- **설명:** 해당 릴리스에는 새로운 기능이 포함되지 않습니다.

### <a name="version-276"></a>버전 2.76

- **릴리스 날짜:**
  - 2018년 5월 9일(Azure) | 2018년 6월 21일(Azure 중국 Vianet 21, Azure Government)
- **OS 지원:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows 클라이언트 7/8.1/10
  - Nano 서버
- **WMF 지원:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 업데이트
  - WMF 4.0
- **환경:**
  - Azure
  - Azure 중국 Vianet 21
  - Azure Government
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - 하위 상태 및 기타 사소한 버그 수정을 위해 확장 메타데이터가 개선됨.

## <a name="supported-versions"></a>지원되는 버전

> [!WARNING]
> 버전 2.4~2.13은 서명 인증서가 2016년 8월에 만료된 WMF 5.0 퍼블릭 미리 보기를 사용합니다.
> 해당 이슈에 대한 자세한 내용은 다음 [블로그 문서](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/)를 참조하세요.

### <a name="version-275"></a>버전 2.75

- **릴리스 날짜:** March 5, 2018
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows 클라이언트 7/8.1/10, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - GitHub의 TLS 1.2 적용 후 Azure Marketplace에서 사용할 수 있는 DIY Resource Manager 템플릿을 사용하여 VM에서 Azure Automation State Configuration에 온보딩할 수 없거나 DSC 확장을 사용하여 GitHub에서 호스트된 구성을 검색할 수 없습니다. 확장을 배포하는 동안 다음과 유사한 오류가 반환됩니다.

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - 새 확장 버전에서 이제 TLS 1.2가 적용됩니다. 확장을 배포하는 동안 Resource Manager 템플릿에 `AutoUpgradeMinorVersion = true`을 이미 지정한 경우 확장이 2.75로 자동 업그레이드됩니다. 수동 업데이트의 경우 Resource Manager 템플릿에서 `TypeHandlerVersion = 2.75`를 지정합니다.

### <a name="version-270---272"></a>버전 2.70 - 2.72

- **릴리스 날짜:** 2017년 11월 13일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows 클라이언트 7/8.1/10, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - Resource Manager 템플릿과 포털에서 Azure Automation State Configuration 사용을 간소화하는 버그 수정 사항 및 향상된 기능입니다. 자세한 내용은 DSC 확장 설명서의 [기본 구성 스크립트](../virtual-machines/extensions/dsc-overview.md)를 참조하세요.

### <a name="version-226"></a>버전 2.26

- **릴리스 날짜:** 2017년 6월 9일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows 클라이언트 7/8.1/10, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - 원격 분석 향상된 기능

### <a name="version-225"></a>버전 2.25

- **릴리스 날짜:** 2017년 6월 2일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows 클라이언트 7/8.1/10, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - 여러 버그 수정 사항 및 기타 사소한 향상된 기능이 추가되었습니다.

### <a name="version-224"></a>버전 2.24

- **릴리스 날짜:** 2017년 4월 13일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - 확장 메타데이터로 VM UUID 및 DSC 에이전트 ID를 노출합니다. 기타 사소한 향상된 기능이 추가되었습니다.

### <a name="version-223"></a>버전 2.23

- **릴리스 날짜:** 2017년 3월 15일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - 버그 수정 사항 및 기타 향상된 기능이 추가되었습니다.

### <a name="version-222"></a>버전 2.22

- **릴리스 날짜:** 2017년 2월 8일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF 지원:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/)을 설치합니다(WMF를 설치하려면 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - DSC 확장은 이제 WMF 5.1을 지원합니다.
  - 기타 사소한 향상된 기능이 추가되었습니다.

### <a name="version-221"></a>버전 2.21

- **릴리스 날짜:** 2016년 12월 2일
- **OS 지원:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF 지원:** WMF 5.1 미리 보기, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 이 버전은 Windows Server 2016에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요). Nano Server의 경우 DSC 역할이 VM에 설치됩니다.
- **새로운 기능:**
  - DSC 확장 이제 Nano Server에서 사용할 수 있습니다. 해당 버전은 주로 Nano Server에서 확장을 실행하기 위한 코드 변경 내용을 포함합니다.
  - 기타 사소한 향상된 기능이 추가되었습니다.

### <a name="version-220"></a>버전 2.20

- **릴리스 날짜:** 2016년 8월 2일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.1 미리 보기, WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - WMF 5.1 미리 보기에 대한 지원 처음 게시할 때 이 버전은 선택적 업그레이드였으며 Resource Manager 템플릿에서 WMF 5.1 미리 보기를 설치하기 위해 Wmfversion = ‘5.1PP’를 지정해야 했습니다.
    Wmfversion = ‘latest’는 여전히 [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다.
    WMF 5.1 미리 보기에 대한 자세한 내용은 [이 블로그](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/)를 참조하세요.
  - 기타 사소한 수정 사항 및 향상된 기능이 추가되었습니다.

### <a name="version--219"></a>버전 2.19

- **릴리스 날짜:** 2016년 6월 3일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure, Azure 중국 Vianet 21, Azure Government
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - DSC 확장은 이제 Azure 중국 21Vianet에서 사용할 수 있습니다. 이 버전은 Azure 중국 21Vianet에서 확장을 실행하기 위한 수정 사항을 포함합니다.

### <a name="version-218"></a>버전 2.18

- **릴리스 날짜:** 2016년 6월 3일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - 원격 분석 핫픽스 다운로드(알려진 Azure DNS 문제) 또는 설치 중 오류가 발생할 때 원격 분석을 비동기로 만듭니다.
  - 확장이 다시 부팅 후 구성 처리를 중지하는 일시적인 문제에 대한 수정 이로 인해 DSC 확장이 ‘전환’ 상태로 유지되었습니다.
  - 기타 사소한 수정 사항 및 향상된 기능이 추가되었습니다.

### <a name="version-217"></a>버전 2.17

- **릴리스 날짜:** 2016년 4월 26일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0 업데이트, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - WMF 4.0 업데이트에 대한 지원 WMF 4.0 업데이트에 대한 자세한 내용은 [이 블로그](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/)를 참조하세요.
  - DSC 확장 설치 중 또는 확장 설치 후 DSC 구성을 적용하는 동안 발생하는 오류에 대한 재시도 논리입니다. 이 변경 내용의 일부로 확장은 이전 설치가 실패한 경우 설치를 다시 시도하거나 완료 상태(성공/오류)에 도달할 때까지 또는 새 요청이 들어오는 경우 이전에 실패했던 DSC 구성을 최대 세 번 다시 적용합니다. 잘못된 사용자 설정/사용자 입력으로 인해 확장이 실패하는 경우 재시도하지 않습니다. 이 경우 확장은 새 요청 및 올바른 사용자 설정으로 다시 호출되어야 합니다. 

  > [!NOTE]
   > DSC 확장은 재시도에 대한 Azure VM 에이전트에 따라 달라집니다. Azure VM 에이전트는 성공 또는 오류 상태에 도달할 때까지 마지막 실패한 요청으로 확장을 호출합니다.

### <a name="version-216"></a>버전 2.16

- **릴리스 날짜:** 2016년 4월 21일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - 오류 처리 및 기타 사소한 버그 수정의 개선
  - DSC 확장 설정의 새로운 속성. 새로 고침 모드가 풀일 때 DSC 확장에서 DSC 구성을 시행하도록 AdvancedOptions에 `ForcePullAndApply`가 추가됩니다(기본 푸시 모드와 반대임). DSC 확장 설정에 대한 자세한 내용은 [이 블로그](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)를 참조하세요.

### <a name="version-215"></a>버전 2.15

- **릴리스 날짜:** 2016년 3월 14일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - 확장 버전 2.14에서 WMF RTM 설치를 위한 변경 내용이 포함되었습니다. 확장 버전 2.13.2.0에서 2.14.0.0으로 업그레이드하는 동안 일부 DSC cmdlet이 실패하거나 구성이 ‘지정된 속성 값으로 인스턴스를 찾을 수 없음’ 오류로 실패하는 것을 확인할 수 있습니다. 자세한 내용은 [DSC 릴리스 정보](/powershell/scripting/wmf/known-issues/known-issues-dsc)를 참조하세요. 이러한 문제에 대한 해결 방법이 2.15 버전에서 추가되었습니다. 
  - 버전 2.14를 이미 설치했고 위의 두 가지 문제 중 하나가 발생한 경우 이러한 단계를 수동으로 수행해야 합니다. 관리자 권한 PowerShell 세션에서 다음 명령을 실행합니다.
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>버전 2.14

- **릴리스 날짜:** 2016년 2월 25일
- **OS 지원:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF 지원:** WMF 5.0 RTM, WMF 4.0
- **환경:** Azure
- **설명:** 해당 버전은 Windows Server 2016 Technical Preview에 포함된 DSC를 사용합니다. 다른 Windows OS의 경우 [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)을 설치합니다(WMF 설치에 재부팅 필요).
- **새로운 기능:**
  - WMF RTM을 사용합니다.
  - DSC 확장의 품질을 개선하기 위해 데이터 수집을 활성화합니다. 자세한 내용은 [블로그 문서](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)를 참조하세요.
  - Resource Manager 템플릿에서 확장에 대한 업데이트된 설정 형식을 제공합니다. 자세한 내용은 [블로그 문서](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)를 참조하세요.
  - 버그 수정 및 고급 기능

## <a name="next-steps"></a>다음 단계

- PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- [DSC 확장에 대한 Resource Manager 템플릿](../virtual-machines/extensions/dsc-template.md)을 검토합니다.
- PowerShell DSC로 관리할 수 있는 다른 기능 및 리소스를 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)를 찾아보세요.
- 중요한 매개 변수를 구성에 전달하는 세부 정보는 [DSC 확장 처리기로 안전하게 자격 증명 관리](../virtual-machines/extensions/dsc-credentials.md)를 참조하세요.
