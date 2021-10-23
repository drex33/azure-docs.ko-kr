---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: ce13f1cdd3effdd08bfa76a996587edc213c5701
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288004"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**registryReadWrite** 정책에 대한 IoT Hub 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 선택합니다. 허브가 있는 리소스 그룹을 선택한 다음, 리소스 목록에서 허브를 선택합니다.

2. 허브의 왼쪽 창에서 **공유 액세스 정책** 을 선택합니다.

3. 정책 목록에서 **registryReadWrite** 정책을 선택합니다.

4. **공유 액세스 키** 에서 **기본 연결 문자열** 의 복사 아이콘을 선택 하 고 값을 저장 합니다.

    :::image type="content" source="./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png" alt-text="연결 문자열을 검색 하는 방법을 보여 주는 화면 캡처" border="true":::

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.
