---
title: Azure IoT Hub TLS 지원
description: IoT Hub와 통신하는 디바이스 및 서비스에 대해 보안 TLS 연결을 사용하는 방법 알아보기
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: jlian
ms.openlocfilehash: 834ccc850a3099857dc1b88e45b0c0c3ee3a186c
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109486452"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT Hub에서 TLS(전송 계층 보안) 지원

IoT Hub는 TLS(전송 계층 보안)를 사용하여 IoT 디바이스 및 서비스의 연결을 보호합니다. 현재 세 가지 버전의 TLS 프로토콜(버전 1.0, 1.1 및 1.2)이 지원됩니다.

TLS 1.0 및 1.1은 레거시로 간주되며 사용 중단될 예정입니다. 자세한 내용은 [IoT Hub용 TLS 1.0 및 1.1 사용 중단](iot-hub-tls-deprecating-1-0-and-1-1.md)을 참조하세요. 향후 문제를 방지하려면 IoT Hub에 연결할 때 TLS 버전으로 TLS 1.2만을 사용합니다.

## <a name="iot-hubs-server-tls-certificate"></a>IoT Hub의 서버 TLS 인증서

TLS 핸드셰이크 도중 IoT Hub는 클라이언트 연결을 위해 RSA 키 서버 인증서를 제공합니다. 루트는 Baltimore Cybertrust Root CA입니다. 최근 새 중개 인증 기관(ICA)에서 발급하도록 TLS 서버 인증서에 대한 변경 사항이 출시되었습니다. 자세한 내용은 [IoT Hub TLS 인증서 업데이트](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)를 참조하세요.

### <a name="4kb-size-limit-on-renewal"></a>갱신 시 4KB 크기 제한

