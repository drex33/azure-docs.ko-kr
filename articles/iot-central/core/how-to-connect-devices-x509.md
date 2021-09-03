---
title: Azure IoT Central 애플리케이션에서 X.509 인증서를 사용하여 디바이스 연결
description: IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 2a9cf8f74410f69d95b0f6944025220de2c94c08
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588150"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>IoT Central 애플리케이션에서 X.509 인증서를 사용하여 디바이스를 연결하는 방법

IoT Central은 SAS(공유 액세스 서명) 및 X.509 인증서를 모두 지원하여 디바이스와 애플리케이션 간의 통신을 보호합니다. [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서는 SAS를 사용합니다. 이 문서에서는 X.509 인증서를 사용하도록 코드 샘플을 수정하는 방법에 대해 알아봅니다. X.509 인증서는 프로덕션 환경에서 권장됩니다. 자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md)을 참조하세요.

이 가이드에서는 X.509 인증서를 사용하는 두 가지 방법 - 일반적으로 프로덕션 환경에서 사용되는 [그룹 등록](how-to-connect-devices-x509.md#use-group-enrollment) 및 테스트에 유용한 [개별 등록](how-to-connect-devices-x509.md#use-individual-enrollment)을 보여 줍니다. 이 문서에서는 인증서가 만료될 때 연결을 유지하기 위해 [디바이스 인증서를 롤링](#roll-x509-device-certificates)하는 방법도 설명합니다.

이 가이드는 C#, 자바, 자바스크립트 및 Python을 사용하는 [클라이언트 애플리케이션을 만들고 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md) 자습서에 표시된 샘플을 기반으로 합니다. C 프로그래밍 언어를 사용하는 예는 [등록 그룹을 사용하여 여러 X.509 디바이스 프로비저닝](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서를 완료합니다. 여기에는 선택한 프로그래밍 언어에 대한 필수 구성 요소 설치가 포함됩니다.

이 방법 가이드에서는 몇 가지 테스트 X.509 인증서를 생성합니다. 이러한 인증서를 생성하려면 다음이 필요합니다.

- [Node.js](https://nodejs.org/) 버전 6 이상이 설치된 개발 머신. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **node** 명령을 실행한다고 가정합니다. 하지만 여러 운영 체제에서 Node.js를 사용할 수 있습니다.
- 테스트 X.509 인증서를 생성하기 위한 스크립트가 포함된 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리의 로컬 복사본. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="use-group-enrollment"></a>그룹 등록 사용

프로덕션 환경에서 그룹 등록에 X.509 인증서를 사용합니다. 그룹 등록에서 루트 또는 중간 X.509 인증서를 IoT Central 애플리케이션에 추가합니다. 루트 또는 중간 인증서에서 파생된 리프 인증서가 있는 디바이스는 애플리케이션에 연결할 수 있습니다.

### <a name="generate-root-and-device-certificates"></a>루트 및 디바이스 인증서 생성

이 섹션에서는 X.509 인증서를 사용하여 IoT Central 등록 그룹의 인증서에서 파생된 인증서와 디바이스를 연결합니다.

> [!WARNING]
> X.509 인증서를 생성하는 이 방법은 테스트용으로만 사용할 수 있습니다. 프로덕션 환경의 경우 인증서를 생성하는 데 공식, 보안 메커니즘을 사용해야 합니다.

1. 다운로드한 Node.js용 Microsoft Azure IoT SDK에서 인증서 생성기 스크립트로 이동합니다. 필요한 패키지를 설치합니다.

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. 루트 인증서를 만든 다음, 스크립트를 실행하여 디바이스 인증서를 파생합니다.

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > 디바이스 ID는 문자, 숫자 및 `-` 문자를 포함할 수 있습니다.

이 명령은 다음 루트와 디바이스 인증서를 생성합니다.

| 파일 이름 | 내용 |
| -------- | -------- |
| mytestrootcert_cert.pem | 루트 X509 인증서의 공개 부분 |
| mytestrootcert_key.pem | 루트 X509 인증서에 대한 프라이빗 키 |
| mytestrootcert_fullchain.pem | 루트 X509 인증서에 대한 전체 키 집합 |
| sampleDevice01_cert.pem | 디바이스 X509 인증서의 공개 부분 |
| sampleDevice01_key.pem | 디바이스 X509 인증서에 대한 프라이빗 키 |
| sampleDevice01_fullchain.pem | 디바이스 X509 인증서에 대한 전체 키 집합 |

이러한 파일의 위치를 기록해 두세요. 나중에 필요합니다.

### <a name="create-a-group-enrollment"></a>그룹 등록 만들기

1. IoT Central 애플리케이션을 열고 왼쪽 창에서 **관리** 로 이동하여 **디바이스 연결** 을 선택합니다.

1. **+ 등록 그룹 만들기** 를 선택하고, 증명 형식 **인증서(X.509)** 를 사용하여 _MyX509Group_ 이라는 새 등록 그룹을 만듭니다.

1. 만든 등록 그룹을 열고 **기본 관리** 를 선택합니다.

1. 파일 옵션을 선택하여 이전에 생성한 _mytestrootcert_cert.pem_ 이라는 루트 인증서 파일을 업로드합니다.

    ![인증서 업로드](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 확인을 완료하려면 확인 코드를 생성하고, 복사한 다음, 이를 사용하여 명령 프롬프트에서 X.509 확인 인증서를 만듭니다.

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. **확인** 을 선택하여 서명된 확인 인증서 _verification_cert.pem_ 을 업로드하고 확인을 완료합니다.

    ![확인된 인증서](./media/how-to-connect-devices-x509/verified.png)

이제 이 기본 루트 인증서에서 파생된 X.509 인증서가 있는 디바이스를 연결할 수 있습니다.

등록 그룹을 저장한 후에는 ID 범위를 기록해 둡니다.

### <a name="run-sample-device-code&quot;></a>샘플 디바이스 코드 실행

:::zone pivot=&quot;programming-language-csharp&quot;

Windows를 사용하는 경우 샘플이 작동하려면 X.509 인증서가 Windows 인증서 저장소에 있어야 합니다. 저장소에 인증서를 추가하려면:

1. `openssl`을 사용하여 PEM 파일에서 PFX 파일을 만듭니다. 이러한 명령을 실행하면 암호를 만들라는 메시지가 표시됩니다. 암호를 기록해 두세요. 다음 단계에서 필요합니다.

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. Windows 탐색기에서 각 PFX 파일을 두 번 클릭합니다. **인증서 가져오기 마법사** 에서 저장소 위치로 **현재 사용자** 를 선택하고 이전 단계의 암호를 입력하면 마법사가 인증서 저장소를 자동으로 선택하도록 합니다. 마법사는 인증서를 현재 사용자의 프라이빗 저장소로 가져옵니다.

인증서를 사용하도록 샘플 코드를 수정하려면:

1. **IoTHubDeviceSamples** Visual Studio 솔루션에서 **TemperatureController** 프로젝트의 *Parameter.cs* 파일을 엽니다.

1. 클래스에 다음 두 매개 변수 정의를 추가합니다.

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    변경 내용을 저장합니다.

1. **IoTHubDeviceSamples** Visual Studio 솔루션에서 **TemperatureController** 프로젝트의 *Program.cs* 파일을 엽니다.

1. 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. 다음 메서드를 클래스에 추가합니다.

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. `SetupDeviceClientAsync` 메서드에서 `case "dps"`의 코드 블록을 다음 코드로 바꿉니다.

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. `ProvisionDeviceAsync` 메서드를 다음 코드로 바꿉니다.

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    변경 내용을 저장합니다.

1. 프로젝트에 다음 환경 변수를 추가합니다.

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD`: *sampleDevice01.pfx* 파일을 만들 때 사용한 암호입니다.

1. 애플리케이션을 빌드 및 실행합니다. 디바이스 프로비저닝을 성공적으로 확인합니다.

:::zone-end

:::zone pivot="programming-language-java"

1. 온도 컨트롤러 디바이스 샘플에 대한 *pom.xml* 파일 및 *src* 폴더가 포함된 _azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample_ 폴더로 이동합니다.

1. *pom.xml* 파일을 편집하여 `<dependencies>` 노드에 다음 종속성 구성을 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    변경 내용을 저장합니다.

1. 텍스트 편집기에서 *src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java* 파일을 엽니다.

1. `SecurityProviderSymmetricKey` 가져오기를 다음 가져오기로 바꿉니다.

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. 다음 가져오기를 추가합니다.

    ```java
    import java.nio.file.*;
    ```

1. `main` 메서드가 throw하는 예외 목록에 `SecurityProviderException`을 추가합니다.

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. `initializeAndProvisionDevice` 메서드를 다음 코드로 바꿉니다.

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    변경 내용을 저장합니다.

1. 셸 환경에서 다음 두 가지 환경 변수를 추가합니다. PEM 파일의 전체 경로를 제공하고 운영 체제에 올바른 경로 구분 기호를 사용하는지 확인합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서를 완료할 때 다른 필수 환경 변수를 설정합니다.

1. 애플리케이션을 빌드 및 실행합니다. 디바이스 프로비저닝을 성공적으로 확인합니다.

:::zone-end

:::zone pivot="programming-language-javascript"

1. **pnpTemperatureController.js** 애플리케이션이 포함된 _azure-iot-sdk-node/device/samples/pnp_ 폴더로 이동하고 다음 명령을 실행하여 X.509 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 텍스트 편집기에서 **pnpTemperatureController.js** 파일을 엽니다.

1. 다음 코드를 포함하도록 `require` 문을 편집합니다.

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. "DPS 연결 정보" 섹션에 다음 네 줄을 추가하여 `deviceCert` 변수를 초기화합니다.

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. 첫 번째 줄을 다음 코드로 바꿔 클라이언트를 만드는 `provisionDevice` 함수를 편집합니다.

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 동일한 함수에서 다음과 같이 `deviceConnectionString` 변수를 설정하는 줄을 수정합니다.

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. `main` 함수에서 `Client.fromConnectionString`을 호출하는 줄 뒤에 다음 줄을 추가합니다.

    ```javascript
    client.setOptions(deviceCert);
    ```

    변경 내용을 저장합니다.

1. 셸 환경에서 다음 두 가지 환경 변수를 추가합니다. PEM 파일의 전체 경로를 제공하고 운영 체제에 올바른 경로 구분 기호를 사용하는지 확인합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서를 완료할 때 다른 필수 환경 변수를 설정합니다.

1. 스크립트를 실행하고 디바이스가 성공적으로 프로비저닝되었는지 확인합니다.

    ```cmd/sh
    node simple_thermostat.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. _azure-iot-device/samples/pnp_ 폴더로 이동하고 텍스트 편집기에서 **temp_controller_with_thermostats.py** 파일을 엽니다.

1. 다음 `from` 문을 추가하여 X.509 기능을 가져옵니다.

    ```python
    from azure.iot.device import X509
    ```

1. 다음과 같이 `provision_device` 함수의 첫 번째 부분을 수정합니다.

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. `main` 함수에서 `symmetric_key` 변수를 설정하는 줄을 다음 코드로 바꿉니다.

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. `main` 함수에서 `provision_device` 함수에 대한 호출을 다음 코드로 바꿉니다.

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. `main` 함수에서 `IoTHubDeviceClient.create_from_symmetric_key` 함수에 대한 호출을 다음 코드로 바꿉니다.

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    변경 내용을 저장합니다.

1. 셸 환경에서 다음 두 가지 환경 변수를 추가합니다. PEM 파일의 전체 경로를 제공하고 운영 체제에 올바른 경로 구분 기호를 사용하는지 확인합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서를 완료할 때 다른 필수 환경 변수를 설정합니다.

1. 스크립트를 실행하고 디바이스가 성공적으로 프로비저닝되었는지 확인합니다.

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

IoT Central 애플리케이션의 디바이스 보기에 원격 분석이 나타나는지 확인합니다.

![IoT Central 애플리케이션에 도착하는 원격 분석을 보여 주는 스크린샷.](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>개별 등록 사용

개별 등록에 X.509 인증서를 사용하여 디바이스와 솔루션을 테스트합니다. 개별 등록에서는 IoT Central 애플리케이션에 루트 또는 중간 X.509 인증서가 없습니다. 디바이스는 자체 서명된 X.509 인증서를 사용하여 애플리케이션에 연결합니다.

### <a name="generate-self-signed-device-certificate"></a>자체 서명된 디바이스 인증서 생성

이 섹션에서는 자체 서명된 X.509 인증서를 사용하여 개별 등록을 위해 디바이스를 연결합니다. 이는 단일 디바이스를 등록하는 데 사용됩니다. 자체 서명된 인증서는 테스트 전용입니다.

> [!WARNING]
> X.509 인증서를 생성하는 이 방법은 테스트용으로만 사용할 수 있습니다. 프로덕션 환경의 경우 인증서를 생성하는 데 공식, 보안 메커니즘을 사용해야 합니다.

다음 명령을 실행하여 자체 서명된 X.509 디바이스 인증서를 만듭니다.

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> 디바이스 ID는 문자, 숫자 및 `-` 문자를 포함할 수 있습니다.

### <a name="create-individual-enrollment"></a>개별 등록 만들기

1. Azure IoT Central 애플리케이션에서 **디바이스** 를 선택하고, 자동 온도 조절기 디바이스 템플릿에서 _mytestselfcertprimary_ 로 **디바이스 ID** 를 사용하여 새 디바이스를 만듭니다. **ID 범위** 값을 기록해 둡니다. 나중에 이를 사용합니다.

1. 만든 디바이스를 열고 **연결** 을 선택합니다.

1. **연결 방법** 으로 **개별 등록** 을 선택하고 메커니즘으로 **인증서(X.509)** 를 선택합니다.

    ![개별 등록](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 기본에서 파일 옵션을 선택하고 이전에 생성한 _mytestselfcertprimary_cert.pem_ 이라는 인증서 파일을 업로드합니다.

1. 보조 인증서에 대한 파일 옵션을 선택하고 _mytestselfcertsecondary_cert.pem_ 이라는 인증서 파일을 업로드합니다. 그런 다음, **저장** 을 선택합니다.

    ![개별 등록 인증서 업로드](./media/how-to-connect-devices-x509/individual-enrollment.png)

이제 디바이스가 X.509 인증서로 프로비전됩니다.

### <a name="run-a-sample-individual-enrollment-device"></a>샘플 개별 등록 디바이스 실행

1. **simple_thermostat.js** 애플리케이션을 포함하는 _mytestselfcertprimary_key.pem_ 및 _mytestselfcertprimary_cert.pem_ 파일을 _azure-iot-sdk-node/device/samples/pnp_ 폴더에 복사합니다. [디바이스 연결(JavaScript) 자습서](./tutorial-connect-device.md)를 완료할 때 이 애플리케이션을 사용했습니다.

1. 위의 샘플에서 사용한 환경 변수를 다음과 같이 수정합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 스크립트를 실행하고 디바이스가 성공적으로 프로비저닝되었는지 확인합니다.

    ```cmd/sh
    node environmentalSensor.js
    ```

    원격 분석이 디바이스 보기에 나타나는지 확인할 수도 있습니다.

    ![원격 분석 개별 등록](./media/how-to-connect-devices-x509/telemetry-primary.png)

_mytestselfcertsecondary_ 인증서에 대해서도 위의 단계를 반복할 수 있습니다.

## <a name="connect-an-iot-edge-device"></a>IoT Edge 디바이스 연결

이 섹션에서는 그룹 등록을 사용하여 IoT Edge 디바이스를 연결한다고 가정합니다. 이전 섹션의 단계에 따라 다음을 수행합니다.

- [루트 및 디바이스 인증서 생성](#generate-root-and-device-certificates)
- [그룹 등록 만들기](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

X.509 디바이스 인증서를 사용하여 IoT Edge 디바이스를 IoT Central에 연결하려면:

- 디바이스 인증서 및 키 파일을 IoT Edge 디바이스에 복사합니다. 이전 그룹 등록 예에서 이러한 파일의 이름은 **sampleDevice01_key.pem** 및 **sampleDevice01_cert.pem** 입니다.
- IoT Edge 디바이스에서 **/etc/iotedge/config.yaml** 구성 파일의 `provisioning` 섹션을 다음과 같이 편집합니다.

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > `registration_id`에 대한 값을 추가할 필요가 없습니다. IoT Edge는 X.509 인증서의 **CN** 값을 사용할 수 있습니다.

- 다음 명령을 실행하여 IoT Edge 런타임을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

자세히 알아보려면 [x.509 인증서를 사용하여 IoT Edge 디바이스 만들기 및 프로비저닝](../../iot-edge/how-to-auto-provision-x509-certs.md)을 참조하세요.

## <a name="connect-an-iot-edge-leaf-device"></a>IoT Edge 리프 디바이스 연결

IoT Edge는 X.509 인증서를 사용하여 리프 디바이스와 게이트웨이 역할을 하는 IoT Edge 디바이스 간의 연결을 보호합니다. 이 시나리오 구성에 대한 자세한 내용은 [Azure IoT Edge 게이트웨이에 다운스트리밍 디바이스 연결](../../iot-edge/how-to-connect-downstream-device.md)을 참조하세요.

## <a name="roll-x509-device-certificates"></a>X.509 디바이스 인증서 롤링

IoT Central 애플리케이션의 수명 주기 동안 x.509 인증서를 롤링해야 합니다. 예를 들면 다음과 같습니다.

- 보안 위반이 있는 경우 인증서를 롤링하는 것이 시스템을 보호할 수 있는 보안 모범 사례입니다.
- x.509 인증서에는 만료 날짜가 있습니다. 인증서의 배포 빈도는 솔루션의 보안 요구 사항에 따라 달라집니다. 중요도 높은 데이터가 포함된 솔루션을 사용하는 고객은 매일 인증서를 배포하기도 하며 어떤 고객은 2년마다 인증서를 배포할 수도 있습니다.

중단 없는 연결을 위해 IoT Central을 사용하면 기본 및 보조 X.509 인증서를 구성할 수 있습니다. 기본 인증서와 보조 인증서의 만료 날짜가 다른 경우 디바이스가 다른 인증서와 계속 연결되는 동안 만료된 인증서를 롤링할 수 있습니다.

자세한 내용은 [위반 방법론 가정](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)을 참조하세요.

이 섹션에서는 IoT Central에서 인증서를 롤링하는 방법을 설명합니다. IoT Central에서 인증서를 롤링할 때 새 디바이스 인증서도 디바이스에 복사해야 합니다.

### <a name="obtain-new-x509-certificates"></a>새 X.509 인증서 가져오기

인증서 공급자로부터 새 X.509 인증서를 얻습니다. OpenSSL과 같은 도구를 사용하여 사용자 고유의 X.509 인증서를 만들 수 있습니다. 이 접근 방식은 X.509 인증서를 테스트하는 데 유용하지만 보안을 거의 보장하지 않습니다. 자체 CA 공급자 역할을 준비하는 것이 아니라면 이 방식은 테스트용으로만 사용합니다.

### <a name="enrollment-groups-and-security-breaches"></a>등록 그룹 및 보안 위반

보안 위반에 대응하여 그룹 등록을 업데이트하려면 현재 인증서를 즉시 업데이트하는 다음 접근 방식을 사용해야 합니다. 기본 인증서와 보조 인증서가 모두 손상된 경우 이러한 단계를 완료합니다.

1. 왼쪽 창에서 **관리** 로 이동하여 **디바이스 연결** 을 선택합니다.

2. **등록 그룹** 을 선택하고 목록에서 그룹 이름을 선택합니다.

3. 인증서 업데이트의 경우 **기본 관리** 또는 **보조 관리** 를 선택합니다.

4. 등록 그룹에서 루트 X.509 인증서를 추가하고 확인합니다.

### <a name="individual-enrollments-and-security-breaches"></a>개별 등록 및 보안 위반

보안 위반에 대응하여 인증서를 롤링하는 경우 다음 방식을 사용하여 현재 인증서를 즉시 업데이트합니다. 기본 인증서와 보조 인증서가 모두 손상된 경우 이러한 단계를 완료합니다.

1. **디바이스** 를 선택하고 디바이스를 선택합니다.

1. **연결** 을 선택하고 **개별 등록** 으로 연결 메서드를 선택합니다.

1. 메커니즘으로 **인증서(X.509)** 를 선택합니다.

1. 인증서 업데이트의 경우 폴더 아이콘을 선택하여 등록 항목에 대해 업로드할 새 인증서를 선택합니다. **저장** 을 선택합니다.

### <a name="enrollment-groups-and-certificate-expiration"></a>등록 그룹 및 인증서 만료

인증서 만료를 처리하려면 다음 방식을 사용하여 현재 인증서를 즉시 업데이트합니다.

1. 왼쪽 창에서 **관리** 로 이동하여 **디바이스 연결** 을 선택합니다.

2. **등록 그룹** 을 선택하고 목록에서 그룹 이름을 선택합니다.

3. 인증서 업데이트의 경우 **기본 관리** 를 선택합니다.

4. 등록 그룹에서 루트 X.509 인증서를 추가하고 확인합니다.

5. 나중에 보조 인증서가 만료되면 다시 돌아가서 보조 인증서를 업데이트합니다.

### <a name="individual-enrollments-and-certificate-expiration"></a>개별 등록 및 인증서 만료

인증서 만료를 처리하기 위해 인증서를 배포하는 경우 다음과 같이 보조 인증서 구성을 사용하여 프로비전을 수행하는 디바이스에서 가동 중지 시간을 줄입니다.

보조 인증서가 만료 시점이 가까워져 롤링이 필요한 경우 기본 구성을 사용하도록 전환할 수 있습니다. 기본 인증서와 보조 인증서를 이런 식으로 전환하면 프로비전을 수행하는 디바이스에서 가동 중지 시간이 줄어듭니다.

1. **디바이스** 를 선택하고 디바이스를 선택합니다.

2. **연결** 을 선택하고 **개별 등록** 으로 연결 메서드를 선택합니다.

3. 메커니즘으로 **인증서(X.509)** 를 선택합니다.

4. 보조 인증서 업데이트의 경우 폴더 아이콘을 선택하여 등록 항목에 대해 업로드할 새 인증서를 선택합니다. **저장** 을 선택합니다.

5. 나중에 기본 인증서가 만료되면 다시 돌아가서 기본 인증서를 업데이트합니다.

## <a name="next-steps"></a>다음 단계

이제 X.509 인증서를 사용하여 디바이스를 연결하는 방법을 배웠으므로 제안된 다음 단계는 [Azure CLI를 사용하여 디바이스 연결을 모니터링](howto-monitor-devices-azure-cli.md)하는 방법을 배우는 것입니다.
