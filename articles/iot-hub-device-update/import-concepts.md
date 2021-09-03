---
title: Device Update for IoT Hub 가져오기 이해 | Microsoft Docs
description: Device Update for IoT Hub로 새 업데이트를 가져오기 위한 주요 개념입니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39a1acb596f4d56915aabfa0a64fc69d6e55aec2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769098"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Device Update for IoT Hub로 업데이트 가져오기
Device Update for IoT Hub에서 디바이스에 업데이트를 배포하려면 먼저 해당 업데이트를 디바이스 업데이트 서비스로 _가져와야 합니다_. 다음은 업데이트를 가져올 때 이해해야 할 몇 가지 중요한 개념에 대한 개요입니다.

## <a name="limits-on-importing-updates"></a>업데이트 가져오기 제한
각 Device Update for IoT Hub 인스턴스의 경우 다음 제한이 적용됩니다.

* ADU 인스턴스당 5개의 공급자
* 공급자당 5개의 이름
* 이름당 25가지 버전

또한 누적 업데이트 버전은 모든 공급자 및 이름에 걸쳐 100개로 제한됩니다.

업데이트 제출 시 개별 파일에 허용되는 최대 파일 크기는 800MB입니다. 한 번의 업데이트 제출에 있는 모든 파일의 _결합된_ 합계도 800MB를 초과해서는 안 됩니다.

## <a name="import-manifest"></a>매니페스트 가져오기

가져오기 매니페스트는 가져오는 업데이트에 대한 중요한 정보를 정의하는 JSON 파일입니다. 가져오기 프로세스의 일부로 가져오기 매니페스트와 관련 업데이트 파일(예: 펌웨어 업데이트 패키지)을 모두 제출합니다. 가져오기 매니페스트에 정의된 메타데이터는 업데이트를 수집하는 데 사용됩니다. 일부 메타데이터는 배포 시에도 사용됩니다(예: 업데이트가 올바르게 설치되었는지 확인).

가져오기 매니페스트에는 중요한 Device Update for IoT Hub 개념을 나타내는 여러 항목이 포함되어 있습니다. 이러한 개념은 아래에 설명되어 있습니다.

### <a name="update-identity-update-id"></a>업데이트 ID

업데이트 ID는 업데이트의 고유 식별자를 나타냅니다. 가져오는 업데이트에 대한 중요한 속성을 정의합니다. 업데이트 ID는 세 파트로 구성됩니다.
* 공급자: 업데이트를 만들거나 직접 책임지는 엔터티입니다. 회사 이름인 경우가 많습니다.
* 이름: 업데이트 클래스의 식별자입니다. 클래스는 사용자가 임의로 선택할 수 있습니다. 디바이스 또는 모델 이름인 경우가 많습니다.
* 버전: 이 업데이트를 동일한 공급자 및 이름을 가진 다른 업데이트와 구별하는 버전 번호입니다. 이 버전은 Device Update for IoT Hub 서비스에서 사용되며 디바이스의 개별 소프트웨어 구성 요소 버전과 일치하거나 일치하지 않을 수 있습니다. 

### <a name="compatibility"></a>호환성

업데이트 배포를 단순화하기 위해 Device Update for IoT Hub는 가져오기 매니페스트에 정의된 업데이트의 호환성 속성을 해당 디바이스 속성과 비교합니다. 일치하는 속성이 있는 업데이트만 반환되고 배포에 사용할 수 있습니다.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria는 디바이스의 업데이트 에이전트에서 업데이트가 성공적으로 설치되었는지 확인하는 데 사용됩니다.


## <a name="next-steps"></a>다음 단계

준비되면 가져오기 프로세스를 단계별로 안내하는 [가져오기 방법 가이드](./import-update.md)를 사용해 보세요.


