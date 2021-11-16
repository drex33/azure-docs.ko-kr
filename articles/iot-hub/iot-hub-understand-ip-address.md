---
title: IoT Hub의 IP 주소 이해 | Microsoft Docs
description: IoT Hub IP 주소 및 해당 속성을 쿼리하는 방법을 이해합니다. IoT Hub의 IP 주소는 재해 복구 또는 지역별 장애 조치(failover)와 같은 특정 시나리오에서 변경될 수 있습니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: b4d3dd6f8ffe3016f5d9842d4b562141ae73dee4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548732"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP 주소

IoT Hub 공용 엔드포인트의 IP 주소 접두사는 _AzureIoTHub_ [서비스 태그](../virtual-network/service-tags-overview.md)에 주기적으로 게시됩니다.

> [!NOTE]
> 온-프레미스 네트워크 내에 배포된 디바이스의 경우 Azure IoT Hub는 프라이빗 엔드포인트와의 VNET 연결 통합을 지원합니다. 자세한 내용은 [VNet에 대한 IoT Hub 지원](./virtual-network-support.md)을 참조하세요.


이러한 IP 주소 접두사를 사용하여 다양한 네트워크 격리 목적을 구현하기 위해 IoT Hub와 디바이스 또는 네트워크 자산 간의 연결을 제어할 수 있습니다.

