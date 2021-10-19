---
title: fhir (미리 보기)에 대 한 IoT Connector
description: fhir (미리 보기) 오류 메시지 및 조건에 대 한 일반적인 Azure IoT 커넥터 문제를 해결 하는 방법 및 매핑 파일 복사
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 0dbd592b3801d879e4950afc8082045693055248
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434951"
---
# <a name="iot-connector-for-fhir-preview-troubleshooting-guide"></a>fhir (미리 보기) 문제 해결 가이드에 대 한 IoT Connector

이 문서에서는 신속한 의료 상호 운용성 리소스 (fhir&#174;) * 오류 메시지 및 조건에 대 한 일반적인 Azure IoT 커넥터 문제를 해결 하는 단계를 제공 합니다.  

또한 fhir 변환 매핑 JSON에 대 한 Azure IoT 커넥터의 복사본을 만드는 방법에 대해 알아봅니다 (예: 장치 및 fhir).  

변환 JSON 복사본을 사용 하 여 Azure Portal 외부에서 편집 및 보관할 수 있습니다.  

> [!TIP]
> fhir 용 Azure IoT 커넥터에 대 한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 열 경우 문제 해결 프로세스에 도움이 되는 변환 매핑 JSON의 복사본을 포함 해야 합니다.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>fhir (미리 보기)에 대 한 Azure IoT 커넥터에 대 한 장치 및 fhir 변환 JSON 템플릿 유효성 검사
이 섹션에서는 fhir 용 Azure IoT Connector가 장치 및 fhir 변환의 유효성을 검사 하기 위해 수행 하는 유효성 검사 프로세스에 대해 알아봅니다.  이러한 요소는 장치 및 FHIR 변환 JSON JSON에 필요 합니다.

**장치 매핑**

|요소|필수|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|값 []. ValueName|True|
|값 []. ValueExpression|True|

> [!NOTE]
> 값 []. ValueName 및 Values []. ValueExpression
>
> 이러한 요소는 배열에 값 항목이 있는 경우에만 필요 하며, 값이 매핑되지 않은 경우에만 유효 합니다. 이는 전송할 원격 분석이 이벤트 일 때 사용 됩니다. 예: wearable IoMT 장치를 on 또는 제거 하는 경우. fhir 일치 및 내보내기에 대 한 커넥터를 Azure IoT 하는 이름을 제외 하 고 요소에는 값이 없습니다. fhir 변환에서 fhir에 대 한 Azure IoT Connector는이를 의미 체계 형식에 따라 코드를 사용할 수 있는 개념에 매핑합니다. 실제 값은 채워지지 않습니다.

**FHIR 매핑**

|요소|필수|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> 지금은 유효성 검사를 수행 하는 데 필요한 FHIR 매핑 요소입니다.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>fhir 용 Azure IoT 커넥터 (미리 보기)에 대 한 오류 메시지 및 수정

|메시지|표시할지|조건|Fix| 
|-------|---------|---------|---|
|매핑 이름이 잘못 되었습니다. 매핑 이름은 장치 또는 FHIR 이어야 합니다.|API|제공 된 매핑 유형은 장치 또는 FHIR이 아닙니다.|지원 되는 두 가지 매핑 유형 (예: 장치 또는 FHIR) 중 하나를 사용 합니다.|
|유효성 검사에 실패했습니다. 필요한 정보가 누락 되었거나 잘못 되었습니다.|API 및 Azure Portal|필요한 정보 또는 요소가 누락 된 변환 매핑을 저장 하는 중입니다.|누락 된 변환 매핑 정보 또는 요소를 추가 하 고 변환 매핑을 다시 저장 하려고 합니다.|
|키 매개 변수가 정의 되지 않았습니다.|API|키 요청을 다시 생성 합니다.|다시 생성 키 요청에 매개 변수를 포함 합니다.|
|이 구독에서 프로 비전 할 수 있는 최대 IoT Connector 인스턴스 수에 도달 했습니다.|API 및 Azure Portal|Azure IoT FHIR 구독 할당량에 대 한 커넥터에 도달 했습니다 (기본값은 구독 당 (2)).|fhir 용 Azure IoT Connector의 기존 인스턴스 중 하나를 삭제 합니다.  구독 할당량에 도달 하지 않은 다른 구독을 사용 합니다.  구독 할당량 증가를 요청 합니다.|
|fhir 리소스에 대해 사용 하도록 설정 된 Azure API IoT Connector에 대해 리소스 이동이 지원 되지 않습니다.|API 및 Azure Portal|fhir 용 Azure IoT 커넥터 인스턴스를 하나 이상 포함 하는 fhir 리소스에 대 한 Azure API에서 이동 작업을 수행 하려고 합니다.|이동 작업을 수행 하기 위해 fhir 용 Azure IoT Connector의 기존 인스턴스를 삭제 합니다.|
|IoT Connector 프로 비전 되지 않았습니다.|API|부모 (fhir 용 Azure IoT Connector)가 프로 비전 되지 않은 경우 자식 서비스 (연결 & 매핑)를 사용 하려고 합니다.|fhir 용 Azure IoT 커넥터를 프로 비전 합니다.|
|요청이 지원되지 않습니다.|API|특정 API 요청은 지원 되지 않습니다.|올바른 API 요청을 사용 합니다.|
|계정이 없습니다.|API|fhir 용 Azure IoT 커넥터 추가 및 fhir 리소스에 대 한 Azure API 추가 시도는 존재 하지 않습니다.|FHIR 리소스에 대 한 Azure API를 만든 후 작업을 다시 시도 합니다.|
|FHIR 리소스에 대 한 Azure API는 IoT Connector에 대해 지원 되지 않습니다.|API|fhir 리소스에 대해 호환 되지 않는 버전의 Azure API에 대 한 Azure IoT 커넥터를 사용 하려고 합니다.|FHIR 리소스에 대 한 새 Azure API (버전 4)를 만들거나 기존 Azure API (버전 4-4)를 사용 합니다.

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>fhir (미리 보기) 데이터를 위한 내 Azure IoT 커넥터가 fhir 용 Azure API에 표시 되지 않는 이유는 무엇 인가요?

|잠재적 이슈|수정 프로그램|
|----------------|-----|
|데이터를 계속 처리 하 고 있습니다.|데이터는 일괄 처리에서 Azure API에 egressed 됩니다 (15 분 마다).  데이터를 계속 처리할 수 있으며 데이터를 FHIR 용 Azure API에서 지속 하기 위해 추가 시간이 필요할 수 있습니다.|
|장치 변환 매핑 JSON이 구성 되지 않았습니다.|준수 하는 장치 변환 매핑 JSON을 구성 하 고 저장 합니다.|
|JSON 변환 매핑 JSON이 구성 되지 않았습니다.|준수 하는 FHIR 변환 매핑 JSON을 구성 하 고 저장 합니다.|
|장치 메시지에 장치 매핑에 정의 된 예상 식이 없습니다.|장치 메시지에 정의 된 장치 매핑 일치 토큰에 정의 된 JsonPath 식을 확인 합니다.|
|Azure API for FHIR (해결 유형: 조회에만 해당) *에서 장치 리소스가 생성 되지 않았습니다.|Azure API for FHIR에서 유효한 장치 리소스를 만듭니다. 장치 리소스에는 들어오는 메시지에 제공 된 장치 식별자와 일치 하는 식별자가 포함 되어 있어야 합니다.|
|Azure API for FHIR (해결 유형: 조회에만 해당) *에서 환자 리소스가 생성 되지 않았습니다.|Azure API for FHIR에서 유효한 환자 리소스를 만듭니다.|
|Device. 환자 참조가 설정 되지 않았거나 참조가 잘못 되었습니다 (해결 유형: 조회 전용) *.|장치 리소스에 환자 리소스에 대 한 올바른 [참조가](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 포함 되어 있는지 확인 합니다.| 

* 참조 [빠른 시작: Azure Portal를 사용 하 여 Azure IoT 커넥터 (미리 보기)를 배포](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) 합니다. 예를 들어, fhir 해상도 유형 (예: 조회 또는 만들기)에 대 한 Azure IoT 커넥터의 기능 설명입니다.

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>메트릭을 사용 하 여 fhir (미리 보기)에 대 한 Azure IoT 커넥터의 문제 해결

Azure IoT FHIR 용 커넥터는 여러 메트릭을 생성 하 여 데이터 흐름 프로세스에 대 한 정보를 제공 합니다. 지원 되는 메트릭 중 하나를 *전체 오류* 라고 하며,이는 fhir 용 Azure IoT 커넥터의 인스턴스 내에서 발생 하는 모든 오류에 대 한 개수를 제공 합니다.

각 오류는 여러 연결 된 속성으로 기록 됩니다. 모든 속성은 오류를 식별 하 고 문제를 해결 하는 데 도움이 되는 오류에 대 한 다양 한 측면을 제공 합니다. 이 섹션에는 *총* 오류 메트릭의 각 오류에 대해 캡처된 여러 속성과 이러한 속성의 가능한 값이 나열 되어 있습니다.

> [!NOTE]
> [fhir (미리 보기) 메트릭 용 Azure IoT 커넥터 페이지](iot-metrics-display.md)에 설명 된 대로 fhir (미리 보기)에 대 한 Azure IoT Connector의 인스턴스에 대 한 *총 오류* 메트릭으로 이동할 수 있습니다.

*총 오류* 그래프를 클릭 한 다음 *필터 추가* 단추를 클릭 하 여 아래 설명 된 속성 중 하나를 사용 하 여 오류 메트릭을 조각화 및 분석 합니다.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>fhir (미리 보기)에 대 한 Azure IoT 커넥터에서 수행 하는 작업

이 속성은 오류가 발생 했을 때 IoT Connector에서 수행 하는 작업을 나타냅니다. 작업은 일반적으로 장치 메시지를 처리 하는 동안 데이터 흐름 단계를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

> [!NOTE]
> [여기](iot-data-flow.md)에서 fhir (미리 보기)에 대 한 Azure IoT 커넥터에서 데이터 흐름의 여러 단계에 대해 자세히 알아볼 수 있습니다.

|데이터 흐름 단계|설명|
|---------------|-----------|
|설치 프로그램|IoT Connector 인스턴스를 설정 하는 것과 관련 된 작업입니다.|
|표준화|장치 데이터를 정규화 하는 데이터 흐름 단계|
|Grouping(그룹화)|정규화 된 데이터가 그룹화 되는 데이터 흐름 단계|
|FHIRConversion|그룹화 된 정규화 된 데이터를 FHIR 리소스로 변환 하는 데이터 흐름 단계|
|Unknown|오류가 발생 했을 때 작업 유형을 알 수 없습니다.|

### <a name="the-severity-of-the-error"></a>오류의 심각도입니다.

이 속성은 발생 한 오류의 심각도를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

|심각도|설명|
|---------------|-----------|
|경고|데이터 흐름 프로세스에 약간의 사소한 문제가 있지만 장치 메시지 처리가 중지 되지 않습니다.|
|오류|특정 장치 메시지를 처리 하는 동안 오류가 발생 하 고 다른 메시지가 계속 해 서 정상적으로 실행 될 수 있습니다.|
|위험|IoT Connector에 일부 시스템 수준 문제가 있으며 처리할 메시지가 없습니다.|

### <a name="the-type-of-the-error"></a>오류의 유형입니다.

이 속성은 지정 된 오류에 대 한 범주를 나타냅니다 .이는 기본적으로 유사한 오류 유형에 대 한 논리적 그룹화를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

|오류 유형|설명|
|----------|-----------|
|DeviceTemplateError|장치 매핑 템플릿과 관련 된 오류|
|DeviceMessageError|특정 장치 메시지를 처리 하는 동안 오류가 발생 했습니다.|
|Fanr템플릿 오류|FHIR 매핑 템플릿과 관련 된 오류|
|FHIRConversionError|메시지를 FHIR 리소스로 변환 하는 동안 오류가 발생 했습니다.|
|FHIRResourceError|IoT Connector에서 참조 하는 FHIR 서버의 기존 리소스와 관련 된 오류|
|FHIRServerError|FHIR 서버와 통신할 때 발생 하는 오류|
|GeneralError|다른 모든 오류 유형|

### <a name="the-name-of-the-error"></a>오류의 이름입니다.

이 속성은 특정 오류에 대 한 이름을 제공 합니다. 다음은 설명 및 관련 오류 유형, 심각도 및 데이터 흐름 단계를 포함 하는 모든 오류 이름 목록입니다.

|오류 이름|설명|오류 유형|오류 심각도|데이터 흐름 단계|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|장치 메시지에 있는 각 식별자에 대해 FHIR 서버에서 여러 환자 또는 장치 리소스를 발견 하는 동안 오류가 발생 했습니다.|FHIRResourceError|오류|FHIRConversion|
|TemplateNotFoundException|장치 또는 FHIR 매핑 템플릿이 인스턴스로 구성 되지 않았습니다 IoT Connector|DeviceTemplateError, Fanr템플릿 오류|위험|정규화, FHIRConversion|
|CorrelationIdNotDefinedException|상관 관계 ID가 장치 매핑 템플릿에서 지정 되지 않았습니다. CorrelationIdNotDefinedException는 FHIR 관찰에서 상관 관계 ID를 사용 하 여 장치 측정을 그룹화 해야 하지만 올바르게 구성 되지 않은 경우에만 발생 하는 조건부 오류입니다.|DeviceMessageError|오류|표준화|
|PatientDeviceMismatchException|이 오류는 FHIR 서버의 장치 리소스에 환자 리소스에 대 한 참조가 있을 때 발생 합니다 .이 리소스는 메시지에 있는 환자 식별자와 일치 하지 않습니다.|FHIRResourceError|오류|FHIRConversionError|
|PatientNotFoundException|장치 메시지에 있는 장치 식별자와 연결 된 장치 FHIR 리소스에서 참조 하는 환자 FHIR 리소스가 없습니다. 참고이 오류는 IoT Connector 인스턴스가 *조회* 확인 유형으로 구성 된 경우에만 발생 합니다.|FHIRConversionError|오류|FHIRConversion|
|DeviceNotFoundException|장치 메시지에 있는 장치 식별자와 연결 된 FHIR 서버에 장치 리소스가 없습니다.|DeviceMessageError|오류|표준화|
|PatientIdentityNotDefinedException|이 오류는 장치 메시지에서 환자 식별자를 구문 분석 하는 식이 장치 매핑 템플릿에서 구성 되지 않았거나 환자 식별자가 장치 메시지에 없는 경우에 발생 합니다. 참고이 오류는 IoT Connector의 해상도 형식이 *Create* 로 설정 된 경우에만 발생 합니다.|DeviceTemplateError|위험|표준화|
|DeviceIdentityNotDefinedException|이 오류는 장치 메시지에서 장치 식별자를 구문 분석 하는 식이 장치 매핑 템플릿에서 구성 되지 않았거나 장치 식별자가 장치 메시지에 없는 경우에 발생 합니다.|DeviceTemplateError|위험|표준화|
|NotSupportedException|지원 되지 않는 형식의 장치 메시지를 받는 동안 오류가 발생 했습니다.|DeviceMessageError|오류|표준화|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>fhir (미리 보기) 변환에 대 한 Azure IoT 커넥터 복사본 만들기 JSON JSON

fhir 매핑 파일에 대 한 Azure IoT 커넥터 복사는 Azure Portal 웹 사이트 외부에서 편집 및 보관 하는 데 유용할 수 있습니다.

문제 해결을 지원 하기 위해 지원 티켓을 열 때 매핑 파일 복사본을 Azure 기술 지원 서비스에 제공 해야 합니다.

> [!NOTE]
> 현재 JSON은 장치 및 FHIR 매핑 파일에 대해 지원 되는 유일한 형식입니다.

> [!TIP]
> fhir [장치 및 fhir 변환](iot-mapping-templates.md) 에 대 한 Azure IoT 커넥터에 대 한 자세한 정보 JSON JSON

1. " **추가 기능"** 섹션에서 Azure API for fhir 리소스 대시보드의 왼쪽 아래에 있는 **"IoT Connector (미리 보기)"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 변환 매핑 JSON을 복사할 **"커넥터"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 이 프로세스를 사용 하 여 **"FHIR 매핑 구성"** JSON의 내용을 복사 하 고 저장할 수도 있습니다.

3. **"장치 매핑 구성"** 을 선택 합니다.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. JSON의 콘텐츠를 선택 하 고 복사 작업을 수행 합니다 (예: Ctrl + c 선택). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 편집기 내의 새 파일 (예: Visual Studio Code, 메모장)에 붙여넣기 작업 (예: Ctrl + v 선택)을 수행 하 고 *. i n i 확장명을 사용 하 여 파일을 저장 합니다.

> [!TIP]
> fhir 용 Azure IoT 커넥터에 대 한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 열 경우 문제 해결 프로세스에 도움이 되는 변환 매핑 JSON의 복사본을 포함 해야 합니다.

## <a name="next-steps"></a>다음 단계

fhir의 Azure IoT 커넥터에 대 한 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[Azure IoT FHIR Faq 용 커넥터](fhir-faq.yml)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.