---
title: Azure Defender for IoT 평가판 설정
description: 이 자습서에서는 가상 머신에서 가상 센서를 통해 Azure Defender for IoT 평가판 구독을 사용하여 Azure Defender for IoT에 온보딩하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/06/2021
ms.custom: template-tutorial
ms.openlocfilehash: 66d95f5700cb3445aa5e2facabe12ea7c70ef92e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544798"
---
# <a name="tutorial-azure-defender-for-iot-trial-setup"></a>자습서: Azure Defender for IoT 평가판 설정

이 자습서는 가상 머신에서 가상 센서를 통해 Azure Defender for IoT 평가판 구독을 사용하여 Azure Defender for IoT에 온보딩하는 방법을 알아보는 데 도움이 됩니다. 이 자습서에서는 Azure Defender for IoT에 가입하고 이를 환경에 통합하기 전에 테스트하려는 사용자를 위한 최적의 설정을 보여 줍니다.

센서를 만드는 데 필요한 소프트웨어와 함께 가상 환경을 사용하면 Defender for IoT를 통해 다음을 수행할 수 있습니다.

- 에이전트 없는 수동적인 네트워크 모니터링을 사용하여 IoT 및 OT 네트워크에 아무 영향도 주지 않고 모든 IoT 및 OT 디바이스, 해당 세부 정보 및 통신 방법에 대한 완전한 인벤토리를 얻습니다.

- IoT 및 OT 환경의 위험과 취약성을 식별합니다. 예를 들어 패치가 적용되지 않은 디바이스, 열려 있는 포트, 권한 없는 애플리케이션 및 무단 연결을 식별합니다. 디바이스 구성, PLC 코드 및 펌웨어의 변경 내용도 확인할 수 있습니다.

- 특수 IoT/OT 인식 위협 인텔리전스 및 동작 분석을 통해 비정상 또는 무단 작업을 검색합니다. 고정 IOC에서 놓친 제로데이 맬웨어, 파일리스 맬웨어, 지상 생활 전술 등의 지능형 위협도 탐지할 수 있습니다.

- Azure Sentinel에 통합되어 전체 조직에 대한 정보를 제공합니다. Splunk, IBM QRadar 및 ServiceNow와 같은 타사 도구를 포함한 기존 워크플로에 통합하여 통합 IoT 및 OT 보안 거버넌스를 구현합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Defender for IoT에 온보딩
> * 가상 센서에 대한 ISO 다운로드
> * 센서에 대한 가상 머신 만들기
> * 가상 센서 소프트웨어 설치
> * SPAN 포트 구성
> * 가상 센서 온보딩 및 활성화

## <a name="prerequisites"></a>필수 조건

- 권한: Azure **구독 소유자** 또는 **구독 기여자** 수준

- 스위치의 SPAN 포트에 연결되어 모니터링할 하나 이상의 디바이스

- VMware(ESXi 5.5 이상) 또는 Hyper-V 하이퍼바이저(Windows 10 Pro 또는 Enterprise)가 설치되어 작동합니다.

- Azure 계정. 아직 Azure 계정이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

## <a name="onboard-with-azure-defender-for-iot"></a>Azure Defender for IoT에 온보딩

Azure Defender for IoT를 시작하려면 Microsoft Azure 구독이 있어야 합니다. 구독이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

평가판 구독은 Defender for IoT를 평가하는 데 사용할 수 있습니다. 평가판은 30일 동안 유효하며, 최대 1,000개의 커밋된 디바이스를 지원합니다. 평가판을 통해 가상 센서를 네트워크에 배포할 수 있습니다. 센서를 사용하여 트래픽을 모니터링하고, 데이터를 분석하고, 경고를 생성하고, 네트워크 위험 및 취약성에 대해 알아봅니다. 또한 평가판을 사용하면 가상 온-프레미스 관리 콘솔을 배포하여 센서에서 생성한 집계 정보를 볼 수 있습니다.

**구독을 Azure Defender for Iot에 온보딩하려면**,

