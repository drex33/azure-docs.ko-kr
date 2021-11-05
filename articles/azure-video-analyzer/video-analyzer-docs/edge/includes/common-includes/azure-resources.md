---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a834a4c33ebbffcf4b335e7116b134164941231
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861200"
---
배포 프로세스는 약 **20분** 이 걸립니다. 배포 프로세스가 완료되면 Azure 구독에 다음을 비롯한 특정 Azure 리소스가 배포됩니다.

1. **Video Analyzer 계정** - 이 [클라우드 서비스](../../../overview.md)는 Video Analyzer 에지 모듈을 등록하고 녹화된 비디오 및 비디오 분석을 재생하는 데 사용됩니다.
1. **스토리지 계정** - 녹화된 비디오 및 비디오 분석을 저장하는 데 사용됩니다.
1. **관리 ID** - 위의 스토리지 계정에 대한 액세스를 관리하는 데 사용되는 사용자 할당 [관리 ID](../../../../../active-directory/managed-identities-azure-resources/overview.md)입니다.
1. **가상 머신** - 시뮬레이션된 에지 디바이스로 작동할 가상 머신입니다.
1. **IoT Hub** - IoT 애플리케이션, IoT Edge 및 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

위에서 언급한 리소스 외에도 빠른 시작 및 자습서에서 사용하기 위해 다음 항목이 스토리지 계정의 '배포-출력' 파일 공유에도 생성됩니다.

- **_appsettings.json_** - 이 파일에는 Visual Studio Code에서 샘플 애플리케이션을 실행하는 데 필요한 **디바이스 연결 문자열** 및 기타 속성이 포함되어 있습니다.
- **_env.txt_** - 이 파일에는 Visual Studio Code를 사용하여 배포 매니페스트를 생성하는 데 필요한 환경 변수가 포함되어 있습니다.
- **_deployment.json_** - 에지 모듈을 시뮬레이션된 에지 디바이스에 배포하기 위해 템플릿에서 사용하는 배포 매니페스트입니다.

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> 필요한 모든 Azure 리소스를 만드는 데 문제가 발생하면 이 [빠른 시작](../../get-started-detect-motion-emit-events-portal.md)의 수동 단계를 사용합니다.
