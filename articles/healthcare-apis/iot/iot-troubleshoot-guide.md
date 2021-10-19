---
title: IoT 커넥터 문제 해결 가이드 및 방법 - Azure Healthcare API
description: 이 문서는 사용자가 IoT 커넥터의 일반적인 오류 메시지, 조건 및 매핑 파일을 복사하는 방법을 해결하는 데 도움이 됩니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jasteppe
ms.openlocfilehash: 07f1ed78abe90b2967335322f0eea17e721a44bc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005412"
---
# <a name="iot-connector-troubleshooting-guide"></a>IoT 커넥터 문제 해결 가이드

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 일반적인 IoT 커넥터 오류 메시지 및 조건 문제를 해결하는 단계를 제공합니다. IoT 커넥터의 디바이스 및 전자 의료 기록 교환(FHIR&#174;) 대상 매핑 복사본을 만드는 방법도 알아봅니다. 또한 디바이스 및 FHIR 대상 매핑 복사본을 사용하여 Azure Portal 외부에서 편집하고 보관할 수 있습니다.  

> [!TIP]
> IoT 커넥터에 대한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 열 때 문제 해결 프로세스를 지원하기 위해 디바이스 및 FHIR 대상 매핑의 복사본을 포함합니다.

## <a name="device-and-fhir-destination-mapping-validations"></a>디바이스 및 FHIR 대상 매핑 유효성 검사

이 섹션에서는 IoT 커넥터가 수행하는 유효성 검사 프로세스에 대해 설명합니다. 유효성 검사 프로세스는 디바이스 및 FHIR 대상 매핑의 유효성을 검사한 후 사용할 수 있도록 저장합니다. 이러한 요소는 디바이스 및 FHIR 대상 매핑에 필요합니다.

**디바이스 매핑**

|요소|필수|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|값[]. ValueName|True|
|값[]. ValueExpression|True|

> [!NOTE]
> `Values[].ValueName and Values[].ValueExpression` 요소는 배열에 값 항목이 있는 경우에만 필요합니다. 매핑된 값이 없는 것이 유효합니다. 전송되는 원격 분석이 이벤트일 때 사용됩니다. 
>
>예를 들어:
> 
>웨어러블 IoMT 디바이스를 켜거나 제거하면 요소에는 IoT 커넥터가 일치하고 내보내는 이름을 제외한 값이 없습니다. FHIR 변환에서 IoT 커넥터는 의미 체계 형식에 따라 코드 가능 개념에 매핑합니다. 즉, 실제 값이 채워지지 않습니다.

**FHIR 대상 매핑**

|요소|필수|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> 현재 유효성이 검사된 유일한 필수 FHIR 대상 매핑 요소입니다.

## <a name="error-messages-and-fixes"></a>오류 메시지 및 수정

### <a name="iot-connector-resource"></a>IoT 커넥터 리소스

|메시지|표시|조건|Fix| 
|-------|---------|---------|---|
|최대 리소스 종류 수에 `iotconnectors` 도달했습니다.|API 및 Azure Portal|IoT 커넥터 구독 할당량에 도달했습니다(기본값은 구독당 25).|IoT 커넥터의 기존 인스턴스 중 하나를 삭제합니다. 구독 할당량에 도달하지 않은 다른 구독을 사용합니다. 구독 할당량 증가를 요청합니다.
|`deviceMapping`매핑이 잘못되었습니다. 유효성 검사 오류: {오류 목록}|API 및 Azure Portal|`properties.deviceMapping`IoT 커넥터 리소스 프로비저닝 요청에 제공된 이 잘못되었습니다.|속성에 제공된 매핑 JSON의 오류를 `properties.deviceMapping` 수정합니다.
|`fullyQualifiedEventHubNamespace` 가 null이거나, 비어 있거나, 형식이 잘못되었습니다.|API 및 Azure Portal|IoT 커넥터 프로비저닝 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 요청이 잘못되었습니다.|IoT 커넥터를 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 올바른 형식으로 업데이트합니다. 이어야 `{YOUR_NAMESPACE}.servicebus.windows.net` 합니다.
|상위 리소스는 자식 리소스를 프로비전하기 전에 완전히 프로비전되어야 합니다.|API|부모 작업 영역은 여전히 프로비전되고 있습니다.|부모 작업 영역 프로비저닝이 완료될 때까지 기다렸다가 프로비전 요청을 다시 제출합니다.
|`Location` 자식 리소스의 속성은 부모 리소스의 속성과 일치해야 `Location` 합니다.|API|IoT 커넥터 프로비저닝 요청 `location` 속성은 부모 작업 영역 `location` 속성과 다릅니다.|`location`프로비전 요청에서 IoT 커넥터의 속성을 부모 작업 영역 속성과 동일한 값으로 `location` 설정합니다.

### <a name="destination-resource"></a>대상 리소스

|메시지|표시|조건|Fix| 
|-------|---------|---------|---|
|최대 리소스 종류 수에 `iotconnectors/destinations` 도달했습니다.|API 및 Azure Portal|IoT 커넥터 대상 리소스 할당량에 도달했으며 기본값은 IoT 커넥터당 1입니다.)|IoT 커넥터 대상 리소스의 기존 인스턴스를 삭제합니다. IoT 커넥터당 하나의 대상 리소스만 허용됩니다.
|`fhirServiceResourceId`제공된 가 잘못되었습니다.|API 및 Azure Portal|`properties.fhirServiceResourceId`대상 리소스 프로비저닝 요청에 제공된 가 Azure Healthcare API FHIR 서비스 인스턴스에 대한 유효한 리소스 ID가 아닙니다.|리소스 ID의 형식이 올바르게 지정되었는지 확인하고 Azure Healthcare API FHIR 인스턴스에 대한 리소스 ID인지 확인합니다. 형식은 다음과 여야 합니다. `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|상위 리소스는 자식 리소스를 프로비전하기 전에 완전히 프로비전되어야 합니다.|API|부모 작업 영역 또는 부모 IoT 커넥터는 여전히 프로비전됩니다.|부모 작업 영역 또는 부모 IoT 커넥터 프로비저닝이 완료될 때까지 기다린 다음 프로비전 요청을 다시 제출합니다.
|`Location` 자식 리소스의 속성은 부모 리소스의 속성과 일치해야 `Location` 합니다.|API|대상 프로비저닝 요청 `location` 속성은 부모 IoT 커넥터 `location` 속성과 다릅니다.|`location`프로비전 요청에서 Destination의 속성을 부모 IoT 커넥터 속성과 동일한 값으로 `location` 설정합니다.

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>IoT 커넥터 데이터가 FHIR 서비스에 표시되지 않는 이유는 무엇인가요?

|잠재적 이슈|수정 프로그램|
|----------------|-----|
|데이터는 여전히 처리되고 있습니다.|데이터는 일괄 처리(~15분마다)로 FHIR 서비스로 송신됩니다.  데이터가 계속 처리되고 있으며 데이터가 FHIR 서비스에 유지되는 데 추가 시간이 필요할 수 있습니다.|
|장치 매핑이 구성 되지 않았습니다.|준수 하는 장치 매핑을 구성 하 고 저장 합니다.|
|대상 매핑이 구성 되지 않았습니다.|준수 하는 대상 매핑을 구성 하 고 저장 합니다.|
|장치 메시지에 장치 매핑에 정의 된 예상 식이 없습니다.|장치 `JsonPath` 메시지에 정의 된 장치 매핑 일치 토큰에 정의 된 식을 확인 합니다.|
|장치 리소스가 FHIR 서비스에 만들어지지 않았습니다 (해결 방법: 조회에만 해당) *.|FHIR 서비스에서 유효한 장치 리소스를 만듭니다. 장치 리소스에 들어오는 메시지에 제공 된 장치 식별자와 일치 하는 식별자가 포함 되어 있는지 확인 합니다.|
|환자 리소스가 FHIR 서비스에 만들어지지 않았습니다 (해결 방법: 조회에만 해당) *.|FHIR 서비스에서 유효한 환자 리소스를 만듭니다.|
|`Device.patient`참조가 설정 되지 않았거나 참조가 잘못 된 경우 (해결 유형: 조회 전용) *.|장치 리소스에 환자 리소스에 대 한 올바른 [참조가](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 포함 되어 있는지 확인 합니다.| 

* 참조 빠른 시작: IoT 커넥터 확인 형식 (예: 조회 또는 만들기)의 기능 설명에 [Azure Portal를 사용 하 여 iot 커넥터를 배포](deploy-iot-connector-in-azure.md) 합니다.

### <a name="the-operation-performed-by-iot-connector"></a>IoT 커넥터에서 수행 하는 작업

이 속성은 오류가 발생 했을 때 IoT 커넥터에서 수행 하는 작업을 나타냅니다. 작업은 일반적으로 장치 메시지를 처리 하는 동안 데이터 흐름 단계를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

> [!NOTE]
> IoT 커넥터에서 데이터 흐름의 여러 단계에 대 한 자세한 내용은 [iot 커넥터 데이터 흐름](iot-data-flow.md)을 참조 하세요.

|데이터 흐름 단계|설명|
|---------------|-----------|
|설치 프로그램|데이터 흐름 설정 단계는 IoT 커넥터의 인스턴스를 설정 하는 것과 관련 된 작업입니다.|
|표준화|정규화는 장치 데이터를 정규화 하는 데이터 흐름 단계입니다.|
|Grouping(그룹화)|정규화 된 데이터가 그룹화 되는 그룹화 데이터 흐름 단계입니다.|
|FHIRConversion|FHIRConversion은 그룹화 된 정규화 된 데이터를 FANR 리소스로 변환 하는 데이터 흐름 단계입니다.|
|Unknown|Unknown은 오류가 발생 하는 경우 알 수 없는 작업 유형입니다.|

### <a name="the-severity-of-the-error"></a>오류의 심각도입니다.

이 속성은 발생 한 오류의 심각도를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

|심각도|설명|
|---------------|-----------|
|경고|일부 사소한 문제는 데이터 흐름 프로세스에 있지만 장치 메시지 처리는 중지 되지 않습니다.|
|오류|이 메시지는 특정 장치 메시지의 처리에 오류가 발생 하 여 다른 메시지가 계속 해 서 예상 대로 실행 될 때 발생 합니다.|
|위험|이 오류는 IoT 커넥터에 일부 시스템 수준 문제가 있고 처리할 메시지가 없는 경우에 발생 합니다.|

### <a name="the-type-of-error"></a>오류 유형

이 속성은 지정 된 오류에 대 한 범주를 나타내며,이는 기본적으로 유사한 오류 유형에 대 한 논리적 그룹화를 나타냅니다. 다음은이 속성에 사용할 수 있는 값의 목록입니다.

|오류 유형|설명|
|----------|-----------|
|`DeviceTemplateError`|이 오류 유형은 장치 매핑과 관련 됩니다.|
|`DeviceMessageError`|이 오류 유형은 특정 장치 메시지를 처리할 때 발생 합니다.|
|`FHIRTemplateError`|이 오류 유형은 FHIR 대상 매핑과 관련 되어 있습니다.|
|`FHIRConversionError`|이 오류 유형은 메시지를 FHIR 리소스로 변환할 때 발생 합니다.|
|`FHIRResourceError`|이 오류 유형은 IoT 커넥터에서 참조 하는 FHIR 서비스의 기존 리소스와 관련 되어 있습니다.|
|`FHIRServerError`|이 오류 유형은 FHIR 서비스와 통신할 때 발생 합니다.|
|`GeneralError`|이 오류 유형은 다른 모든 유형의 오류에 대 한 것입니다.|

### <a name="the-name-of-the-error"></a>오류의 이름입니다.

이 속성은 특정 오류에 대 한 이름을 제공 합니다. 다음은 설명 및 관련 오류 유형, 심각도 및 데이터 흐름 단계를 포함 하는 모든 오류 이름 목록입니다.

|오류 이름|설명|오류 유형|오류 심각도|데이터 흐름 단계|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|이 오류는 장치 메시지에 있는 각 식별자에 대해 FHIR 서비스에서 여러 환자 또는 장치 리소스를 찾을 때 발생 합니다.|`FHIRResourceError`|오류|`FHIRConversion`|
|`TemplateNotFoundException`|IoT 커넥터 인스턴스로 구성 되지 않은 장치 또는 FHIR 대상 매핑입니다.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|상관 관계 ID가 장치 매핑에서 지정 되지 않았습니다. `CorrelationIdNotDefinedException` 는 올바르게 구성 되지 않았기 때문에 상관 관계 ID를 사용 하 여 장치 측정을 그룹화 해야 하는 경우에만 발생 하는 조건부 오류입니다.|`DeviceMessageError`|오류|표준화|
|`PatientDeviceMismatchException`|이 오류는 FHIR 서비스의 장치 리소스가 환자 리소스에 대 한 참조를 가질 때 발생 합니다. 이 오류 유형은 메시지에 있는 환자 식별자와 일치 하지 않음을 의미 합니다.|`FHIRResourceError`|오류|`FHIRConversionError`|
|`PatientNotFoundException`|장치 메시지에 있는 장치 식별자와 연결 된 장치 FHIR 리소스에서 참조 하는 환자 FHIR 리소스가 없습니다. 참고이 오류는 IoT 커넥터 인스턴스가 *조회* 확인 유형으로 구성 된 경우에만 발생 합니다.|`FHIRConversionError`|오류|`FHIRConversion`|
|`DeviceNotFoundException`|장치 메시지에 있는 장치 식별자와 연결 된 FHIR 서비스에 장치 리소스가 없습니다.|`DeviceMessageError`|오류|표준화|
|`PatientIdentityNotDefinedException`|이 오류는 장치 메시지에서 환자 식별자를 구문 분석 하는 식이 장치 매핑에서 구성 되지 않았거나 환자 식별자가 장치 메시지에 없는 경우에 발생 합니다. 참고이 오류는 IoT 커넥터의 해상도 유형이 *만들기* 로 설정 된 경우에만 발생 합니다.|`DeviceTemplateError`|위험|표준화|
|`DeviceIdentityNotDefinedException`|이 오류는 장치 메시지에서 장치 식별자를 구문 분석 하는 식이 장치 매핑에서 구성 되지 않았거나 장치 식별자가 장치 메시지에 없는 경우에 발생 합니다.|`DeviceTemplateError`|위험|표준화|
|`NotSupportedException`|지원 되지 않는 형식의 장치 메시지를 받는 동안 오류가 발생 했습니다.|`DeviceMessageError`|오류|표준화|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>IoT 커넥터 장치 및 FHIR 대상 매핑의 복사본 만들기

IoT 커넥터 매핑을 복사 하면 Azure Portal 웹 사이트 외부에서 편집 및 보관 하는 데 유용할 수 있습니다.

문제 해결 프로세스에 도움이 되는 지원 티켓을 열 때 Azure 기술 지원 서비스에 매핑 복사본을 제공 해야 합니다.

> [!NOTE]
> 현재 JSON은 장치 및 FHIR 대상 매핑에 대해 지원 되는 유일한 형식입니다.

> [!TIP]
> IoT 커넥터 [장치 및 FHIR 대상 매핑에](how-to-use-fhir-mapping-iot.md) 대 한 자세한 정보

1. 의료 Api 작업 영역의 왼쪽에서 **"IoT 커넥터"** 를 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="IoT 커넥터를 선택 합니다." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. 장치를 복사해 넣을 **IoT 커넥터** 의 이름과에서 대상 매핑을 선택 합니다.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > 이 프로세스를 사용 하 여 **"destination"** fhir 대상 매핑의 내용을 복사 하 고 저장할 수도 있습니다.

3. JSON의 콘텐츠를 선택 하 고 복사 작업을 수행 합니다 (예: **ctrl + C** 누르기). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. 붙여넣기 작업(예: **Ctrl +V** 누르기)을 Microsoft Visual Studio Code 또는 메모장 같은 편집기 내의 새 파일에 붙여넣습니다. **.json** 확장자를 사용하여 파일을 저장해야 합니다.

> [!TIP]
> IoT 커넥터에 대한 [Azure 기술 지원](https://azure.microsoft.com/support/create-ticket/) 티켓을 여는 경우 문제 해결 프로세스에 도움이 되도록 디바이스 및 FHIR 대상 매핑의 복사본을 포함해야 합니다.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.