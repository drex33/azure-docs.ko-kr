---
title: Azure IoT Embedded C SDK 용 개발자 가이드 | Microsoft Docs
description: 임베디드 C SDK Azure IoT을 시작 하 고 IoT hub와 통신 하는 장치 앱을 만드는 방법을 알아봅니다.
author: danewalton
ms.author: dawalton
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/5/2021
ms.custom: mqtt
ms.openlocfilehash: 0a5a51f7e53c8111302d783951dc5cb0faf8ba5b
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943460"
---
# <a name="azure-iot-embedded-c-sdk"></a>임베디드 C SDK Azure IoT

[임베디드 C sdk](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) 는 사용자의 네트워크 (byon) 접근 방식을 지 원하는 제한 된 장치에 대 한 sdk입니다. IoT 개발자는 MQTT 클라이언트, TLS, 선택한 소켓을 가져와서 자유롭게 디바이스 솔루션을 만들 수 있습니다. 다음 디자인 결정은이 SDK를 사용 하 여 제한 된 장치에 대 한 솔루션을 구축 하는 완벽 한 옵션입니다.

- SDK의 고객은 소스 코드와 함께 소스 코드를 컴파일합니다.
- C99 프로그래밍 언어를 대상으로 하며 gcc, clang, & MS Visual C 컴파일러를 사용 하 여 테스트 합니다.
- 코드를 쉽게 이해 하 고 디버그할 수 있도록 하는 추상화를 제공 합니다.
- SDK가 할당 되지 않았습니다. 고객은 필요에 맞게 (전역 메모리, 힙, 스택 등) 데이터 구조를 할당 한 다음 할당 된 구조의 주소를 함수에 전달 하 여이를 초기화 하 고 다양 한 작업을 수행 해야 합니다.
- 다른 언어 Sdk와 달리, 많은 항목 (예: 정책의 HTTP 파이프라인 작성)은 런타임과는 반대로 소스 코드에서 수행 됩니다. 이러한 최적화는 코드 크기를 줄이고 실행 속도와 잠금 동작을 향상 시켜 런타임에 버그가 발생할 가능성을 줄입니다.
- 운영 체제가 없는 마이크로컨트롤러, 실시간 운영 체제 (예: [Azure RTOS](https://azure.microsoft.com/services/rtos/)), Linux 및 Windows가 포함 된 마이크로컨트롤러를 지원 합니다. 고객은 사용자 지정 장치에서 SDK를 사용 하는 사용자 지정 플랫폼 계층을 구현할 수 있습니다. 일부 플랫폼 계층을 제공 하 고, 커뮤니티에서 플랫폼 계층을 제출 하 여 지원 되는 플랫폼을 향상 시키는 것이 좋습니다.

> [!NOTE]
> Azure rtos 및 freertos 용 Azure IoT Embedded C SDK를 기반으로 구축 된 추가 미들웨어 라이브러리가 있습니다. 이러한 플랫폼 중 하나를 사용 하는 경우 해당 플랫폼을 사용 하는 것이 좋습니다.
>
> 해당 라이브러리는 여기를 참조 하세요.
> - [Azure rtos의 Azure IoT 미들웨어](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
> - [freertos에 대 한 Azure IoT 미들웨어](https://github.com/Azure/azure-iot-middleware-freertos)

## <a name="architecture"></a>아키텍처

앞서 설명한 것 처럼 Azure IoT Embedded C SDK는 "사용자 지정-네트워크" 방법을 사용 합니다. 즉, 사용자는 mqtt, TLS 및 tcp/ip 스택을 자유롭게 선택 하 고 Azure IoT Embedded C SDK와 함께 사용할 수 있습니다. 이 SDK의 유틸리티는 Azure IoT mqtt 기능을 사용 하기 쉽게 만드는 데 도움이 됩니다. 인바운드 항목을 구문 분석 하 고, 인증 세부 정보를 제공 하 고, 구독 항목을 만드는 등의 기능을 제공 합니다 (다른 기능 중).

![Azure I O T Embedded C S D K의 계층화 된 아키텍처를 보여 주는 다이어그램](./media/iot-hub-device-embedded-c-sdk/embedded-c-high-level-architecture.png)

다이어그램에서 볼 수 있듯이, 포함 된 C SDK Azure IoT는 네트워킹 종속성 *이 없으므로 응용* 프로그램 코드와 *함께* 사용 해야 합니다. 대신, 오프 로드 된, 하드웨어 MQTT 또는 TLS 또는 기타 사용자 지정 네트워킹 스택과 같은 방법으로 솔루션에 필요한 구성 요소를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작 하는 데 도움이 되는 몇 가지 샘플이 있습니다. 사용 가능한 샘플은 아래 링크를 참조 하세요.

- [Linux 및 Windows](https://github.com/Azure/azure-sdk-for-c/tree/main/sdk/samples/iot)
- [ESP32](https://github.com/Azure/azure-sdk-for-c/tree/main/sdk/samples/iot/aziot_esp32)
- [ESP8266](https://github.com/Azure/azure-sdk-for-c/tree/main/sdk/samples/iot/aziot_esp8266)
- [Realtek AmebaD](https://github.com/Azure/azure-sdk-for-c/tree/main/sdk/samples/iot/aziot_realtek_amebaD)
- [마이크로칩으로 PIC IoT 보드](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)
- [마이크로칩으로 SAM IoT 보드](https://github.com/Azure-Samples/Microchip-SAM-IoT-Wx)
