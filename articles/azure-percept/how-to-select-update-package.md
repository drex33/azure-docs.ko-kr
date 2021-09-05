---
title: Azure Percept DK 업데이트 패키지 선택
description: Azure Percept DK 버전을 식별하고 가장 적합한 업데이트 패키지를 선택하는 방법
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e2256ba6ad1f2f125b21e7cb59ab74b44864bce6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529656"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>OTA 및 USB 업데이트에 가장 적합한 업데이트 패키지를 확인하고 다운로드하는 방법

이 페이지에서는 개발 키트에 가장 적합한 업데이트 패키지와 업데이트 패키지의 다운로드 위치를 선택하는 방법에 관한 지침을 제공합니다.

디바이스를 업데이트하는 방법에 관한 자세한 내용은 다음 문서를 참조하세요.
- [무선으로 Azure Percept DK 업데이트](./how-to-update-over-the-air.md)
- [USB를 통해 Azure Percept DK 업데이트](./how-to-update-via-usb.md)


## <a name="prerequisites"></a>사전 요구 사항

- [Azure Percept Studio 및 IoT Hub로 설정되고 연결된](./quickstart-percept-dk-set-up.md) [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270).

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>개발 키트의 모델 이름 및 소프트웨어 버전 식별
개발 키트에 올바른 업데이트 패키지를 적용하는지 확인하려면 먼저 현재 실행 중인 소프트웨어 버전을 확인해야 합니다.

> [!WARNING]
> 잘못된 업데이트 패키지를 적용하면 개발 키트가 작동하지 않을 수 있습니다. 다음 단계에 따라 올바른 업데이트 패키지를 적용하는지 확인해야 합니다.

옵션 1:
1. [Azure Percept Studio](./overview-azure-percept-studio.md)에 로그인합니다.
2. **디바이스** 에서 개발 키트 디바이스를 선택합니다.
3. **일반** 탭에서 **모델** 및 **SW 버전** 정보를 찾습니다.

옵션 2:
1. Microsoft Azure Portal에서 **IoT Hub** 서비스의 **IoT Edge 디바이스** 를 봅니다.
2. 디바이스 목록에서 개발 키트 디바이스를 선택합니다.
3. **디바이스 쌍** 을 선택합니다.
4. 디바이스 쌍 속성을 스크롤하여 **“deviceInformation”** 아래에서 **“model”** 및 **“swVersion”** 을 찾고 해당 값을 기록해 둡니다.

## <a name="determine-the-correct-update-package"></a>올바른 업데이트 패키지 확인
이전 섹션에서 식별된 **model**, **swVersion** 을 사용하여 아래 표에서 다운로드할 업데이트 패키지를 확인합니다.


|model  |swVersion  |Update 메서드  |다운로드 링크  |참고  |
|---------|---------|---------|---------|---------|
|PE-101     |모든 swVersion       |**USB만**         |[2021.107.129.116 USB 업데이트 패키지](https://go.microsoft.com/fwlink/?linkid=2169086)         |7월 릴리스(2107)         |
|APDK-101     |2021.106.111.115 이전의 모든 swVersion |**USB만**         |[2021.107.129.116 USB 업데이트 패키지](https://go.microsoft.com/fwlink/?linkid=2169086)         |7월 릴리스(2107)         |
|APDK-101     |2021.106.111.115        |OTA 또는 USB       |[2021.107.129.116 OTA 업데이트 패키지](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[2021.107.129.116 USB 업데이트 패키지](https://go.microsoft.com/fwlink/?linkid=2169086)        |7월 릴리스(2107)         |


## <a name="next-steps"></a>다음 단계
이전 섹션에서 확인된 방법 및 업데이트 패키지를 통해 개발 키트를 업데이트합니다.
- [무선으로 Azure Percept DK 업데이트](./how-to-update-over-the-air.md)
- [USB를 통해 Azure Percept DK 업데이트](./how-to-update-via-usb.md)
