---
title: Defender for IoT 설치
description: Azure Defender for IoT용 센서 및 온-프레미스 관리 콘솔을 설치하는 방법을 알아봅니다.
ms.date: 10/05/2021
ms.topic: how-to
ms.openlocfilehash: a5b4beff2d01896082974bf761cb7544d439af9e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537305"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT 설치

이 문서에서는 Azure Defender for IoT의 다음 요소를 설치하는 방법을 설명합니다.

- **센서**: Defender for IoT 센서는 수동(에이전트 없는) 모니터링을 사용하여 ICS 네트워크 트래픽을 수집합니다. 수동 및 비간섭 센서는 OT 및 IoT 네트워크와 디바이스에 어떠한 영향도 주지 않습니다. 센서는 SPAN 포트 또는 네트워크 TAP에 연결하고 즉시 네트워크 모니터링을 시작합니다. 감지된 항목이 센서 콘솔에 표시됩니다. 여기에서 네트워크 맵, 디바이스 인벤토리, 광범위한 보고서의 검색 항목을 보고, 조사하고, 분석할 수 있습니다. 예를 들면 위험 평가 보고서, 데이터 마이닝 쿼리, 공격 벡터가 있습니다. [Defender for IoT 센서 사용자 가이드](./getting-started.md)(직접 다운로드)에서 센서 기능에 대해 자세히 알아보세요.

- **온-프레미스 관리 콘솔**: 온-프레미스 관리 콘솔을 사용하여 디바이스 관리, 위험 관리, 취약성 관리를 수행할 수 있습니다. 또한 엔터프라이즈 전체에서 위협 모니터링 및 인시던트 대응을 수행하는 데 사용할 수 있습니다. 센서를 배포한 시설에서 감지된 모든 네트워크 디바이스, 주요 IoT, OT 위험 표시기, 경고의 통합된 보기를 제공합니다. 온-프레미스 관리 콘솔을 사용하여 에어 갭이 있는 네트워크에서 센서를 보고 관리합니다.

이 문서에서는 다음 설치 정보에 대해 설명합니다.

- **하드웨어:** Dell 및 HPE 물리적 어플라이언스 세부 정보.

- **소프트웨어:** 센서 및 온-프레미스 관리 콘솔 소프트웨어 설치.

- **가상 어플라이언스:** 가상 머신 세부 정보 및 소프트웨어 설치.

설치 후 센서를 네트워크에 연결합니다.

## <a name="about-defender-for-iot-appliances"></a>Defender for IoT 어플라이언스 정보

다음 섹션에서는 Defender for IoT 센서 어플라이언스 및 Defender for IoT 온-프레미스 관리 콘솔용 어플라이언스에 대한 정보를 제공합니다.

### <a name="physical-appliances"></a>물리적 어플라이언스

Defender for IoT 어플라이언스 센서는 SPAN 포트 또는 네트워크 TAP에 연결하고, 수동(에이전트 없는) 모니터링을 사용하여 ICS 네트워크 트래픽 수집을 즉시 시작합니다. 이 프로세스는 데이터 경로에 배치되지 않고 OT 디바이스를 적극적으로 검사하지 않으므로 OT 네트워크 및 디바이스에 어떠한 영향도 주지 않습니다.

다음 랙 탑재 어플라이언스를 사용할 수 있습니다.

| **배포 유형** | **회사** | **엔터프라이즈** | **SMB** |**SMB 러기드** |
|--|--|--|--|--|
| **모델** | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| **포트 모니터링** | 최대 15개의 RJ45 또는 8개의 OPT | 최대 8개의 RJ45 또는 6개의 OPT | 최대 4개의 RJ45 | 최대 5개 RJ45 |
| **최대 대역폭\*** | 3Gb/초 | 1Gb/초 | 200Mb/초 | 100MB/초 |
| **최대 보호 디바이스 수** | 30,000 | 15,000 | 1,000 | 800 |

\* 최대 대역폭 용량은 프로토콜 배포에 따라 다를 수 있습니다.

### <a name="virtual-appliances"></a>가상 어플라이언스

다음 가상 어플라이언스를 사용할 수 있습니다.

| **배포 유형** | **회사** | **엔터프라이즈** | **SMB** |
|--|--|--|--|
| **설명** | 기업 배포를 위한 가상 어플라이언스 | 엔터프라이즈 배포용 가상 어플라이언스 | SMB 배포용 가상 어플라이언스 |
| **최대 대역폭\*** | 2.5Gb/초 | 800Mb/초 | 160Mb/초 |
| **최대 보호 디바이스 수** | 30,000 | 10000 | 800 |
| **배포 유형** | 회사 | Enterprise | SMB |

\* 최대 대역폭 용량은 프로토콜 배포에 따라 다를 수 있습니다.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 대한 하드웨어 사양

 | 항목 | Description |
 |----|--|
 **설명** | 다중 계층 아키텍처에서 온-프레미스 관리 콘솔은 지리적으로 분산된 사이트에 대한 표시 여부 및 제어 능력을 제공합니다. SIEM, 티켓 시스템, 차세대 방화벽, 보안 원격 액세스 플랫폼, Defender for IoT ICS 맬웨어 샌드박스를 비롯하여 SOC 보안 스택과 통합됩니다. |
 **배포 유형** | Enterprise |
 **어플라이언스 유형**  | Dell R340, VM |
 **관리형 센서 수** | 제한 없음 |

## <a name="prepare-for-the-installation"></a>설치 준비

### <a name="access-the-iso-installation-image"></a>ISO 설치 이미지에 액세스

설치 이미지는 Defender for IoT 포털에서 액세스할 수 있습니다.

파일에 액세스하려면 다음을 수행합니다.

1. Defender for IoT 계정에 로그인합니다.

1. **네트워크 센서** 또는 **온-프레미스 관리 콘솔** 페이지로 이동하여 다운로드할 버전을 선택합니다.

### <a name="install-from-dvd"></a>DVD에서 설치

설치하기 전에 다음이 있는지 확인합니다.

- USB 커넥터를 사용하는 휴대용 DVD 드라이브

- ISO 설치 관리자 이미지

설치하려면 다음을 수행합니다.

1. 이미지를 DVD로 굽거나 키에 대한 디스크를 준비합니다. 휴대용 DVD 드라이브를 컴퓨터에 연결하고, ISO 이미지를 마우스 오른쪽 단추로 클릭하고, **디스크에 굽기** 를 선택합니다.

1. 키에 대한 DVD 또는 디스크를 연결하고 키에 대한 DVD 또는 디스크에서 부팅하도록 어플라이언스를 구성합니다.

### <a name="install-from-disk-on-a-key"></a>키에 대한 디스크에서 설치

설치하기 전에 다음이 있는지 확인합니다.

- Rufus가 설치되어 있습니다.
  
- USB 버전 3.0 이상을 사용하는 키에 대한 디스크. 최소 크기는 4GB입니다.

- ISO 설치 관리자 이미지 파일.

키에 대한 디스크는 이 프로세스에서 지워집니다.

키에 대한 디스크를 준비하려면 다음을 수행합니다.

1. Rufus를 실행하고 **센서 ISO** 를 선택합니다.

1. 키에 대한 디스크를 전면 패널에 연결합니다.

1. USB에서 부팅할 서버의 BIOS를 설정합니다.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL 설치

Dell 어플라이언스에 소프트웨어를 설치하기 전에 어플라이언스의 BIOS 구성을 조정해야 합니다.