| 목표 | 적용 가능한 시나리오 | 접근 방식 |
|------|-----------|----------|
| 디바이스 및 서비스가 IoT Hub 엔드포인트와만 통신하는지 확인 | [디바이스-클라우드](./iot-hub-devguide-messaging.md) 및 [클라우드-디바이스](./iot-hub-devguide-messages-c2d.md) 메시지, [직접 메서드](./iot-hub-devguide-direct-methods.md), [디바이스 및 모듈 쌍](./iot-hub-devguide-device-twins.md) 및 [디바이스 스트림](./iot-hub-device-streams-overview.md) | _AzureIoTHub_ 및 _EventHub_ 서비스 태그를 사용하여 IoT Hub와 이벤트 허브 IP 주소 접두사를 검색하고 해당 IP 주소 접두사에 대한 디바이스 및 서비스의 방화벽 설정에 대한 허용 규칙을 적절하게 구성합니다. 디바이스 또는 서비스에서 통신하지 않으려는 다른 대상 IP 주소에 대한 트래픽을 삭제합니다. |
| IoT Hub 디바이스 엔드포인트가 디바이스 및 네트워크 자산의 연결만을 수신하는지 확인 | [디바이스-클라우드](./iot-hub-devguide-messaging.md) 및 [클라우드-디바이스](./iot-hub-devguide-messages-c2d.md) 메시지, [직접 메서드](./iot-hub-devguide-direct-methods.md), [디바이스 및 모듈 쌍](./iot-hub-devguide-device-twins.md) 및 [디바이스 스트림](./iot-hub-device-streams-overview.md) | IoT Hub [IP 필터 기능](iot-hub-ip-filtering.md)을 사용하여 디바이스 및 네트워크 자산 IP 주소에서의 연결을 허용합니다([제한 사항](#limitations-and-workarounds) 섹션 참조). | 
| 네트워크 자산에서만 경로의 사용자 지정 엔드포인트 리소스(스토리지 계정, 서비스 버스 및 이벤트 허브)에 연결할 수 있는지 확인 | [메시지 라우팅](./iot-hub-devguide-messages-d2c.md) | 연결 제한(예: [방화벽 규칙](../storage/common/storage-network-security.md), [Private Link](../private-link/private-endpoint-overview.md)또는 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 통해)에 대한 리소스의 지침을 따릅니다. _AzureIoTHub_ 서비스 태그를 사용하여 IoT Hub IP 주소 접두사를 검색하고 리소스의 방화벽 구성에서 해당 IP 접두사에 대한 허용 규칙을 추가합니다([제한 사항](#limitations-and-workarounds) 섹션 참조). |

## <a name="best-practices"></a>모범 사례

* IoT Hub의 IP 주소는 예고 없이 변경될 수 있습니다. 중단을 최소화하려면 가능할 때마다 네트워킹 및 방화벽 구성에 IoT Hub 호스트 이름(예: myhub.azure-devices.net)을 사용하세요. 

* DNS(Domain Name System)가 없는 제한된 IoT 시스템의 경우 IoT Hub IP 주소 범위는 변경 사항이 적용되기 전에 서비스 태그를 통해 주기적으로 게시됩니다. 따라서 최신 서비스 태그를 정기적으로 검색하고 사용하는 프로세스를 개발하는 것이 중요합니다. 이 프로세스는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)를 통해 자동화할 수 있습니다. 서비스 태그 검색 API는 아직 미리 보기 상태이며 경우에 따라 태그 및 IP 주소의 전체 목록을 생성하지 못할 수도 있습니다. 검색 API를 일반적으로 사용할 수 있을 때까지 [서비스 태그를 다운로드 가능한 JSON 형식](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 사용하는 것이 좋습니다. 


* *AzureIoTHub.[지역 이름]* 태그를 사용하여 특정 지역의 IoT hub 엔드포인트에서 사용하는 IP 접두사를 식별합니다. 데이터 센터 재해 복구 또는 [지역별 장애 조치(failover)](iot-hub-ha-dr.md)를 고려하여 IoT Hub의 지역 쌍 지역에 대한 IP 접두사 연결도 사용하도록 설정해야 합니다.

* IoT Hub 방화벽 규칙을 설정하면 IoT Hub에 대해 Azure CLI 및 PowerShell 명령을 실행하는 데 필요한 연결을 차단할 수 있습니다. 이를 방지하려면 클라이언트의 IP 주소 접두사에 대한 허용 규칙을 추가하여 CLI 또는 PowerShell 클라이언트가 IoT Hub와 통신할 수 있도록 다시 설정할 수 있습니다.  

* 디바이스의 방화벽 구성에서 허용 규칙을 추가할 때 [적용 가능한 프로토콜에서 사용하는 특정 포트](./iot-hub-devguide-protocols.md#port-numbers)를 제공하는 것이 가장 좋습니다.

## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

* IoT Hub IP 필터 기능에는 규칙 100개 제한이 있습니다. 이 제한은 Azure 고객 지원을 통한 요청을 통해 발생할 수 있습니다. 

* 구성된 [IP 필터링 규칙](iot-hub-ip-filtering.md)은 IoT Hub의 기본 제공 이벤트 허브 엔드포인트가 아니라 IoT Hub IP 엔드포인트에만 적용됩니다. 또한 메시지가 저장되는 이벤트 허브에 IP 필터링을 적용해야 하는 경우 원하는 IP 필터링 규칙을 직접 구성할 수 있는 사용자 고유의 이벤트 허브 리소스를 가져올 수 있습니다. 이렇게 하려면 사용자 고유의 이벤트 허브 리소스를 프로비저닝하고 [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)을 설정하여 IoT Hub의 기본 제공 이벤트 허브 대신 해당 리소스로 메시지를 전송해야 합니다. 마지막으로 위의 표에서 설명한 것처럼 메시지 라우팅 기능을 사용하도록 설정하려면 IoT Hub의 IP 주소 접두사에서 프로비저닝된 이벤트 허브 리소스로의 연결을 허용해야 합니다.

* 스토리지 계정으로 라우팅할 때 IoT Hub의 IP 주소 접두사에서 트래픽을 허용하는 것은 스토리지 계정이 IoT Hub와 다른 지역에 있는 경우에만 가능합니다.

## <a name="support-for-ipv6"></a>IPv6에 대한 지원 

IPv6은 현재 IoT Hub에서 지원되지 않습니다.
