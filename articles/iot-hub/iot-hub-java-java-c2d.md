---
title: Azure IoT Hub(Java)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: Java용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 1493e9166e9d9b1584187fe6d7210936db7cc038
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234436"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>IoT Hub(Java)를 사용하여 클라우드-디바이스 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) 빠른 시작에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비저닝하고 디바이스-클라우드 메시지를 보내는 시뮬레이션된 디바이스 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)를 기반으로 합니다. 다음을 수행하는 방법을 보여줍니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서, IoT Hub에서 디바이스로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

[IoT Hub 개발자 가이드에서 클라우드-디바이스 메시지](iot-hub-devguide-messaging.md)에 대한 자세한 내용을 찾아볼 수 있습니다.

이 자습서의 끝 부분에서는 다음 두 개의 Java 콘솔 앱을 실행합니다.

* **simulated-device** - [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)에서 만든 앱의 수정 버전입니다. IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다.

* **send-c2d-messages** - IoT Hub를 통해 시뮬레이션된 디바이스 앱에 클라우드-디바이스 메시지를 보낸 다음 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 Javascript 포함)를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub에서 허브로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) 빠른 시작 또는 [IoT Hub로 메시지 라우팅 구성](tutorial-routing.md) 자습서의 전체 작업 버전

* [Java SE Development Kit 8](/java/azure/jdk/). JDK 8용 다운로드를 가져오려면 **장기 지원** 에서 **Java 8** 을 선택해야 합니다.

* [Maven 3](https://maven.apache.org/download.cgi)

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)에서 만든 시뮬레이션된 디바이스 앱을 수정하고 IoT Hub로부터 클라우드-디바이스 메시지를 수신합니다.

1. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 파일을 엽니다.

2. **App** 클래스 안에 중첩 클래스로 다음과 같은 **MessageCallback** 클래스를 추가합니다. 디바이스가 IoT Hub에서 메시지를 받을 때 **execute** 메서드가 호출됩니다. 이 예제에서 디바이스는 항상 IoT Hub에 메시지를 완료했음을 알립니다.

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. 다음과 같이 클라이언트를 열기 전에 **main** 메서드를 수정하여 **AppMessageCallback** 인스턴스를 만들고 **setMessageCallback** 메서드를 호출합니다.

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Maven을 사용하여 **simulated-device** 앱을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`AppMessageCallback` 클래스의 `execute` 메서드는 `IotHubMessageResult.COMPLETE`를 반환합니다. 이렇게 하면 메시지가 성공적으로 처리되었으며 디바이스 큐에서 메시지를 안전하게 제거할 수 있다는 알림이 IoT Hub에 전달됩니다. 디바이스는 사용 중인 프로토콜에 관계없이 처리가 성공적으로 완료되면 이 값을 반환해야 합니다.

AMQP 및 HTTPS를 사용하지만 MQTT를 사용하지 않는 경우에는 디바이스가 다음을 수행할 수도 있습니다.

* 메시지 중단 - IoT Hub가 나중에 사용하기 위해 디바이스 큐에 메시지를 보관합니다.
* 메시지 거부 - 디바이스 큐에서 메시지가 영구적으로 제거됩니다.

디바이스에서 메시지를 완료, 중단 또는 거부할 수 없도록 하는 문제가 발생할 경우 IoT Hub는 정해진 시간 제한 기간이 지나면 메시지를 다시 배달하도록 큐에 넣습니다. 이런 이유로 디바이스 앱의 메시지 처리 논리는 *idempotent* 이므로 같은 메시지를 여러 번 수신해도 동일한 결과가 생성됩니다.

클라우드-디바이스 메시지 수명 주기의 세부 정보를 포함하여 IoT Hub에서 클라우드-디바이스 메시지를 처리하는 방법에 대한 자세한 내용은 [IoT Hub에서 클라우드-디바이스 메시지 보내기](iot-hub-devguide-messages-c2d.md)를 참조하세요.

> [!NOTE]
> MQTT 또는 AMQP 대신 HTTPS를 전송으로 사용하는 경우 **DeviceClient** 인스턴스는 IoT Hub의 메시지를 자주(최소 25분 간격) 확인합니다. MQTT, AMQP, HTTPS 지원 간 차이점에 대한 자세한 내용은 [클라우드-디바이스 통신 지침](iot-hub-devguide-c2d-guidance.md) 및 [통신 프로토콜 선택](iot-hub-devguide-protocols.md)을 참조하세요.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)에서 만든 IoT Hub를 통해 클라우드-디바이스 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-디바이스 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service** 라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이트된 디바이스 앱으로 보내는 Java 콘솔 응용 프로그램을 만듭니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) 빠른 시작에서 추가한 디바이스의 디바이스 ID가 필요합니다. 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열도 필요합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 **send-c2d-messages** 라는 Maven 프로젝트를 만듭니다. 이 명령은 긴 단일 명령입니다.

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 send-c2d-messages 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 send-c2d-messages 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 의존성을 추가하면 IoT Hub 서비스와 통신하기 위해 애플리케이션에서 **iothub-java-service-client** 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)을 사용하여 **iot-service-client** 의 최신 버전을 확인할 수 있습니다.

4. pom.xml 파일을 저장하고 닫습니다.

5. 텍스트 편집기를 사용하여 send-c2d-messages\src\main\java\com\mycompany\app\App.java 파일을 엽니다.

6. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. **App** 클래스에 다음 클래스 수준 변수를 추가하고 **{yourhubconnectionstring}** 및 **{yourdeviceid}** 를 앞에서 기록해 둔 값으로 바꿉니다.

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. **main** 메서드를 다음 코드로 바꿉니다. 이 코드는 IoT hub에 연결하고 디바이스에 메시지를 보낸 다음 디바이스가 메시지를 수신하고 처리했다는 승인을 기다립니다.

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.

9. Maven을 사용하여 **simulated-device** 앱을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석 데이터 전송을 시작하고 사용자의 허브에서 보낸 클라우드-장치 메시지를 수신 대기합니다.

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-java-java-c2d/receivec2d.png)

2. 명령 프롬프트의 send-c2d-messages 폴더에서 다음 명령을 실행하여 클라우드-디바이스 메시지를 보내고 피드백 승인을 대기합니다.

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![명령을 실행하여 클라우드-디바이스 메시지 보내기](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.