---
title: Azure Percept DK 고급 네트워크 설정
description: 이 문서에서는 Azure Percept DK 설치 중에 고급 네트워크 설정을 설명합니다
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: bc680025e37ded3fd91b840d00ba075772cbb656
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528322"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>Azure Percept DK 고급 네트워크 설정

Azure Percept DK를 사용하면 개발 키트에서 다양한 네트워킹 구성 요소를 제어할 수 있습니다. 이 작업은 설치 환경의 고급 네트워크 설정에서 수행됩니다. 이러한 설정에 액세스하려면, [설치 환경을 시작하고](./quickstart-percept-dk-set-up.md) **네트워크 연결** 페이지에서 **고급 네트워크 설정 액세스** 를 선택해야 합니다.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="네트워크 연결 페이지에서 고급 네트워크 설정 시작":::

## <a name="select-the-security-setting"></a>보안 설정 선택
로컬 연결에 대한 Azure Percept DK는 IPv4 및 IPv6를 모두 지원합니다.

> [!NOTE]
> Azure IoTHub는 [IPv6 를 지원하지 않습니다](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6). IoTHub와 통신하기 위해서는 IPv4를 사용해야 합니다.
1. IPv4 라디오 단추를 선택한 다음, 네트워크 설정 항목을 선택하여 IPv4 설정을 변경합니다
1. IPv6 라디오 단추를 선택한 다음, 네트워크 설정 항목을 선택하여 IPv6 설정을 변경합니다
1. 선택에 따라 **네트워크 설정** 옵션이 변경될 수 있습니다

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="네트워크 옵션 목록을 보려면 보안 프로토콜을 선택합니다":::

## <a name="define-a-static-ip-address"></a>고정 IP 주소 정의

1. **고급 네트워크 설정** 페이지에서의 목록에서 **고정 IP 주소 정의** 를 선택합니다
1. 드롭다운 메뉴에서 **네트워크 인터페이스** 를 선택합니다
1. **동적 IP 주소** 를 선택 해제합니다
1. 고정 IP 주소를 입력합니다
1. 서브넷 IP 주소(서브넷 마스크라고도 함)를 입력합니다
1. 게이트웨이 IP 주소(기본 게이트웨이라고도 함)를 입력합니다
1. 해당되는 경우 DNS 주소를 입력합니다
1. **저장** 을 선택합니다.
1. **뒤로** 를 선택하여 **고급 네트워크설정** 페이지로 돌아갑니다

## <a name="define-dns-server-for-docker"></a>Docker용 DNS 서버 정의
이 설정을 사용하면 새 Docker DNS IP 주소를 수정하거나 추가할 수 있습니다.

> [!NOTE]
> Docker 서비스는 IPv4 DNS 항목만 허용하도록 구성됩니다.  IPv6 화면에서 추가된 항목은 무시됩니다.

1. **고급 네트워크 설정** 페이지에서의 목록에서 **Docker용 DNS 서버 정의** 를 선택합니다
1. Docker IPv4 DNS 주소를 입력합니다
1. **저장** 을 선택합니다.
1. **뒤로** 를 선택하여 **고급 네트워크설정** 페이지로 돌아갑니다

## <a name="define-bridge-internet-protocol-for-docker"></a>Docker용 Bridge Internet Protocol 정의
Bridge Internet Protocol 화면에서 Docker 컨테이너의 IPv4 주소 공간을 변경할 수 있습니다.

디바이스의 IP 주소가 Azure Percept Devkit의 Docker 서비스(172.17.x.x)와 동일한 경로를 공유하는 경우, Docker의 브리지를 다른 것으로 변경하여 Docker 컨테이너와 Azure IoTHub 간의 통신을 허용해야 합니다.  

1. **고급 네트워크 설정** 페이지에서의 목록에서 **Docker용 Bridge Internet Protocol 정의** 를 선택합니다
1. Docker Bridge Internet Protocol IPv4 주소(BIP)를 입력합니다
1. **저장** 을 선택합니다.
1. **뒤로** 를 선택하여 **고급 네트워크설정** 페이지로 돌아갑니다

## <a name="define-an-internet-proxy-server"></a>인터넷 프록시 서버 정의
이 옵션을 통해 프록시 서버를 정의할 수 있습니다.    

1. **고급 네트워크 설정** 페이지에서의 목록에서 **인터넷 프록시 서버 정의** 를 선택합니다
1. **프록시 서버 사용** 상자를 체크하여 이 옵션을 사용하도록 설정합니다.
1. 프록시 서버의 **HTTP 주소** 를 입력합니다(해당되는 경우)
1. 프록시 서버의 **HTTPS 주소** 를 입력합니다(해당되는 경우)
1. 프록시 서버의 **FTP 주소** 를 입력합니다(해당되는 경우)
1. **프록시 주소 없음** 상자에서 프록시 서버를 사용할 수 없는 IP 주소를 입력합니다
1. **저장** 을 선택합니다.
1. **뒤로** 를 선택하여 **고급 네트워크설정** 페이지로 돌아갑니다

## <a name="setup-zero-touch-provisioning"></a>Zero Touch 프로비전 설정

> [!IMPORTANT]
> **Zero Touch 프로비전 설정** 은 현재 작동하지 않습니다

이 옵션을 사용하면 Azure Percept DK를 [Wi-Fi Easy Connect<sup>TM</sup> 대량 구성기](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.)로 전환하여 여러 디바이스를 Wi-Fi 인프라에 한 번에 온보딩할 수 있습니다.  

## <a name="define-access-point-passphrase"></a>액세스 지점 암호 정의 
이 옵션을 사용하면 Azure Percept DK Wi-Fi 액세스 지점 암호를 업데이트할 수 있습니다.  

> [!CAUTION]
> 새 암호를 저장하면 Wi-Fi 액세스 지점에서 즉시 연결이 끊어집니다.  액세스 권한을 다시 얻으려면 새 암호로 다시 연결하세요.  

암호 요구 사항:
- 길이는 12~123자 사이여야 합니다
- 암호에는 소문자 1개, 대문자 1개, 숫자 1개, 특수문자 1개가 포함되어야 합니다.

1. **고급 네트워크 설정** 페이지에서의 목록에서 **액세스 지점 암호 정의** 를 선택합니다
1. 새 암호 입력
1. **저장** 을 선택합니다.
1. **뒤로** 를 선택하여 **고급 네트워크설정** 페이지로 돌아갑니다

## <a name="next-steps"></a>다음 단계
**고급 네트워크 설정** 을 변경한 후, **뒤로** 단추를 선택하여 [Azure Percept DK 설정 환경을 계속 진행합니다](./quickstart-percept-dk-set-up.md).