---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: b676e4c801b447291288fdd07ff64177f1201e6c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732691"
---
클라이언트의 아키텍처와 일치하는 버전이면 각 Windows 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [VPN Gateway FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)의 지점 및 사이트 간 연결 섹션을 참조하세요.

>[!NOTE]
>연결하려는 Windows 클라이언트 컴퓨터에서 관리자 권한이 있어야 합니다.
>

### <a name="install-the-configuration-files"></a>구성 파일 설치

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. 
1. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **자세한 정보**, **실행** 을 차례로 클릭합니다.

### <a name="verify-and-connect"></a>확인 및 연결

1. 클라이언트 컴퓨터에 클라이언트 인증서를 설치했는지 확인합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 클라이언트 인증서를 보려면 **사용자 인증서 관리** 를 엽니다. 클라이언트 인증서는 **현재 User\Personal\Certificates** 에 설치됩니다.
1. 연결하려면 **네트워크 설정** 으로 이동하여 **VPN** 을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.
