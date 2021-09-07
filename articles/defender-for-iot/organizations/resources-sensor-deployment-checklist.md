---
title: Azure Defender for IoT 배포 전 검사 목록
description: 이 문서에서는 사이트를 준비할 때 배포 전에 사용해야 하는 정보 및 검사 목록을 제공합니다.
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676953"
---
# <a name="pre-deployment-checklist-overview"></a>배포 전 검사 목록 개요

이 문서에서는 성공적인 온보딩을 위해 사이트를 준비할 때 배포 전에 사용해야 하는 정보 및 검사 목록을 제공합니다.

- Defender for IoT 물리적 센서는 계층 1과 계층 2(일부 경우에는 계층 3) 사이의 산업용 통신을 보는 관리되는 스위치에 연결해야 합니다.
- 센서는 스위치 미러 포트(SPAN 포트) 또는 TAP에서 수신 대기합니다.
- 관리 포트는 SSL을 사용하여 비즈니스/회사 네트워크에 연결됩니다.

## <a name="checklist"></a>검사 목록

산업용 네트워크 개요 다이어그램을 사용하여 사이트 엔지니어는 Azure Defender for IoT 장비의 적절한 위치를 정의할 수 있습니다.

### <a name="1-global-network-diagram"></a>1. 글로벌 네트워크 다이어그램

글로벌 네트워크 다이어그램은 산업용 OT 환경의 다이어그램을 제공합니다.

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="설치에서 Azure Defender for IoT 센서가 장착되는 위치입니다.":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="purdue 모델에서 Azure Defender for IoT 센서가 장착되는 위치입니다.":::

> [!Note] 
> Defender for IoT 어플라이언스는 스위치의 포트 간 트래픽을 확인하는 하위 수준 스위치에 연결되어야 합니다. 

### <a name="2-committed-devices"></a>2. 커밋된 디바이스

모니터링할 대략적인 디바이스 수를 제공합니다. Azure Defender for IoT에 구독을 온보딩할 때 이 정보가 필요합니다. 온보딩 프로세스 중에 1,000단위로 디바이스 수를 입력하라는 메시지가 표시됩니다.

### <a name="3-optional-subnet-list"></a>3. (선택 사항) 서브넷 목록 

프로덕션 네트워크의 서브넷 목록을 제공합니다.

| **#** | **서브넷 이름** | **설명** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4. VLAN

프로덕션 네트워크의 VLAN 목록을 제공합니다.

| **#** | **VLAN 이름** | **설명** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5. 스위치 모델 및 미러링 지원

스위치에 포트 미러링 기능이 있는지 확인하려면 Defender for IoT 플랫폼이 연결되어야 하는 스위치 모델 번호를 제공합니다.

| **#** | **스위치** | **모델** | **트래픽 미러링 지원(SPAN, RSPAN 또는 없음)** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6. 타사 스위치 관리

스위치를 타사가 관리하나요? Y 또는 N 

‘예’라면 어느 회사인가요? __________________________________ 

해당 회사의 정책은 무엇인가요? __________________________________ 

### <a name="7-serial-connection"></a>7. 직렬 연결

직렬 연결을 통해 통신하는 디바이스가 네트워크에 있나요? Yes 또는 No 

‘예’라면 직렬 통신 프로토콜을 지정합니다. ________________ 

‘예’인 경우 직렬 프로토콜과 통신하는 디바이스와 디바이스의 위치를 네트워크 다이어그램에 지정합니다.

표시된 직렬 연결을 사용하여 네트워크 다이어그램을 추가합니다.

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8. 공급업체 및 프로토콜(산업용 장비)

산업 장비의 공급업체 및 프로토콜 목록을 제공합니다. (선택 사항)

| **#** | **공급업체** | **통신 프로토콜** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

예를 들면 다음과 같습니다.

- Siemens

- Rockwell Automation – 이더넷 또는 IP

- Emerson – DeltaV, Ovation

### <a name="9-qos"></a>9. QoS

QoS의 경우 센서의 기본 설정은 1.5Mbps입니다. 이를 변경할지 여부를 지정합니다. ________________ 

   사업부(BU): ________________

### <a name="10-sensor"></a>10. 센서  

센서 어플라이언스는 네트워크 어댑터를 통해 스위치 SPAN 포트에 연결됩니다. 센서 어플라이언스는 다른 전용 네트워크 어댑터를 통해 관리할 수 있도록 고객의 회사 네트워크에 연결되어 있습니다.

회사 네트워크에 연결될 센서 NIC의 주소 세부 정보를 제공합니다. 

| 항목 | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
|--|--|--|--|
| 어플라이언스 IP 주소 |  |  |  |
| 서브넷 |  |  |  |
| 기본 게이트웨이 |  |  |  |
| DNS |  |  |  |
| 호스트 이름 |  |  |  |

### <a name="11-idraciloserver-management"></a>11. iDRAC/iLO/서버 관리

| 항목 | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
|--|--|--|--|
| 어플라이언스 IP 주소 |  |  |  |
| 서브넷 |  |  |  |
| 기본 게이트웨이 |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12. 온-프레미스 관리 콘솔  

| 항목 | Active | 패시브(HA를 사용하는 경우) |
|--|--|--|
| IP 주소 |  |  |
| 서브넷 |  |  |
| 기본 게이트웨이 |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13. SNMP  

| 항목 | 세부 정보 |
|--|--|
| IP |  |
| IP 주소 |  |
| 사용자 이름 |  |
| 암호 |  |
| 인증 유형 | MD5 또는 SHA |
| 암호화 | DES 또는 AES |
| 비밀 키 |  |
| SNMP v2 커뮤니티 문자열 |

### <a name="14-ssl-certificate"></a>14. SSL 인증서

SSL 인증서를 사용할 계획입니까? Yes 또는 No

‘예’라면 인증서를 생성하는 데 사용할 서비스는 무엇인가요? 인증서에 포함할 특성(예: 도메인 또는 IP 주소)은 무엇인가요?

### <a name="15-smtp-authentication"></a>15. SMTP 인증

SMTP를 사용하여 이메일 서버에 경고를 전달할 계획입니까? Yes 또는 No

‘예’라면 어떤 인증 방법을 사용하려 하나요?  

### <a name="16-active-directory-or-local-users"></a>16. Active Directory 또는 로컬 사용자

Active Directory 관리자에게 문의하여 Active Directory 사이트 사용자 그룹을 만들거나 로컬 사용자를 만듭니다. 사용자가 배포 날짜에 준비되어 있어야 합니다.

### <a name="17-iot-device-types-in-the-network"></a>17. 네트워크의 IoT 디바이스 유형

| 디바이스 유형 | 네트워크의 디바이스 수 | 평균 대역폭 |
|--|--|--|
| 예: 카메라 |  |
| 예: X선 기계 |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>다음 단계

[Azure Defender for IoT 네트워크 설정 정보](how-to-set-up-your-network.md)

[Defender for IoT 설치 정보](how-to-install-software.md)
