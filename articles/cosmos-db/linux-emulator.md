---
title: Linux용 Docker에서 Azure Cosmos DB 에뮬레이터 실행
description: Linux 및 macOS에서 Azure Cosmos DB Linux 에뮬레이터를 실행하고 사용하는 방법을 알아봅니다. 이 에뮬레이터를 사용하면 Azure를 구독하지 않고도 무료로 애플리케이션을 로컬에서 개발하고 테스트할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 06/04/2021
ms.openlocfilehash: 5c6ddce8d50da1660bfcba8b29bb032b56197bb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566315"
---
# <a name="run-the-emulator-on-docker-for-linux-preview"></a>Linux용 Docker에서 에뮬레이터 실행(미리 보기)

Azure Cosmos DB Linux 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 현재 Linux 에뮬레이터는 SQL API만 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 애플리케이션을 테스트할 수 있습니다. Azure Cosmos DB Linux 에뮬레이터에서 애플리케이션이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다. 이 문서에서는 macOS 및 Linux 환경에서 에뮬레이터를 설치하고 사용하는 방법을 설명합니다.

> [!NOTE]
> 현재 Cosmos DB Linux 에뮬레이터는 미리 보기 모드이고 SQL API만 지원합니다. Windows 버전과 비교 시 에뮬레이터에서 처리하는 초당 요청 수 면에서 성능이 약간 저하될 수 있습니다. 프로비전할 수 있는 컨테이너 수에 직접적으로 영향을 미치는 실제 파티션 기본 수는 10개입니다.
> 
> 프로덕션에서는 에뮬레이터(미리 보기)를 사용하지 않는 것이 좋습니다. 워크로드가 더 많은 경우에는 [Windows 에뮬레이터](local-emulator.md)를 사용합니다.

## <a name="how-does-the-emulator-work"></a>에뮬레이터 작동 방식

Azure Cosmos DB Linux 에뮬레이터는 정확도가 높은 Azure Cosmos DB 서비스 에뮬레이션을 제공합니다. Azure Cosmos DB와 동일한 기능을 지원합니다. 여기에는 데이터 만들기 및 쿼리, 컨테이너 프로비저닝 및 스케일링, 저장 프로시저 및 트리거 실행이 포함됩니다. Azure Cosmos DB Linux 에뮬레이터를 사용하여 애플리케이션을 개발 및 테스트하고 Azure Cosmos DB 연결 엔드포인트를 업데이트하여 전역 규모로 Azure에 배포할 수 있습니다.

따라서 에뮬레이터를 사용할 때 글로벌 복제, 한 자리 밀리초 읽기/쓰기 대기 시간, 튜닝 가능한 일관성 수준 등 Azure 인프라를 기반으로 하는 기능은 적용할 수 없습니다.

## <a name="differences-between-the-linux-emulator-and-the-cloud-service"></a>Linux 에뮬레이터와 클라우드 서비스 간의 차이점
Azure Cosmos DB 에뮬레이터는 로컬 개발자 워크스테이션에서 실행되는 에뮬레이트된 환경을 제공하므로 에뮬레이터와 클라우드의 Azure Cosmos 계정 간에 기능 면에서 다음과 같은 몇 가지 차이점이 있습니다.

- 현재 에뮬레이터의 **데이터 탐색기** 창은 SQL API 클라이언트만 완전히 지원합니다.

- Linux 에뮬레이터를 사용하면 [프로비전된 처리량](set-throughput.md) 모드에서만 Azure Cosmos 계정을 만들 수 있습니다. 현재 [서버리스](serverless.md) 모드는 지원되지 않습니다.

