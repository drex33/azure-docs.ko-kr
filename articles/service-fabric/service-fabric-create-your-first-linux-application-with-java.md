---
title: Linux에서 Azure Service Fabric Reliable Actors Java 애플리케이션 만들기
description: 5분 안에 Java Service Fabric Reliable Actors 애플리케이션을 만들고 배포하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/18/2018
ms.custom: devx-track-java
ms.openlocfilehash: d51bc288eac658f7b0fe2ef019fc77eaa83837c7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013748"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Linux에서 첫 번째 Java Service Fabric Reliable Actors 애플리케이션 만들기
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

이 빠른 시작을 통해 몇 분만에 Linux 개발 환경에서 첫 번째 Azure Service Fabric Java 애플리케이션을 만들 수 있습니다.  작업이 완료되면 간단한 Java 단일 서비스 애플리케이션이 로컬 개발 클러스터에서 실행됩니다.

## <a name="prerequisites"></a>필수 구성 요소
시작하기 전에 [Linux 개발 환경](service-fabric-get-started-linux.md)에서 Service Fabric SDK, Service Fabric CLI, Yeoman을 설치하고, Java 개발 환경을 설정하고, 개발 클러스터를 설정합니다. Mac OS X을 사용하는 경우 [Docker를 사용하여 Mac에서 개발 환경 설정](service-fabric-get-started-mac.md)할 수 있습니다.

[Service Fabric CLI](service-fabric-cli.md)도 설치합니다.

### <a name="install-and-set-up-the-generators-for-java"></a>Java용 생성기 설치 및 설정
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 Service Fabric Java 애플리케이션을 만들 수 있는 스캐폴딩 도구를 제공합니다.  Yeoman이 아직 설치되지 않은 경우 Yeoman 설정에 대한 지침은 [Linux로 시작하는 Service Fabric](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)을 참조하세요. 다음 명령을 실행하여 Java용 Service Fabric Yeoman 템플릿 생성기를 설치합니다.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>기본 개념
Reliable Actors를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **행위자 서비스**. Reliable Actors는 서비스 패브릭 인프라에 배포될 수 있는 Reliable Services에 패키징됩니다. 행위자 인스턴스는 명명된 서비스 인스턴스에서 활성화됩니다.
* **행위자 등록**. Reliable Services와 마찬가지로 Reliable Actor 서비스를 Service Fabric 런타임에 등록해야 합니다. 또한 행위자 형식을 행위자 런타임에 등록해야 합니다.
* **행위자 인터페이스**. 행위자 인터페이스는 행위자에 대한 강력한 형식의 공용 인터페이스를 정의하는 데 사용됩니다. Reliable Actor 모델 용어에서 행위자 인터페이스는 행위자가 이해하고 처리할 수 있는 메시지의 유형을 정의합니다. 행위자 인터페이스는 다른 행위자 또는 클라이언트 애플리케이션에서 메시지를 행위자에게 "보내는"(비동기) 데 사용됩니다. Reliable Actors는 여러 인터페이스를 구현할 수 있습니다.
* **ActorProxy 클래스**. ActorProxy 클래스는 클라이언트 애플리케이션에서 행위자 인터페이스를 통해 노출되는 메서드를 호출하는 데 사용됩니다. ActorProxy 클래스는 다음 두 가지 중요한 기능을 제공합니다.

  * 이름 확인: 클러스터에서 행위자를 찾을 수 있습니다(호스트되는 클러스터의 노드 찾기).
  * 오류 처리: 메서드 호출을 다시 시도하고 행위자를 클러스터의 다른 노드로 재배치해야 하는 경우 등의 오류가 발생한 후 행위자의 위치를 다시 파악할 수 있습니다.

행위자 인터페이스와 관련된 다음 규칙을 확인하면 도움이 됩니다.

* 행위자 인터페이스 메서드는 오버로드할 수 없습니다.
* 행위자 인터페이스 메서드에는 out, ref 또는 선택적 매개 변수가 없어야 합니다.
* 제네릭 인터페이스는 지원되지 않습니다.

## <a name="create-the-application"></a>애플리케이션 만들기
Service Fabric 애플리케이션은 애플리케이션의 기능을 제공하는 특정 역할이 있는 하나 이상의 서비스를 포함합니다. 마지막 섹션에는 설치한 생성기는 쉽게 첫 번째 서비스를 만들고 나중에 더 추가할 수 있습니다.  Eclipse용 플러그 인을 사용하여 Service Fabric Java 애플리케이션을 만들고 빌드하고 배포할 수 있습니다. [Eclipse를 사용하여 첫 번째 Java 애플리케이션 만들기 및 배포](service-fabric-get-started-eclipse.md)를 참조하세요. 빠른 시작에서 Yeoman을 사용하여 카운터 값을 저장하고 가져오는 단일 서비스를 포함한 애플리케이션을 만듭니다.

