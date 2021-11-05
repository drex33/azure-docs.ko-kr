---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: 374098dfd8d78d0558b36105415658fc75979f66
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860590"
---
1. Visual Studio Code에서 src/edge로 이동합니다. 여러분이 만든 .env 파일이 몇 가지 배포 템플릿 파일과 함께 표시됩니다.

   배포 템플릿은 일부 자리 표시자 값이 있는 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. .env 파일에는 이러한 변수에 대한 값이 있습니다.
2. 다음으로, src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

   - operations.json - 이 파일에는 프로그램이 실행할 다양한 작업이 나열됩니다.
   - main.py - 다음을 수행하는 샘플 프로그램 코드입니다.
     - 앱 설정을 로드합니다.
     - Azure Video Analyzer 모듈의 직접 메서드를 호출하여 토폴로지를 만들고, 파이프라인을 인스턴스화하고, 활성화합니다.
     - **TERMINAL** 창에서 파이프라인 출력을 검사하고 **OUTPUT** 창에서 IoT 허브로 전송된 이벤트를 검사할 수 있도록 일시 중지합니다.
     - 라이브 파이프라인을 비활성화하고, 라이브 파이프라인을 삭제하고, 토폴로지를 삭제합니다.
