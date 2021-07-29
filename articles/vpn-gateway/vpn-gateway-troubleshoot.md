---
title: 문제 해결 구성 및 연결
titleSuffix: Azure VPN Gateway
description: 이 문서에서는 VPN Gateway 구성, 연결 문제를 해결하고 처리량이 유효한지 검사하기 위한 문서로 연결할 수 있게 도와줍니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 19edbd41617a01e54cd14ff7f2991763b2da9bba
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108204498"
---
# <a name="troubleshoot-vpn-gateway"></a>VPN Gateway 문제 해결

VPN Gateway 연결은 다양 한 이유로 실패할 수 있습니다. 이 문서에는 문제 해결을 시작하기 위한 링크가 포함되어 있습니다. 전체 목록을 보려면 이 페이지 왼쪽의 **문제 해결** 아래에 나오는 목차의 문서를 참조하세요.

## <a name="troubleshooting-scenarios-and-solutions"></a>문제 해결 시나리오 및 솔루션

* [VNet에 대한 VPN 처리량의 유효성 검사](vpn-gateway-validate-throughput-to-vnet.md)<br>VPN Gateway 연결을 통해 Azure 내 Virtual Network와 온-프레미스 IT 인프라 사이에 안전한 프레미스 간 연결을 설정할 수 있습니다. 이 문서에서는 온-프레미스 리소스에서 Azure VM(가상 컴퓨터)으로의 네트워크 처리량을 유효성 검사하는 방법을 보여줍니다. 문제 해결 지침도 제공합니다.

* [VPN 및 방화벽 디바이스 설정](vpn-gateway-third-party-settings.md)<br>이 문서에서는 VPN Gateway와 함께 사용되는 타사 VPN 또는 방화벽 디바이스에 대해 제안된 몇 가지 솔루션을 제공합니다. 타사 VPN 또는 방화벽 디바이스에 대한 기술 지원은 디바이스 공급업체에서 제공합니다.

* [지점 및 사이트 간 연결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>이 문서에서는 발생할 수 있는 일반적인 지점 및 사이트 간 연결 문제를 나열합니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 설명합니다.

* [사이트 간 연결](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>온-프레미스 네트워크와 Azure Virtual Network 사이에 사이트 간 VPN 연결을 구성한 후 VPN 연결이 갑자기 작동을 중지하며, 이를 다시 연결할 수 없는 경우가 있습니다. 이 문서에서는 이 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.

* [진단 로그를 사용하여 Azure VPN Gateway 문제 해결](troubleshoot-vpn-with-azure-diagnostics.md)<br>진단 로그를 사용하여 구성 작업, VPN 터널 연결, IPsec 로깅, BGP 경로 교환, 지점 및 사이트 간 고급 로깅 등 여러 VPN gateway 관련 이벤트 문제를 해결할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계를 사용하여 [VNet 및 VPN 연결이 유효한지 검사](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)할 수도 있습니다.
