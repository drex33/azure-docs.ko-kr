---
title: 진단을 실행하고 로그를 수집하여 Azure Stack Edge 디바이스 문제 해결 | Microsoft Docs
description: 진단을 실행하고 로그를 사용하여 Azure Stack Edge Pro GPU 디바이스 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: e2c2bc7b34316b3d18b8f10a4f1be35e7ab52e0a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130175848"
---
# <a name="run-diagnostics-collect-logs-to-troubleshoot-azure-stack-edge-device-issues"></a>진단을 실행하고 로그를 수집하여 Azure Stack Edge 디바이스 문제 해결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 진단을 실행하고, 지원 패키지를 수집하고, 고급 보안 로그를 수집하고, 로그를 검토하여 Azure Stack Edge 디바이스에서 디바이스 업로드 및 새로 고침 문제를 해결하는 방법을 설명합니다.


## <a name="run-diagnostics"></a>진단 실행

디바이스 오류를 진단하고 해결하기 위해 진단 테스트를 실행합니다. 진단 테스트를 실행하려면 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **문제 해결 > 진단 테스트** 로 이동합니다. 실행할 테스트를 선택하고 **테스트 실행** 을 선택합니다. 디바이스에서 테스트가 실행 중이라는 알림이 표시됩니다. 

    ![테스트 선택 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)

    다음은 Azure Stack Edge 디바이스에서 실행되는 각 진단 테스트를 설명하는 표입니다.

    | 테스트 이름                        | 설명        |
    |----------------------------------|---------------------------------------------------------------------------------------------------------|
    | Azure Portal 연결        |  테스트는 Azure Portal Azure Stack Edge 디바이스의 연결 유효성을 검사합니다.      |
    | Azure 일관된 상태 관리 서비스 | Azure Resource Manager, 컴퓨팅 리소스 공급자, 네트워크 리소스 공급자 및 Blob Storage 서비스와 같은 여러 서비스가 디바이스에서 실행됩니다. 이러한 서비스는 Azure 일관된 스택을 제공합니다. 상태 검사를 통해 이러한 Azure 일관된 서비스가 실행되고 있는지 확인합니다. |
    | 인증서                     | 테스트는 만료 날짜와 디바이스 및 DNS 도메인 변경이 인증서에 미치는 영향을 확인합니다. 상태 검사에서 모든 인증서를 가져와서 모든 디바이스 노드에 적용했는지 확인했습니다.                                                                                      |
    | Azure Edge Compute 런타임       | 테스트는 Azure Stack Edge Kubernetes 서비스가 예상대로 작동하는지 확인합니다. 여기에는 Kubernetes VM 상태 및 디바이스에서 배포한 Kubernetes 서비스의 상태 확인이 포함됩니다.  |
    | 디스크                            |  테스트는 모든 디바이스 디스크가 연결되어 있고 작동 중이면 유효성을 검사합니다. 여기에는 디스크에 올바른 펌웨어가 설치되어 있고 Bitlocker가 올바르게 구성되었는지 확인하는 것이 포함됩니다. |
    | PSU(전원 공급 장치)                             |  테스트는 모든 전원 공급 장치가 연결되어 작동하는지 확인합니다.  |
    | 네트워크 인터페이스               | 테스트는 모든 네트워크 인터페이스가 디바이스에 연결되어 있고 해당 시스템에 대한 네트워크 토폴로지의 유효성을 예상대로 검사합니다.    |
    | CPU(중앙 처리 장치)                             |  테스트는 시스템의 CPU가 올바른 구성을 가지고 있고 작동 중이고 작동 중임을 확인합니다.    |
    | 컴퓨팅 가속             | 이 테스트는 하드웨어와 소프트웨어 측면에서 컴퓨팅 가속이 예상대로 작동하는지 확인합니다. 디바이스 모델에 따라 컴퓨팅 가속은 GPU(그래픽 처리 장치) 또는 VPU(비전 처리 장치) 또는 FPGA(Field Programmable Gate Array)일 수 있습니다.   |
    | 네트워크 설정                 |  이 테스트는 디바이스의 네트워크 구성에 대한 유효성을 검사합니다.    |
    | 인터넷 연결            |  이 테스트는 디바이스의 인터넷 연결 유효성을 검사합니다.   |
    | 시스템 소프트웨어                  |  이 테스트는 시스템 스토리지 및 소프트웨어 스택이 예상대로 작동하는지 확인합니다.   |
    | 시간 동기화                        |  이 테스트는 디바이스 시간 설정의 유효성을 검사하고 디바이스에 구성된 시간 서버가 유효하고 액세스할 수 있는지 확인합니다.     |
    | 소프트웨어 업데이트 준비 상태        |  이 테스트는 구성된 업데이트 서버가 유효하고 액세스할 수 있는지 확인합니다.   |
 
