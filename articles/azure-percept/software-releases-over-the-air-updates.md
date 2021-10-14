---
title: Azure Percept DK OTA 업데이트용 소프트웨어 릴리스
description: Azure Percept DK 무선 업데이트 패키지에 대한 정보 및 다운로드 링크
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: 1eb6f41d738f7ab5a2dbd68fab341c2b7c9917ad
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001166"
---
# <a name="software-releases-for-ota-updates"></a>OTA 업데이트용 소프트웨어 릴리스

OTA 업데이트는 항상 개발 키트를 최신 상태로 유지하려는 사용자를 위해 빌드됩니다. 따라서 여기서는 하드 중지 버전과 최신 버전만 제공됩니다. 개발 키트를 특정(이전) 버전으로 변경하려면 USB 케이블 업데이트를 사용합니다. [USB-C 케이블 연결을 통해 Azure Percept DK 업데이트](./how-to-update-via-usb.md)를 참조하세요. 또한 훨씬 고급 버전으로 이동하려는 경우에도 USB 업데이트를 사용합니다.

>[!CAUTION]
>개발 키트는 OTA를 통한 SW 버전 다운그레이드를 지원하지 않습니다. IoT Hub 프레임워크용 장치 업데이트는 현재 버전보다 오래된 버전의 이미지 배포를 차단하지 않습니다. 그러나 개발 키트에 그렇게 하면 데이터와 기능이 손실됩니다.

>[!IMPORTANT]
>OTA 또는 USB 케이블 업데이트를 결정하기 전에 다음 문서를 확인해야 합니다.
>
>[업데이트 전략을 확인하는 방법](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>OTA의 하드 중지 버전

Microsoft는 OTA 패키지를 사용하여 각 개발 키트 릴리스를 서비스합니다. 그러나 개발 키트 OS/펌웨어 또는 OTA 플랫폼에 대한 호환성이 손상되는 변경이 있기 때문에 이전 버전에서 훨씬 고급 버전으로 직접 OTA 업데이트를 수행하면 문제가 될 수 있습니다. 일반적으로 호환성이 손상되는 변경이 발생하면 Microsoft는 OTA 업데이트 프로세스가 이전 시스템을 **이 호환성이 손상되는 변경을 도입/제공하는 첫 번째 버전** 으로 원활하게 전환하도록 합니다. 이 특정 버전은 OTA에 대한 하드 중지 버전이 됩니다. 알려진 하드 중지 버전인 **6월 릴리스** 를 예로 들어 보겠습니다. 사용자가 개발 키트를 2104에서 2106으로 업데이트한 다음 2106에서 2107로 업데이트하면 OTA가 작동합니다. 그러나 사용자가 하드 중지(2106)를 건너뛰고 개발 키트를 2104에서 2107로 직접 업데이트하려고 하면 작동하지 않습니다.

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="OTA의 하드 중지 버전":::

## <a name="recommendations-for-applying-the-ota-update"></a>OTA 업데이트 적용에 대한 권장 사항

**시나리오 1:** 개발 키트를 자주(매월) 업데이트하여 항상 최신 상태로 유지합니다.

- 항상 OTA를 수행하여 개발 키트를 마지막 릴리스에서 새로 릴리스된 버전으로 업데이트하는 경우 문제가 없어야 합니다.

**시나리오 2:** 몇 가지 버전을 건너뛸 수 있을 때 업데이트를 수행합니다.

1. 개발 키트의 현재 소프트웨어 버전을 식별합니다.
1. OTA 패키지 릴리스 목록을 검토하여 현재 버전과 대상 버전 간의 하드 중지 버전을 찾습니다.
    - 하드 중지 버전이 있으면, 최신 업데이트 패키지를 배포할 수 있을 때까지 하드 중지 버전을 순차적으로 배포해야 합니다.
    - 하드 중지 버전이 없으면, 최신 OTA 패키지를 개발 키트에 직접 배포할 수 있습니다.

## <a name="identify-the-current-software-version-of-dev-kit"></a>개발 키트의 현재 소프트웨어 버전 식별

**옵션 1:**

1. [Azure Percept Studio](./overview-azure-percept-studio.md)에 로그인합니다.
1. **장치** 에서 개발 키트 장치를 선택합니다.
1. **일반** 탭에서 **모델** 및 **SW 버전** 정보를 찾습니다.

**옵션 2:**

1. Microsoft Azure Portal에서 **IoT Hub** 서비스의 **IoT Edge 장치** 를 봅니다.
1. 장치 목록에서 개발 키트 장치를 선택합니다.
1. **디바이스 쌍** 을 선택합니다.
1. 디바이스 쌍 속성을 스크롤하여 **“deviceInformation”** 아래에서 **“model”** 및 **“swVersion”** 을 찾고 해당 값을 기록해 둡니다.

## <a name="identify-the-ota-packages-to-be-deployed"></a>배포할 OTA 패키지 식별

>[!IMPORTANT]
>개발 키트의 현재 버전이 아래 릴리스에 포함되어 있지 않으면 OTA 업데이트가 지원되지 않습니다. USB 케이블 업데이트를 수행하여 최신 버전으로 다운로드하세요.

**최신 릴리스:**

|해제|해당하는 버전|다운로드 링크|참고|
|---|---|---|---|
|9월 서비스 릴리스(2109)|2021.106.111.115,<br>2021.107.129.116|[2021.109.129.108 OTA 업데이트 패키지](https://go.microsoft.com/fwlink/?linkid=2174634)||

**하드 중지 릴리스:**

|해제|해당하는 버전|다운로드 링크|참고|
|---|---|---|---|
|6월 서비스 릴리스(2106)|2021.102.108.112, 2021.104.110.103, 2021.105.111.122 |[2021.106.111.115 OTA 매니페스트(PE-101용)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_PE-101_2021.106.111.115_v3.json)<br>[2021.106.111.115 OTA 매니페스트(APDK-101용)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_Azure-Percept-DK_2021.106.111.115_v3.json) <br>[2021.106.111.115 OTA 업데이트 패키지](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/Microsoft-Azure-Percept-DK-2021.106.111.115.swu) |"모델 이름"(PE-101/APDK-101)에 따라 올바른 매니페스트를 사용해야 합니다.|

## <a name="next-steps"></a>다음 단계

[OTA(무선)로 Azure Percept DK 업데이트](./how-to-update-over-the-air.md)
