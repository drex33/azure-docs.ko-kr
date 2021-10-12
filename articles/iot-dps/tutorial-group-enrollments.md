---
title: 자습서 - Java 및 등록 그룹을 사용하여 Azure IoT Hub에 시뮬레이션된 X.509 디바이스 프로비전
description: 이 자습서에서는 IoT Hub DPS(Device Provisioning Service)용 Java 디바이스, 서비스 SDK 및 등록 그룹을 사용하여 시뮬레이션된 X.509 디바이스를 만들고 프로비저닝합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: c15a2e525cfe614a3cced41a905f0c25762553c1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272607"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>자습서: IoT Hub Device Provisioning Service용 Java 디바이스, 서비스 SDK 및 등록 그룹을 사용하여 시뮬레이션된 X.509 디바이스 만들기 및 프로비전

다음 단계에서는 Windows OS를 실행 중인 개발 머신에서 X.509 디바이스를 시뮬레이션하고, 코드 샘플을 사용하여 등록 그룹을 통해 이 시뮬레이션된 디바이스를 Device Provisioning Service 및 IoT Hub와 연결하는 방법을 보여줍니다. 

계속 진행하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다.


## <a name="prerequisites"></a>필수 구성 요소

1. 컴퓨터에 [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure)이 설치되어 있는지 확인합니다.

