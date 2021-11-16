---
title: Azure IoT Hub AMQP 지원 이해 | Microsoft Docs
description: 개발자 가이드 - AMQP 프로토콜을 사용하여 IoT Hub 디바이스 지향 및 서비스 지향 엔드포인트에 대한 디바이스 연결을 지원합니다. Azure IoT 디바이스 SDK의 기본 제공 AMQP 지원에 대한 정보가 포함되어 있습니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: lizross
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ccdf206c9ccd9d91b125af658bf03d3ec40c4b02
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549986"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>AMQP 프로토콜을 사용하여 IoT 허브와 통신

Azure IoT Hub는 [OASIS AMQP(고급 메시지 큐 프로토콜) 버전 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)을 지원하여 디바이스 지향 및 서비스 지향 엔드포인트를 통해 다양한 기능을 제공합니다. 이 문서에서는 AMQP 클라이언트를 사용하여 IoT Hub 기능을 사용하는 IoT 허브에 연결하는 방법을 설명합니다.

## <a name="service-client"></a>서비스 클라이언트

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>IoT 허브에 연결 및 인증(서비스 클라이언트)

AMQP를 사용하여 IoT 허브에 연결하려면 클라이언트는 [CBS(클레임 기반 보안)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) 또는 [SASL(Simple Authentication and Security Layer) 인증](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)을 사용할 수 있습니다.

서비스 클라이언트에는 다음과 같은 정보가 필요합니다.

