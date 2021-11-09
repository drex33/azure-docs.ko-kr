---
title: Azure Service Fabric 프로브
description: 애플리케이션 및 서비스 매니페스트 파일을 사용하여 Azure Service Fabric 활동성 및 준비 상태 프로브를 모델링하는 방법입니다.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 1f78499d6be8ee68011540abfba00a404b8c6ec5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059717"
---
# <a name="service-fabric-probes"></a>Service Fabric 프로브
이 문서를 더 읽기 전에 [Service Fabric 애플리케이션 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알고 있어야 합니다. 이 문서에서는 매니페스트 파일을 사용하여 활동성 및 준비 상태 프로브를 정의하는 방법에 대한 개요를 제공합니다.

## <a name="liveness-probe"></a>활동성 프로브
버전 7.1부터 Azure Service Fabric 컨테이너화 및 컨테이너화하지 않은 애플리케이션에 대한 활동성 프로브 메커니즘을 지원합니다. 활동성 프로브는 코드 패키지의 활동성을 보고하는 데 도움이 되며, 신속하게 응답하지 않으면 다시 시작됩니다.

## <a name="readiness-probe"></a>준비 프로브
8.2부터 준비 프로브도 지원됩니다. 준비 프로브는 코드 패키지가 트래픽을 수락할 준비가 되었는지 여부를 결정하는 데 사용됩니다. 예를 들어 컨테이너가 요청을 처리하는 데 시간이 오래 걸리거나 요청 큐가 가득 찬 경우 코드 패키지는 더 이상 트래픽을 수락할 수 없으므로 코드 패키지에 도달하는 엔드포인트가 제거됩니다. 

준비 프로브의 동작은 다음과 같습니다.
1.  컨테이너/코드 패키지 인스턴스가 시작됩니다.
2.  엔드포인트가 즉시 게시됩니다.
3.  준비 프로브 실행 시작
4.  준비 프로브가 결국 실패 임계값에 도달하고 엔드포인트가 제거되어 사용할 수 없게 됩니다.
5.  인스턴스가 최종적으로 준비됩니다.
6.  준비 프로브는 인스턴스가 준비 상태임을 알리고 엔드포인트를 다시 게시합니다.
7.  요청이 다시 라우팅되고 요청을 제공할 준비가 되었기 때문에 성공합니다.

> [!NOTE] 
> 준비 프로브의 경우 코드 패키지가 다시 시작되지 않고 엔드포인트만 게시되지 않으므로 의 복제본/파티션 집합이 영향을 받지 않습니다.
>

## <a name="semantics"></a>의미 체계
코드 패키지당 하나의 라이브 상태 및 준비 프로브만 지정할 수 있으며 다음 필드를 사용하여 해당 동작을 제어할 수 있습니다.

* `type`: 프로브 유형이 라이브 상태인지 또는 준비 상태인지를 지정하는 데 사용됩니다. 지원되는 값은 **라이브** 또는 **준비입니다.**

* `initialDelaySeconds`: 컨테이너가 시작된 후 프로브 실행을 시작하는 초기 지연 시간(초)입니다. 지원되는 값은 **int** 입니다. 기본값은 0이고 최솟값은 0입니다.

* `timeoutSeconds`: 프로브가 성공적으로 완료되지 않은 경우 프로브를 실패한 것으로 간주하는 데 걸린 시간(초)입니다. 지원되는 값은 **int** 입니다. 기본값은 1이고 최솟값은 1입니다.

* `periodSeconds`: 프로브 빈도를 지정하는 시간(초)입니다. 지원되는 값은 **int** 입니다. 기본값은 10이고 최솟값은 1입니다.

* `failureThreshold`: 해당 값에 도달하면 컨테이너가 다시 시작됩니다. 지원되는 값은 **int** 입니다. 기본값은 3이고 최솟값은 1입니다.

* `successThreshold`: 실패한 경우 프로브를 성공적인 것으로 간주하려면 해당 값에 대해 성공적으로 실행되어야 합니다. 지원되는 값은 **int** 입니다. 기본값은 1이고 최솟값은 1입니다.

어떤 시점이든지 대부분 하나의 컨테이너에 하나의 프로브가 있을 수 있습니다. 프로브가 **timeoutSeconds** 에서 설정된 시간 내에 완료되지 않으면 기다렸다가 **failureThreshold** 까지의 시간을 계산합니다. 

또한 Service Fabric은 **DeployedServicePackage** 에서 다음 프로브 [상태 보고서][health-introduction-link]를 생성합니다.

* `OK`: **successThreshold** 에 설정된 값에 대한 프로브가 성공합니다.

* `Error`: 컨테이너를 다시 시작하기 전 **failureCount** ==  **failureThreshold** 입니다.

* `Warning`: 
    * 프로브에 실패하고 **failureCount** < **failureThreshold** 입니다. 상태 보고서는 **failureCount** 가 **failureThreshold** 또는 **successThreshold** 에 설정된 값에 도달할 때까지 유지됩니다.
    * 실패 이후에 성공하면 경고는 유지되지만 연속적인 성공이 업데이트됩니다.

## <a name="specifying-a-probe"></a>프로브 지정

**ServiceManifestImport** 의 ApplicationManifest.xml 파일에서 프로브를 지정할 수 있습니다.

프로브는 다음 중 하나일 수 있습니다.

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP 프로브

HTTP 프로브의 경우 Service Fabric은 지정된 포트 및 경로에 HTTP 요청을 보냅니다. 200 이상 400 미만인 반환 코드는 성공을 나타냅니다.

HTTP 라이브성 프로브를 지정하는 방법의 예는 다음과 같습니다.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HTTP 프로브에는 설정할 수 있는 추가 속성이 있습니다.

* `path`: HTTP 요청에서 사용할 경로입니다.

* `port`: 프로브에 사용할 포트입니다. 해당 속성은 필수입니다. 범위는 1에서 65535 사이입니다.

* `scheme`: 코드 패키지에 연결하는 데 사용하는 체계입니다. 해당 속성이 HTTPS로 설정되어 있으면 인증서 확인을 건너뜁니다. 기본 설정은 HTTP입니다.

* `httpHeader`: 요청에 설정할 헤더입니다. 여러 노드를 지정할 수 있습니다.

* `host`: 연결할 호스트 IP 주소입니다.

### <a name="tcp-probe"></a>TCP 프로브

TCP 프로브의 경우 Service Fabric은 지정된 포트를 사용하여 컨테이너에서 소켓을 열려고 시도합니다. 연결을 설정할 수 있는 경우 프로브는 성공한 것으로 간주됩니다. TCP 소켓을 사용하는 프로브를 지정하는 방법의 예는 다음과 같습니다.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec 프로브

해당 프로브는 컨테이너에 **exec** 명령을 실행하고 명령이 완료될 때까지 기다립니다.

> [!NOTE]
> **Exec** 명령은 쉼표로 구분된 문자열을 사용합니다. 다음 예제의 명령은 Linux 컨테이너에서 작동합니다.
> Windows 컨테이너를 검색하려는 경우 **cmd** 를 사용합니다.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>다음 단계
관련 정보는 다음 문서를 참조하세요.
* [Service Fabric 및 컨테이너][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

