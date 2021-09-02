---
title: 문제 해결 Azure IoT Hub 오류 409001 DeviceAlreadyExists
description: 오류 409001 DeviceAlreadyExists 해결 방법 이해
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829202"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

이 문서에서는 **409001 DeviceAlreadyExists** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 디바이스를 등록하려고 하면 요청이 실패하고 **409001 DeviceAlreadyExists** 오류가 표시됩니다.

## <a name="cause"></a>원인

IoT 허브에는 이미 동일한 디바이스 ID를 가진 디바이스가 있습니다. 

## <a name="solution"></a>솔루션

다른 디바이스 ID를 사용하여 다시 시도해 보세요.