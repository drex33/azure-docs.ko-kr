---
title: 진단 설정을 통해 IoT 커넥터 메트릭 내보내기-Azure 의료 Api
description: 이 문서에서는 진단 설정을 통해 IoT connector 메트릭을 내보내는 방법을 설명 합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/25/2021
ms.author: jasteppe
ms.openlocfilehash: 08edb99de13e0102597c7a078adbcb4225e28d15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085964"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>진단 설정을 통해 IoT 커넥터 메트릭 내보내기

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 IoT 커넥터 메트릭 로그를 내보내는 방법에 대해 알아봅니다. 메트릭 로깅을 사용 하도록 설정 하는 기능은 Azure Portal의 [**진단 설정**](../../azure-monitor/essentials/diagnostic-settings.md) 입니다. 

## <a name="enable-metrics-logging-for-iot-connector"></a>IoT 커넥터에 메트릭 로깅 사용
1. IoT 커넥터에 대 한 메트릭 로깅을 사용 하도록 설정 하려면 Azure Portal에서 신속한 의료 기능 상호 운용성 리소스 (FHIR&#174;) 서비스를 선택 합니다. 

2. **진단 설정** 으로 이동 

3. **+ 진단 설정 추가** 를 선택합니다

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. **진단 설정 이름** 대화 상자에 이름을 입력 합니다.

5. 진단 로그에 액세스 하는 데 사용할 방법을 선택 합니다.

    1. 감사 또는 수동 검사를 위해 **저장소 계정에 보관** 합니다. 사용 하려는 저장소 계정이 이미 만들어져 있어야 합니다.
    2. 타사 서비스 또는 사용자 지정 분석 솔루션에의 한 수집을 위해 **이벤트 허브로 스트리밍합니다** . 이 단계를 구성 하려면 먼저 Event hubs 네임 스페이스 및 이벤트 허브 정책을 만들어야 합니다.
    3. Azure Monitor에서 Log Analytics 작업 영역 **으로 스트리밍합니다** . 이 옵션을 선택 하려면 먼저 로그 분석 작업 영역을 만들어야 합니다.

6. IoT 커넥터에 대 한 **오류, 트래픽 및 대기 시간** 을 선택 합니다.  FHIR 서비스에 대해 캡처할 추가 메트릭 범주를 선택 합니다.

7. **저장** 을 선택합니다.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 선택한 리포지토리에서 첫 번째 메트릭 로그가 표시 되는 데 최대 15 분이 걸릴 수 있습니다.  
 
진단 로그를 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스 로그 설명서](../../azure-monitor/essentials/platform-logs-overview.md) 를 참조 하세요.

## <a name="conclusion"></a>결론 
메트릭 로그에 대 한 액세스 권한은 모니터링 및 문제 해결에 필수적입니다.  IoT 커넥터를 사용 하면 메트릭 로그를 통해 이러한 작업을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

IoT 커넥터에 대 한 자주 묻는 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 Faq](iot-connector-faqs.md)

(FHIR&#174;)는 HL7의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.