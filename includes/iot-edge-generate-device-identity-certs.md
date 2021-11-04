---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 374fcb6470ab22bfd3531d9ef543bf3cc06e17ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576791"
---
## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

X.509 인증서를 사용하여 수동으로 프로비저닝하려면 IoT Edge 버전 1.0.10 이상이 필요합니다.

X.509 인증서를 사용하여 IoT Edge 디바이스를 프로비저닝하는 경우에는 ‘디바이스 ID 인증서’를 사용합니다. 이 인증서는 IoT Edge 디바이스를 프로비저닝하고 Azure IoT Hub에서 디바이스를 인증하는 데만 사용됩니다. 다른 인증서에 서명하지 않는 리프 인증서입니다. 디바이스 ID 인증서는 IoT Edge 디바이스가 확인을 위해 모듈 또는 다운스트림 디바이스에 제시하는 CA(인증 기관) 인증서와는 별개입니다.

X.509 인증서 인증의 경우 각 디바이스의 인증 정보는 디바이스 ID 인증서에서 가져온 ‘지문’ 형태로 제공됩니다. 디바이스 등록 시 IoT Hub에 이 지문이 제공되어 서비스가 연결될 때 디바이스를 인식할 수 있습니다.

IoT Edge 디바이스에서 CA 인증서를 사용하는 방법에 대한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해](../articles/iot-edge/iot-edge-certs.md)를 참조하세요.

X.509를 사용한 수동 프로비저닝에는 다음 파일이 필요합니다.

* .cer 또는. pem 형식의 일치하는 프라이빗 키 인증서를 포함하는 두 개의 디바이스 ID 인증서.

  하나의 인증서/키 파일 집합이 IoT Edge 런타임에 제공됩니다. 디바이스 ID 인증서를 만들 때 IoT 허브의 디바이스에 원하는 디바이스 ID로 인증서 일반 이름(CN)을 설정합니다.

* 디바이스 ID 인증서에서 가져온 지문.

  지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다. 디바이스 등록 시 IoT Hub에 두 지문이 모두 제공됩니다.

인증서가 없는 경우 [IoT Edge 디바이스 기능을 테스트하기 위한 데모 인증서](../articles/iot-edge/how-to-create-test-certificates.md)를 만들 수 있습니다. 이 문서의 안내에 따라 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만든 다음 두 개의 IoT Edge 디바이스 ID 인증서를 만듭니다.

인증서에서 지문을 검색하는 한 가지 방법은 다음 openssl 명령을 사용하는 것입니다.

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```
