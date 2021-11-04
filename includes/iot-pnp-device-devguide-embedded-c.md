---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 09/07/2021
ms.openlocfilehash: 3f580069fc44f676bc6fcffa40bd74256a818a50
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520806"
---
## <a name="sdks"></a>SDK

이 문서의 코드 조각은 Azure RTOS Azure IoT [미들웨어 추가를](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)사용하는 샘플을 기반으로 합니다. addon은 [Azure RTOS](/azure/rtos/overview-rtos) [Azure SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)간의 바인딩 계층입니다.

이 문서의 코드 조각은 다음 샘플을 기반으로 합니다.

- [샘플 IoT 플러그 앤 플레이 자동 온도 조절기](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/sample_azure_iot_embedded_sdk_pnp.c)
- [샘플 IoT 플러그 앤 플레이 온도 컨트롤러](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot/samples/sample_pnp_temperature_controller)

## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리려면 디바이스가 연결 정보에 해당 ID를 포함해야 합니다.

```c
#include "nx_azure_iot_hub_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"

// ...

status = nx_azure_iot_hub_client_model_id_set(iothub_client_ptr, (UCHAR *)SAMPLE_PNP_MODEL_ID, sizeof(SAMPLE_PNP_MODEL_ID) - 1);
```

> [!TIP]
> 디바이스가 모델 ID를 설정할 수 있는 유일한 시간이며 디바이스 연결 후에는 업데이트할 수 없습니다.

## <a name="dps-payload"></a>DPS 페이로드

[DPS(Device Provisioning Service)를](../articles/iot-dps/about-iot-dps.md) 사용하는 디바이스에는 `modelId` 다음 JSON 페이로드를 사용하여 프로비전 프로세스 중에 사용할 가 포함될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

샘플에서는 다음 코드를 사용하여 이 페이로드를 보냅니다.

```c
#include "nx_azure_iot_provisioning_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"
#define SAMPLE_PNP_DPS_PAYLOAD "{\"modelId\":\"" SAMPLE_PNP_MODEL_ID "\"}"

// ...

status = nx_azure_iot_provisioning_client_registration_payload_set(prov_client_ptr, (UCHAR *)SAMPLE_PNP_DPS_PAYLOAD, sizeof(SAMPLE_PNP_DPS_PAYLOAD) - 1);
```

## <a name="use-components"></a>구성 요소 사용

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](../articles/iot-develop/concepts-modeling-guide.md)에 설명된 대로 디바이스 빌더가 구성 요소를 사용하여 디바이스를 설명할지 결정해야 합니다. 구성 요소를 사용하는 경우 디바이스는 다음 섹션에 설명된 규칙을 따라야 합니다. 구성 요소에 대한 IoT 플러그 앤 플레이 규칙 작업을 간소화하기 위해 샘플은 [nx_azure_iot_hub_client.h](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/nx_azure_iot_hub_client.h)의 도우미 함수를 사용합니다.

## <a name="telemetry"></a>원격 분석

기본 구성 요소에는 원격 분석 메시지에 추가된 특수 속성이 필요하지 않습니다.

중첩된 구성 요소를 사용하는 경우 디바이스는 구성 요소 이름으로 메시지 속성을 설정해야 합니다. 다음 조각에서 `component_name_ptr` 는 와 같은 구성 요소의 이름입니다. `thermostat1` `nx_azure_iot_pnp_helper_telemetry_message_create` *nx_azure_iot_pnp_helpers.h에* 정의된 도우미 함수는 구성 요소 이름을 가진 메시지 속성을 추가합니다.

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR telemetry_name[] = "temperature";

// ...

