---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: dfedba441cc4df8436f32d3985182c16ef2cbb5e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465706"
---
필수 구성 요소의 일부로 샘플 코드를 폴더에 다운로드했습니다. 다음 단계에 따라 샘플 파일을 검사하고 편집합니다.

1. Visual Studio Code에서 src/edge로 이동합니다. env 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.
배포 템플릿은 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. 일부 자리 표시자 값을 포함합니다. .env 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.
1. src/cloud-to-device-console-app 폴더로 이동합니다. 여기에는 appsettings.json 파일 및 몇 가지 다른 파일이 표시됩니다.

    * c2d-console-app.csproj - Visual Studio Code에 대한 프로젝트 파일
    * operations.json - 프로그램을 실행하려는 작업의 목록입니다.
    * main.py - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.
        
        * 앱 설정을 로드합니다.
        * Azure Video Analyzer 모듈에서 공개하는 직접 메서드를 호출합니다. 
        * 터미널 창에서 프로그램의 출력을 검사하고, 출력 창에서 모듈에서 생성된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.
1. **operations.json** 파일을 편집합니다.

    * pipelineTopology에 대한 링크 변경<br/>`"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-with-httpExtension/topology.json"`
    * livePipelineSet에서 이전 링크의 값과 일치하도록 pipelineTopology의 이름을 편집합니다.<br/>`"pipelineTopologyName" : "EVROnMotionPlusHttpExtension"`
    * PipelineTopologyDelete에서 이름을 편집합니다.<br/>`"name": "EVROnMotionPlusHttpExtension"`