1. [Azure Portal](https://ms.portal.azure.com/)로 이동합니다.

1. **Azure Defender for IoT** 를 검색하여 선택합니다.

1. **구독 등록** 을 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/onboard-subscription.png" alt-text="시작 페이지에서 구독 온보드 단추를 선택하는 스크린샷":::

1. 가격 책정 페이지에서 **평가판 시작** 을 선택합니다.

   :::image type="content" source="media/tutorial-onboarding/start-with-trial.png" alt-text="평가판 창을 평가판 시작 단추의 스크린샷":::

1. 평가판 구독 온보딩 창에서 구독을 선택한 다음, **평가** 를 선택합니다.

1. 평가를 확인합니다.

## <a name="download-the-iso-for-the-virtual-sensor"></a>가상 센서에 대한 ISO 다운로드

가상 어플라이언스에는 센서와 관리 콘솔 모두에 필요한 최소 사양이 있습니다. 다음 표에서는 환경에 따라 센서에 필요한 사양을 보여 줍니다.

### <a name="virtual-sensor"></a>가상 센서

| 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 메모리 | 32GB | 32GB | 8GB |
| 스토리지 | 5.6TB | 1.8TB | 500GB |

**가상 센서에 대한 ISO 파일을 다운로드하려면**,

1. [Azure Portal](https://ms.portal.azure.com/)로 이동합니다.

1. **Azure Defender for IoT** 를 검색하여 선택합니다.

1. 시작 페이지에서 **센서** 탭을 선택합니다.

1. **다운로드** 를 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/sensor-download.png" alt-text="다운로드를 선택하여 가상 센서에 대한 ISO 파일을 다운로드할 수 있는 센서 탭의 스크린샷":::

## <a name="create-a-virtual-machine-for-the-sensor"></a>센서에 대한 가상 머신 만들기

가상 센서는 VMware 및 Hyper-V 배포 옵션을 모두 지원합니다. 설치를 시작하기 전에 다음 항목을 갖추고 있는지 확인합니다.

- 설치되어 작동하는 VMware(ESXi 5.5 이상) 또는 Hyper-V 하이퍼바이저(Windows 10 Pro 또는 Enterprise)

- 가상 머신에 사용 가능한 하드웨어 리소스

- Azure Defender for IoT 센서에 대한 ISO 설치 파일

- 하이퍼바이저가 실행 중인지 확인합니다.

### <a name="create-the-virtual-machine-for-the-sensor-with-esxi"></a>ESXi를 사용하여 센서에 대한 가상 머신 만들기

**센서에 대한 가상 머신을 만들려면(ESXi)** ,

1. ESXi에 로그인하고, 관련 **데이터 저장소** 를 선택하고, **데이터 저장소 브라우저** 를 선택합니다.

1. 이미지를 **업로드** 하고 **닫기** 를 선택합니다.

1. **가상 머신** 으로 이동한 다음 **VM 만들기/등록** 을 선택합니다.

1. **새 가상 머신 만들기** 를 선택하고 **다음** 을 선택합니다.

1. 센서 이름을 추가하고 다음을 선택합니다.

   - 호환성: **&lt;최신 ESXi 버전&gt;**

   - 게스트 OS 제품군: **Linux**

   - 게스트 OS 버전: **Ubuntu Linux(64비트)**

1. **다음** 을 선택합니다.

1. 관련 데이터 저장소를 선택하고 **다음** 을 선택합니다.

1. 필요한 [아키텍처](#download-the-iso-for-the-virtual-sensor)에 따라 가상 하드웨어 매개 변수를 변경합니다.

1. **CD/DVD 드라이브 1** 에 대해 **데이터 저장소 ISO 파일** 을 선택하고 이전에 업로드한 ISO 파일을 선택합니다.

1. **다음** > **마침** 을 선택합니다.

1. VM의 전원을 켜고 콘솔을 엽니다.

### <a name="create-a-virtual-machine-for-the-sensor-with-hyper-v"></a>Hyper-V를 사용하여 센서에 대한 가상 머신 만들기

이 절차에서는 Hyper-V를 사용하여 가상 머신을 만드는 방법을 설명합니다.

**Hyper-V를 사용하여 가상 머신을 만들려면**,

1. Hyper-V 관리자에서 가상 디스크를 만듭니다.

1. **형식 = VHDX** 를 선택합니다.

1. **유형 = 동적 확장** 을 선택합니다.

1. VHD의 이름과 위치를 입력합니다.

1. [아키텍처](#download-the-iso-for-the-virtual-sensor)에 따라 필요한 크기를 입력합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. **작업** 메뉴에서 새 가상 머신을 만듭니다.

1. 가상 컴퓨터의 이름을 입력합니다.

1. **세대 지정** > **1세대** 를 선택합니다.

1. [아키텍처](#download-the-iso-for-the-virtual-sensor)에 따라 메모리 할당을 지정하고 동적 메모리의 확인란을 선택합니다.

1. 서버 네트워크 토폴로지에 따라 네트워크 어댑터를 구성합니다.

1. 이전에 만든 VHDX를 가상 머신에 연결합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. 새 가상 머신을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택합니다.

1. **하드웨어 추가** 를 선택하고 새 네트워크 어댑터를 추가합니다.

1. 센서 관리 네트워크에 연결되는 가상 스위치를 선택합니다.

1. [아키텍처](#download-the-iso-for-the-virtual-sensor)에 따라 CPU 리소스를 할당합니다.

1. 관리 콘솔의 ISO 이미지를 가상 DVD 드라이브에 연결합니다.

1. 가상 머신을 시작합니다.

1. **작업** 메뉴에서 **연결** 을 선택하여 소프트웨어 설치를 계속합니다.

## <a name="install-the-virtual-sensor-software-with-esxi-or-hyper-v"></a>ESXi 또는 Hyper-V를 사용하여 가상 센서 소프트웨어 설치

ESXi 또는 Hyper-V를 사용하여 가상 센서 소프트웨어를 설치할 수 있습니다.

**소프트웨어를 가상 센서에 설치하려면**,

1. 가상 머신 콘솔을 엽니다.

1. ISO 이미지에서 VM이 시작되고 언어 선택 화면이 표시됩니다. **영어** 를 선택합니다.

1. 필요한 [아키텍처](#download-the-iso-for-the-virtual-sensor)를 선택합니다.

1. 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | 필요한 [아키텍처](#download-the-iso-for-the-virtual-sensor)를 기반으로 하는 프로필입니다. |
    | **관리 인터페이스** | **ens192** |
    | **네트워크 매개 변수(고객이 제공)** | **관리 네트워크 IP 주소:** <br/>**서브넷 마스크:** <br>**어플라이언스 호스트 이름:** <br/>**DNS:** <br/>**기본 게이트웨이:** <br/>**입력 인터페이스:**|
    | **브리지 인터페이스:** | 브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별한 사용 사례용입니다. |

1. 설정을 적용하려면 **Y** 를 입력합니다.

1. 로그인 자격 증명은 자동으로 생성되고 표시됩니다. 사용자 이름 및 암호는 로그인하고 디바이스를 관리하는 데 필요하므로 안전한 곳에 복사합니다. 사용자 이름과 암호는 다시 표시되지 않습니다.

    - **지원**: 사용자 관리를 위한 관리자입니다.

    - **CyberX**: 어플라이언스에 액세스하기 위한 루트와 동일합니다.

1. 어플라이언스가 다시 시작됩니다.

1. 이전에 구성된 IP 주소(`https://ip_address`)를 통해 센서에 액세스합니다.

### <a name="post-installation-validation"></a>설치 후 유효성 검사

물리적 어플라이언스 설치의 유효성을 검사하려면 많은 테스트를 수행해야 합니다.

유효성 검사는 **지원** 및 **CyberX** 사용자 모두가 사용할 수 있습니다.

**사후 유효성 검사 도구에 액세스하려면**,

1. 센서에 로그인합니다.

1. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. :::image type="icon" source="media/tutorial-onboarding/system-statistics-icon.png" border="false"::: 단추를 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/system-health-check-screen.png" alt-text="시스템 상태 확인의 스크린샷" lightbox="media/tutorial-onboarding/system-health-check-screen-expanded.png":::

설치 후 유효성 검사의 경우 시스템이 실행되는지, 올바른 버전인지, 그리고 설치 프로세스 중에 구성된 모든 입력 인터페이스가 실행되는지 확인하기 위해 테스트해야 합니다.

**시스템이 실행되는지 확인하려면**,

1. **어플라이언스** 를 선택하고, 각 항목에 `Running`이 표시되고 아래쪽 줄에 `System is up`이 표시되는지 확인합니다.

1. **버전** 을 선택하고, 올바른 버전이 표시되는지 확인합니다.

1. **ifconfig** 를 선택하여 어플라이언스의 물리적 인터페이스에 대한 매개변수를 표시하고 올바른지 확인합니다.

## <a name="configure-a-span-port"></a>SPAN 포트 구성

vSwitch에는 미러링 기능이 없지만 SPAN 포트를 구현하기 위한 해결 방법을 사용할 수 있습니다. 해결 방법은 ESXi 또는 Hyper-V를 사용하여 구현할 수 있습니다.

### <a name="configure-a-span-port-with-esxi"></a>ESXi를 사용하여 SPAN 포트 구성

**ESXi를 사용하여 SPAN 포트를 구성하려면**,

1. vSwitch 속성을 엽니다.

1. **추가** 를 선택합니다.

1. **가상 머신** > **다음** 을 선택합니다.

1. 네트워크 레이블 **SPAN 네트워크** 를 삽입하고 **VLAN ID** > **모두** 를 선택한 후 **다음** 을 선택합니다.

1. **마침** 을 선택합니다.

1. **SPAN 네트워크** > **편집* 을 선택합니다.

1. **보안** 을 선택하고 **무차별 모드** 정책이 **수락** 모드로 설정되어 있는지 확인합니다.

1. **확인** 을 선택하고 **닫기** 를 선택하여 vSwitch 속성을 닫습니다.

1. **Xsense VM** 속성을 엽니다.

1. **네트워크 어댑터 2** 의 경우 **SPAN** 네트워크를 선택합니다.

1. **확인** 을 선택합니다.

1. 센서에 연결하고 미러링이 작동하는지 확인합니다.

### <a name="configure-a-span-port-with-hyper-v"></a>Hyper-V를 사용하여 SPAN 포트 구성

시작하기 전에 다음을 수행해야 합니다.

- 실행 중인 ClearPass VA 인스턴스가 없는지 확인합니다.

- 관리 포트가 아닌 데이터 포트에서 SPAN 보장을 사용하도록 설정합니다.

- 데이터 포트 SPAN 구성이 IP 주소로 구성되지 않았는지 확인합니다.

**Hyper-V를 사용하여 SPAN 포트를 구성하려면**,

1. 가상 스위치 관리자를 엽니다.

1. 가상 스위치 목록에서 전용 스팬 네트워크 어댑터 유형으로 **새 가상 네트워크 스위치** > **외부** 를 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/new-virtual-network.png" alt-text="가상 스위치를 만들기 전에 새 가상 네트워크 및 외부를 선택하는 스크린샷":::

1. 선택 **가상 스위치를 만들** 합니다.

1. 연결 형식 아래에서 **외부 네트워크** 를 선택합니다.

1. **관리 운영 체제에서 이 네트워크 어댑터를 공유할 수 있음** 확인란이 선택되어 있는지 확인합니다.

   :::image type="content" source="media/tutorial-onboarding/external-network.png" alt-text="외부 네트워크를 선택하고 관리 운영 체제에서 네트워크 어댑터를 공유할 수 있도록 허용":::

1. **확인** 을 선택합니다.

#### <a name="attach-a-clearpass-span-virtual-interface-to-the-virtual-switch"></a>가상 스위치에 ClearPass SPAN 가상 인터페이스 연결

Windows PowerShell 또는 Hyper-V 관리자를 통해 ClearPass SPAN 가상 인터페이스를 가상 스위치에 연결할 수 있습니다.

**PowerShell을 사용하여 ClearPass SPAN 가상 인터페이스를 가상 스위치에 연결하려면**,

1. 새로 추가된 SPAN 가상 스위치를 선택하고, 다음 명령을 사용하여 새 네트워크 어댑터를 추가합니다.

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. 다음 명령을 사용하여 포트 미러링을 SPAN 대상으로 선택한 인터페이스에 사용하도록 설정합니다.

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | 매개 변수 | Description |
    |--|--|
    | VK-C1000V-LongRunning-650 | CPPM VA 이름 |
    |vSwitch_Span |새로 추가된 SPAN 가상 스위치 이름 |
    |Monitor |새로 추가된 어댑터 이름 |

1. **확인** 을 선택합니다.

이러한 명령은 새로 추가된 어댑터 하드웨어의 이름을 `Monitor`로 설정합니다. Hyper-V 관리자를 사용하는 경우 새로 추가된 어댑터 하드웨어의 이름은 `Network Adapter`로 설정됩니다.

**Hyper-V 관리자를 사용하여 ClearPass SPAN 가상 인터페이스를 가상 스위치에 연결하려면**,

1. 하드웨어 목록에서 **네트워크 어댑터** 를 선택합니다.

1. 가상 스위치 필드에서 **vSwitch_Span** 을 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/vswitch-span.png" alt-text="가상 스위치 화면에서 다음 옵션을 선택하는 스크린샷":::

1. 하드웨어 목록의 네트워크 어댑터 드롭다운 목록 아래에서 **고급 기능** 을 선택합니다.

1. 포트 미러링 섹션에서 새 가상 인터페이스에 대한 미러링 모드로 **대상** 을 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/destination.png" alt-text="미러링 모드를 구성하는 데 필요한 선택 항목의 스크린샷":::

1. **확인** 을 선택합니다.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>가상 스위치에 Microsoft NDIS 캡처 확장 사용

Microsoft NDIS 캡처 확장을 새 가상 스위치에 사용하도록 설정해야 합니다.

**Microsoft NDIS 캡처 확장을 새로 추가된 가상 스위치에 사용하도록 설정하려면**,

1. Hyper-V 호스트에서 가상 스위치 관리자를 엽니다.

1. 가상 스위치 목록에서 `vSwitch_Span`이라는 가상 스위치 이름을 펼치고, **확장** 을 선택합니다.

1. 스위치 확장 필드에서 **Microsoft NDIS 캡처** 를 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/microsoft-ndis.png" alt-text="스위치 확장 메뉴에서 Microsoft NDIS를 선택하여 사용하도록 설정하는 스크린샷":::

1. **확인** 을 선택합니다.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>외부 포트에서 미러링 모드 설정

미러링 모드는 새 가상 스위치의 외부 포트에서 원본으로 설정해야 합니다.

외부 원본 포트로 들어오는 트래픽을 대상으로 구성한 가상 네트워크 어댑터로 전달하도록 Hyper-V 가상 스위치(vSwitch_Span)를 구성해야 합니다.

다음 PowerShell 명령을 사용하여 외부 가상 스위치 포트를 원본 미러 모드로 설정합니다.

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| 매개 변수 | Description |
|--|--|
| vSwitch_Span | 새로 추가된 SPAN 가상 스위치 이름 |
| MonitorMode=2 | 원본 |
| MonitorMode=1 | 대상 |
| MonitorMode=0 | 없음 |

다음 PowerShell 명령을 사용하여 모니터링 모드 상태를 확인합니다.

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| 매개 변수 | Description |
|--|--|
| vSwitch_Span | 새로 추가된 SPAN 가상 스위치 이름 |
## <a name="onboard-and-activate-the-virtual-sensor"></a>가상 센서 온보딩 및 활성화

Defender for IoT 센서 사용을 시작하려면 먼저 만든 가상 센서를 Azure 구독에 온보딩하고 센서를 활성화하기 위한 가상 센서의 활성화 파일을 다운로드해야 합니다.

### <a name="onboard-the-virtual-sensor"></a>가상 센서 온보딩

**가상 센서를 온보딩하려면,**

1. [Defender for IoT 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)의 **시작** 페이지로 이동합니다.

1. **온보드 센서** 를 선택합니다.

   :::image type="content" source="media/tutorial-onboarding/onboard-a-sensor.png" alt-text="센서에 대한 온보딩 프로세스를 시작하기 위해 센서를 온보딩하도록 선택하는 스크린샷":::

1. 센서의 이름을 입력합니다.

   센서의 IP 주소를 이름의 일부로 포함하거나 쉽게 식별할 수 있는 이름을 사용하는 것이 좋습니다. 센서 이름을 이 방식으로 지정하면 더 쉽게 추적할 수 있습니다.

1. 드롭다운 메뉴에서 구독을 선택합니다.

    :::image type="content" source="media/tutorial-onboarding/name-subscription.png" alt-text="의미 있는 이름을 입력하고 센서를 구독에 연결하는 스크린샷":::

1. **클라우드 연결** 토글을 사용하여 센서 연결 모드를 선택합니다. 토글이 켜져 있으면 센서가 클라우드에 연결된 것입니다. 토글이 꺼져 있으면 센서는 로컬 관리형입니다.

   - **클라우드 연결 센서**: 센서가 감지하는 정보가 센서 콘솔에 표시됩니다. 경고 정보는 IoT 허브를 통해 전달되며 다른 Azure 서비스(예: Azure Sentinel)와 공유할 수 있습니다. 또한 Azure Defender for IoT 포털에서 센서로 위협 인텔리전스 패키지를 푸시할 수 있습니다. 반대로 센서가 클라우드에 연결되어 있지 않으면 위협 인텔리전스 패키지를 다운로드한 다음 엔터프라이즈 센서에 업로드해야 합니다. Defender for IoT가 패키지를 센서에 푸시하도록 허용하려면 **자동 위협 인텔리전스 업데이트** 토글을 사용하도록 설정합니다. 자세한 내용은 [위협 인텔리전스 연구 및 패키지](how-to-work-with-threat-intelligence-packages.md)를 참조하세요.

      클라우드 연결 센서의 경우 온보딩 중에 정의된 이름은 센서 콘솔에 표시되는 이름입니다. 콘솔에서 직접 이 이름을 변경할 수 없습니다. 로컬 관리형 센서의 경우 온보딩 중에 적용된 이름이 Azure에 저장되지만, 센서 콘솔에서 업데이트할 수 있습니다.

   - **로컬 관리형 센서**: 센서에서 탐지한 정보가 센서 콘솔에 표시됩니다. 에어-갭 네트워크에서 작업 중이고 여러 로컬 관리형 센서가 탐지한 모든 정보를 통합 보기에서 확인하려는 경우에는 온-프레미스 관리 콘솔을 사용합니다.

1. IoT Hub 내에서 센서를 연결할 사이트를 선택합니다. IoT Hub는 이 센서와 Azure Defender for IoT 포털 간에 게이트웨이 역할을 합니다. 표시 이름 및 영역을 정의합니다. 설명 태그를 추가할 수도 있습니다. 표시 이름, 영역 및 태그는 [온보딩된 센서 보기](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors)의 설명 항목입니다.

1. **등록** 을 선택합니다.

### <a name="download-the-sensor-activation-file"></a>센서 활성화 파일 다운로드

센서 등록이 완료되면 센서 활성화 파일을 다운로드할 수 있습니다. 센서 활성화 파일에는 센서의 관리 모드에 대한 지침이 포함되어 있습니다. 다운로드한 활성화 파일은 배포하는 각 센서에 대해 고유합니다. 센서 콘솔에 처음으로 로그인하는 사용자는 활성화 파일을 센서에 업로드합니다.

**활성화 파일을 다운로드하려면:**

1. **센서 온보딩** 페이지에서 **등록** 을 선택합니다.

1. **활성화 파일 다운로드** 를 선택합니다.

1. 처음으로 센서 콘솔에 로그인한 사용자가 파일에 액세스할 수 있도록 설정합니다.

### <a name="sign-in-and-activate-the-sensor"></a>로그인 및 센서 활성화

**로그인 및 활성화하려면**

1. 설치 중에 정의한 IP를 사용하여 브라우저에서 센서 콘솔로 이동합니다.

    :::image type="content" source="media/tutorial-onboarding/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender for IoT 센서의 스크린샷":::

1. 센서 설치 중에 정의한 자격 증명을 입력합니다.

1. 로그인하면 **활성화** 대화 상자가 열립니다. **업로드** 를 선택하고 센서 온보딩 중에 다운로드한 활성화 파일로 이동합니다.

   :::image type="content" source="media/tutorial-onboarding/activation-upload-screen-with-upload-button.png" alt-text="업로드를 선택하고 활성화 파일로 이동하도록 선택한 스크린샷":::

1. 사용 약관에 동의합니다.

1. **활성화** 를 선택합니다. SSL/TLS 인증서 대화 상자가 열립니다.

1. 인증서 이름을 정의합니다.

1. CRT 및 키 파일을 업로드합니다.

1. 암호를 입력하고, 필요한 경우 PEM 파일을 업로드합니다.

1. **다음** 을 선택합니다. 유효성 검사 화면이 열립니다. 기본적으로 관리 콘솔과 연결된 센서 간의 유효성 검사를 사용하도록 설정되어 있습니다.

1. **시스템 차원의 유효성 검사 사용** 토글을 꺼서 유효성 검사를 사용하지 않게 설정합니다. 유효성 검사는 사용하는 것이 좋습니다.

1. **저장** 을 선택합니다.  

CA 서명된 인증서를 업로드한 후에는 화면을 새로 고쳐야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[추가 어플라이언스](how-to-install-software.md#about-defender-for-iot-appliances)를 설정하는 방법을 알아봅니다.
[에이전트 없는 아키텍처](architecture.md)를 참조합니다.