- Linux 에뮬레이터는 확장성 있는 서비스가 아니며 많은 수의 컨테이너를 지원하지 않습니다. Azure Cosmos DB 에뮬레이터를 사용할 경우 기본적으로 400RU/s에서 고정 크기 컨테이너를 최대 10개까지 만들거나(Azure Cosmos DB SDK를 사용하는 경우에만 지원됨) 무제한 컨테이너를 5개 만들 수 있습니다. 이 값을 변경하는 방법에 대한 자세한 내용은 [PartitionCount 값 설정](emulator-command-line-parameters.md#set-partitioncount) 문서를 참조하세요.

- [일관성 수준](consistency-levels.md)이 시나리오 테스트 전용 명령줄 인수를 사용하여 조정될 수 있지만(기본 설정: 세션) 사용자는 클라우드 서비스에서와 같은 동작을 기대하지 않을 수도 있습니다. 예를 들어 강력하면서 제한된 부실 일관성은 계정의 기본 일관성을 Cosmos DB SDK에 전달하는 것 외에 에뮬레이터에 영향을 미치지 않습니다.

- Linux 에뮬레이터는 [다중 지역 복제](distribute-data-globally.md)를 제공하지 않습니다.

- Azure Cosmos DB Linux 에뮬레이터 복사본은 항상 Azure Cosmos DB 서비스의 최신 변경 사항이 적용된 최신 상태로 유지되지 않을 수 있으므로 [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)를 사용하여 애플리케이션에 필요한 처리량(RU)을 정확하게 예측해야 합니다.

- Linux 에뮬레이터는 최대 ID 속성 크기 254자를 지원합니다.

## <a name="run-the-linux-emulator-on-macos"></a><a id="run-on-macos"></a>macOS에서 Linux 에뮬레이터 실행

> [!NOTE]
> 에뮬레이터는 Intel 프로세서가 장착된 MacBook만 지원합니다.

시작하려면 Docker Hub로 이동하여 [macOS용 Docker Desktop](https://hub.docker.com/editions/community/docker-ce-desktop-mac/)을 설치합니다. 다음 단계를 수행하여 macOS에서 에뮬레이터를 실행합니다.

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

## <a name="install-the-certificate"></a><a id="install-certificate"></a>인증서 설치

1. 에뮬레이터가 실행되면 여러 터미널을 사용하여 로컬 컴퓨터의 IP 주소를 변수에 로드합니다.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. 그런 다음, 에뮬레이터의 인증서를 다운로드합니다.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
    ```


## <a name="consume-the-endpoint-via-ui"></a><a id="consume-endpoint-ui"></a>UI를 통해 엔드포인트 사용

에뮬레이터는 자체 서명된 인증서를 사용하여 엔드포인트에 대한 연결을 보호하고 수동으로 신뢰받아야 합니다. 원하는 웹 브라우저를 사용해 UI를 통해 엔드포인트를 사용하려면 다음 단계를 수행합니다.

1. 에뮬레이터 자체 서명 인증서를 다운로드했는지 확인합니다.

   ```bash
   curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
   ```

1. Mac에서 **키체인 접근** 앱을 열어 에뮬레이터 인증서를 가져옵니다.

1. **파일** 과 **항목 가져오기** 를 선택하고 **emulatorcert.crt** 를 가져옵니다.

1. *emulatorcert.crt* 가 키체인에 로드되면 **localhost** 이름을 두 번 클릭하고 신뢰 설정을 **항상 신뢰** 로 변경합니다.

1. 이제 `https://localhost:8081/_explorer/index.html` 또는 `https://{your_local_ip}:8081/_explorer/index.html`을 찾아보고 에뮬레이터에 대한 연결 문자열을 검색할 수 있습니다.

필요에 따라 애플리케이션에서 SSL 유효성 검사를 사용하지 않도록 설정할 수 있습니다. 이는 개발 목적으로만 권장되며 프로덕션 환경에서 실행하는 경우에는 수행할 수 없습니다.

## <a name="run-the-linux-emulator-on-linux-os"></a><a id="run-on-linux"></a>Linux OS에서 Linux 에뮬레이터 실행

시작하려면 `apt` 패키지를 사용하여 최신 버전의 Docker를 설치합니다.

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

WSL(Linux용 Windows 하위 시스템)을 사용하는 경우 다음 명령을 실행하여 `ifconfig`를 가져옵니다.

```bash
sudo apt-get install net-tools
```

다음 단계를 수행하여 Linux에서 에뮬레이터를 실행합니다.

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

4. 에뮬레이터가 실행되면 여러 터미널을 사용하여 로컬 컴퓨터의 IP 주소를 변수에 로드합니다.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

5. 그런 다음, 에뮬레이터의 인증서를 다운로드합니다. 또는 자체 서명된 에뮬레이터 인증서를 다운로드하는 다음 엔드포인트를 에뮬레이터 엔드포인트가 다른 애플리케이션의 요청을 받을 수 있는 시기를 알리는 데 사용할 수도 있습니다.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > ~/emulatorcert.crt
    ```

6. Linux 배포에서 사용자 지정 인증서가 포함된 폴더에 CRT 파일을 복사합니다. 일반적으로 Debian 배포판에서는 `/usr/local/share/ca-certificates/`에 있습니다.

   ```bash
   cp ~/emulatorcert.crt /usr/local/share/ca-certificates/
   ```

7. TLS/SSL 인증서를 업데이트합니다. 그러면 `/etc/ssl/certs/` 폴더가 업데이트됩니다.

   ```bash
   update-ca-certificates
   ```

    Java 기반 애플리케이션의 경우 [Java 신뢰 저장소](local-emulator-export-ssl-certificates.md)에 인증서를 가져와야 합니다.

    ```bash
    keytool -keystore ~/cacerts -importcert -alias  emulator_cert -file ~/emulatorcert.crt
    java -ea -Djavax.net.ssl.trustStore=~/cacerts -Djavax.net.ssl.trustStorePassword="changeit" $APPLICATION_ARGUMENTS
    ```

## <a name="configuration-options"></a><a id="config-options"></a>구성 옵션

|속성  |기본값  |설명  |
|---------|---------|---------|
|  포트: `-p`   |         |   현재 에뮬레이터 엔드포인트에서는 포트 8081 및 10251~10255만 필요합니다.     |
| `AZURE_COSMOS_EMULATOR_PARTITION_COUNT`    |    10     |    총 실제 파티션 수를 제어하며 특정 시점에 만들거나 존재할 수 있는 컨테이너 수를 제어합니다. 에뮬레이터 시작 시간이 빨라지도록 작은 값으로 시작하는 것이 좋습니다. 즉, 3.     |
|  메모리: `-m`   |         | 3GB 이상의 메모리가 필요합니다.     |
| 코어: `--cpus`  |         |   충분한 메모리와 CPU 코어를 프로비전해야 합니다. 에뮬레이터는 최소 0.5코어로 실행될 수 있지만(매우 느림) 코어는 최소 2개 이상 있는 것이 좋습니다.      |
|`AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE`  | false  | 단독으로 사용되는 이 설정은 컨테이너 다시 시작 간에 데이터를 유지하는 데 도움이 됩니다.  |

## <a name="troubleshoot-issues"></a><a id="troubleshoot-issues"></a>문제 해결

이 섹션에서는 Linux 에뮬레이터를 사용할 때 발생하는 오류를 해결하는 팁을 설명합니다.

### <a name="connectivity-issues"></a>연결 문제

#### <a name="my-app-cant-connect-to-emulator-endpoint-the-ssl-connection-could-not-be-established-or-i-cant-start-the-data-explorer"></a>앱에서 에뮬레이터 엔드포인트에 연결할 수 없거나(“SSL 연결을 설정할 수 없습니다”) 데이터 탐색기를 시작할 수 없음

- 에뮬레이터가 다음 명령으로 실행되는지 확인합니다.

    ```bash
    docker ps --all
    ```

- 특정 에뮬레이터 컨테이너가 실행 상태인지 확인합니다.

- 에뮬레이터 포트 8081 및 10250~10255를 사용하는 다른 애플리케이션이 없는지 확인합니다.

- 컨테이너 포트 8081이 올바르게 매핑되고 컨테이너 외부 환경에서 액세스할 수 있는지 확인합니다.  

   ```bash
   netstat -lt
   ```

- “localhost” 대신 Docker 컨테이너의 IP 주소를 사용하여 에뮬레이터의 엔드포인트와 포트에 액세스해 봅니다.

- 에뮬레이터 자체 서명된 인증서가 [키체인](#consume-endpoint-ui)에 올바르게 추가되었는지 확인합니다.

- Java 애플리케이션의 경우 인증서를 [Java 인증서 저장소 섹션](#run-on-linux)으로 가져왔는지 확인합니다.

- .NET 애플리케이션의 경우 SSL 유효성 검사를 사용하지 않도록 설정할 수 있습니다.

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

.NET Standard 2.1 이상과 호환되는 프레임워크에서 실행되는 애플리케이션의 경우 `CosmosClientOptions.HttpClientFactory`를 활용할 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

.NET Standard 2.0과 호환되는 프레임워크에서 실행되는 애플리케이션의 경우 `CosmosClientOptions.HttpClientFactory`를 활용할 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

---

#### <a name="my-nodejs-app-is-reporting-a-self-signed-certificate-error"></a>내 Node.js 앱에서 자체 서명된 인증서 오류를 보고하고 있습니다.

컨테이너 IP 주소와 같이 `localhost` 이외의 주소를 통해 에뮬레이터에 연결하려고 하면 Node.js는 인증서가 설치되어 있어도 자체 서명된 인증서에 대한 오류를 발생시킵니다.

환경 변수 `NODE_TLS_REJECT_UNAUTHORIZED`를 `0`으로 설정하여 TLS 확인을 사용하지 않도록 설정할 수 있습니다.

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

이 플래그는 Node.js TLS를 사용하지 않도록 설정하기 때문에 로컬 개발에만 사용하는 것이 좋습니다. 자세한 내용은 [Node.js 문서](https://nodejs.org/api/cli.html#cli_node_tls_reject_unauthorized_value) 및 [Cosmos DB 에뮬레이터 인증서 문서](local-emulator-export-ssl-certificates.md#how-to-use-the-certificate-in-nodejs)에서 찾을 수 있습니다.

#### <a name="the-docker-container-failed-to-start"></a>Docker 컨테이너 시작 실패

에뮬레이터에서 다음 메시지가 표시되면서 오류가 발생합니다.

```bash
/palrun: ERROR: Invalid mapping of address 0x40037d9000 in reserved address space below 0x400000000000. Possible causes:
1. The process (itself, or via a wrapper) starts up its own running environment sets the stack size limit to unlimited via syscall setrlimit(2);
2. The process (itself, or via a wrapper) adjusts its own execution domain and flag the system its legacy personality via syscall personality(2);
3. Sysadmin deliberately sets the system to run on legacy VA layout mode by adjusting a sysctl knob vm.legacy_va_layout.
```

현재 Docker Host 프로세서 형식이 Microsoft Docker 이미지와 호환되지 않으므로 이 오류가 발생할 수 있습니다. 즉, 컴퓨터는 M1 칩셋이 장착된 MacBook입니다.

#### <a name="my-app-received-too-many-connectivity-related-timeouts"></a>앱에서 너무 많은 연결 관련 시간 제한을 수신함

- Docker 컨테이너가 충분한 리소스[(코어 또는 메모리)](#config-options)로 프로비전되지 않습니다. 코어 수를 늘리거나 시작 시 프로비전된 실제 파티션 수를 줄이는 것이 좋습니다.

- TCP 연결 수가 현재 OS 설정을 초과하지 않았는지 확인합니다.

- 애플리케이션에서 문서 크기를 줄여 봅니다.
    
#### <a name="my-app-could-not-provision-databasescontainers"></a>앱에서 데이터베이스/컨테이너를 프로비전할 수 없음

에뮬레이터에서 프로비전된 실제 파티션 수가 너무 적습니다. 사용하지 않는 데이터베이스/컬렉션을 삭제하거나 [다수의 실제 파티션](#config-options)으로 에뮬레이터를 시작합니다.

### <a name="reliability-and-crashes"></a>안정성 및 크래시

- 에뮬레이터 시작이 실패했습니다.

  - [Linux용 Cosmos DB 에뮬레이터의 최신 이미지가 실행](#refresh-linux-container)되고 있는지 확인합니다. 그렇지 않으면 연결 관련 이슈와 관련된 위 섹션을 참조하세요.

  - Cosmos DB 에뮬레이터 데이터 폴더가 “볼륨이 탑재됨”이면 볼륨에 충분한 공간이 있고 볼륨이 읽기/쓰기인지 확인합니다.

  - 권장 설정으로 컨테이너 만들기가 작동하는지 확인합니다. 작동하면 실패 원인은 컨테이너 시작 시에 해당 Docker 명령을 통해 전달된 추가 설정일 가능성이 매우 높습니다.

  - 에뮬레이터가 다음 오류로 인해 시작하지 않는 경우:
  
    ```bash
    "Failed loading Emulator secrets certificate. Error: 0x8009000f or similar, a new policy might have been added to your host that prevents an application such as Azure Cosmos DB emulator from creating and adding self signed certificate files into your certificate store."
    ```

    관리자 컨텍스트에서 실행되는 경우에도 이 문제가 발생할 수 있습니다. 일반적으로 IT 부서에서 추가한 특정 정책이 로컬 관리자보다 우선 적용되기 때문입니다. 여전히 자체 서명된 에뮬레이터 SSL 인증서를 호스트 컴퓨터 컨텍스트에 추가할 수 있는 권한이 있는 경우 대신 에뮬레이터용 Docker 이미지를 사용하면 이 사례에 도움이 될 수 있습니다(Java 및 .NET Cosmos SDK 클라이언트 애플리케이션에서 필요).

- 에뮬레이터가 크래시 중입니다.

  - [권장 설정](#run-on-linux)으로 컨테이너 만들기가 작동하는지 확인합니다. 작동하면 실패 원인은 컨테이너 시작 시에 해당 Docker 명령을 통해 전달된 추가 설정일 가능성이 매우 높습니다.

  - 연결된 모드에서 에뮬레이터의 Docker 컨테이너를 시작합니다(`docker start -it` 참조).

  - 크래시 관련 덤프/데이터를 수집하고 [설명된 단계](#report-an-emulator-issue)를 수행하여 이슈를 보고합니다.  

### <a name="data-explorer-errors"></a>데이터 탐색기 오류

- 데이터가 표시되지 않습니다.

  - 연결 관련 이슈와 관련된 위 섹션을 참조하세요.

  - 브라우저에서 데이터 탐색기 페이지에 액세스할 수 있도록 자체 서명된 에뮬레이터 인증서를 올바르게 가져왔고 수동으로 신뢰할 수 있는지 확인합니다.

  - 데이터베이스/컨테이너를 만들고 데이터 탐색기를 사용하여 항목을 삽입해 봅니다. 성공하면 이슈 원인은 애플리케이션 내에 있을 가능성이 매우 놓습니다. 그렇지 않으면 [Cosmos DB 팀에 문의](#report-an-emulator-issue)하세요.

### <a name="performance-issues"></a>성능 문제

초당 요청 수가 낮고 요청 대기 시간이 높습니다.

- Docker 컨테이너가 충분한 리소스[(코어 또는 메모리)](#config-options)로 프로비전되지 않습니다. 코어 수를 늘리거나 시작 시 프로비전된 실제 파티션 수를 줄이는 것이 좋습니다.

## <a name="refresh-linux-container"></a><a id="refresh-linux-container"></a>Linux 컨테이너 새로 고침

Linux 컨테이너를 새로 고치려면 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 모든 Docker 컨테이너를 봅니다.

   ```bash
   docker ps --all
   ```

1. 위 명령에서 검색된 ID를 사용하여 컨테이너를 제거합니다.

   ```bash
   docker rm ID_OF_CONTAINER_FROM_ABOVE
   ```

1. 그런 다음, 모든 Docker 이미지를 나열합니다.

   ```bash
   docker images
   ```

1. 이전 단계에서 검색한 ID를 사용하여 이미지를 제거합니다.

   ```bash
   docker rmi ID_OF_IMAGE_FROM_ABOVE
   ```

1. Cosmos DB Linux 에뮬레이터의 최신 이미지를 가져옵니다.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. 중지한 컨테이너를 시작하려면 다음을 실행합니다.

   ```bash
   docker start -ai ID_OF_CONTAINER
   ```

## <a name="report-an-emulator-issue"></a>에뮬레이터 이슈 보고

Linux 에뮬레이터에서 발생한 이슈를 보고하는 경우 문제에 대한 정보를 가능한 많이 제공합니다. 세부 정보에는 다음이 포함됩니다.

- 발생한 오류/이슈에 대한 설명
- 환경(OS, 호스트 구성)
- 컴퓨터 및 프로세서 형식
- 에뮬레이터를 만들고 시작하는 데 사용한 명령(Docker Compose가 사용된 경우 YML 파일)
- 워크로드 설명
- 사용된 데이터베이스/컬렉션 및 항목의 샘플
- 연결된 모드에서 에뮬레이터의 Docker 컨테이너를 시작할 때 출력된 콘솔 포함
- 위 모든 정보를 [Azure Cosmos DB 팀](mailto:cdbportalfeedback@microsoft.com)에 보냅니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 무료 로컬 개발에 Cosmos DB Linux 에뮬레이터를 사용하는 방법을 살펴보았습니다. 이제 다음 문서로 진행할 수 있습니다.

- [Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)
- [에뮬레이터를 사용하여 이슈 디버그](troubleshoot-local-emulator.md)