UINT sample_pnp_thermostat_telemetry_send(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT status;
NX_PACKET *packet_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
UINT buffer_length;

    // ...

    /* Create a telemetry message packet. */
    if ((status = nx_azure_iot_pnp_helper_telemetry_message_create(iothub_client_ptr, handle -> component_name_ptr,
        handle -> component_name_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_hub_client_telemetry_send(iothub_client_ptr, packet_ptr,
        (UCHAR *)scratch_buffer, buffer_length, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    return(status);
}
```

## <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고하는 데 특별한 구문이 필요하지 않습니다.

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

// ...

static UINT sample_build_reported_property(NX_AZURE_IOT_JSON_WRITER *json_builder_ptr, double temp)
{
UINT ret;

    if (nx_azure_iot_json_writer_append_begin_object(json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(json_builder_ptr,
            (UCHAR *)reported_max_temp_since_last_reboot,
            sizeof(reported_max_temp_since_last_reboot) - 1,
            temp, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_end_object(json_builder_ptr))
    {
        ret = 1;
        printf("Failed to build reported property\r\n");
    }
    else
    {
        ret = 0;
    }

    return(ret);
}

// ...

if ((status = sample_build_reported_property(&json_builder, device_max_temp)))
{
    // ...
}

reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(&(context -> iothub_client),
    scratch_buffer,
    reported_properties_length,
    &request_id, &response_status,
    &reported_property_version,
    (5 * NX_IP_PERIODIC_RATE))))
{
    // ...
}
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

중첩된 구성 요소를 사용하는 경우 구성 요소 이름 내에 속성을 만들고 표식과 표식이 포함되어야 합니다. 다음 조각에서 `component_name_ptr` 는 와 같은 구성 요소의 이름입니다. `thermostat1` `nx_azure_iot_pnp_helper_build_reported_property` *nx_azure_iot_pnp_helpers.h에* 정의된 도우미 함수는 reported 속성을 올바른 형식으로 만듭니다.

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

UINT sample_pnp_thermostat_report_max_temp_since_last_reboot_property(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT reported_properties_length;
UINT status;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_builder;
ULONG reported_property_version;

    // ...

    if ((status = nx_azure_iot_pnp_helper_build_reported_property(handle -> component_name_ptr,
        handle -> component_name_length,
        append_max_temp, (VOID *)handle,
        &json_builder)))
    {
        // ...
    }

    reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
    if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(iothub_client_ptr,
        scratch_buffer,
        reported_properties_length,
        &request_id, &response_status,
        &reported_property_version,
        (5 * NX_IP_PERIODIC_RATE))))
    {
        // ...
    }

    // ...
}
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
    "reported": {
        "thermostat1" : {  
            "__t" : "c",  
            "maxTemperature" : 38.7
        } 
    }
}
```

## <a name="writable-properties"></a>쓰기 가능한 속성

이러한 속성은 디바이스에서 설정하거나 솔루션에서 업데이트할 수 있습니다. IoT 플러그 앤 플레이 규칙을 따르려면 디바이스에서 속성이 성공적으로 수신되었음을 서비스에 알려야 합니다.

### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

디바이스에서 쓰기 가능한 속성을 보고하는 경우 규칙에 정의된 `ack` 값을 포함해야 합니다.

기본 구성 요소에서 쓰기 가능한 속성을 보고하려면 다음을 수행합니다.

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_temp_property_name[] = "targetTemperature";
static const CHAR reported_value_property_name[] = "value";
static const CHAR reported_status_property_name[] = "ac";
static const CHAR reported_version_property_name[] = "av";
static const CHAR reported_description_property_name[] = "ad";

// ...

static VOID sample_send_target_temperature_report(SAMPLE_CONTEXT *context, double current_device_temp_value,
    UINT status, UINT version, UCHAR *description_ptr,
    UINT description_len)
{
NX_AZURE_IOT_JSON_WRITER json_builder;
UINT bytes_copied;
UINT response_status;
UINT request_id;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_name(&json_builder,
            (UCHAR *)reported_temp_property_name,
            sizeof(reported_temp_property_name) - 1) ||
        nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_with_double_value(&json_builder,
            (UCHAR *)reported_value_property_name,
            sizeof(reported_value_property_name) - 1,
            current_device_temp_value, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_status_property_name,
            sizeof(reported_status_property_name) - 1,
            (int32_t)status) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_version_property_name,
            sizeof(reported_version_property_name) - 1,
            (int32_t)version) ||
        nx_azure_iot_json_writer_append_property_with_string_value(&json_builder,
            (UCHAR *)reported_description_property_name,
            sizeof(reported_description_property_name) - 1,
            description_ptr, description_len) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder))
    {
        // ...
    }
    else
    // ...
}
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
  }
}
```

중첩된 구성 요소에서 쓰기 가능한 속성을 보고하려면 쌍에 표식이 포함되어야 하며 구성 요소 이름 내에 속성을 만들어야 합니다. 다음 조각에서 `component_name_ptr` 는 와 같은 구성 요소의 이름입니다. `thermostat1` `nx_azure_iot_pnp_helper_build_reported_property_with_status` *nx_azure_iot_pnp_helpers.h에* 정의된 도우미 함수는 reported 속성 페이로드를 만듭니다.

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_send_target_temperature_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr, double temp,
    INT status_code, UINT version, const CHAR *description)
{
UINT bytes_copied;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_writer;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_pnp_helper_build_reported_property_with_status(handle -> component_name_ptr, handle -> component_name_length,
        (UCHAR *)target_temp_property_name,
        sizeof(target_temp_property_name) - 1,
        append_temp, (VOID *)&temp, status_code,
        (UCHAR *)description,
        strlen(description), version, &json_writer))
    {
        // ...
    }
    else
    {
        // ...
    }

    // ...
}
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>원하는 속성 업데이트 구독

서비스는 연결된 디바이스에 대한 알림을 트리거하는 원하는 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트된 desired 속성과 업데이트를 식별하는 버전 번호가 포함됩니다. 디바이스는 서비스로 다시 전송된 메시지에 이 버전 번호를 포함해야  `ack` 합니다.

기본 구성 요소는 단일 속성을 보고 수신된 버전으로 보고된 `ack`를 만듭니다.

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR temp_response_description[] = "success";

// ...

static UINT sample_parse_desired_temp_property(SAMPLE_CONTEXT *context,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    UINT is_partial)
{
double parsed_value;
UINT version;
NX_AZURE_IOT_JSON_READER copy_json_reader;
UINT status;

    // ...

    copy_json_reader = *json_reader_ptr;
    if (sample_json_child_token_move(&copy_json_reader,
            (UCHAR *)desired_version_property_name,
            sizeof(desired_version_property_name) - 1) ||
        nx_azure_iot_json_reader_token_int32_get(&copy_json_reader, (int32_t *)&version))
    {
        // ...
    }

    // ...

    sample_send_target_temperature_report(context, current_device_temp, 200,
        (UINT)version, (UCHAR *)temp_response_description,
        sizeof(temp_response_description) - 1);

    // ...
}
```