1. 터미널에서 ``yo azuresfjava``을 입력합니다.
2. 애플리케이션의 이름을 지정합니다.
3. 첫 번째 서비스의 형식을 선택하고 이름을 지정합니다. 이 자습서에서 Reliable Actor 서비스를 선택합니다. 다른 종류의 서비스에 대한 자세한 내용은 [Service Fabric 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조하세요.
   ![Java용 Service Fabric Yeoman 생성기][sf-yeoman]

애플리케이션의 이름을 "HelloWorldActorApplication"으로 지정하고 작업자의 이름을 "HelloWorldActor"라고 지정하면 다음과 같은 스캐폴딩이 만들어집니다.

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>신뢰할 수 있는 행위자 기본 구성 요소
앞에서 설명한 기본 개념은 신뢰할 수 있는 행위자 서비스의 기본 구성 요소로 변환합니다.

### <a name="actor-interface"></a>행위자 인터페이스
여기에는 행위자에 대한 인터페이스 정의가 포함됩니다. 이 인터페이스는 행위자 구현과 행위자를 호출하는 클라이언트에 의해 공유되는 행위자 계약을 정의하므로 일반적으로 행위자 구현과는 별개이고 다른 여러 서비스 또는 클라이언트 애플리케이션이 공유할 수 있는 장소에서 정의하는 것이 좋습니다.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>행위자 서비스
여기에는 행위자 구현 및 행위자 등록 코드가 포함됩니다. 행위자 클래스는 행위자 인터페이스를 구현합니다. 사용자 행위자가 작업을 수행하는 위치입니다.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>행위자 등록
행위자 서비스는 서비스 패브릭 런타임에서 서비스 유형에 등록되어야 합니다. 행위자 서비스에서 행위자 인스턴스를 실행하려면 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());

