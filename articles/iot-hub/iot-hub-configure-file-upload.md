---
title: Azure Portal을 사용하여 파일 업로드 구성 | Microsoft Docs
description: Azure Portal을 사용하여 연결된 디바이스에서 파일 로드를 사용하도록 IoT Hub를 구성하는 방법입니다. 대상 Azure Storage 계정 구성에 대한 정보가 포함됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: bf8bf9d1b472ff1986596f6cae7e1f7b415d3bc6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528445"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure Portal을 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

이 문서에서는 Azure Portal을 사용하여 IoT 허브에서 파일 업로드를 구성하는 방법을 보여 줍니다. 

[IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하려면 먼저 Azure 스토리지 계정 및 Blob 컨테이너를 IoT 허브에 연결해야 합니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다. 스토리지 계정 및 Blob 컨테이너 외에도 SAS URI의 TTL(Time to Live) 및 IoT Hub가 Azure 스토리지에서 사용하는 인증 유형을 설정할 수 있습니다. IoT Hub가 백 엔드 서비스에 제공할 수 있는 선택적 파일 업로드 알림에 대한 설정도 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* Azure IoT Hub - IoT 허브가 없는 경우 [포털을 사용하여 IoT 허브 만들기](iot-hub-create-through-portal.md)를 참조하세요.

## <a name="configure-your-iot-hub"></a>IoT Hub 구성

1. [Azure Portal](https://portal.azure.com)에서 IoT 허브로 이동하고 **파일 업로드** 를 선택하여 파일 업로드 속성을 표시합니다. 그런 다음, **스토리지 컨테이너 설정** 에서 **Azure Storage 컨테이너** 를 선택합니다.

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-settings.png" alt-text="포털에서 IoT Hub 파일 보기 설정 보기":::

1. 현재 구독에 있는 Azure Storage 계정 및 Blob 컨테이너를 선택하여 IoT 허브와 연결합니다. 필요에 따라 **Storage 계정** 창에 Azure Storage 계정을 만들고 **컨테이너** 창에 Blob 컨테이너를 만들 수 있습니다. 

   :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-container-selection.png" alt-text="포털에서 파일 업로드에 대한 스토리지 컨테이너 보기":::

1. Azure Storage 계정 및 Blob 컨테이너를 선택한 후 파일 업로드 속성의 나머지 부분을 구성합니다.    

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-selected-container.png" alt-text="포털에서 IoT Hub 파일 업로드 구성":::

    * **업로드된 파일에 대한 알림 받기**: 토글을 통해 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

    * **SAS TTL**: 이 설정은 IoT Hub에서 디바이스로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

    * **파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

    * **파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

    * **인증 유형**: 기본적으로 Azure IoT Hub는 키 기반 인증을 사용하여 Azure Storage에 연결하고 권한을 부여합니다. Azure Storage로 Azure IoT Hub를 인증하도록 사용자가 할당한 관리 ID 또는 시스템이 할당한 관리 ID를 구성할 수도 있습니다. 관리 ID는 Azure AD에서 자동으로 관리되는 ID를 안전한 방식으로 Azure 서비스에 제공합니다. 관리 ID를 구성하는 방법을 알아보려면 [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)을 참조하세요. Azure Storage 계정 및 IoT 허브에서 하나 이상의 관리 ID를 구성한 후 **시스템 할당** 또는 **사용자 할당** 단추를 사용하여 Azure 스토리지로 인증에 대해 선택할 수 있습니다.

        > [!NOTE]
        > 인증 유형 설정은 IoT 허브가 Azure Storage 계정으로 인증하는 방법을 구성합니다. 디바이스는 항상 IoT 허브에서 가져온 SAS URI를 사용하여 Azure Storage로 인증합니다. 

1. **저장** 을 선택하여 설정을 저장합니다. 성공적인 완료에 대해 확인해야 합니다. **인증 유형** 과 같은 일부 선택은 설정을 저장한 후에만 유효성이 검사됩니다. 

## <a name="next-steps"></a>다음 단계

* [디바이스 개요에서 파일 업로드](iot-hub-devguide-file-upload.md)
* [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)
* [파일 업로드 방법 가이드](./iot-hub-csharp-csharp-file-upload.md)