중첩된 구성 요소는 구성 요소 이름으로 래핑된 desired 속성을 수신하고 `ack` 수신된 버전으로 reported를 만듭니다.

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR target_temp_property_name[] = "targetTemperature";
static const CHAR temp_response_description_success[] = "success";
static const CHAR temp_response_description_failed[] = "failed";

// ...

UINT sample_pnp_thermostat_process_property_update(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr,
    UCHAR *component_name_ptr, UINT component_name_length,
    UCHAR *property_name_ptr, UINT property_name_length,
    NX_AZURE_IOT_JSON_READER *property_value_reader_ptr, UINT version)
{
double parsed_value = 0;
INT status_code;
const CHAR *description;

    // ...

    if (property_name_length != (sizeof(target_temp_property_name) - 1) ||
        strncmp((CHAR *)property_name_ptr, (CHAR *)target_temp_property_name, property_name_length) != 0)
    {
        // ...
    }
    else if (nx_azure_iot_json_reader_token_double_get(property_value_reader_ptr, &parsed_value))
    {
        status_code = 401;
        description = temp_response_description_failed;
    }
    else
    {
        status_code = 200;
        description = temp_response_description_success;

        // ...
    }

    sample_send_target_temperature_report(handle, iothub_client_ptr, parsed_value,
                                          status_code, version, description);

    // ...
}
```

중첩된 구성 요소에 대한 디바이스 쌍은 다음과 같이 원하는 섹션과 보고된 섹션을 보여줍니다.

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

## <a name="commands"></a>명령

기본 구성 요소는 서비스에서 호출된 명령 이름을 받습니다.

중첩 된 구성 요소는 구성 요소 이름과 `*` 구분 기호가 접두사로 붙은 명령 이름을 받습니다. 다음 조각에서 `nx_azure_iot_pnp_helper_command_name_parse` *nx_azure_iot_pnp_helpers.h에* 정의된 도우미 함수는 디바이스가 서비스에서 수신하는 메시지에서 구성 요소 이름과 명령 이름을 구문 분석합니다.

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_direct_method_action(SAMPLE_CONTEXT *sample_context_ptr)
{
NX_PACKET *packet_ptr;
UINT status;
USHORT method_name_length;
const UCHAR *method_name_ptr;
USHORT context_length;
VOID *context_ptr;
UINT component_name_length;
const UCHAR *component_name_ptr;
UINT pnp_command_name_length;
const UCHAR *pnp_command_name_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
NX_AZURE_IOT_JSON_READER json_reader;
NX_AZURE_IOT_JSON_READER *json_reader_ptr;
UINT status_code;
UINT response_length;

    // ...

    if ((status = nx_azure_iot_hub_client_direct_method_message_receive(&(sample_context_ptr -> iothub_client),
        &method_name_ptr, &method_name_length,
        &context_ptr, &context_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_pnp_helper_command_name_parse(method_name_ptr, method_name_length,
        &component_name_ptr, &component_name_length,
        &pnp_command_name_ptr,
        &pnp_command_name_length)) != NX_AZURE_IOT_SUCCESS)
    {
        // ...
    }
    
    // ...

    else
    {
        // ...

        if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_1, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_2, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if((status = sample_pnp_temp_controller_process_command(component_name_ptr, component_name_length,
            pnp_command_name_ptr, pnp_command_name_length,
            json_reader_ptr, &json_writer,
            &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else
        {
            printf("Failed to find any handler for method %.*s\r\n", method_name_length, method_name_ptr);
            status_code = SAMPLE_COMMAND_NOT_FOUND_STATUS;
            response_length = 0;
        }

        // ...
    }
}
```

### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용하여 요청 및 응답 페이로드를 정의합니다. 디바이스는 들어오는 입력 매개 변수를 역직렬화하고 응답을 직렬화해야 합니다.

다음 예제에서는 페이로드에 정의된 복합 형식을 사용하여 명령을 구현하는 방법을 보여줍니다.

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

다음 코드 조각은 직렬화 및 역직렬화를 사용 설정하는 데 사용되는 형식을 포함하여 디바이스가 이 명령 정의를 구현하는 방법을 보여줍니다.

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR report_max_temp_name[] = "maxTemp";
static const CHAR report_min_temp_name[] = "minTemp";
static const CHAR report_avg_temp_name[] = "avgTemp";
static const CHAR report_start_time_name[] = "startTime";
static const CHAR report_end_time_name[] = "endTime";
static const CHAR fake_start_report_time[] = "2020-01-10T10:00:00Z";
static const CHAR fake_end_report_time[] = "2023-01-10T10:00:00Z";

// ...

static UINT sample_get_maxmin_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    NX_AZURE_IOT_JSON_WRITER *out_json_builder_ptr)
{
UINT status;
UCHAR *start_time = (UCHAR *)fake_start_report_time;
UINT start_time_len = sizeof(fake_start_report_time) - 1;
UCHAR time_buf[32];

    // ...

    /* Build the method response payload */
    if (nx_azure_iot_json_writer_append_begin_object(out_json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_max_temp_name,
            sizeof(report_max_temp_name) - 1,
            handle -> maxTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_min_temp_name,
            sizeof(report_min_temp_name) - 1,
            handle -> minTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_avg_temp_name,
            sizeof(report_avg_temp_name) - 1,
            handle -> avgTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_start_time_name,
            sizeof(report_start_time_name) - 1,
            (UCHAR *)start_time, start_time_len) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_end_time_name,
            sizeof(report_end_time_name) - 1,
            (UCHAR *)fake_end_report_time,
            sizeof(fake_end_report_time) - 1) ||
        nx_azure_iot_json_writer_append_end_object(out_json_builder_ptr))
    {
        status = NX_NOT_SUCCESSFUL;
    }
    else
    {
        status = NX_AZURE_IOT_SUCCESS;
    }

    return(status);
}
```

> [!Tip]
> 유선을 통해 전송되는 직렬화된 페이로드에 요청 및 응답 이름이 없습니다.
