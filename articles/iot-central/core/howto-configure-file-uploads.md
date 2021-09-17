---
title: 디바이스에서 Azure 스토리지로 파일 업로드 | Microsoft Docs
description: 디바이스에서 클라우드로 파일 업로드를 구성하는 방법입니다. 파일 업로드를 구성한 후 디바이스에서 파일 업로드를 구현합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2021
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 029571010d80aa6249d91f3d678e273cc5a44279
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468009"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>디바이스에서 클라우드로 파일 업로드

IoT Central을 사용하면 연결된 디바이스에서 클라우드 스토리지로 미디어 및 기타 파일을 업로드할 수 있습니다. IoT Central 애플리케이션에서 파일 업로드 기능을 구성한 다음, 디바이스 코드에서 파일 업로드를 구현합니다.

## <a name="prerequisites"></a>필수 구성 요소

파일 업로드를 구성하려면 IoT Central 애플리케이션에서 관리자여야 합니다.

업로드된 파일을 저장하려면 Azure 스토리지 계정 및 컨테이너가 필요합니다. 사용할 기존 스토리지 계정 및 컨테이너가 없는 경우 [Azure Portal에서 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다.

## <a name="configure-device-file-uploads"></a>디바이스 파일 업로드 구성

디바이스 파일 업로드를 구성하려면 다음을 수행합니다.

1. 애플리케이션의 **관리** 섹션으로 이동합니다.

1. **디바이스 파일 업로드** 를 선택합니다.

1. 사용할 스토리지 계정 및 컨테이너를 선택합니다. 스토리지 계정이 애플리케이션과 다른 Azure 구독에 있는 경우 스토리지 계정 연결 문자열을 입력합니다.

1. 필요한 경우 업로드 요청이 유효한 상태로 유지되는 시간을 설정하는 업로드 시간 제한을 조정합니다. 유효한 값은 1~24시간입니다.

1. **저장** 을 선택합니다. 상태가 **구성됨** 으로 표시되면 디바이스에서 파일을 업로드할 준비가 된 것입니다.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="애플리케이션에서 파일 업로드 구성":::

## <a name="disable-device-file-uploads"></a>디바이스 파일 업로드 사용 안 함

IoT Central 애플리케이션에 대한 디바이스 파일 업로드를 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 애플리케이션의 **관리** 섹션으로 이동합니다.

1. **디바이스 파일 업로드** 를 선택합니다.

1. **삭제** 를 선택합니다.

## <a name="upload-a-file-from-a-device"></a>디바이스에서 파일 업로드

IoT Central은 IoT Hub의 파일 업로드 기능을 사용하여 디바이스에서 파일을 업로드할 수 있도록 합니다. 디바이스에서 파일을 업로드하는 방법을 보여 주는 샘플 코드는 [IoT Central 파일 업로드 디바이스 샘플](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 IoT Central에서 디바이스 파일 업로드를 구성하고 구현하는 방법을 배웠으므로 다음 단계는 디바이스 파일 업로드에 대해 자세히 알아보는 것입니다.

- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Python)](../../iot-hub/iot-hub-python-python-file-upload.md)