1. [Maven](https://maven.apache.org/install.html)을 다운로드하고 설치합니다.

1. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash** 가 포함됩니다. 

1. 다음 [인증서 개요](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)를 사용하여 테스트 인증서를 만듭니다.

    > [!NOTE]
    > 이 단계를 수행하려면 원본에서 빌드 및 설치하거나 [이](https://sourceforge.net/projects/openssl/)와 같은 [타사](https://wiki.openssl.org/index.php/Binaries)에서 다운로드 및 설치할 수 있는 [OpenSSL](https://www.openssl.org/)이 필요합니다. 이미 _루트_, _중간_ 및 _디바이스_ 인증서를 만든 경우 이 단계를 건너뛸 수 있습니다.
    >

    1. 처음 두 단계를 실행하여 _루트_ 및 _중간_ 인증서를 만듭니다.

    1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, 프로비전 서비스를 엽니다.

        1. Device Provisioning Service 요약 블레이드에서 **인증서** 를 선택하고 위쪽의 **추가** 단추를 클릭합니다.

        1. **인증서 추가** 아래에서 다음 정보를 입력합니다.
            - 고유한 인증서 이름을 입력합니다.
            - 자신이 만든 **_RootCA.pem_** 파일을 선택합니다.
            - 완료되면 **저장** 단추를 클릭합니다.

           ![인증서 추가](./media/tutorial-group-enrollments/add-certificate.png)

        1. 새로 만든 인증서를 선택합니다.
            - **확인 코드 생성** 을 클릭합니다. 생성된 코드를 복사합니다.
            - 확인 단계를 실행합니다. _확인 코드_ 를 입력하거나 마우스 오른쪽 단추로 클릭하여 실행 중인 PowerShell 창에 붙여넣습니다.  **Enter** 키를 누릅니다.
            - Azure Portal에서 새로 만든 **_verifyCert4.pem_** 파일을 선택합니다. **확인** 을 클릭합니다.

              ![인증서 유효성 검사](./media/tutorial-group-enrollments/validate-certificate.png)

    1. 디바이스 인증서 및 정리 리소스를 만드는 단계를 실행하여 완료합니다.

       > [!NOTE]
       > 디바이스 인증서를 만들 때 디바이스 이름에 소문자 영숫자와 하이픈만 사용 해야 합니다.
       >


## <a name="create-a-device-enrollment-entry"></a>디바이스 등록 항목 만들기

1. 명령 프롬프트를 엽니다. Java SDK 코드 샘플용 GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. 다운로드한 소스 코드에서 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** 샘플 폴더로 이동합니다. 원하는 편집기에서 **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** 파일을 열고 다음 세부 정보를 추가합니다.

    1. 다음과 같이 포털에서 프로비전 서비스에 대한 `[Provisioning Connection String]`을 추가합니다.

        1. [Azure Portal](https://portal.azure.com)에서 프로비전 서비스로 이동합니다.

        1. **공유 액세스 정책** 을 열고, *EnrollmentWrite* 권한이 있는 정책을 선택합니다.

        1. **기본 키 연결 문자열** 을 복사합니다.

            ![포털에서 프로비전 연결 문자열 가져오기](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. **_ServiceEnrollmentGroupSample.java_** 샘플 코드 파일에서 `[Provisioning Connection String]`을 **기본 키 연결 문자열** 로 바꿉니다.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. 텍스트 편집기에서 중간 서명 인증서 파일을 엽니다. `PUBLIC_KEY_CERTIFICATE_STRING` 값을 중간 서명 인증서의 값으로 업데이트합니다.

        Bash 셸을 사용하여 디바이스 인증서를 생성한 경우 *./certs/azure-iot-test-only.intermediate.cert.pem* 에 중간 인증서 키가 포함됩니다. PowerShell을 사용하여 인증서가 생성된 경우 *./Intermediate1.pem* 이 중간 인증서 파일이 됩니다.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. [Azure Portal](https://portal.azure.com)에서 프로비전 서비스와 연결된 IoT Hub로 이동합니다. 허브에 대한 **개요** 탭을 열고 **호스트 이름** 을 복사합니다. *IOTHUB_HOST_NAME* 매개 변수에 이 **호스트 이름** 을 할당합니다.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. 샘플 코드를 조사합니다. X.509 디바이스에 대한 그룹 등록을 생성, 업데이트, 쿼리 및 삭제합니다. 포털에서 성공적으로 등록되었는지 확인하려면 _ServiceEnrollmentGroupSample.java_ 파일 끝에 다음 코드 줄을 일시적으로 주석 처리합니다.

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. _ServiceEnrollmentGroupSample.java_ 파일을 저장합니다.

1. 명령 창을 열고 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** 폴더로 이동합니다.

1. 다음 명령을 사용하여 샘플 코드를 빌드합니다.

    ```cmd\sh
    mvn install -DskipTests
    ```

1. 명령 창에서 다음 명령을 사용하여 샘플을 실행합니다.

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. 성공적으로 등록되었는지 출력 창을 확인합니다.

    ![성공적인 등록](./media/tutorial-group-enrollments/enrollment.png) 

1. Azure Portal에서 프로비전 서비스로 이동합니다. **등록 관리** 를 클릭합니다. **등록 그룹** 탭 아래에 자동 생성된 *그룹 이름* 이 있는 X.509 디바이스 그룹이 표시됩니다.

## <a name="simulate-the-device"></a>디바이스 시뮬레이션

1. Device Provisioning Service 요약 블레이드에서 **개요** 를 선택하고, _ID 범위_ 및 _프로비전 서비스 글로벌 엔드포인트_ 를 기록해 둡니다.

    ![서비스 정보](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. 명령 프롬프트를 엽니다. 샘플 프로젝트 폴더로 이동합니다.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. 앞에서 설명한 대로 _ID 범위_ 및 _프로비저닝 서비스 글로벌 엔드포인트_ 를 포함하도록 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`를 편집합니다.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. `leafPublicPem` 및 `leafPrivateKey` 변수를 공용 및 프라이빗 디바이스 인증서로 업데이트합니다.

    PowerShell을 사용하여 디바이스 인증서를 생성한 경우 mydevice* 파일에 디바이스에 대한 공개 키, 프라이빗 키 및 PFX가 포함됩니다.

    Bash 셸을 사용하여 디바이스 인증서를 생성한 경우 ./certs/new-device.cert.pem에 공개 키가 포함됩니다. 디바이스의 프라이빗 키는 ./private/new-device.key.pem 파일에 있게 됩니다.

    공개 키 파일을 열고 `leafPublicPem` 변수를 해당 값으로 업데이트합니다. _-----BEGIN PRIVATE KEY-----_ 부터 _-----END PRIVATE KEY-----_ 까지 텍스트를 복사합니다.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    프라이빗 키 파일을 열고 `leafPrivatePem` 변수를 해당 값으로 업데이트합니다. _-----BEGIN RSA PRIVATE KEY-----_ 부터 _-----END RSA PRIVATE KEY-----_ 까지 텍스트를 복사합니다.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. 중간 인증서에 대한 새 변수를 `leafPrivateKey` 바로 아래에 추가합니다. 이 새 변수 `intermediateKey`의 이름을 지정합니다. 중간 서명 인증서의 값으로 지정합니다.

    Bash 셸을 사용하여 디바이스 인증서를 생성한 경우 *./certs/azure-iot-test-only.intermediate.cert.pem* 에 중간 인증서 키가 포함됩니다. PowerShell을 사용하여 인증서가 생성된 경우 *./Intermediate1.pem* 이 중간 인증서 파일이 됩니다.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. `main` 함수에서 `securityProviderX509`를 초기화하기 전에 `intermediateKey`를 `signerCertificates` 컬렉션에 추가합니다.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. 변경 내용을 저장하고 샘플을 빌드합니다. 대상 폴더로 이동하고 만든 jar 파일을 실행합니다.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![성공적인 등록](./media/tutorial-group-enrollments/registration.png)

1. 포털에서 프로비전 서비스에 연결된 IoT 허브로 이동하여 **디바이스 탐색기** 블레이드를 엽니다. 시뮬레이션된 X.509 디바이스가 허브에 성공적으로 프로비전되면 디바이스 ID가 **Device Explorer** 블레이드에 표시되고 *상태* 가 **사용** 으로 표시됩니다. 샘플 디바이스 애플리케이션을 실행하기 전에 블레이드를 이미 열어 놓은 경우 화면 상단의 **새로 고침** 단추를 클릭해야 합니다. 

    ![디바이스가 IoT Hub에 등록됨](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>리소스 정리

디바이스 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 디바이스 클라이언트 샘플 출력 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 클릭한 다음, 사용자의 Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리** 블레이드를 연 다음, **개별 등록** 탭을 클릭합니다. 이 빠른 시작에서 등록한 디바이스의 *등록 ID* 를 선택하고, 위쪽의 **삭제** 단추를 클릭합니다. 
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 클릭한 다음 사용자의 IoT Hub를 선택합니다. 허브에 대한 **IoT 디바이스** 블레이드를 열고, 이 빠른 시작에서 등록한 디바이스의 *디바이스 ID* 를 선택한 다음, 위쪽의 **삭제** 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 시뮬레이션된 X.509 디바이스를 Windows 머신에 만들고, Azure IoT Hub Device Provisioning Service 및 등록 그룹을 사용하여 IoT Hub에 이 디바이스를 프로비전했습니다. X.509 디바이스에 대한 자세한 내용은 디바이스 개념으로 이어집니다. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service 개념](concepts-service.md)