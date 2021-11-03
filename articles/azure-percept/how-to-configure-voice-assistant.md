---
title: Azure Percept 음성 도우미 애플리케이션 구성
description: Azure IoT Hub를 사용하여 음성 도우미 애플리케이션 구성
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 82be52611d12b69e146fdaa477614a3dbc25242d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006566"
---
# <a name="configure-your-azure-percept-voice-assistant-application"></a>Azure Percept 음성 도우미 애플리케이션 구성

이 문서에서는 IoT Hub를 사용하여 음성 도우미 애플리케이션을 구성하는 방법을 설명합니다. 음성 도우미를 만드는 프로세스에 대한 단계별 자습서는 [Azure Percept Studio 및 Azure Percept Audio를 사용하여 코드 없는 음성 도우미 빌드](./tutorial-no-code-speech.md)를 참조하세요.

## <a name="update-your-voice-assistant-configuration"></a>음성 도우미 구성 업데이트

1. [Azure Portal](https://portal.azure.com)을 열고 검색 창에 **IoT Hub** 를 입력합니다. 아이콘을 선택하여 IoT Hub 페이지를 엽니다.

1. IoT Hub 페이지에서 디바이스가 프로비전된 IoT Hub를 선택합니다.

1. 왼쪽 탐색 메뉴의 **자동 디바이스 관리** 아래에서 **IoT Edge** 를 선택하여 IoT Hub에 연결된 모든 디바이스를 봅니다.

1. 음성 도우미 애플리케이션이 배포된 디바이스를 선택합니다.

1. **모듈 설정** 을 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="모듈 설정이 강조 표시된 디바이스 페이지의 스크린샷.":::

1. **Container Registry 자격 증명** 섹션 아래에 다음 항목이 있는지 확인합니다. 필요한 경우 자격 증명을 제공합니다.

    |Name|주소|사용자 이름|암호|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. **IoT Edge 모듈** 섹션에서 **azureearspeechclientmodule** 를 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="디바이스의 모든 IoT Edge 모듈 목록을 보여주는 스크린샷.":::

1. **모듈 설정** 탭을 선택합니다. 다음 구성을 확인합니다.

    이미지 URI|다시 시작 정책|원하는 상태
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule: preload-devkit|always|실행 중

    설정이 일치하지 않으면 설정을 편집하고 **업데이트** 를 선택합니다.

1. **환경 변수** 탭을 선택합니다. 정의된 환경 변수가 없는지 확인합니다.

1. **모듈 쌍 설정** 탭을 선택합니다. **speechConfigs** 섹션을 다음과 같이 업데이트합니다.

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > 위에서 사용된 키워드는 공개적으로 사용할 수 있는 기본 키워드입니다. 사용자 고유의 키워드를 사용하려면 만든 테이블 파일을 Blob Storage에 업로드하여 사용자 지정 키워드를 추가합니다. Blob 스토리지는 익명 컨테이너 액세스 또는 익명 blob 액세스로 구성해야 합니다.

## <a name="how-to-find-out-appid-key-and-region"></a>appId, 키 및 지역을 확인하는 방법

**appID**, **키** 및 **지역** 을 찾으려면 [Speech Studio](https://speech.microsoft.com/)로 이동합니다.

1. 로그인하고 적절한 음성 리소스를 선택합니다.
1. **Speech Studio** 홈페이지의 **음성 도우미** 아래에서 **사용자 지정 명령** 을 선택합니다.
1. 대상 플랫폼을 선택합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Speech Studio의 프로젝트 페이지 스크린샷.":::

1. 왼쪽 메뉴 패널에서 **설정** 을 선택합니다.
1. **appID** 와 **키** 는 **일반** 설정 탭에 있습니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="음성 프로젝트 일반 설정의 스크린샷.":::

1. **지역** 을 찾으려면 설정 내에서 **LUIS 리소스** 탭을 엽니다. **작성 리소스** 선택은 지역 정보를 포함합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="음성 프로젝트 LUIS 리소스의 스크린샷.":::

1. **speechConfigs** 정보를 입력한 후 **업데이트** 를 선택합니다.

1. **모듈 설정** 페이지 맨 위에서 **경로** 탭을 선택합니다. 다음 값을 가진 경로가 있는지 확인합니다.

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    경로가 없으면 경로를 추가합니다.

1. **검토 + 생성** 를 선택합니다.

1. **만들기** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

음성 도우미 구성을 업데이트한 후에는 Azure Percept Studio의 데모로 돌아와서 애플리케이션과 상호 작용합니다.
