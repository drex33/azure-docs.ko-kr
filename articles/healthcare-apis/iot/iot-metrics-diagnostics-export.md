---
title: 진단 설정을 통해 IoT 커넥터 메트릭 내보내기 - Azure Healthcare API
description: 이 문서에서는 진단 설정을 통해 IoT 커넥터 메트릭을 내보내는 방법을 설명합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 09/30/2021
ms.author: jasteppe
ms.openlocfilehash: 23908c8ab910324cda4cd2802158447a1c97db4e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354603"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>진단 설정을 통해 IoT 커넥터 메트릭 내보내기

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 IoT 커넥터 메트릭 로그를 내보내는 방법을 알아봅니다. 메트릭 로깅을 사용하도록 설정하는 기능은 Azure Portal [**진단 설정입니다.**](../../azure-monitor/essentials/diagnostic-settings.md) 

## <a name="enable-metrics-logging-for-iot-connector"></a>IoT 커넥터에 대한 메트릭 로깅 사용
1. IoT 커넥터에 대한 메트릭 로깅을 사용하도록 설정하려면 Azure Portal 전자 의료 기록 교환(FHIR&#174;) 서비스를 선택합니다. 

2. 진단 **설정으로** 이동합니다. 

3. **+ 진단 설정 추가** 를 선택합니다

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. **진단 설정** 이름 대화 상자에 이름을 입력합니다.

5. 진단 로그에 액세스하는 데 사용할 방법을 선택합니다.

    1. 감사 또는 수동 검사를 **위해 스토리지 계정에 보관합니다.** 사용하려는 스토리지 계정을 이미 만들어야 합니다.
    2. 타사 서비스 또는 사용자 지정 분석 솔루션에서 데이터를 스트리밍하여 **이벤트 허브로** 스트리밍합니다. 이 단계를 구성하려면 이벤트 허브 네임스페이스 및 이벤트 허브 정책을 만들어야 합니다.
    3. Azure Monitor **Log Analytics** 작업 영역으로 스트리밍합니다. 이 옵션을 선택하려면 Logs Analytics 작업 영역을 만들어야 합니다.

6. IoT 커넥터에 대한 **오류, 트래픽 및 대기 시간을** 선택합니다.  FHIR 서비스에 대해 캡처하려는 추가 메트릭 범주를 선택합니다.

7. **저장** 을 선택합니다.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT 커넥터2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 첫 번째 메트릭 로그가 선택한 리포지토리에 표시되는 데 최대 15분이 걸릴 수 있습니다.  
 
진단 로그를 작업하는 방법에 대한 자세한 내용은 [Azure 리소스 로그 설명서를 참조하세요.](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>결론 
메트릭 로그에 액세스하는 것은 모니터링 및 문제 해결에 필수적입니다.  IoT 커넥터를 사용하면 메트릭 로그를 통해 이러한 작업을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

IoT 커넥터에 대한 질문과 대답을 확인하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 FAQ](../fhir/fhir-faq.md)

(FHIR&#174;)는 HL7의 등록 상표이며 HL7의 사용 권한으로 사용됩니다.