2. 테스트가 완료된 후 결과가 표시됩니다. 

    ![테스트 결과 보기](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    테스트가 실패하면 권장 조치에 대한 URL이 표시됩니다. URL을 선택하여 권장 작업을 확인합니다.
 
    ![실패한 테스트에 대한 경고 살펴보기](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>지원 패키지 수집

로크 패키지는 Microsoft 지원에서 디바이스 문제를 해결하는 데 도움을 줄 수 있는 모든 관련 로그로 구성됩니다. 로그 패키지는 로컬 웹 UI를 통해 생성할 수 있습니다.

지원 패키지를 수집하려면 다음 단계를 수행합니다. 

1. 로컬 웹 UI에서 **문제 해결 > 지원** 으로 이동합니다. **지원 패키지 만들기** 를 선택합니다. 시스템이 지원 패키지를 수집하기 시작합니다. 패키지 수집에는 몇 분 정도 걸릴 수 있습니다.

    ![사용자 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 지원 패키지가 생성된 후에는 **지원 패키지 다운로드** 를 선택합니다. 압축된 패키지가 선택한 경로에 다운로드됩니다. 패키지의 압축을 풀면 시스템 로그 파일을 볼 수 있습니다.

    ![사용자 2 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>고급 보안 로그 수집

고급 보안 로그는 Azure Stack Edge Pro 디바이스에 관한 소프트웨어 또는 하드웨어 침입 로그일 수 있습니다.

### <a name="software-intrusion-logs"></a>소프트웨어 침입 로그

소프트웨어 침입 또는 기본 방화벽 로그는 인바운드 및 아웃바운드 트래픽에 대해 수집됩니다. 

- 디바이스가 센터에서 이미지화되면 기본 방화벽 로깅이 사용으로 설정됩니다. 이 로그는 로컬 UI 또는 디바이스의 Windows PowerShell 인터페이스를 통해 지원 패키지를 만들 때 기본적으로 지원 패키지에 번들됩니다.

- 디바이스의 소프트웨어(NW) 침입을 검토하기 위해 지원 패키지에 방화벽 로그만 필요한 경우 지원 패키지를 만들 때 `-Include FirewallLog` 옵션을 사용합니다. 

- 특정 포함 옵션이 제공되지 않으면 방화벽 로그가 지원 패키지에 기본값으로 포함됩니다.

- 지원 패키지에서 방화벽 로그는 `pfirewall.log`이며 루트 폴더에 있습니다. 다음은 Azure Stack Edge Pro 디바이스의 소프트웨어 침입 로그 예제입니다. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>하드웨어 침입 로그

디바이스에 대한 하드웨어 침입을 탐지하기 위해 섀시 열기 또는 닫기와 같은 모든 섀시 이벤트가 현재 로그됩니다. 

- 디바이스의 시스템 이벤트 로그는 `racadm` cmdlet을 사용하여 읽습니다. 그런 다음 이 이벤트는 `HWIntrusion.txt` 파일에서 섀시 관련 이벤트를 기준으로 필터링됩니다.

- 지원 패키지에서 하드웨어 침입 로그만 가져오려면 지원 패키지를 만들 때 `-Include HWSelLog` 옵션을 사용합니다. 

- 특정 포함 옵션이 제공되지 않으면 하드웨어 침입 로그가 지원 패키지에 기본값으로 포함됩니다.

- 지원 패키지에서 하드웨어 침입 로그는 `HWIntrusion.txt`이며 루트 폴더에 있습니다. 다음은 Azure Stack Edge Pro 디바이스의 하드웨어 침입 로그 예제입니다. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="troubleshoot-device-upload-and-refresh-errors"></a>디바이스 업로드 및 새로 고침 오류 문제 해결

업로드 및 새로 고침 프로세스 중에 발생한 모든 오류는 해당 오류 파일에 포함됩니다.

1. 오류 파일을 보려면 공유로 이동하여 공유를 선택하고 콘텐츠를 봅니다. 


2. _Microsoft Data Box Edge 폴더_ 를 선택합니다. 이 폴더에는 두 개의 하위 폴더가 있습니다.

    - 업로드 오류에 대한 로그 파일이 있는 Upload 폴더
    - 새로 고침 중 오류를 위한 Refresh 폴더

    다음은 새로 고침에 대한 샘플 로그 파일입니다.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 이 파일에 오류가 표시되면(샘플에 강조 표시됨), 오류 코드를 적어두십시오. 이 경우에는 16001입니다. 다음 오류 참조에서 이 오류 코드에 대한 설명을 찾아봅니다.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>다음 단계

- [디바이스 활성화 문제를 해결](azure-stack-edge-gpu-troubleshoot-activation.md)합니다.
- [Azure Resource Manager 문제를 해결](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)합니다.
- [Blob 스토리지 문제를 해결](azure-stack-edge-gpu-troubleshoot-blob-storage.md)합니다.
- [IoT Edge의 컴퓨팅 문제를 해결](azure-stack-edge-gpu-troubleshoot-iot-edge.md)합니다.