IoT Hub 서버 측 인증서를 갱신하는 동안 `Server Hello`가 4KB 크기를 초과하지 않도록 IoT Hub 서비스 측에서 확인이 이루어집니다. 클라이언트는 수신 TLS 최대 콘텐츠 길이 버퍼에 대해 최소 4KB 이상의 RAM을 보유해야 합니다. 그래야 4KB 제한이 설정된 기존 디바이스가 인증서 갱신 후에도 계속 작동합니다. 제약이 있는 디바이스의 경우 IoT Hub는 [TLS 최대 조각 길이 협상(미리 보기)](#tls-maximum-fragment-length-negotiation-preview)을 지원합니다. 

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>ECC(타원 곡성 암호화) 서버 TLS 인증서(미리 보기)

IoT Hub ECC 서버 TLS 인증서는 공개 미리 보기로 제공됩니다. RSA 인증서에 유사한 보안을 제공하면서 ECC 인증서 유효성 검사(ECC 전용 암호 그룹 사용)는 최대 40% 절감된 컴퓨팅, 메모리 및 대역폭을 사용합니다. 이러한 절감은 작은 프로필 및 메모리로 인해 IoT 디바이스에서 중요하며, 네트워크 대역폭이 제한된 환경에서 사용 사례를 지원합니다. ECC 서버 인증서의 루트는 DigiCert Global Root G3입니다.

IoT Hub의 ECC 서버 인증서를 미리 보려면 다음을 수행합니다.

1. [미리 보기 모드를 사용하여 새 IoT 허브를 만듭니다](iot-hub-preview-mode.md).
1. ECDSA 암호 그룹 *만* 을 포함하여 모든 RSA 암호 그룹을 *제외* 하도록 [클라이언트를 구성](#tls-configuration-for-sdk-and-iot-edge)합니다. ECC 인증서 공개 미리 보기에 대해 지원되는 암호 그룹입니다.
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. 클라이언트를 미리 보기 IoT 허브에 연결합니다.

## <a name="tls-12-enforcement-available-in-select-regions"></a>일부 지역에서 TLS 1.2 적용 가능

보안을 강화하려면 TLS 버전 1.2를 사용하는 클라이언트 연결 *만* 허용하고 [암호 그룹](#cipher-suites) 사용을 적용하도록 IoT Hub를 구성합니다. 이 기능은 다음 지역에서만 지원됩니다.

* 미국 동부
* 미국 중남부
* 미국 서부 2
* US Gov 애리조나
* US Gov 버지니아(TLS 1.0/1.1 지원이 이 지역에서는 가능하지 않음 - TLS 1.2 적용을 사용 설정하지 않으면 IoT 허브 생성 실패)

TLS 1.2 적용을 사용하려면 [Azure Portal에서 IoT 허브 만들기](iot-hub-create-through-portal.md)의 단계를 따르되, 다음은 예외입니다.

- 위의 목록 중 하나에서 **지역** 을 선택합니다.
- **관리 -> 고급 -> 전송 계층 보안(TLS) -> 최소 TLS 버전** 에서 **1.2** 를 선택합니다. 이 설정은 지원되는 지역에서 만든 IoT 허브에 대해서만 표시됩니다.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="IoT 허브를 만드는 도중 TLS 1.2 적용을 설정하는 방법을 보여주는 스크린샷":::

ARM 템플릿을 사용하여 만들려면 지원되는 지역에서 새로운 IoT 허브를 프로비전하고 리소스 사양에서 `minTlsVersion` 속성을 `1.2`로 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

이 구성을 사용하여 만든 IoT Hub 리소스는 TLS 버전 1.0 및 1.1을 사용하여 연결을 시도하는 디바이스 및 서비스 클라이언트를 거부합니다. 마찬가지로, `ClientHello` 메시지에 [권장되는 암호](#cipher-suites)가 나열되지 않는 경우 TLS 핸드셰이크가 거부됩니다.

> [!NOTE]
> `minTlsVersion` 속성은 읽기 전용이며 IoT Hub 리소스를 만든 후에는 변경할 수 없습니다. 따라서 *모든*  IoT 디바이스 및 서비스가 TLS 1.2 및 [권장되는 암호](#cipher-suites)와 호환되는지 적절히 테스트하고 유효성을 검사하는 것이 중요합니다.
> 
> 장애 조치(failover) 시 IoT Hub의 `minTlsVersion` 속성은 지리적으로 쌍을 이루는 지역에서 장애 조치 후에 적용됩니다.

## <a name="cipher-suites"></a>암호 그룹

TLS 1.2만 허용하도록 구성된 IoT Hub는 다음과 같은 권장 암호 그룹 사용도 적용합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1.2 적용을 위해 구성되지 않은 IoT Hub의 경우 TLS 1.2는 여전히 다음 암호 그룹을 사용합니다.

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

클라이언트에서 `ClientHello` 도중 사용할 더 높은 암호 그룹 목록을 제안할 수 있습니다. 하지만 일부는 IoT Hub에서 지원되지 않을 수 있습니다(예: `ECDHE-ECDSA-AES256-GCM-SHA384`). 이 경우 IoT Hub는 클라이언트의 기본 설정을 따르도록 시도하지만 결국 `ServerHello`를 사용하여 암호 그룹을 협상합니다.

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>SDK 및 IoT Edge에 대한 TLS 구성

IoT Hub 클라이언트 SDK에서 TLS 1.2 및 허용되는 암호를 구성하려면 아래 링크를 사용하세요.

| 언어 | TLS 1.2를 지원하는 버전 | 문서화 |
|----------|------------------------------------|---------------|
| C        | 태그 2019-12-11 이상            | [링크](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 버전 2.0.0 이상             | [링크](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 버전 1.21.4 이상            | [링크](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 버전 1.19.0 이상            | [링크](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 버전 1.12.2 이상            | [링크](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge 디바이스는 IoT Hub와 통신할 때 TLS 1.2를 사용하도록 구성할 수 있습니다. 이러한 목적을 위해 [IoT Edge 설명서 페이지](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)를 사용합니다.

## <a name="device-authentication"></a>디바이스 인증

TLS 핸드셰이크에 성공한 이후 IoT Hub는 대칭 키 또는 X.509 인증서를 사용하여 디바이스를 인증할 수 있습니다. 인증서 기반 인증의 경우 ECC를 포함한 모든 X.509 인증서를 사용할 수 있습니다. IoT Hub는 제공하는 지문 또는 CA(인증 기관)에 대해 인증서의 유효성을 검사합니다. 자세한 내용은 [지원되는 X.509 인증서](iot-hub-dev-guide-sas.md#supported-x509-certificates)를 참조하세요.

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS 최대 조각 길이 협상(미리 보기)

IoT Hub는 또한 TLS 프레임 크기 협상이라고도 하는 TLS 최대 조각 길이 협상도 지원합니다. 이 기능은 공개 미리 보기 상태입니다. 

이 기능을 사용하여 최대 일반 텍스트 조각 길이를 기본값 2^14바이트 보다 작은 값으로 지정할 수 있습니다. 협상이 완료되면 IoT Hub와 클라이언트는 모든 조각이 협상된 길이보다 작도록 메시지 조각화를 시작합니다. 이 동작은 컴퓨팅 또는 메모리 제한이 있는 디바이스에 유용합니다. 자세한 내용은 [공식 TLS 확장 사양](https://tools.ietf.org/html/rfc6066#section-4)을 참조하세요.

이 공개 미리 보기 기능에 대한 공식 SDK 지원은 아직 제공되지 않습니다. 시작하려면

1. [미리 보기 모드를 사용하여 새 IoT 허브를 만듭니다](iot-hub-preview-mode.md).
1. OpenSSL을 사용할 때 [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html)를 호출하여 조각 크기를 지정합니다.
1. 클라이언트를 미리 보기 IoT Hub에 연결합니다.

## <a name="next-steps"></a>다음 단계

- IoT Hub 보안 및 액세스 제어에 대한 자세한 내용은 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)를 참조하세요.
- 디바이스 인증에 X509 인증서 사용에 대해 자세히 알아보려면 [X.509 CA 인증서를 사용한 디바이스 인증](iot-hub-x509ca-overview.md)을 참조하세요.
