---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 10759b4d80b39b1fc9a04d513e75d98b7c691281
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441301"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>OSM (Open Service 메시) 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.

Windows의 PowerShell 기반 셸에서를 사용 `Invoke-WebRequest` 하 여 OSM 릴리스를 다운로드 한 후 다음과 같이를 사용 하 여 추출 합니다 `Expand-Archive` .

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.9.1"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

`osm` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 이를 통해 AKS 클러스터에서 OSM 컨트롤러를 관리할 수 있습니다. 다음 명령을 사용하여 Windows의 PowerShell 기반 셸에 OSM `osm` 클라이언트 이진 파일을 설치합니다. 이러한 명령은 `osm` 클라이언트 이진 파일을 OSM 폴더에 복사한 다음, `PATH`를 통해 즉시(현재 셸에서) 및 영구적으로(셸 다시 시작에서) 사용할 수 있습니다. 이러한 명령을 실행하는 데에는 상승된(관리자) 권한이 필요하지 않으며 셸을 다시 시작하지 않아도 됩니다.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
