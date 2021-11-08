---
title: Azure RTOS API용 Defender-IoT-micro-agent
description: Azure RTOS용 Defender-IoT-micro-agent에 대한 API를 참조합니다.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 291dfa88ea9be2c17706828ac59a2321fee11ef2
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998020"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Azure RTOS API용 Defender-IoT-micro-agent(미리 보기)

IoT 용 Defender Api는 [MICROSOFT Api 라이선스 및 사용 약관](/legal/microsoft-apis/terms-of-use)의 적용을 받습니다.

이 API는 Azure RTOS용 Defender-IoT-micro-agent에만 사용하도록 설계되었습니다. 추가 리소스는 [Azure RTOS GitHub 리소스에 대한 Defender-IoT-micro-agent](https://github.com/azure-rtos/azure-iot-preview/releases)를 참조하세요.

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Azure RTOS용 Defender-IoT-micro-agent를 사용하도록 설정

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>프로토타입

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

이 루틴은 Azure IoT Defender-IoT-micro-agent 하위 시스템을 사용하도록 설정합니다. 내부 상태 시스템은 보안 이벤트의 컬렉션을 관리하고 Azure IoT Hub로 보냅니다. 데이터 컬렉션을 관리하는 데 NX_AZURE_IOT_SECURITY_MODULE 인스턴스 하나만 필요합니다.

### <a name="parameters"></a>매개 변수

| 속성 | Description |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT`에 대한 포인터입니다.  |

### <a name="return-values"></a>반환 값

|반환 값  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT 보안 모듈을 사용하도록 설정했습니다.     |
|NX_AZURE_IOT_FAILURE   |  내부 오류로 인해 Azure IoT 보안 모듈을 사용하도록 설정하지 못했습니다.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  보안 모듈에 올바른 #NX_AZURE_IOT 인스턴스가 필요합니다.      |

### <a name="allowed-from"></a>허용되는 위치

스레드

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Azure IoT Defender-IoT-micro-agent 사용 안 함

**nx_azure_iot_security_module_disable**

### <a name="prototype"></a>프로토타입

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

이 루틴은 Azure IoT Defender-IoT-micro-agent 하위 시스템을 사용하지 않도록 설정합니다.

### <a name="parameters"></a>매개 변수

| 속성 | Description |
|---------|---------|
| nx_azure_iot_ptr  [in]    | `NX_AZURE_IOT`에 대한 포인터입니다. NULL인 경우 단일 인스턴스를 사용하지 않습니다. |

### <a name="return-values"></a>반환 값

|반환 값  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Azure IoT 보안 모듈을 사용하지 않도록 설정하면 성공합니다.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub 인스턴스가 단일 복합 인스턴스와 다릅니다.       |
|NX_AZURE_IOT_FAILURE    |  내부 오류로 인해 Azure IoT 보안 모듈을 사용하지 않도록 설정하지 못했습니다.       |

### <a name="allowed-from"></a>허용되는 위치

스레드

## <a name="next-steps"></a>다음 단계

Azure RTOS Defender-IoT-micro-agent를 시작하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- Defender for IoT RTOS Defender-IoT-micro-agent [개요](iot-security-azure-rtos.md)를 검토합니다.