| 정보 | 값 |
|-------------|--------------|
| IoT 허브 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 키 이름 | `service` |
| 액세스 키 | 서비스와 연결된 기본 키 또는 보조 키 |
| 공유 액세스 서명 | 단기 실행 공유 액세스 서명 URL의 형식은 다음과 같습니다. `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` 해당 서명을 생성하는 코드를 가져오려면 [IoT Hub에 대한 액세스 제어](./iot-hub-dev-guide-sas.md#security-token-structure)를 참조하세요.

다음 코드 조각은 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 보낸 사람 링크를 통해 IoT 허브에 연결합니다.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>클라우드-디바이스 메시지 호출(서비스 클라이언트)

서비스와 IoT 허브 간 및 디바이스와 IoT 허브 간의 클라우드-디바이스 메시지 교환에 대한 자세한 내용은 [IoT 허브에서 클라우드-디바이스 메시지 보내기](iot-hub-devguide-messages-c2d.md)를 참조하세요. 다음 테이블에서 설명하는 것처럼 서비스 클라이언트는 두 개의 링크를 사용하여 메시지를 보내고 이전에 보낸 메시지에 대한 피드백을 받습니다.

| 만든 사람 | 링크 형식 | 링크 경로 | Description |
|------------|-----------|-----------|-------------|
| 서비스 | 보낸 사람 링크 | `/messages/devicebound` | 디바이스를 대상으로 하는 클라우드-디바이스 메시지는 서비스에 의해 해당 링크로 전송됩니다. 해당 링크를 통해 전송되는 메시지에는 대상 디바이스의 받는 사람 링크 경로인 `/devices/<deviceID>/messages/devicebound`로 설정된 `To` 속성이 있습니다. |
| 서비스 | 받는 사람 링크 | `/messages/serviceBound/feedback` | 서비스에서 해당 링크를 받은 디바이스에서 제공하는 완료, 거부 및 중단 피드백 메시지입니다. 피드백 메시지에 대한 자세한 내용은 [IoT 허브에서 클라우드-디바이스 메시지 보내기](./iot-hub-devguide-messages-c2d.md#message-feedback)를 참조하세요. |

다음 코드 조각에서는 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 클라우드-디바이스 메시지를 만들고 이를 디바이스로 보내는 방법을 보여 줍니다.

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

피드백을 받기 위해 서비스 클라이언트는 받는 사람 링크를 만듭니다. 다음 코드 조각에서는 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 링크를 만드는 방법을 보여 줍니다.

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

위의 코드에 표시된 것처럼 클라우드-디바이스 피드백 메시지의 콘텐츠 형식은 *application/vnd.microsoft.iothub.feedback.json* 입니다. 메시지의 JSON 본문에서 속성을 사용하여 원본 메시지의 전송 상태를 유추할 수 있습니다.

* 피드백 본문의 `statusCode` 키에는 *성공*, *만료됨*, *DeliveryCountExceeded*, *거부됨* 또는 *제거됨* 과 같은 값 중 하나가 있습니다.

* 피드백 본문의 `deviceId` 키에는 대상 디바이스의 ID가 있습니다.

* 피드백 본문의 `originalMessageId` 키에는 서비스에서 보낸 원본 클라우드-디바이스 메시지의 ID가 있습니다. 전송 상태를 사용하여 피드백을 클라우드-디바이스 메시지와 상호 연결할 수 있습니다.

### <a name="receive-telemetry-messages-service-client"></a>원격 분석 메시지 받기(서비스 클라이언트)

기본적으로 IoT 허브는 수집 디바이스 원격 분석 메시지를 기본 제공 이벤트 허브에 저장합니다. 서비스 클라이언트는 AMQP 프로토콜을 사용하여 저장된 이벤트를 받을 수 있습니다.

이러한 목적을 위해 서비스 클라이언트는 먼저 IoT 허브 엔드포인트에 연결하고 기본 제공 이벤트 허브에 대한 리디렉션 주소를 받아야 합니다. 그러면 서비스 클라이언트는 제공된 주소를 사용하여 기본 제공 이벤트 허브에 연결합니다.

각 단계에서 클라이언트는 다음과 같은 정보를 제공해야 합니다.

* 유효한 서비스 자격 증명(서비스 공유 액세스 서명 토큰).

* 메시지를 검색할 소비자 그룹 파티션에 대한 올바른 형식의 경로. 지정된 소비자 그룹 및 파티션 ID의 경우 경로의 형식은 `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>`(기본 소비자 그룹은 `$Default`)입니다.

* 파티션의 시작 지점을 지정하는 선택적 필터링 조건자. 해당 조건자는 시퀀스 번호, 오프셋 또는 큐에 넣은 타임스탬프 형식이 될 수 있습니다.

다음 코드 조각은 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 이전 단계를 보여 줍니다.

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

주어진 디바이스 ID의 경우 IoT 허브는 디바이스 ID의 해시를 사용하여 메시지를 저장할 파티션을 결정합니다. 위의 코드 조각에서는 단일 파티션에서 이벤트를 받는 방법을 보여 줍니다. 그러나 일반적인 애플리케이션에서는 모든 이벤트 허브 파티션에 저장된 이벤트를 검색해야 하는 경우가 많습니다.

## <a name="device-client"></a>디바이스 클라이언트

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>IoT 허브에 연결 및 인증(디바이스 클라이언트)

AMQP를 사용하여 IoT 허브에 연결하기 위해 디바이스는 [CBS(클레임 기반 보안](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)) 또는 [SASL(Simple Authentication and Security Layer))](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) 인증을 사용할 수 있습니다.

디바이스 클라이언트에는 다음과 같은 정보가 필요합니다.

| 정보 | 값 |
|-------------|--------------|
| IoT 허브 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 액세스 키 | 디바이스와 연결된 기본 키 또는 보조 키 |
| 공유 액세스 서명 | 단기 실행 공유 액세스 서명 URL의 형식은 다음과 같습니다. `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` 해당 서명을 생성하는 코드를 가져오려면 [IoT Hub에 대한 액세스 제어](./iot-hub-dev-guide-sas.md#security-token-structure)를 참조하세요.

다음 코드 조각은 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 보낸 사람 링크를 통해 IoT 허브에 연결합니다.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

다음 링크 경로는 디바이스 작업으로 지원됩니다.

| 만든 사람 | 링크 형식 | 링크 경로 | Description |
|------------|-----------|-----------|-------------|
| 디바이스 | 받는 사람 링크 | `/devices/<deviceID>/messages/devicebound` | 디바이스를 대상으로 하는 클라우드-디바이스 메시지는 각 대상 디바이스에 의해 해당 링크에서 받습니다. |
| 디바이스 | 보낸 사람 링크 | `/devices/<deviceID>/messages/events` | 디바이스에서 전송되는 디바이스-클라우드 메시지는 해당 링크를 통해 전송됩니다. |
| 디바이스 | 보낸 사람 링크 | `/messages/serviceBound/feedback` | 디바이스에서 해당 링크를 통해 서비스로 전송되는 클라우드-디바이스 메시지 피드백입니다. |

### <a name="receive-cloud-to-device-commands-device-client"></a>클라우드-디바이스 명령 받기(디바이스 클라이언트)

디바이스로 전송되는 클라우드-디바이스 명령이 `/devices/<deviceID>/messages/devicebound` 링크에 도착합니다. 디바이스는 이러한 메시지를 일괄 처리로 받아 필요에 따라 메시지 데이터 페이로드, 메시지 속성, 주석 또는 메시지의 애플리케이션 속성을 사용할 수 있습니다.

다음 코드 조각은 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 디바이스에서 클라우드-디바이스 메시지를 받습니다.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>원격 분석 메시지 보내기(디바이스 클라이언트)

AMQP를 사용하여 디바이스에서 원격 분석 메시지를 보낼 수도 있습니다. 필요에 따라 디바이스는 애플리케이션 속성 사전을 제공하거나 메시지 ID와 같은 다양한 메시지 속성을 제공할 수 있습니다.

다음 코드 조각은 [Python의 uAMQP 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용하여 디바이스에서 디바이스-클라우드 메시지를 보냅니다.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>추가적인 참고 사항

* 네트워크 결함 또는 인증 토큰의 만료(코드에서 생성됨)로 인해 AMQP 연결이 중단될 수 있습니다. 서비스 클라이언트는 이러한 상황을 처리하고 필요한 경우 연결 및 링크를 다시 설정해야 합니다. 인증 토큰이 만료되는 경우 만료 전에 토큰을 사전에 갱신하여 연결 삭제를 방지할 수 있습니다.

* 클라이언트는 때때로 링크 리디렉션을 올바르게 처리할 수 있어야 합니다. 이러한 작업을 이해하려면 AMQP 클라이언트 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

AMQP 프로토콜에 대한 자세한 내용은 [AMQP v1.0 사양](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)을 참조하세요.

IoT Hub 메시징에 대해 자세히 알아보려면 다음을 참조합니다.

* [클라우드-디바이스 메시지](./iot-hub-devguide-messages-c2d.md)
* [추가 프로토콜에 대한 지원](iot-hub-protocol-gateway.md)
* [MQTT(메시지 큐 원격 분석 전송) 프로토콜에 대한 지원](./iot-hub-mqtt-support.md)
