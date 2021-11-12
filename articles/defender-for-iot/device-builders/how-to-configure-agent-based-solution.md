---
title: IoT용 Microsoft Defender 에이전트 기반 솔루션 구성
description: Microsoft Defender for IoT 에이전트 기반 솔루션에서 데이터 수집을 구성하는 방법 알아보기
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 65e3febe4a1715ee1a55b377a5c54cbb4f3f88c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306209"
---
# <a name="configure-microsoft-defender-for-iot-agent-based-solution"></a>IoT용 Microsoft Defender 에이전트 기반 솔루션 구성  

이 문서에서는 Microsoft Defender for IoT 에이전트 기반 솔루션에서 데이터 수집을 구성하는 방법을 설명합니다.

## <a name="configure-data-collection"></a>데이터 수집 구성

Microsoft Defender for IoT 에이전트 기반 솔루션에서 데이터 수집을 구성하려면 다음을 수행합니다. 

1. Azure Portal로 이동하고 Defender for IoT가 연결된 IoT Hub를 선택합니다. 

1.  **보안** 메뉴에서 **설정** 을 선택합니다. 

1.  **데이터 수집** 을 선택합니다. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="보안 메뉴 설정에서 데이터 수집 선택.":::

## <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리 

IoT 솔루션의 보안을 유지하기 위해 IoT 디바이스, IoT Edge, IoT Hub의 수신 및 발신 연결의 IP 주소가 기본적으로 수집되고 저장됩니다. 이 정보는 의심스러운 IP 주소 원본에서 시도하는 비정상적인 연결을 검색하는 데 필요하며 사용됩니다. 알려진 봇네트의 IP 주소 원본이나 지리적 위치 외부의 IP 주소 원본에서 연결을 설정하려고 시도하는 경우를 예로 들 수 있습니다. Defender for IoT 서비스는 언제든지 IP 주소 데이터 수집을 사용하거나 사용하지 않도록 설정할 수 있는 유연성을 제공합니다. 

IP 주소 데이터 수집을 사용하거나 사용하지 않도록 설정하려면: 

1. IoT Hub를 연 다음  **보안** 메뉴에서  **설정** 을 선택합니다. 

1.  **데이터 수집** 화면을 선택하고, 지리적 위치 및 IP 주소 처리 설정을 필요에 맞게 수정합니다. 

## <a name="log-analytics-creation"></a>Log Analytics 만들기 

Defender for IoT를 사용하면 보안 경고, 권장 사항 및 원시 보안 데이터를 Log Analytics 작업 영역에 저장할 수 있습니다. IoT Hub의 Log Analytics 수집은 Defender for IoT 솔루션에서 기본적으로 **해제** 되어 있습니다. Defender for IoT를 Log Analytics 작업 영역에 연결하고 여기에 보안 데이터도 저장할 수 있습니다. 

Defender for IoT는 기본적으로 다음 두 가지 유형의 정보를 Log Analytics 작업 영역에 저장합니다.
 
- 보안 경고.

- 권장 사항. 

추가 정보 유형의 스토리지를 `raw events`로 추가하도록 선택할 수 있습니다. 

> [!Note] 
> Log Analytics에  `raw events` 를 저장하면 추가 스토리지 비용이 발생합니다. 

Log Analytics에서 마이크로 에이전트를 사용하려면: 

1. **작업 영역 구성** > **데이터 수집** 으로 이동하고, 토글을  **켜기** 로 전환합니다. 

1. 새 Log Analytics 작업 영역을 만들거나 기존 작업 영역을 선택합니다. 

1.  **원시 보안 데이터에 대한 액세스** 옵션이 선택되어 있는지 확인합니다.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="원시 보안 데이터에 대한 액세스가 선택되어 있는지 확인.":::

1.  **저장** 을 선택합니다.

매월 Azure Log Analytics 서비스에 수집되는 고객당 처음 5GB 데이터는 무료입니다. Azure Log Analytics 작업 영역에 수집된 모든 데이터(GB)는 처음 31일간 무료로 보존됩니다. 가격 책정에 대한 자세한 내용은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요. 

Log Analytics의 작업 영역 구성을 변경하는 방법은 다음과 같습니다. 

1. IoT Hub의 **보안** 메뉴에서  **설정** 을 선택합니다. 

1.  **데이터 수집** 화면을 선택하고 필요에 따라 Log Analytics 설정의 작업 영역 구성을 수정합니다. 

구성 후 Log Analytics 작업 영역에서 경고에 액세스하려면:

1. Defender for IoT에서 경고를 선택합니다.

1. **Log Analytics 작업 영역에서 경고 조사** 를 선택합니다.

구성 후 Log Analytics 작업 영역에서 경고에 액세스하려면:

1. Defender for IoT에서 권장 사항을 선택합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사** 를 선택합니다. 
 
Log Analytics에서 데이터를 쿼리하는 방법에 대한 자세한 내용은  [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md)을 참조하세요. 

## <a name="turn-off-defender-for-iot"></a>Defender for IoT 끄기 

특정 IoT Hub에서 Defender for IoT 서비스를 켜거나 끄려면: 

1. IoT Hub의 **보안** 메뉴에서  **설정** 을 선택합니다.

1.  **데이터 수집** 화면을 선택하고 필요에 따라 Log Analytics 설정의 작업 영역 구성을 수정합니다.

## <a name="next-steps"></a>다음 단계 

[솔루션을 구성](quickstart-configure-your-solution.md)하려면 다음 문서로 계속 진행하세요.
