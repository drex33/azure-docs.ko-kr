---
title: 자습서 - Azure IoT Hub에 디바이스를 인증하는 X.509 인증서의 기능 테스트 | Microsoft Docs
description: 자습서 - X.509 인증서를 테스트하여 Azure IoT Hub에 인증
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: f3f59cb5346f6a2526ee4e8b0d433b577c9cfa57
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553803"
---
# <a name="tutorial-testing-certificate-authentication"></a>자습서: 인증서 인증 테스트

다음 C# 코드 예제를 사용하여 인증서가 디바이스를 IoT Hub에 인증할 수 있는지 테스트할 수 있습니다. 테스트 코드를 실행하기 전에 다음을 수행해야 합니다.

* 루트 CA 또는 하위 CA 인증서를 만듭니다.
* IoT Hub에 CA 인증서를 업로드합니다.
* CA 인증서를 소유하고 있음을 증명합니다.
* IoT Hub에 디바이스를 추가합니다.
* 디바이스와 동일한 디바이스 ID를 사용하여 디바이스 인증서를 만듭니다.

>[!IMPORTANT]
>인증 프로세스는 디바이스가 올바른 IoT Hub 이름과 연결되어 있는지 확인합니다. 

## <a name="code-example"></a>코드 예제

다음 코드 예제에서는 IoT 허브에 등록된 X.509 디바이스를 시뮬레이션하는 C# 애플리케이션을 만드는 방법을 보여줍니다. 이 예제에서는 시뮬레이션된 디바이스에서 허브로 온도 및 습도 값을 보냅니다. 이 자습서에서는 디바이스 애플리케이션만 만듭니다. 이 시뮬레이션된 디바이스에서 보낸 이벤트에 응답을 보낼 IoT Hub 서비스 애플리케이션을 만드는 것은 리더가 연습하도록 남겨두었습니다.

1. Visual Studio를 열고 **새 프로젝트 만들기** 를 선택한 다음, **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 선택합니다. **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 프로젝트 이름을 *SimulateX509Device* 로 지정한 다음, **만들기** 를 선택합니다.

   ![Visual Studio에서 X.509 디바이스 프로젝트 만들기](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 솔루션 탐색기에서 **SimulateX509Device** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리** 를 선택합니다.

1. **NuGet 패키지 관리자** 에서 **찾아보기** 를 선택하고 **Microsoft.Azure.Devices.Client** 를 검색한 후 선택합니다. **설치** 를 선택합니다.

   ![Visual Studio에서 디바이스 SDK NuGet 패키지 추가](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    이 단계에서는 Azure IoT 디바이스 SDK NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    다음 코드를 입력하고 실행합니다.

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```