---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: de266fe50b163820b3dd50545546d099431daefa
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114729901"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage 계정을 IoT Hub에 연결

디바이스에서 파일을 업로드하려면 IoT 허브와 연결된 Azure Storage 계정 및 Azure Blob Storage 컨테이너가 있어야 합니다. 스토리지 계정 및 컨테이너를 IoT 허브와 연결하면 IoT 허브가 디바이스에서 요청될 때 SAS URI의 요소를 제공할 수 있습니다. 그런 다음, 디바이스는 이러한 요소를 사용하여 Azure Storage를 인증하고 Blob 컨테이너에 파일을 업로드하는 데 사용하는 SAS URI를 생성할 수 있습니다.

Azure Storage 계정을 IoT 허브와 연결하려면 다음을 수행합니다.

1. **메시징** 에서 IoT 허브 왼쪽 창의 **파일 업로드** 를 선택합니다.

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="포털에서 파일 업로드 설정을 선택합니다.":::

1. **파일 업로드** 창에서 **Azure Storage 컨테이너** 를 선택합니다. 이 문서에서는 스토리지 계정과 IoT Hub를 동일한 지역에 배치하는 것이 좋습니다. 
    * 사용하려는 스토리지 계정이 이미 있는 경우 목록에서 선택합니다. 

    * 새 스토리지 계정을 만들려면 **+스토리지 계정** 을 선택합니다. 스토리지 계정에 대한 이름을 제공하고 **위치** 가 IoT 허브와 동일한 지역으로 설정되었는지 확인한 다음, **확인** 을 선택합니다. 새 계정은 IoT 허브와 동일한 리소스 그룹에 만들어집니다. 배포가 완료되면 목록에서 스토리지 계정을 선택합니다. 

    스토리지 계정을 선택하면 **컨테이너** 창이 열립니다. 

1. **컨테이너** 창에서 Blob 컨테이너를 선택합니다.
    * 사용하려는 Blob 컨테이너가 이미 있는 경우 목록에서 선택하고 **선택** 을 클릭합니다. 
    
    * 새 Blob 컨테이너를 만들려면 **+ 컨테이너** 를 선택합니다. 새 컨테이너에 대한 이름을 제공합니다. 이 문서의 목적을 위해 다른 모든 필드를 기본값으로 그대로 둘 수 있습니다. **만들기** 를 선택합니다. 배포가 완료되면 목록에서 컨테이너를 선택하고 **선택** 을 클릭합니다.

1. **파일 업로드** 창으로 다시 돌아가서 파일 알림이 **켜짐** 으로 설정되었는지 확인합니다. 다른 모든 설정은 기본값으로 그대로 둘 수 있습니다. **저장** 을 선택하고 다음 섹션으로 이동하기 전에 설정이 완료될 때까지 기다립니다. 

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="포털에서 파일 업로드 설정을 확인합니다.":::

Azure Storage 계정을 만드는 방법에 대한 자세한 지침은 [스토리지 계정 만들기](../articles/storage/common/storage-account-create.md)를 참조하세요. 스토리지 계정 및 Blob 컨테이너를 IoT 허브와 연결하는 방법에 대한 자세한 지침은 [Azure Portal을 사용하여 파일 업로드 구성](../articles/iot-hub/iot-hub-configure-file-upload.md)을 참조하세요.
