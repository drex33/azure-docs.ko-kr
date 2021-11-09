---
title: Windows용 Docker에서 에뮬레이터 실행
itleSuffix: Running the Azure Cosmos DB emulator on Docker for Windows
description: Windows용 Docker에서 Azure Cosmos DB Emulator를 실행하고 사용하는 방법을 알아봅니다. 이 에뮬레이터를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 04/20/2021
ms.openlocfilehash: a083efb897c0ce3c0628134da0ea3d29720ac0e7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054888"
---
# <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Windows용 Docker에서 에뮬레이터 사용
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Windows Docker 컨테이너에서 Azure Cosmos DB Emulator를 실행할 수 있습니다. 자세한 내용은 [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) 를 참조 하세요 `Dockerfile` . 현재, 이 에뮬레이터는 Oracle Linux용 Docker에서 작동하지 않습니다. Windows용 Docker에서 에뮬레이터를 실행하려면 다음 지침을 따르세요.

1. [Windows용 Docker](https://www.docker.com/docker-windows)가 설치되면 도구 모음에서 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환** 을 선택하여 Windows 컨테이너로 전환합니다.

1. 다음으로 즐겨 찾는 셸에서 다음 명령을 실행하여 Docker 허브에서 에뮬레이터 이미지를 끌어옵니다.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. 이미지를 시작하려면 명령줄 또는 PowerShell 환경에 따라 다음 명령을 실행합니다.

   # <a name="command-line"></a>[명령줄](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows 기반 Docker 이미지는 일반적으로 모든 Windows 호스트 OS와 호환되지 않을 수 있습니다. 예를 들어, 기본 Azure Cosmos DB 에뮬레이터 이미지는 Windows 10 및 Windows Server 2016과만 호환됩니다. Windows Server 2019와 호환되는 이미지가 필요한 경우 다음 명령을 대신 실행합니다.

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   응답은 다음과 유사합니다.

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > `docker run` 명령을 실행할 때 포트 충돌 오류가 표시되는 경우(지정된 포트가 이미 사용 중인 경우) 포트 번호를 변경하여 사용자 지정 포트를 전달합니다. 예를 들어, "-p 8081:8081" 매개 변수를 "-p 443:8081"로 변경할 수 있습니다.

1. 이제 응답에서 에뮬레이터 엔드포인트 및 기본 키를 사용하고 TLS/SSL 인증서를 호스트로 가져옵니다. TLS/SSL 인증서를 가져오려면 관리자 명령 프롬프트에서 다음 단계를 수행합니다.

   # <a name="command-line"></a>[명령줄](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. 에뮬레이터가 시작된 후 대화형 셸을 닫으면 에뮬레이터의 컨테이너가 종료됩니다. 데이터 탐색기를 다시 열려면 브라우저에서 다음 URL로 이동합니다. 에뮬레이터 엔드포인트가 위에 표시된 응답 메시지에 제공됩니다.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Linux docker 컨테이너에서 .NET 클라이언트 애플리케이션이 실행되며 호스트 머신에서 Azure Cosmos DB 에뮬레이터를 실행하는 경우 다음 섹션의 지침을 사용하여 인증서를 Linux docker 컨테이너로 가져옵니다.

## <a name="regenerate-the-emulator-certificates"></a>에뮬레이터 인증서 다시 생성

Docker 컨테이너에서 에뮬레이터를 실행하는 경우 해당 컨테이너를 중지했다가 다시 시작할 때마다 에뮬레이터와 연결된 인증서가 다시 생성됩니다. 이때문에 각 컨테이너가 시작된 후 인증서를 다시 가져와야 합니다. 이 제한을 해결하기 위해 Docker 작성 파일을 사용하여 Docker 컨테이너를 특정 IP 주소 및 컨테이너 이미지에 바인딩할 수 있습니다.

예를 들어, Docker 작성 파일 내에서 다음 구성을 사용할 수 있습니다. 요구 사항에 따라 형식을 지정해야 합니다. 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 무료 로컬 개발을 위해 로컬 에뮬레이터를 사용하는 방법을 살펴보았습니다. 이제 다음 문서로 진행할 수 있습니다.

* [Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)
* [명령줄 매개 변수 및 PowerShell 명령을 사용하여 에뮬레이터 제어](emulator-command-line-parameters.md)
* [에뮬레이터를 사용하여 이슈 디버그](troubleshoot-local-emulator.md)
