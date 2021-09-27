---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 09/13/2021
ms.openlocfilehash: 58f0c7bab2308912979d1a8dfc442f7af4d27591
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548199"
---
Visual Studio Code에서 논리 앱 프로젝트에는 다음 형식 중 하나가 있습니다.

* 기본 형식인 확장 번들 기반(Node.js)
* 기본 형식에서 변환할 수 있는 NuGet 패키지 기반(.NET)

이러한 형식에 따라 프로젝트에는 약간 다른 폴더와 파일이 포함됩니다. NuGet 기반 프로젝트에는 패키지 및 기타 라이브러리 파일이 포함된 .bin 폴더가 포함되어 있습니다. 번들 기반 프로젝트에는 .bin 폴더 및 기타 파일이 포함되지 않습니다. 일부 시나리오에서는 사용자 지정 기본 제공 작업을 개발하고 실행하려는 경우와 같이 앱을 실행하기 위해 NuGet 기반 프로젝트가 필요합니다. NuGet을 사용하도록 프로젝트를 변환하는 방법을 자세히 알아보려면 [기본 제공 커넥터 작성 사용](../articles/logic-apps/create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)을 검토하세요.

기본 번들 기반 프로젝트의 경우 프로젝트에는 다음 예제와 유사한 폴더 및 파일 구조가 있습니다.

```text
MyBundleBasedLogicAppProjectName
| .vscode
| Artifacts
  || Maps 
     ||| MapName1
     ||| ...
  || Schemas
     ||| SchemaName1
     ||| ...
| WorkflowName1
  || workflow.json
  || ...
| WorkflowName2
  || workflow.json
  || ...
| workflow-designtime
| .funcignore
| connections.json
| host.json
| local.settings.json
```

프로젝트의 루트 수준에서 다른 항목이 있는 다음 파일 및 폴더를 찾을 수 있습니다.

| 이름 | 폴더 또는 파일 | 설명 |
|------|----------------|-------------|
| **.vscode** | 폴더 | **extensions.json, launch.json, settings.json** 및 **tasks.json** 파일과 같은  Visual Studio Code 관련 **설정** 파일을 포함합니다. |
| **아티팩트** | 폴더 | B2B(Business-to-Business) 시나리오를 지원하는 워크플로에서 정의하고 사용하는 통합 계정 아티팩트를 포함합니다. 예를 들어 예제 구조에는 XML 변환 및 유효성 검사 작업에 대한 맵과 스키마가 포함됩니다. |
| **<*WorkflowName*>** | 폴더 | 각 워크플로에 대해 <*WorkflowName*> 폴더에는 워크플로의 기본 JSON 정의가 포함된 **workflow.json** 파일이 포함되어 있습니다. |
| **workflow-designtime** | 폴더 | 개발 환경 관련 설정 파일을 포함합니다. |
| **.funcignore** | 파일 | 설치된 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) 관련 정보를 포함합니다. |
| **connections.json** | 파일 | 워크플로에서 사용하는 관리형 연결 및 Azure 함수에 대한 메타데이터, 엔드포인트 및 키를 포함합니다. <p><p>**중요**: 각 환경에 대해 서로 다른 연결과 함수를 사용하려면 이 **connections.json** 파일을 매개 변수화하고 엔드포인트를 업데이트해야 합니다. |
| **host.json** | 파일 | 런타임 관련 구성 설정 및 값(예: 단일 테넌트 Azure Logic Apps 플랫폼, 논리 앱, 워크플로, 트리거 및 작업에 대한 기본 제한)이 포함되어 있습니다. 논리 앱 프로젝트의 루트 수준에서 **host.json** 메타데이터 파일에는 로컬 또는 Azure에 상관없이 동일한 논리 앱의 *모든 워크플로* 가 실행되는 동안 사용하는 구성 설정 및 기본값이 포함됩니다. <p>**참고:** 논리 앱을 만들 때 Visual Studio Code 스토리지 컨테이너에 백업 **host.snapshot.*.json** 파일을 만듭니다. 논리 앱을 삭제하면 이 백업 파일이 삭제되지 않습니다. 동일한 이름의 다른 논리 앱을 만들면 다른 스냅샷 파일이 만들어집니다. 동일한 논리 앱에 대해 최대 10개의 스냅샷만 가질 수 있습니다. 이 제한을 초과하면 다음 오류가 발생합니다. <p>`Microsoft.Azure.WebJobs.Script.WebHost: Repository has more than 10 non-decryptable secrets backups (host))` <p>이 오류를 해결하려면 스토리지 컨테이너에서 추가 스냅샷 파일을 삭제합니다. |
| **local.settings.json** | 파일 | 로컬에서 실행하는 동안 워크플로에 사용되는 앱 설정, 연결 문자열 및 기타 설정을 포함합니다. 즉, 이러한 설정 및 값은 로컬 개발 환경에서 프로젝트를 실행하는 경우에 *만* 적용됩니다. Azure에 배포하는 동안 파일 및 설정이 무시되고 배포에 포함되지 않습니다. <p><p>이 파일은 로컬 개발 도구에서 `appSettings` 값으로 사용하는 *로컬 환경 변수* 로 설정 및 값을 저장합니다. *앱 설정* 및 *매개 변수* 를 사용하여 런타임 및 배포 시에 이러한 환경 변수를 호출하고 참조할 수 있습니다. <p><p>**중요**: **local.settings.json** 파일은 비밀을 포함할 수 있으므로 프로젝트 원본 제어에서 이 파일을 제외해야 합니다. |
||||