- [Dell PowerEdge R340 전면 패널](#dell-poweredge-r340-front-panel) 및 [Dell PowerEdge R340 후면 패널](#dell-poweredge-r340-back-panel)에는 드라이버 및 포트와 같은 설치에 필요한 정보와 함께 전면 및 후면 패널에 대한 설명이 포함되어 있습니다.

- [Dell BIOS 구성](#dell-bios-configuration)은 Dell 어플라이언스 관리 인터페이스에 연결하고 BIOS를 구성하는 방법에 대한 정보를 제공합니다.

- [소프트웨어 설치(Dell R340)](#software-installation-dell-r340)는 Defender for IoT 센서 소프트웨어를 설치하는 데 필요한 절차를 설명합니다.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL 요구 사항

Dell PowerEdge R340XL 어플라이언스를 설치하려면 다음이 필요합니다.

- Dell 원격 액세스 컨트롤러에 대한 엔터프라이즈 라이선스(iDrac)

- BIOS 구성 XML

- 서버 펌웨어 버전:

  - BIOS 버전 2.1.6

  - iDrac 버전 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 전면 패널

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 전면 패널.":::

 1. 왼쪽 제어판
 1. 광학 드라이브(선택 사항)
 1. 오른쪽 제어판
 1. 정보 태그
 1. 드라이브  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 후면 패널

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 후면 패널.":::

1. 직렬 포트
1. NIC 포트(Gb 1)
1. NIC 포트(Gb 1)
1. 절반 높이 PCIe
1. 전체 높이 PCIe 확장 카드 슬롯
1. 전원 공급 장치 1
1. 전원 공급 장치 2
1. 시스템 ID
1. 시스템 상태 표시기 케이블 포트(CMA) 단추
1. USB 3.0 포트(2)
1. iDRAC9 전용 네트워크 포트
1. VGA 포트

### <a name="dell-bios-configuration"></a>Dell BIOS 구성

소프트웨어를 사용하기 위해 Dell 어플라이언스를 조정하려면 Dell BIOS 구성이 필요합니다.

Dell 어플라이언스는 LC(수명 주기 컨트롤러)와 통합된 iDRAC에서 관리합니다. LC는 모든 Dell PowerEdge 서버에 포함되어 있으며, Dell PowerEdge 어플라이언스를 배포, 업데이트, 모니터링, 유지 관리하는 데 도움이 되는 기능을 제공합니다.

Dell 어플라이언스와 관리 컴퓨터 간에 통신을 설정하려면 동일한 서브넷에서 iDRAC IP 주소와 관리 컴퓨터의 IP 주소를 정의해야 합니다.

연결이 설정되면 BIOS를 구성할 수 있습니다.

**DELL BIOS를 구성 하려면**:

1. [iDRAC IP 주소 구성](#configure-idrac-ip-address)

1. [BIOS 구성](#configuring-the-bios)

#### <a name="configure-idrac-ip-address"></a>iDRAC IP 주소 구성

1. 센서 전원을 켭니다.

1. OS가 이미 설치되어 있는 경우 F2 키를 선택하여 BIOS 구성을 입력합니다.

1. **iDRAC 설정** 을 선택합니다.

1. **네트워크** 를 선택합니다.

   > [!NOTE]
   > 설치하는 동안 다음 단계에서 설명하는 기본 iDRAC IP 주소와 암호를 구성해야 합니다. 설치 후에 이러한 정의를 변경합니다.

1. 고정 IPv4 주소를 **10.100.100.250** 으로 변경합니다.

1. 고정 서브넷 마스크를 **255.255.255.0** 으로 변경합니다.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="고정 서브넷 마스크를 보여 주는 스크린샷.":::

1. **뒤로** > **마침** 을 선택합니다.

#### <a name="configuring-the-bios"></a>BIOS 구성

다음과 같은 경우 어플라이언스 BIOS를 구성 해야 합니다.

- 화살표를 통해 어플라이언스를 구입하지 않았습니다.

- 어플라이언스가 있지만 XML 구성 파일에 대한 액세스 권한이 없습니다.

BIOS에 액세스한 후 **디바이스 설정** 으로 이동합니다.

**BIOS를 구성 하려면**:

1. 키보드 및 화면을 사용 하 여 직접 어플라이언스의 BIOS에 액세스 하거나 iDRAC를 사용 합니다.

   - 어플라이언스가 Defender for IoT 어플라이언스가 아닌 경우 브라우저를 열고 이전에 구성한 IP 주소로 이동합니다. Dell 기본 관리자 권한으로 로그인합니다. 사용자 이름에 **root** 를 사용하고 암호에 **calvin** 을 사용합니다.

   - 어플라이언스가 Defender for IoT 어플라이언스인 경우에는 사용자 이름에 **XXX** 를 사용하고 암호에 **XXX** 를 사용하여 로그인합니다.

1. BIOS에 액세스한 후 **디바이스 설정** 으로 이동합니다.

1. **Integrated RAID Controller 1: Dell PERC\<PERC H330 Adapter\> 구성 유틸리티** 를 선택하여 RAID 제어 구성을 선택합니다.

1. **구성 관리** 를 선택합니다.

1. **가상 디스크 만들기** 를 선택합니다.

1. **RAID 수준 선택** 필드에서 **RAID5** 를 선택합니다. **가상 디스크 이름** 필드에 **ROOT** 를 입력하고 **실제 디스크** 를 선택합니다.

1. **모두 선택** 을 선택하고 **변경 내용 적용** 을 선택합니다.

1. **확인** 을 선택합니다.

1. 아래로 스크롤하여 **가상 디스크 만들기** 를 선택합니다.

1. **확인** 확인란을 선택하고 **예** 를 선택합니다.

1. **확인** 을 선택합니다.

1. 주 화면으로 돌아가서 **시스템 BIOS** 를 선택합니다.

1. **부팅 설정** 을 선택합니다.

1. **부팅 모드** 옵션에서 **BIOS** 를 선택합니다.

1. **뒤로** 를 선택하고 **마침** 을 선택하여 BIOS 설정을 종료합니다.

### <a name="software-installation-dell-r340"></a>소프트웨어 설치(Dell R340)

이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

설치하려면 다음을 수행합니다.

1. 다음 방법 중 하나로 버전 미디어가 어플라이언스에 탑재되었는지 확인합니다.

   - 키에 대한 외부 CD 또는 디스크를 릴리스와 연결합니다.

   - iDRAC을 사용하여 ISO 이미지를 탑재합니다. iDRAC에 로그인한 후 가상 콘솔을 선택하고 **가상 미디어** 를 선택합니다.

1. **CD/DVD 매핑** 섹션에서 **파일 선택** 을 선택합니다.

1. 열리는 대화 상자에서 이 버전에 대한 버전 ISO 이미지 파일을 선택합니다.

1. **디바이스 매핑** 단추를 선택합니다.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="매핑된 디바이스를 보여 주는 스크린샷.":::

1. 미디어가 탑재됩니다. **닫기** 를 선택합니다.

1. 어플라이언스를 시작합니다. iDRAC를 사용하는 경우 **Consul 컨트롤** 단추를 선택하여 서버를 다시 시작할 수 있습니다. 그런 다음 **키보드 매크로** 에서 **적용** 단추를 선택하면 Ctrl+Alt+Delete 시퀀스가 시작됩니다.

1. **영어** 를 선택합니다.

1. **SENSOR-RELEASE-\<version\> Enterprise** 를 선택합니다.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="센서 버전 및 엔터프라이즈 유형을 선택합니다.":::

1. 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="어플라이언스 프로필 및 네트워크 속성을 보여 주는 스크린샷.":::

   | 매개 변수 | 구성 |
   |--|--|
   | **하드웨어 프로필** | **enterprise** |
   | **관리 인터페이스** | **eno1** |
   | **네트워크 매개 변수(고객이 제공)** | - |
   |**관리 네트워크 IP 주소:** | - |
   | **서브넷 마스크:** | - |
   | **어플라이언스 호스트 이름:** | - |
   | **DNS:** | - |
   | **기본 게이트웨이 IP 주소:** | - |
   | **입력 인터페이스:** |  시스템에서 입력 인터페이스 목록을 생성합니다. 입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용하여 목록에 표시된 모든 항목을 복사합니다. 브리지 인터페이스를 구성하지 않아도 됩니다. 이 옵션은 특별한 사용 사례로만 사용됩니다. |

1. 약 10분 후에 두 개의 자격 증명 집합이 표시됩니다. 하나는 **CyberX** 사용자를 위한 것이고, 다른 하나는 **지원** 사용자를 위한 것입니다.  

1. 어플라이언스 ID와 암호를 저장합니다. 플랫폼에 처음 액세스하여 사용하는 경우 자격 증명이 필요합니다.

1. 계속하려면 **Enter** 키를 선택합니다.

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 설치

이 섹션에서는 다음 단계를 포함하여 HPE ProLiant DL20 설치 프로세스에 대해 설명합니다.

- 원격 액세스를 사용하도록 설정하고 기본 관리자 암호를 업데이트합니다.
- BIOS 및 RAID 설정을 구성합니다.
- 소프트웨어를 설치합니다.

### <a name="about-the-installation"></a>설치 정보

- 엔터프라이즈 및 SMB 어플라이언스를 설치할 수 있습니다. 설치 프로세스는 배열 구성을 제외하고 두 어플라이언스 유형 모두 동일합니다.
- 기본 관리자가 제공됩니다. 네트워크 구성 프로세스 중에 암호를 변경하는 것이 좋습니다.
- 네트워크 구성 프로세스 중에 네트워크 포트 1에서 iLO 포트를 구성합니다.
- 이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 전면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 전면 패널.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 후면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20의 후면 패널.":::

### <a name="enable-remote-access-and-update-the-password"></a>원격 액세스를 사용하도록 설정하고 암호 업데이트

다음 절차를 사용하여 네트워크 옵션을 설정하고 기본 암호를 업데이트합니다.

암호를 사용하도록 설정하고 업데이트하려면 다음을 수행합니다.

1. HP 어플라이언스에 화면과 키보드를 연결하고, 어플라이언스를 켜고, **F9** 키를 누릅니다.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="HPE ProLiant 창을 보여 주는 스크린샷.":::

1. **시스템 유틸리티** > **시스템 구성** > **iLO 5 구성 유틸리티** > **네트워크 옵션** 으로 이동합니다.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="시스템 구성 창을 보여 주는 스크린샷.":::

    1. **네트워크 인터페이스 어댑터** 필드에서 **공유 네트워크 포트-LOM** 을 선택합니다.

    1. DHCP를 사용하지 않도록 설정합니다.

    1. IP 주소, 서브넷 마스크, 게이트웨이 IP 주소를 입력합니다.

1. **F10: 저장** 을 선택합니다.

1. **Esc** 키를 선택하여 **iLO 5 구성 유틸리티** 로 돌아간 다음 **사용자 관리** 를 선택합니다.

1. **사용자 편집/제거** 를 선택합니다. 관리자는 정의된 유일한 기본 사용자입니다.

1. 기본 암호를 변경하고 **F10: 저장** 을 선택합니다.

### <a name="configure-the-hpe-bios"></a>HPE BIOS 구성

다음 절차에서는 엔터프라이즈 및 SMB 어플라이언스에 대해 HPE BIOS를 구성하는 방법을 설명합니다.

**HPE BIOS를 구성 하려면**:

1. **시스템 유틸리티** > **시스템 구성** > **BIOS/플랫폼 구성(RBSU)** 을 선택합니다.

1. **BIOS/플랫폼 구성(RBSU)** 양식에서 **부팅 옵션** 을 선택합니다.

1. **부팅 모드** 를 **레거시 BIOS 모드** 로 변경한 다음 **F10: 저장** 을 선택합니다.

1. **Esc** 키를 두 번 선택하여 **시스템 구성** 양식을 닫습니다.

#### <a name="for-the-enterprise-appliance"></a>엔터프라이즈 어플라이언스의 경우

1. **Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10** > **배열 구성** > **배열 만들기** 를 선택합니다.

1. **배열 만들기** 양식에서 모든 옵션을 선택합니다. **엔터프라이즈** 어플라이언스에 세 가지 옵션을 사용할 수 있습니다.

#### <a name="for-the-smb-appliance"></a>SMB 어플라이언스의 경우

1. **Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10** > **배열 구성** > **배열 만들기** 를 선택합니다.

1. **다음 양식으로 이동** 을 선택합니다.

1. **RAID 수준 설정** 양식에서 엔터프라이즈 배포의 경우 이 수준을 **RAID 5** 로, SMB 배포의 경우 **RAID 1** 로 설정합니다.

1. **다음 양식으로 이동** 을 선택합니다.

1. **논리 드라이브 레이블** 양식에 **논리 드라이브 1** 을 입력합니다.

1. **변경 내용 제출** 을 선택합니다.

1. **제출** 양식에서 **주 메뉴로 돌아가기** 를 선택합니다.

1. **F10: 저장** 을 선택하고 **Esc** 키를 두 번 누릅니다.

1. **시스템 유틸리티** 창에서 **일회성 부팅 메뉴** 를 선택합니다.

1. **일회성 부팅 메뉴** 양식에서 **레거시 BIOS 일회성 부팅 메뉴** 를 선택합니다.

1. **레거시로 부팅** 및 **부팅 재정의** 창이 나타납니다. 부팅 재정의 옵션을 선택합니다(예: CD-ROM, USB, HDD 또는 UEFI 셸).

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="첫 번째 부팅 재정의 창을 보여 주는 스크린샷.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="두 번째 부팅 재정의 창을 보여 주는 스크린샷.":::

### <a name="software-installation-hpe-proliant-dl20-appliance"></a>소프트웨어 설치(HPE ProLiant DL20 어플라이언스)

이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

소프트웨어를 설치하려면 다음을 수행합니다.

1. 화면과 키보드를 어플라이언스에 연결한 다음 CLI에 연결합니다.

1. Defender for IoT 포털의 **업데이트** 페이지에서 다운로드한 ISO 이미지와 키의 외부 CD 또는 디스크를 연결합니다.

1. 어플라이언스를 시작합니다.

1. **영어** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="CLI 창에서 영어 선택.":::

1. **SENSOR-RELEASE-\<version> Enterprise** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="버전을 선택하는 화면의 스크린샷.":::

1. 설치 마법사에서 하드웨어 프로필 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="설치 마법사를 보여 주는 스크린샷.":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | SMB 배포용 **Enterprise** 또는 **Office** 를 선택합니다. |
    | **관리 인터페이스** | **eno2** |
    | **기본 네트워크 매개 변수(일반적으로 고객이 매개 변수 제공)** | **관리 네트워크 IP 주소:** <br/> <br/>**어플라이언스 호스트 이름:** <br/>**DNS:** <br/>**기본 게이트웨이 IP 주소:**|
    | **입력 인터페이스:** | 시스템에서 입력 인터페이스 목록을 생성합니다.<br/><br/>입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용하여 목록에 표시된 모든 항목을 복사합니다(**eno5, eno3, eno1, eno6, eno4**).<br/><br/>**HPE DL20의 경우: eno1, enp1s0f4u4(iLo 인터페이스)를 나열하지 않습니다.**<br/><br/>**BRIDGE**: 브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별한 사용 사례로만 사용됩니다. **Enter** 키를 눌러 계속 진행합니다. |

1. 약 10분 후에 두 개의 자격 증명 집합이 표시됩니다. 하나는 **CyberX** 사용자를 위한 것이고, 다른 하나는 **지원** 사용자를 위한 것입니다.

1. 어플라이언스의 ID와 암호를 저장합니다. 플랫폼에 처음 액세스하는 경우 자격 증명이 필요합니다.

1. 계속하려면 **Enter** 키를 선택합니다.

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 설치

- 기본 관리자가 제공됩니다. 네트워크 구성 중에 암호를 변경하는 것이 좋습니다.

- 네트워크를 구성하는 동안 iLO 포트를 구성합니다.

- 이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 전면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 전면 패널.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 후면 패널

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 후면 패널.":::

### <a name="enable-remote-access-and-update-the-password"></a>원격 액세스를 사용하도록 설정하고 암호 업데이트

HPE ProLiant DL20 설치에 대한 이전 섹션을 참조하세요.

- "원격 액세스를 사용하도록 설정하고 암호 업데이트"

- "HPE BIOS 구성"

엔터프라이즈 구성이 동일합니다.

> [!Note]
> 배열 형식에서 모든 옵션을 선택했는지 확인합니다.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO 원격 설치(가상 드라이브에서)

이 절차에서는 가상 드라이브에서의 iLO 설치에 대해 설명합니다.

설치하려면 다음을 수행합니다.

1. iLO 콘솔에 로그인한 다음 서버 화면을 마우스 오른쪽 단추로 클릭합니다.

1. **HTML5 콘솔** 을 선택합니다.

1. 콘솔에서 CD 아이콘을 선택하고 CD/DVD 옵션을 선택합니다.

1. **로컬 ISO 파일** 을 선택합니다.

1. 대화 상자에서 관련 ISO 파일을 선택합니다.

1. 왼쪽 아이콘으로 이동하여 **전원** 을 선택하고 **다시 설정** 을 선택합니다.

1. 어플라이언스가 다시 시작되고 센서 설치 프로세스를 실행합니다.

### <a name="software-installation-hpe-dl360"></a>소프트웨어 설치(HPE DL360)

이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

설치하려면 다음을 수행합니다.

1. 화면과 키보드를 어플라이언스에 연결한 다음 CLI에 연결합니다.

1. Defender for IoT 포털의 **업데이트** 페이지에서 다운로드한 ISO 이미지와 키에 대한 외부 CD 또는 디스크를 연결합니다.

1. 어플라이언스를 시작합니다.

1. **영어** 를 선택합니다.

1. **SENSOR-RELEASE-\<version> Enterprise** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="버전 선택을 보여 주는 스크린샷.":::

1. 설치 마법사에서 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="설치 마법사를 보여 주는 스크린샷.":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | **회사** 를 선택합니다. |
    | **관리 인터페이스** | **eno2** |
    | **기본 네트워크 매개 변수(고객이 제공)** | **관리 네트워크 IP 주소:** <br> **서브넷 마스크:** <br/>**어플라이언스 호스트 이름:** <br/>**DNS:** <br/>**기본 게이트웨이 IP 주소:**|
    | **입력 인터페이스:**  | 시스템에서 입력 인터페이스 목록을 생성합니다.<br/><br/>입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용하여 목록에 표시된 모든 항목을 복사합니다.<br/><br/> 브리지 인터페이스를 구성하지 않아도 됩니다. 이 옵션은 특별한 사용 사례로만 사용됩니다. |

1. 약 10분 후에 두 개의 자격 증명 집합이 표시됩니다. 하나는 **CyberX** 사용자를 위한 것이고, 다른 하나는 **지원** 사용자를 위한 것입니다.

1. 어플라이언스의 ID와 암호를 저장합니다. 플랫폼에 처음 액세스하는 경우 자격 증명이 필요합니다.

1. 계속하려면 **Enter** 키를 선택합니다.

## <a name="hp-edgeline-300-installation"></a>HP EdgeLine 300 설치

- 기본 관리자가 제공됩니다. 네트워크 구성 중에 암호를 변경하는 것이 좋습니다.

- 이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

### <a name="hp-edgeline-300-back-panel"></a>HP EdgeLine 300 후면 패널

:::image type="content" source="media/tutorial-install-components/edgeline-el300-panel.png" alt-text="EL300의 후면 패널 보기":::

### <a name="enable-remote-access"></a>원격 액세스 사용

1. 웹 브라우저에 iSM IP 주소를 입력합니다.

1. 어플라이언스에 있는 기본 사용자 이름 및 암호를 사용하여 로그인합니다.

1. **유선 및 무선 네트워크** > **IPV4** 로 이동합니다

    :::image type="content" source="media/tutorial-install-components/wired-and-wireless.png" alt-text="강조 표시된 섹션으로 이동합니다.":::

1. **DHCP 토글** 을 해제합니다.

1. IPv4 주소를 다음과 같이 구성합니다.
    - **IPV4 주소**: `192.168.1.125`
    - **IPV4 서브넷 마스크**: `255.255.255.0`
    - **IPV4 게이트웨이**: `192.168.1.1`

1. **적용** 을 선택합니다.

1. 로그아웃하고 어플라이언스를 다시 부팅합니다.

### <a name="configure-the-bios"></a>BIOS 구성

다음 절차에서는 HP EL300 어플라이언스에 대한 BIOS를 구성하는 방법을 설명합니다.

**BIOS를 구성 하려면**:

1. 어플라이언스를 켜고 **F9** 키를 눌러 BIOS를 시작합니다.

1. **고급** 을 선택하고 아래로 스크롤하여 **CSM 지원** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/csm-support.png" alt-text="CSM 지원을 활성화하여 추가 메뉴를 엽니다.":::

1. **엔터** 를 눌러 CSM 지원을 활성화합니다.

1. **스토리지** 로 이동하여 **+/-** 를 눌러 레거시로 변경합니다.

1. **비디오** 로 이동하여 **+/-** 를 눌러 레거시로 변경합니다.

    :::image type="content" source="media/tutorial-install-components/storage-and-video.png" alt-text="스토리지 및 비디오로 이동하여 레거시로 변경합니다.":::

1. **부팅** > **부팅 모드 선택** 으로 이동합니다.

1. **+/-** 을 눌러 레거시로 변경합니다.

    :::image type="content" source="media/tutorial-install-components/boot-mode.png" alt-text="부팅 모드 선택을 레거시로 변경합니다.":::

1. **저장 및 종료** 로 이동합니다.

1. **변경 내용 저장 및 종료** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/save-and-exit.png" alt-text="변경 내용을 저장하고 시스템을 종료합니다.":::

1. **예** 를 선택하면 어플라이언스 다시 부팅됩니다.

1. **F11** 을 눌러 **부팅 메뉴** 에 진입합니다.

1. 센서 이미지가 있는 디바이스를 선택합니다. **DVD** 또는 **USB** 중 하나.

1. 언어를 선택합니다.

1. **sensor-10.0.3.12-62a2a3f724 Office: 4 CPUS, 8GB RAM, 100GB STORAGE** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-select-screen.png" alt-text="표시된 대로 센서 버전을 선택합니다.":::

1. 설치 마법사에서 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/appliance-parameters.png" alt-text="다음 매개 변수를 사용하여 어플라이언스의 프로필 및 네트워크 구성을 정의합니다.":::

    | 매개 변수 | 구성 |
    |--|--|
    | **하드웨어 프로필 구성** | **오피스** |
    | **관리 네트워크 인터페이스 구성** | **enp3s0** <br /> 또는 <br />**가능한 값** |
    | **관리 네트워크 IP 주소 구성:** | **고객이 제공한 IP 주소** |
    | **서브넷 마스크 구성:** | **고객이 제공한 IP 주소** |
    | **DNS 구성:** | **고객이 제공한 IP 주소** |
    | **기본 게이트웨이 IP 주소 구성:** | **고객이 제공한 IP 주소** |
    | **입력 인터페이스 구성** | **enp4s0** <br /> 또는 <br />**가능한 값** |
    | **브리지 인터페이스 구성** | 해당 없음 |

1. 설정을 적용하고 `Y`을 입력하여 계속합니다.

## <a name="sensor-installation-for-the-virtual-appliance"></a>가상 어플라이언스에 대한 센서 설치

다음 아키텍처에서 Defender for IoT 센서에 대한 가상 머신을 배포할 수 있습니다.

| Architecture | 사양 | 사용량 | 주석 |
|---|---|---|---|
| **엔터프라이즈** | CPU: 8<br/>메모리: 32G RAM<br/>HDD: 1800GB | 프로덕션 환경 | 기본 및 가장 일반적으로 사용 |
| **중소기업** | CPU: 4 <br/>메모리: 8G RAM<br/>HDD: 500GB | 테스트 또는 소규모 프로덕션 환경 | -  |
| **Office** | CPU: 4<br/>메모리: 8G RAM<br/>HDD: 100GB | 소규모 테스트 환경 | -  |

### <a name="prerequisites"></a>사전 요구 사항

온-프레미스 관리 콘솔은 VMware 및 Hyper-V 배포 옵션을 모두 지원합니다. 설치를 시작하기 전에 다음 항목을 갖추고 있는지 확인합니다.

- VMware(ESXi 5.5 이상) 또는 Hyper-V 하이퍼바이저(Windows 10 Pro 또는 Enterprise)가 설치되어 있고 작동함

- 가상 머신에 사용 가능한 하드웨어 리소스

- Azure Defender for IoT 센서용 ISO 설치 파일

하이퍼바이저가 실행 중인지 확인합니다.

### <a name="create-the-virtual-machine-esxi"></a>가상 머신 만들기(ESXi)

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

1. 필요한 아키텍처에 따라 가상 하드웨어 매개 변수를 변경합니다.

1. **CD/DVD 드라이브 1** 에 대해 **데이터 저장소 ISO 파일** 을 선택하고 이전에 업로드한 ISO 파일을 선택합니다.

1. **다음** > **마침** 을 선택합니다.

### <a name="create-the-virtual-machine-hyper-v"></a>가상 머신 만들기(Hyper-V)

이 절차에서는 Hyper-V를 사용하여 가상 머신을 만드는 방법을 설명합니다.

가상 머신을 만들려면 다음을 수행합니다.

1. Hyper-V 관리자에서 가상 디스크를 만듭니다.

1. **형식 = VHDX** 를 선택합니다.

1. **유형 = 동적 확장** 을 선택합니다.

1. VHD의 이름과 위치를 입력합니다.

1. 아키텍처에 따라 필요한 크기를 입력합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. **작업** 메뉴에서 새 가상 머신을 만듭니다.

1. 가상 컴퓨터의 이름을 입력합니다.

1. **세대 지정** > **1세대** 를 선택합니다.

1. 아키텍처에 따라 메모리 할당을 지정하고 동적 메모리의 확인란을 선택합니다.

1. 서버 네트워크 토폴로지에 따라 네트워크 어댑터를 구성합니다.

1. 이전에 만든 VHDX를 가상 머신에 연결합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. 새 가상 머신을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택합니다.

1. **하드웨어 추가** 를 선택하고 새 네트워크 어댑터를 추가합니다.

1. 센서 관리 네트워크에 연결되는 가상 스위치를 선택합니다.

1. 아키텍처에 따라 CPU 리소스를 할당합니다.

1. 관리 콘솔의 ISO 이미지를 가상 DVD 드라이브에 연결합니다.

1. 가상 머신을 시작합니다.

1. **작업** 메뉴에서 **연결** 을 선택하여 소프트웨어 설치를 계속합니다.

### <a name="software-installation-esxi-and-hyper-v"></a>소프트웨어 설치(ESXi 및 Hyper-V)

이 섹션에서는 ESXi 및 Hyper-V 소프트웨어 설치에 대해 설명합니다.

설치하려면 다음을 수행합니다.

1. 가상 머신 콘솔을 엽니다.

1. ISO 이미지에서 VM이 시작되고 언어 선택 화면이 표시됩니다. **영어** 를 선택합니다.

1. 필요한 아키텍처를 선택합니다.

1. 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | &lt;필수 아키텍처&gt; |
    | **관리 인터페이스** | **ens192** |
    | **네트워크 매개 변수(고객이 제공)** | **관리 네트워크 IP 주소:** <br/>**서브넷 마스크:** <br/>**어플라이언스 호스트 이름:** <br/>**DNS:** <br/>**기본 게이트웨이:** <br/>**입력 인터페이스:**|
    | **브리지 인터페이스:** | 브리지 인터페이스를 구성할 필요가 없습니다. 이 옵션은 특별한 사용 사례용입니다. |

1. 설정을 적용하려면 **Y** 를 입력합니다.

1. 로그인 자격 증명은 자동으로 생성되고 표시됩니다. 사용자 이름 및 암호는 로그인 및 관리에 필요하므로 안전한 곳에 복사합니다.

      - **지원**: 사용자 관리를 위한 관리자입니다.

      - **CyberX**: 어플라이언스에 액세스하기 위한 루트와 동일합니다.

1. 어플라이언스가 다시 시작됩니다.

1. 이전에 구성한 IP 주소(`https://ip_address`)를 통해 관리 콘솔에 액세스합니다.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="관리 콘솔에 대한 액세스를 보여 주는 스크린샷.":::

## <a name="on-premises-management-console-installation"></a>온-프레미스 관리 콘솔 설치

어플라이언스에 소프트웨어를 설치하기 전에 어플라이언스의 BIOS 구성을 조정해야 합니다.

### <a name="bios-configuration"></a>BIOS 구성

**어플라이언스에 대한 BIOS를 구성하려면:**

1. [원격 액세스 활성화 및 암호 업데이트](#enable-remote-access-and-update-the-password).

1. [BIOS 구성](#configure-the-hpe-bios).

### <a name="software-installation"></a>소프트웨어 설치

이 설치 프로세스는 20분 정도 걸립니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

설치 과정에서 보조 NIC를 추가할 수 있습니다. 설치하는 동안 보조 NIC를 설치하지 않도록 선택하는 경우, 나중에 [보조 NIC를 추가](#add-a-secondary-nic)할 수 있습니다.

소프트웨어를 설치하려면 다음을 수행합니다.

1. 설치 프로세스에 대한 기본 설정 언어를 선택합니다.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="설치 프로세스에 대한 기본 설정 언어를 선택합니다.":::

1. **MANAGEMENT-RELEASE-\<version\>\<deployment type\>** 를 선택합니다.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="버전을 선택합니다.":::

1. 설치 마법사에서 네트워크 속성을 정의합니다.

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="어플라이언스 프로필을 보여 주는 스크린샷.":::

   | 매개 변수 | 구성 |
   |--|--|
   | **관리 네트워크 인터페이스 구성** | Dell의 경우: **eth0, eth1** <br /> HP의 경우: **enu1, enu2** <br>  또는 <br />**가능한 값** |
   | **관리 네트워크 IP 주소 구성:** | **고객이 제공한 IP 주소** |
   | **서브넷 마스크 구성:** | **고객이 제공한 IP 주소** |
   | **DNS 구성:** | **고객이 제공한 IP 주소** |
   | **기본 게이트웨이 IP 주소 구성:** | **고객이 제공한 IP 주소** |

1. **(선택 사항)** 보조 NIC(네트워크 인터페이스 카드)를 설치하려면 다음 어플라이언스 프로필 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="보조 NIC 설치 질문을 보여 주는 스크린샷.":::

   | 매개 변수 | 구성 |
   |--|--|
   | **센서 모니터링 인터페이스 구성(선택 사항):** | **eth1** 또는 **가능한 값** |
   | **센서 모니터링 인터페이스에 대한 IP 주소를 구성합니다.** | **고객이 제공한 IP 주소** |
   | **센서 모니터링 인터페이스에 대한 서브넷 마스크를 구성합니다.** | **고객이 제공한 IP 주소** |

1. 설정을 수락하고 `Y`을 입력하여 계속 진행합니다.

1. 약 10분 후에 두 개의 자격 증명 집합이 표시됩니다. 하나는 **CyberX** 사용자를 위한 것이고, 다른 하나는 **지원** 사용자를 위한 것입니다.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="이러한 자격 증명은 다시 나타나지 않기 때문에 복사합니다.":::  

   사용자 이름 및 암호를 저장합니다. 처음 사용할 때 플랫폼에 액세스하려면 이러한 자격 증명이 필요합니다.

1. 계속하려면 **Enter** 키를 선택합니다.

어플라이언스에서 실제 포트를 찾는 방법에 대한 자세한 내용은 [포트 찾기](#find-your-port)를 참조하세요.

### <a name="add-a-secondary-nic"></a>보조 NIC 추가

보조 NIC를 추가하여 온-프레미스 관리 콘솔에 대한 보안을 강화할 수 있습니다. 보조 NIC를 추가하면 하나는 사용자 전용으로 사용되고, 다른 하나는 라우팅된 네트워크용 게이트웨이의 구성을 지원합니다. 두 번째 NIC는 IP 주소 범위 내에서 연결된 모든 센서 전용입니다.

:::image type="content" source="media/tutorial-install-components/secondary-nic.png" alt-text="보조 NIC의 전체 아키텍처.":::

두 NIC 모두 UI(사용자 인터페이스)를 지원합니다.
보조 NIC를 배포하지 않도록 선택하면 주 NIC를 통해 모든 기능을 사용할 수 있습니다.

온-프레미스 관리 콘솔을 이미 구성했고 온-프레미스 관리 콘솔에 보조 NIC를 추가하려는 경우 다음 단계를 수행합니다.

1. 네트워크 재구성 명령을 사용합니다.

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. 다음 질문에 대한 다음 응답을 입력합니다.

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="다음 답변을 입력하여 어플라이언스를 구성합니다.":::

    | 매개 변수 | Enter에 대한 응답 |
    |--|--|
    | **관리 네트워크 IP 주소** | `N` |
    | **서브넷 마스크** | `N` |
    | **DNS** | `N` |
    | **기본 게이트웨이 IP 주소** | `N` |
    | **센서 모니터링 인터페이스 (선택 사항으로, 센서가 다른 네트워크 세그먼트에 있는 경우에 적용할 수 있습니다. 자세한 내용은 설치 지침을 참조하세요)**| `Y`, **가능한 값 선택** |
    | **센서 모니터링 인터페이스의 IP 주소(센서에서 액세스할 수 있음)** | `Y`, **고객이 제공한 IP 주소**|
    | **센서 모니터링 인터페이스(센서에서 액세스할 수 있음)용 서브넷 마스크** | `Y`, **고객이 제공한 IP 주소** |
    | **호스트 이름** | **고객 제공** |

1. 모든 선택 항목을 검토하고 `Y`를 입력하여 변경 내용을 적용합니다. 시스템이 재부팅됩니다.

### <a name="find-your-port"></a>포트 찾기

장치에서 실제 포트를 찾는 데 문제가 있는 경우 다음 명령을 사용하여 작업을 수행할 수 있습니다.

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

이 명령을 수행하면 지정된 기간 동안 포트의 표시등이 깜박입니다. 예를 들어 `sudo ethtool -p eno1 120`을 입력하면 2분간 포트 eno1 플래시를 통해 어플라이언스 후면의 포트를 찾을 수 있습니다.

## <a name="virtual-appliance-on-premises-management-console-installation"></a>가상 어플라이언스: 온-프레미스 관리 콘솔 설치

온-프레미스 관리 콘솔 VM은 다음 아키텍처를 지원합니다.

| Architecture | 사양 | 사용량 |
|--|--|--|
| Enterprise <br/>(기본 및 가장 일반적으로 사용) | CPU: 8 <br/>메모리: 32G RAM<br/> HDD: 1.8TB | 대규모 프로덕션 환경 |
| Small | CPU: 4 <br/> 메모리: 8G RAM<br/> HDD: 500GB | 대규모 프로덕션 환경 |
| Office | CPU: 4 <br/>메모리: 8G RAM <br/> HDD: 100GB | 소규모 테스트 환경 |

### <a name="prerequisites"></a>사전 요구 사항

온-프레미스 관리 콘솔은 VMware 및 Hyper-V 배포 옵션을 모두 지원합니다. 설치를 시작하기 전에 다음을 확인합니다.

- VMware(ESXi 5.5 이상) 또는 Hyper-V 하이퍼바이저(Windows 10 Pro 또는 Enterprise)가 설치되어 있고 작동합니다.

- 가상 머신에 하드웨어 리소스를 사용할 수 있습니다.

- 온-프레미스 관리 콘솔용 ISO 설치 파일이 있습니다.

- 하이퍼바이저가 실행 중입니다.

### <a name="create-the-virtual-machine-esxi"></a>가상 머신 만들기(ESXi)

가상 머신(ESXi)을 만들려면 다음을 수행합니다.

1. ESXi에 로그인하고, 관련 **데이터 저장소** 를 선택하고, **데이터 저장소 브라우저** 를 선택합니다.

1. 이미지를 업로드하고 **닫기** 를 선택합니다.

1. **가상 머신** 으로 이동합니다.

1. **VM 만들기/등록** 을 선택합니다.

1. **새 가상 머신 만들기** 를 선택하고 **다음** 을 선택합니다.

1. 센서 이름을 추가하고 다음을 선택합니다.

   - 호환성: \<latest ESXi version>

   - 게스트 OS 제품군: Linux

   - 게스트 OS 버전: Ubuntu Linux(64비트)

1. **다음** 을 선택합니다.

1. 관련 데이터 저장소를 선택하고 **다음** 을 선택합니다.

1. 필요한 아키텍처에 따라 가상 하드웨어 매개 변수를 변경합니다.

1. **CD/DVD 드라이브 1** 에 대해 **데이터 저장소 ISO 파일** 을 선택하고 이전에 업로드한 ISO 파일을 선택합니다.

1. **다음** > **마침** 을 선택합니다.

### <a name="create-the-virtual-machine-hyper-v"></a>가상 머신 만들기(Hyper-V)

Hyper-V를 사용하여 가상 머신을 만들려면 다음을 수행합니다.

1. Hyper-V 관리자에서 가상 디스크를 만듭니다.

1. **VHDX** 형식을 선택합니다.

1. **다음** 을 선택합니다.

1. **동적 확장** 유형을 선택합니다.

1. **다음** 을 선택합니다.

1. VHD의 이름과 위치를 입력합니다.

1. **다음** 을 선택합니다.

1. 아키텍처에 따라 필요한 크기를 입력합니다.

1. **다음** 을 선택합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. **작업** 메뉴에서 새 가상 머신을 만듭니다.

1. **다음** 을 선택합니다.

1. 가상 컴퓨터의 이름을 입력합니다.

1. **다음** 을 선택합니다.

1. **세대** 를 선택하고 **1 세대** 로 설정합니다.

1. **다음** 을 선택합니다.

1. 아키텍처에 따라 메모리 할당을 지정하고 동적 메모리의 확인란을 선택합니다.

1. **다음** 을 선택합니다.

1. 서버 네트워크 토폴로지에 따라 네트워크 어댑터를 구성합니다.

1. **다음** 을 선택합니다.

1. 이전에 만든 VHDX를 가상 머신에 연결합니다.

1. **다음** 을 선택합니다.

1. 요약을 검토하고 **마침** 을 선택합니다.

1. 새 가상 머신을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택합니다.

1. **하드웨어 추가** 를 선택하고 **네트워크 어댑터** 용 새 어댑터를 추가합니다.

1. **가상 스위치** 에 대해 센서 관리 네트워크에 연결되는 스위치를 선택합니다.

1. 아키텍처에 따라 CPU 리소스를 할당합니다.

1. 관리 콘솔의 ISO 이미지를 가상 DVD 드라이브에 연결합니다.

1. 가상 머신을 시작합니다.

1. **작업** 메뉴에서 **연결** 을 선택하여 소프트웨어 설치를 계속합니다.

### <a name="software-installation-esxi-and-hyper-v"></a>소프트웨어 설치(ESXi 및 Hyper-V)

가상 머신을 시작하면 ISO 이미지에서 설치 프로세스가 시작됩니다.

소프트웨어를 설치하려면 다음을 수행합니다.

1. **영어** 를 선택합니다.

1. 배포에 필요한 아키텍처를 선택합니다.

1. 센서 관리 네트워크에 대한 네트워크 인터페이스(인터페이스, IP, 서브넷, DNS 서버, 기본 게이트웨이)를 정의합니다.

1. 로그인 자격 증명은 자동으로 생성됩니다. 사용자 이름 및 암호를 저장합니다. 처음 사용할 때 플랫폼에 액세스하려면 이러한 자격 증명이 필요합니다.

   그러면 어플라이언스가 재부팅됩니다.

1. 이전에 구성한 IP 주소(`<https://ip_address>`)를 통해 관리 콘솔에 액세스합니다.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="관리 콘솔의 로그인 화면을 보여 주는 스크린샷.":::

## <a name="legacy-appliances"></a>레거시 어플라이언스

이 섹션에서는 ‘레거시’ 어플라이언스의 설치 절차에 대해서만 설명합니다. 새 어플라이언스를 구입하는 경우 [필수 어플라이언스 식별](how-to-identify-required-appliances.md#identify-required-appliances)을 참조하세요.

### <a name="nuvo-5006lp-installation"></a>Nuvo 5006LP 설치

이 섹션에서는 Nuvo 5006LP 설치 프로시저를 제공합니다. Nuvo 5006LP 어플라이언스에 소프트웨어를 설치하기 전에 어플라이언스의 BIOS 구성을 조정해야 합니다.

#### <a name="nuvo-5006lp-front-panel"></a>Nuvo 5006LP 전면 패널

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_frontpanel.png" alt-text="Nuvo 5006LP 디바이스의 전면 패널 보기입니다.":::

1. 전원 단추, 전원 표시기
1. DVI 비디오 커넥터
1. HDMI 비디오 커넥터
1. VGA 비디오 커넥터
1. 원격 켜기/끄기 제어 및 상태 LED 출력
1. 리셋 단추
1. 관리 네트워크 어댑터
1. 미러된 데이터를 수신하는 포트

#### <a name="nuvo-back-panel"></a>Nuvo 후면 패널

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_backpanel.png" alt-text="Nuvo 5006lp의 후면 패널 보기입니다.":::

1. SIM 카드 슬롯
1. 마이크 및 스피커
1. COM 포트
1. USB 커넥터
1. DC 전원 포트(DC IN)

#### <a name="configure-the-nuvo-5006lp-bios"></a>Nuvo 5006LP BIOS 구성

다음 프로시저에서는 Nuvo 5006LP BIOS를 구성하는 방법을 설명합니다. 운영 체제가 이전에 어플라이언스에 설치되었는지 확인합니다.

**BIOS를 구성하려면:**

1. 어플라이언스 전원을 켭니다.

1. **F2** 키를 눌러 BIOS 구성에 진입합니다.

1. **전원** 으로 이동하고 전원 오류가 발생한 후 전원 켜기를 S0-Power On으로 변경합니다.

    :::image type="content" source="media/tutorial-install-components/nuvo-power-on.png" alt-text="전원 오류가 발생한 후 전원을 켜도록 Nuvo 5006을 변경합니다.":::

1. **부팅** 으로 이동하여 **LAN에 PXE 부팅** 이 **사용 안 함** 으로 설정되어 있는지 확인합니다.

1. **F10** 키를 눌러 저장한 다음 **끝내기** 를 선택합니다.

#### <a name="software-installation-nuvo-5006lp"></a>소프트웨어 설치(Nuvo 5006LP)

설치를 완료하는 데 약 20분이 소요됩니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

**소프트웨어를 설치하려면:**

1. 키에 대한 외부 CD 또는 디스크를 ISO 이미지와 연결합니다.

1. 어플라이언스를 부팅합니다.

1. **영어** 를 선택합니다.

1. **XSENSE-RELEASE-\<version> Office...** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="설치할 센서 버전을 선택합니다.":::

1. 어플라이언스 아키텍처 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Nuvo의 아키텍처 및 네트워크 속성을 정의합니다.":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | **Office** 를 선택합니다. |
    | **관리 인터페이스** | **eth0** |
    | **관리 네트워크 IP 주소** | **고객이 제공한 IP 주소** |
    | **관리 서브넷 마스크** | **고객이 제공한 IP 주소** |
    | **DNS** | **고객이 제공한 IP 주소** |
    | **기본 게이트웨이 IP 주소** | **0.0.0.0** |
    | **입력 인터페이스** | 입력 인터페이스 목록은 시스템에 의해 생성됩니다. <br />입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용하여 목록에 표시된 모든 항목을 복사합니다. |
    | **브리지 인터페이스** | - |

1. 설정을 적용하고 `Y`을 입력하여 계속합니다.

약 10분 후에 로그인 자격 증명이 자동으로 생성됩니다. 사용자 이름 및 암호를 저장합니다. 처음 사용할 때 플랫폼에 액세스하려면 이러한 자격 증명이 필요합니다.

### <a name="fitlet2-mini-sensor-installation"></a>Fitlet2 미니 센서 설치

이 섹션에서는 Fitlet2 설치 절차를 제공합니다. Fitlet 어플라이언스에 소프트웨어를 설치하기 전에 어플라이언스의 BIOS 구성을 조정해야 합니다.

#### <a name="fitlet2-front-panel"></a>Fitlet2 전면 패널

:::image type="content" source="media/tutorial-install-components/fitlet-front-panel.png" alt-text="Fitlet 2의 전면 패널 보기입니다.":::

#### <a name="fitlet2-back-panel"></a>Fitlet2 후면 패널

:::image type="content" source="media/tutorial-install-components/fitlet2-back-panel.png" alt-text="Fitlet 2의 후면 패널 보기입니다.":::

#### <a name="configure-the-fitlet2-bios"></a>Fitlet2 BIOS 구성

**Fitlet2 BIOS를 구성하려면:**

1. 어플라이언스 전원을 켭니다.

1. **주** > **OS 선택** 으로 이동합니다.

1. **+/-** 을 눌러 **Linux** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/fitlet-linux.png" alt-text="Fitlet2에서 OS를 Linux로 설정합니다.":::

1. 시스템 날짜 및 시간이 설치 날짜 및 시간으로 업데이트되었는지 확인합니다.

1. **고급** 로 이동하여 **ACPI 설정** 을 선택합니다.

1. **최대 절전 모드 사용** 을 선택하고 **+/-** 를 눌러 **사용 안함** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/disable-hibernation.png" alt-text="Fitlet2에서 최대 절전 모드를 비활성화 합니다.":::

1. **Esc** 키를 누릅니다.

1. **고급** > **TPM 구성** 으로 이동합니다.

1. **fTPM** 을 선택하고 **+/-** 를 눌러 **사용 안 함** 을 선택합니다.

1. **Esc** 키를 누릅니다.

1. **CPU 구성** > **VT-d** 로 이동합니다.

1. **+/-** 를 눌러 **사용** 을 선택합니다.

1. **CSM 구성** > **CSM 지원** 으로 이동합니다.

1. **+/-** 를 눌러 **사용** 을 선택합니다.

1. **고급** > **부팅 옵션 필터[레거시 전용]** 로 이동하고 다음 필드의 설정을 **레거시** 로 변경합니다.

    - 네트워크
    - Storage
    - 동영상
    - 기타 PCI

    :::image type="content" source="media/tutorial-install-components/legacy-only.png" alt-text="모든 필드를 레거시로 설정합니다.":::

1. **Esc** 키를 누릅니다.

1. **보안**  > **보안 부팅 사용자 지정** 으로 이동합니다.

1. **+/-** 를 눌러 **사용 안 함** 을 선택합니다.

1. **Esc** 키를 누릅니다.

1. **부팅** > **부팅 모드** 로 이동하여 **레거시** 를 선택합니다.

1. **부팅 옵션 #1 – [USB CD/DVD]** 를 선택합니다.

1. **저장 및 끝내기** 를 선택합니다.

#### <a name="software-installation-fitlet2"></a>소프트웨어 설치(Fitlet2)

설치를 완료하는 데 약 20분이 소요됩니다. 설치 후 시스템이 여러 번 다시 시작됩니다.

1. 키에 대한 외부 CD 또는 디스크를 ISO 이미지와 연결합니다.

1. 어플라이언스를 부팅합니다.

1. **영어** 를 선택합니다.

1. **XSENSE-RELEASE-\<version> Office...** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="설치할 센서 버전을 선택합니다.":::

    > [!Note]
    > 러기드화는 선택하지 않습니다.

1. 어플라이언스 아키텍처 및 네트워크 속성을 정의합니다.

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Nuvo의 아키텍처 및 네트워크 속성을 정의합니다.":::

    | 매개 변수 | 구성 |
    | ----------| ------------- |
    | **하드웨어 프로필** | **Office** 를 선택합니다. |
    | **관리 인터페이스** | **em1** |
    | **관리 네트워크 IP 주소** | **고객이 제공한 IP 주소** |
    | **관리 서브넷 마스크** | **고객이 제공한 IP 주소** |
    | **DNS** | **고객이 제공한 IP 주소** |
    | **기본 게이트웨이 IP 주소** | **0.0.0.0** |
    | **입력 인터페이스** | 입력 인터페이스 목록은 시스템에 의해 생성됩니다. <br />입력 인터페이스를 미러링하려면 쉼표 구분 기호를 사용하여 목록에 표시된 모든 항목을 복사합니다. |
    | **브리지 인터페이스** | - |

1. 설정을 적용하고 `Y`을 입력하여 계속합니다.

약 10분 후에 로그인 자격 증명이 자동으로 생성됩니다. 사용자 이름 및 암호를 저장합니다. 처음 사용할 때 플랫폼에 액세스하려면 이러한 자격 증명이 필요합니다.

## <a name="post-installation-validation"></a>설치 후 유효성 검사

물리적 어플라이언스 설치의 유효성을 검사하려면 많은 테스트를 수행해야 합니다. 모든 어플라이언스 유형에 동일한 유효성 검사 프로세스가 적용됩니다.

GUI 또는 CLI를 사용하여 유효성 검사를 수행합니다. 사용자 **지원** 및 사용자 **CyberX** 유효성 검사를 수행할 수 있습니다.

설치 후 유효성 검사에는 다음 테스트를 포함해야 합니다.

- **온전성 테스트**: 시스템이 실행 중인지 확인합니다.

- **버전**: 버전이 올바른지 확인합니다.

- **ifconfig**: 설치 프로세스 중에 구성된 모든 입력 인터페이스가 실행 중인지 확인합니다.

### <a name="check-system-health-by-using-the-gui"></a>GUI를 사용하여 시스템 상태 확인

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="시스템 상태 검사를 보여 주는 스크린샷.":::

#### <a name="sanity"></a>온전성

- **어플라이언스**: 어플라이언스 온전성 검사를 실행합니다. CLI 명령 `system-sanity`를 사용하여 동일한 검사를 수행할 수 있습니다.

- **버전**: 어플라이언스 버전을 표시합니다.

- **네트워크 속성**: 센서 네트워크 매개 변수를 표시합니다.

#### <a name="redis"></a>Redis

- **메모리**: 사용된 메모리 양 및 남은 메모리 양과 같은 메모리 사용량에 대한 전반적인 상태를 제공합니다.

- **가장 긴 키**: 광범위한 메모리 사용량을 유발할 수 있는 가장 긴 키를 표시합니다.

#### <a name="system"></a>시스템

- **핵심 로그**: 전체 시스템 로그를 내보내지 않고 최근 로그 행을 볼 수 있도록 핵심 로그의 마지막 500행을 제공합니다.

- **작업 관리자**: 프로세스의 테이블에 표시되는 작업을 다음 계층으로 변환합니다.
  
  - 영구 계층(Redis)

  - 현금 계층(SQL)

- **네트워크 통계**: 네트워크 통계를 표시합니다.

- **TOP**: 프로세스의 테이블을 표시합니다. 실행 중인 시스템의 동적 실시간 보기를 제공하는 Linux 명령입니다.

- **백업 메모리 확인**: 다음을 확인하여 백업 메모리의 상태를 제공합니다.

  - 백업 폴더의 위치

  - 백업 폴더의 크기

  - 백업 폴더의 제한 사항

  - 마지막 백업이 수행된 시간

  - 추가 백업 파일에 사용할 수 있는 공간

- **ifconfig**: 어플라이언스의 실제 인터페이스에 대한 매개 변수를 표시합니다.

- **CyberX nload**: 6초 테스트를 사용하여 네트워크 트래픽 및 대역폭을 표시합니다.

- **Core 로그의 오류**: Core 로그 파일의 오류를 표시합니다.

**도구에 액세스하려면:**

1. **지원** 사용자 자격 증명을 사용하여 센서에 로그인합니다.

1. **시스템 설정** 창에서 **시스템 통계** 를 선택합니다.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="check-system-health-by-using-the-cli"></a>CLI를 사용하여 시스템 상태 확인

시스템의 온전성을 테스트하기 전에 시스템이 실행 중인지 확인합니다.

**시스템의 온전성을 테스트하려면:**

1. Linux 터미널(예: PuTTY) 및 사용자 **지원** 을 사용하여 CLI에 연결합니다.

1. `system sanity`를 입력합니다.

1. 모든 서비스가 녹색(실행 중)인지 확인합니다.

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="실행 중인 서비스를 보여 주는 스크린샷.":::

1. **System is UP! (prod)** 가 맨 아래에 표시되는지 확인합니다.

올바른 버전을 사용하고 있는지 확인합니다.

**시스템의 버전을 확인 하려면**:

1. Linux 터미널(예: PuTTY) 및 사용자 **지원** 을 사용하여 CLI에 연결합니다.

1. `system version`를 입력합니다.

1. 올바른 버전이 표시되는지 확인합니다.

설치 프로세스 중에 구성된 모든 입력 인터페이스가 실행 중인지 확인합니다.

**시스템의 네트워크 상태를 확인 하려면**:

1. Linux 터미널(예: PuTTY) 및 사용자 **지원** 을 사용하여 CLI에 연결합니다.

1. `network list`(Linux 명령 `ifconfig`에 해당)를 입력합니다.

1. 필요한 입력 인터페이스가 나타나는지 확인합니다. 예를 들어 2개의 쿼드 구리 NIC가 설치된 경우 목록에 10개의 인터페이스가 있어야 합니다.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="인터페이스 목록을 보여 주는 스크린샷.":::

콘솔 웹 GUI에 액세스할 수 있는지 확인합니다.

**관리에서 UI에 대 한 액세스 권한이 있는지 확인 하려면**:

1. 이더넷 케이블을 사용하여 랩톱을 관리 포트(**Gb1**)에 연결합니다.

1. 어플라이언스와 동일한 범위에 있어야 하는 랩톱 NIC 주소를 정의합니다.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="UI에 대한 관리 액세스를 보여 주는 스크린샷.":::

1. 랩톱에서 어플라이언스의 IP 주소를 ping하여 연결을 확인합니다(기본값: 10.100.10.1).

1. 랩톱에서 크롬 브라우저를 열고 어플라이언스의 IP 주소를 입력합니다.

1. **프라이빗 연결이 아닙니다.** 창에서 **고급** 을 선택하고 계속 진행합니다.

1. Defender for IoT 로그인 화면이 나타나면 테스트가 성공한 것입니다.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="관리 콘솔에 대한 액세스를 보여 주는 스크린샷.":::

## <a name="troubleshooting"></a>문제 해결

### <a name="you-cant-connect-by-using-a-web-interface"></a>웹 인터페이스를 사용하여 연결할 수 없습니다.

1. 연결하려는 컴퓨터가 어플라이언스와 동일한 네트워크에 있는지 확인합니다.

1. GUI 네트워크가 관리 포트에 연결되어 있는지 확인합니다.

1. 어플라이언스의 IP 주소를 Ping합니다. ping이 없으면 다음을 수행합니다.

   1. 모니터와 키보드를 어플라이언스에 연결합니다.

   1. **지원** 사용자 및 암호를 사용하여 로그인합니다.

   1. `network list` 명령을 사용하여 현재 IP 주소를 확인합니다.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="네트워크 목록을 보여 주는 스크린샷.":::

1. 네트워크 매개 변수가 잘못 구성된 경우 다음 절차를 사용하여 해당 매개 변수를 변경합니다.

   1. `network edit-settings` 명령을 사용합니다.

   1. 관리 네트워크 IP 주소를 변경하려면 **Y** 를 선택합니다.

   1. 서브넷 마스크를 변경하려면 **Y** 를 선택합니다.

   1. DNS를 변경하려면 **Y** 를 선택합니다.

   1. 기본 게이트웨이 IP 주소를 변경하려면 **Y** 를 선택합니다.

   1. 입력 인터페이스를 변경(센서 전용)하려면 **N** 을 선택합니다.

   1. 설정을 적용하려면 **Y** 를 선택합니다.

1. 다시 시작한 후 지원 사용자 자격 증명을 사용하여 연결하고 `network list` 명령을 사용하여 매개 변수가 변경되었는지 확인합니다.

1. ping하고 GUI에서 다시 연결해 보세요.

### <a name="the-appliance-isnt-responding"></a>어플라이언스가 응답하지 않는 경우

1. 모니터와 키보드를 어플라이언스에 연결하거나 PuTTY를 사용하여 CLI에 원격으로 연결합니다.

1. **지원** 사용자의 자격 증명을 사용하여 로그인합니다.

1. `system sanity` 명령을 사용하여 모든 프로세스가 실행 중인지 확인합니다.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="시스템 온전성 명령을 보여 주는 스크린샷.":::

다른 문제에 대해서는 [Microsoft 지원](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의하세요.

## <a name="configure-a-span-port"></a>SPAN 포트 구성

vSwitch에는 미러링 기능이 없지만 SPAN 포트를 구현하기 위한 해결 방법을 사용할 수 있습니다. 해결 방법은 ESXi 또는 Hyper-V를 사용하여 구현할 수 있습니다.

:::image type="content" source="media/tutorial-install-components/purdue-model.png" alt-text="아키텍처에서 센서를 배치 해야 하는 위치에 대 한 스크린샷":::

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

1. 센서에 커넥트 하 고 미러링이 작동 하는지 확인 합니다.

### <a name="configure-a-span-port-with-hyper-v"></a>Hyper-V를 사용하여 SPAN 포트 구성

시작하기 전에 다음을 수행해야 합니다.

- 을 실행 하는 가상 어플라이언스의 인스턴스가 없는지 확인 합니다.

- 관리 포트가 아닌 데이터 포트에서 SPAN 보장을 사용하도록 설정합니다.

- 데이터 포트 SPAN 구성이 IP 주소로 구성되지 않았는지 확인합니다.

**Hyper-V를 사용하여 SPAN 포트를 구성하려면**,

1. 가상 스위치 관리자를 엽니다.

1. 가상 스위치 목록에서 전용 스팬 네트워크 어댑터 유형으로 **새 가상 네트워크 스위치** > **외부** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/new-virtual-network.png" alt-text="가상 스위치를 만들기 전에 새 가상 네트워크 및 외부를 선택하는 스크린샷":::

1. 선택 **가상 스위치를 만들** 합니다.

1. 연결 형식 아래에서 **외부 네트워크** 를 선택합니다.

1. **관리 운영 체제에서 이 네트워크 어댑터를 공유할 수 있음** 확인란이 선택되어 있는지 확인합니다.

   :::image type="content" source="media/tutorial-install-components/external-network.png" alt-text="외부 네트워크를 선택하고 관리 운영 체제에서 네트워크 어댑터를 공유할 수 있도록 허용":::

1. **확인** 을 선택합니다.

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>가상 스위치에 범위 가상 인터페이스 연결

Windows PowerShell 또는 hyper-v 관리자를 통해 가상 스위치에 범위 가상 인터페이스를 연결할 수 있습니다.

**PowerShell을 사용 하 여 가상 스위치에 범위 가상 인터페이스를 연결 하려면**:

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

**Hyper-v 관리자를 사용 하 여 가상 스위치에 범위 가상 인터페이스를 연결 하려면**:

1. 하드웨어 목록에서 **네트워크 어댑터** 를 선택합니다.

1. 가상 스위치 필드에서 **vSwitch_Span** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/vswitch-span.png" alt-text="가상 스위치 화면에서 다음 옵션을 선택하는 스크린샷":::

1. 하드웨어 목록의 네트워크 어댑터 드롭다운 목록 아래에서 **고급 기능** 을 선택합니다.

1. 포트 미러링 섹션에서 새 가상 인터페이스에 대한 미러링 모드로 **대상** 을 선택합니다.

    :::image type="content" source="media/tutorial-install-components/destination.png" alt-text="미러링 모드를 구성하는 데 필요한 선택 항목의 스크린샷":::

1. **확인** 을 선택합니다.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>가상 스위치에 Microsoft NDIS 캡처 확장 사용

Microsoft NDIS 캡처 확장을 새 가상 스위치에 사용하도록 설정해야 합니다.

**새로 추가 된 가상 스위치에 대해 MICROSOFT NDIS 캡처 확장을 사용 하도록 설정 하려면 다음을 수행 합니다**.

1. Hyper-V 호스트에서 가상 스위치 관리자를 엽니다.

1. 가상 스위치 목록에서 `vSwitch_Span`이라는 가상 스위치 이름을 펼치고, **확장** 을 선택합니다.

1. 스위치 확장 필드에서 **Microsoft NDIS 캡처** 를 선택합니다.

    :::image type="content" source="media/tutorial-install-components/microsoft-ndis.png" alt-text="스위치 확장 메뉴에서 Microsoft NDIS를 선택하여 사용하도록 설정하는 스크린샷":::

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

## <a name="access-sensors-from-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에서 센서 액세스

사용자가 센서에 직접 액세스하지 못하도록 하여 시스템 보안을 강화할 수 있습니다. 대신, 사용자가 단일 방화벽 규칙을 사용하여 온-프레미스 관리 콘솔에서 센서에 액세스할 수 있도록 프록시 터널링을 사용합니다. 이 기술은 센서를 벗어나는 네트워크 환경에 대한 무단 액세스 가능성을 줄입니다. 센서에 로그인할 때의 사용자 환경은 동일하게 유지됩니다.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="센서에 대한 액세스를 보여 주는 스크린샷.":::

**터널링을 사용 하려면**:

1. **CyberX** 또는 **지원** 사용자 자격 증명을 사용하여 온-프레미스 관리 콘솔의 CLI에 로그인합니다.

1. `sudo cyberx-management-tunnel-enable`를 입력합니다.

1. **Enter** 키를 선택합니다.

1. `--port 10000`를 입력합니다.

### <a name="next-steps"></a>다음 단계

[네트워크 설정](how-to-set-up-your-network.md)
