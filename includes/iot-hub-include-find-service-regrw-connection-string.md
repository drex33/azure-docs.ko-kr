---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9d5710e78b323af65a12b1f92095cdfa1f385ecb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287549"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

**서비스 연결** 및 **레지스트리 쓰기** 권한을 부여하는 공유 액세스 정책을 만들고 이 정책에 대한 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 선택합니다. 허브가 있는 리소스 그룹을 선택한 다음, 리소스 목록에서 허브를 선택합니다.

1. 허브의 왼쪽 창에서 **공유 액세스 정책** 을 선택합니다.

1. 정책 목록 위의 메뉴에서 공유 **액세스 정책 추가를** 선택합니다.

1. **공유 액세스 정책 추가** 에서 정책에 대한 설명형 이름(예: *serviceAndRegistryReadWrite*)을 입력합니다. **권한에서** 레지스트리 **쓰기** 및 **서비스 연결** 를 선택한 다음, **추가를** 선택합니다. (레지스트리 **읽기** 권한은 **레지스트리 쓰기** 를 선택하면 자동으로 포함됩니다.)

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png" alt-text="새 공유 액세스 정책을 추가하는 방법을 보여 주는 화면 캡처" border="true":::

1. 정책 목록에서 새 정책을 선택합니다.

1. **공유 액세스 키** 아래에서 기본 연결 **문자열의** 복사 아이콘을 선택하고 값을 저장합니다.

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png" alt-text="연결 문자열을 검색하는 방법을 보여 주는 화면 캡처" border="true":::

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.
