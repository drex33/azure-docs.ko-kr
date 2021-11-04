---
title: 문제 해결 Azure IoT Hub 오류 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: 오류 403006 DeviceMaximumActiveFileUploadLimitExceeded 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 5b90593c44fbab0342def8b85a1a71e8ef15d6ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011071"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

이 문서에서는 **403006 DeviceMaximumActiveFileUploadLimitExceeded** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

오류 코드 **403006** 과 함께 파일 업로드 요청이 실패하고 “Number of active file upload requests cannot exceed 10(활성 파일 업로드 요청 수는 10개를 초과할 수 없습니다.)”라는 메시지가 표시됩니다.

## <a name="cause"></a>원인

각 디바이스 클라이언트는 [동시 파일 업로드 10개](./iot-hub-devguide-quotas-throttling.md#other-limits)로 제한됩니다. 

파일 업로드가 완료되었을 때 디바이스가 IoT Hub에 알리지 않으면 제한을 쉽게 초과할 수 있습니다. 이 문제는 일반적으로 신뢰할 수 없는 디바이스 측 네트워크로 인해 발생합니다.

## <a name="solution"></a>솔루션

디바이스가 즉시 [IoT Hub 파일 업로드 완료를 알릴](./iot-hub-devguide-file-upload.md#device-notify-iot-hub-of-a-completed-file-upload) 수 있는지 확인합니다. 그런 다음 [파일 업로드 구성을 위해 SAS 토큰 TTL을 줄여](iot-hub-configure-file-upload.md)보세요.

## <a name="next-steps"></a>다음 단계

파일 업로드에 대한 자세한 내용은 [IoT Hub를 사용하여 파일 업로드](./iot-hub-devguide-file-upload.md) 및 [Azure Portal을 사용하여 IoT Hub 파일 업로드 구성](./iot-hub-configure-file-upload.md)을 참조하세요.