public static void main(String[] args) throws Exception {

        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>애플리케이션 빌드
Service Fabric Yeoman 템플릿은 [Gradle](https://gradle.org/)에 대한 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 애플리케이션을 빌드할 수 있습니다.
Maven에서 Service Fabric Java 종속성을 가져옵니다. Service Fabric Java 애플리케이션을 빌드하고 사용하려면 JDK 및 Gradle이 설치되어 있는지 확인해야 합니다. 아직 설치되지 않은 경우 JDK 및 Gradle 설치에 대한 지침은 [Linux로 시작하는 Service Fabric](service-fabric-get-started-linux.md#set-up-java-development)을 참조하세요.

애플리케이션을 빌드하고 패키징하려면 다음을 실행합니다.

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>애플리케이션 배포
애플리케이션이 빌드되면 로컬 클러스터에 배포할 수 있습니다.

1. 로컬 Service Fabric 클러스터에 연결합니다(클러스터를 [설정 및 실행](service-fabric-get-started-linux.md#set-up-a-local-cluster)해야 함).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 템플릿에 제공된 설치 스크립트를 실행하여 클러스터의 이미지 저장소에 애플리케이션 패키지를 복사하고 애플리케이션 유형을 등록하며 애플리케이션의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```

빌드된 애플리케이션을 배포하는 방법은 다른 Service Fabric 애플리케이션과 같습니다. 자세한 지침은 [Service Fabric CLI에서 Service Fabric 애플리케이션 관리](service-fabric-application-lifecycle-sfctl.md)에 대한 설명서를 참조하세요.

애플리케이션 패키지 내에 생성된 매니페스트에서 이러한 명령의 매개 변수를 찾을 수 있습니다.

애플리케이션이 배포되면 브라우저를 열고 `http://localhost:19080/Explorer`에 있는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)로 이동합니다.
그런 다음 **애플리케이션** 노드를 확장하면 애플리케이션 형식에 대한 항목 및 해당 형식의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

> [!IMPORTANT]
> 애플리케이션을 Azure의 보안 Linux 클러스터에 배포하려면 Service Fabric 런타임으로 애플리케이션의 유효성을 검사하는 인증서를 구성해야 합니다. 이렇게 하면 Reliable Actors 서비스에서 기본 Service Fabric 런타임 API와 통신할 수 있습니다. 자세히 알아보려면 [Linux 클러스터에서 실행하도록 Reliable Services 앱 구성](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)을 참조하세요.  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>테스트 클라이언트 시작 및 장애 조치 수행
행위자는 자체적으로 아무 작업도 수행하지 않습니다. 따라서 메시지를 보낼 다른 서비스 또는 클라이언트가 필요합니다. 행위자 템플릿은 행위자 서비스와 상호 작용하는 데 사용할 수 있는 간단한 테스트 스크립트를 포함합니다.

> [!NOTE]
> 테스트 클라이언트는 행위자 서비스와 동일한 클러스터 내에서 실행해야 하며 동일한 IP 주소 공간을 공유해야 하는 ActorProxy 클래스를 사용하여 행위자와 통신합니다.  로컬 개발 클러스터와 동일한 컴퓨터에 테스트 클라이언트를 실행할 수 있습니다.  원격 클러스터의 작업자와 통신하려면 행위자와의 외부 통신을 처리하는 클러스터에서 게이트웨이를 배포해야 합니다.

1. 행위자 서비스의 출력을 확인하려면 조사식 유틸리티를 사용하여 스크립트를 실행합니다.  테스트 스크립트는 행위자의 `setCountAsync()` 메서드를 호출하여 카운터를 증가시키고 행위자의 `getCountAsync()` 메서드를 호출하여 새 카운터 값을 가져오고 해당 값을 콘솔에 표시합니다.

   MAC OS X의 경우 다음 추가 명령을 실행하여 HelloWorldTestClient 폴더를 컨테이너 내에서 동일한 위치에 복사해야 합니다.

    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer에서 행위자 서비스에 대한 주 복제본을 호스팅하는 노드를 찾습니다. 아래 스크린샷에 있는 노드 3입니다. 기본 서비스 복제본은 읽기 및 쓰기 작업을 처리합니다.  서비스 상태의 변경 사항은 보조 복제본에 복제되고 아래 스크린샷의 0 및 1노드에서 실행됩니다.

    ![Service Fabric Explorer에서 기본 복제본 찾기][sfx-primary]

3. **노드** 에서는 이전 단계에서 찾은 노드를 클릭한 다음 작업 메뉴에서 **비활성화(다시 시작)** 를 선택합니다. 이 작업은 주 서비스 복제본을 실행하는 노드를 다시 시작하고 다른 노드에서 실행 중인 보조 복제본 중 하나에 장애 조치를 강제합니다.  해당 보조 복제본이 주 복제본으로 승격되면 다른 노드에서 다른 보조 복제본이 생성되며 주 복제본이 읽기/쓰기 작업을 수행하기 시작합니다. 노드가 다시 시작되면 테스트 클라이언트의 출력에 주의하고 장애 조치에도 불구하고 카운터가 계속 증가하게 됩니다.

## <a name="remove-the-application"></a>애플리케이션을 제거합니다.
템플릿에 제공된 설치 제거 스크립트를 사용하여 애플리케이션 인스턴스를 제거하고 애플리케이션 패키지를 등록 취소하고 클러스터의 이미지 저장소에서 애플리케이션 패키지를 제거합니다.

```bash
./uninstall.sh
```

Service Fabric Explorer에서 애플리케이션이 표시되고 애플리케이션 형식이 **애플리케이션** 노드에 더 이상 표시되지 않습니다.

## <a name="service-fabric-java-libraries-on-maven"></a>Maven의 Service Fabric Java 라이브러리
Service Fabric Java 라이브러리는 Maven에서 호스팅되었습니다. 프로젝트의 ``pom.xml`` 또는 ``build.gradle``에서 종속성을 추가하고 **mavenCentral** 에서 Service Fabric Java 라이브러리를 사용할 수 있습니다.

### <a name="actors"></a>행위자

애플리케이션에 대한 Service Fabric Reliable Actor 지원입니다.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Services

애플리케이션에 대한 Service Fabric Reliable Services 지원입니다.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Others
#### <a name="transport"></a>전송

Service Fabric Java 애플리케이션에 대한 전송 계층 지원입니다. 전송 계층에서 프로그래밍하지 않으면 Reliable Actor 또는 Service 애플리케이션에 이 종속성을 명시적으로 추가할 필요가 없습니다.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>패브릭 지원

Service Fabric에 대한 시스템 수준 지원이며 네이티브 Service Fabric 런타임에 지시합니다. Reliable Actor 또는 Service 애플리케이션에 이 종속성을 명시적으로 추가할 필요가 없습니다. 그러면 위의 다른 종속성을 포함하는 경우 자동으로에서 Maven에서 가져옵니다.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>다음 단계

* [Eclipse를 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기](service-fabric-get-started-eclipse.md)
* [Reliable Actors에 대해 자세히 알아보기](service-fabric-reliable-actors-introduction.md)
* [Service Fabric CLI를 사용하여 Service Fabric 클러스터와 상호 작용](service-fabric-cli.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기
* [Service Fabric CLI 시작](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
