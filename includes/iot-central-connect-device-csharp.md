---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 32afd7c4fff650e41d04a4ed809ad700cacc4e36
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516658"
---
[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/)가 포함된 개발 머신.

- 샘플 코드가 포함된 [C#용 Microsoft Azure IoT 샘플(.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리의 로컬 복사본입니다. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 C#용 Microsoft Azure IoT 샘플 리포지토리의 복사본에서 Visual Studio의 *azure-iot-samples-csharp-main\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* 솔루션 파일을 엽니다. **솔루션 탐색기** 에서 *PnpDeviceSamples > TemperatureController* 폴더를 확장하고 *Program.cs* 및 *TemperatureControllerSample.cs* 파일을 열어 이 샘플에 대한 코드를 확인합니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 환경에서 필요한 DPS 연결 정보를 검색합니다.

*Program.cs* 에서 `Main` 메서드는 `SetupDeviceClientAsync`를 호출하여 다음을 수행합니다.

* DPS를 사용하여 디바이스를 프로비저닝할 때 모델 ID `dtmi:com:example:TemperatureController;2`을 사용합니다. IoT Central은 모델 ID를 사용하여 이 디바이스에 대한 디바이스 템플릿을 식별하거나 생성합니다. 자세한 내용은 [디바이스 템플릿과 디바이스 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)을 참조하세요.
* IoT Central에 연결할 **DeviceClient** 인스턴스를 만듭니다.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
      // ...

    default:
      // ...
  }
  return deviceClient;
}
```

그런 다음, 기본 메서드는 **TemperatureControllerSample** 인스턴스를 만들고 `PerformOperationsAsync` 메서드를 호출하여 IoT Central과의 상호 작용을 처리합니다.

*TemperatureControllerSample.cs* 에서 `PerformOperationsAsync` 메서드는 다음을 수행합니다.

* 기본 구성 요소에서 **reboot** 명령에 대한 처리기를 설정합니다.
* 두 자동 온도 조절기 구성 요소에서 **getMaxMinReport** 명령에 대한 처리기를 설정합니다.
* 두 자동 온도 조절기 구성 요소에서 대상 온도 속성 업데이트를 수신하도록 처리기를 설정합니다.
* 초기 디바이스 정보 속성 업데이트를 보냅니다.
* 두 자동 온도 조절기 구성 요소에서 온도 원격 분석을 주기적으로 보냅니다.
* 기본 구성 요소에서 작업 세트 원격 분석을 주기적으로 보냅니다.
* 마지막 재부팅 이후 두 자동 온도 조절기 구성 요소에서 새로운 최대 온도에 도달할 때마다 최대 온도를 보냅니다.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);

  // For a component-level command, the command name is in the format "<component-name>*<command-name>".
  await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommand, Thermostat1, cancellationToken);
  await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommand, Thermostat2, cancellationToken);

  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(SetDesiredPropertyUpdateCallback, null, cancellationToken);
  _desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
  _desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

  await UpdateDeviceInformationAsync(cancellationToken);
  await SendDeviceSerialNumberAsync(cancellationToken);

  bool temperatureReset = true;
  _maxTemp[Thermostat1] = 0d;
  _maxTemp[Thermostat2] = 0d;

  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading for each "Thermostat" component.
      _temperature[Thermostat1] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
      _temperature[Thermostat2] = Math.Round(s_random.NextDouble() * 40.0 + 5.0, 1);
    }

    await SendTemperatureAsync(Thermostat1, cancellationToken);
    await SendTemperatureAsync(Thermostat2, cancellationToken);
    await SendDeviceMemoryAsync(cancellationToken);

    temperatureReset = _temperature[Thermostat1] == 0 && _temperature[Thermostat2] == 0;
    await Task.Delay(5 * 1000);
  }
}
```

`SendTemperatureAsync` 메서드는 디바이스가 구성 요소에서 IoT Central로 온도 원격 분석을 보내는 방법을 보여 줍니다. `SendTemperatureTelemetryAsync` 메서드는 `PnpConvention` 클래스를 사용하여 메시지를 빌드합니다.

```csharp
private async Task SendTemperatureAsync(string componentName, CancellationToken cancellationToken)
{
  await SendTemperatureTelemetryAsync(componentName, cancellationToken);

  double maxTemp = _temperatureReadingsDateTimeOffset[componentName].Values.Max<double>();
  if (maxTemp > _maxTemp[componentName])
  {
    _maxTemp[componentName] = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync(componentName, cancellationToken);
  }
}

private async Task SendTemperatureTelemetryAsync(string componentName, CancellationToken cancellationToken)
{
  const string telemetryName = "temperature";
  double currentTemperature = _temperature[componentName];
  using Message msg = PnpConvention.CreateMessage(telemetryName, currentTemperature, componentName);

  await _deviceClient.SendEventAsync(msg, cancellationToken);

  if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
  {
    _temperatureReadingsDateTimeOffset[componentName].TryAdd(DateTimeOffset.UtcNow, currentTemperature);
  }
  else
  {
    _temperatureReadingsDateTimeOffset.TryAdd(
      componentName,
      new Dictionary<DateTimeOffset, double>
      {
        { DateTimeOffset.UtcNow, currentTemperature },
      });
  }
}
```

`UpdateMaxTemperatureSinceLastRebootAsync` 메서드는 IoT Central에 `maxTempSinceLastReboot` 속성 업데이트를 보냅니다. 이 메서드는 `PnpConvention` 클래스를 사용하여 패치를 만듭니다.

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync(string componentName, CancellationToken cancellationToken)
{
  const string propertyName = "maxTempSinceLastReboot";
  double maxTemp = _maxTemp[componentName];
  TwinCollection reportedProperties = PnpConvention.CreateComponentPropertyPatch(componentName, propertyName, maxTemp);

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties, cancellationToken);
}
```

`TargetTemperatureUpdateCallbackAsync` 메서드는 IoT Central에서 쓰기 가능 대상 온도 속성 업데이트를 처리합니다. 이 메서드는 `PnpConvention` 클래스를 사용하여 속성 업데이트 메시지를 읽고 응답을 생성합니다.

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
  const string propertyName = "targetTemperature";
  string componentName = (string)userContext;

  bool targetTempUpdateReceived = PnpConvention.TryGetPropertyFromTwin(
    desiredProperties,
    propertyName,
    out double targetTemperature,
    componentName);
  if (!targetTempUpdateReceived)
  {
      return;
  }

  TwinCollection pendingReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      targetTemperature,
      (int)StatusCode.InProgress,
      desiredProperties.Version);

  await _deviceClient.UpdateReportedPropertiesAsync(pendingReportedProperty);

  // Update Temperature in 2 steps
  double step = (targetTemperature - _temperature[componentName]) / 2d;
  for (int i = 1; i <= 2; i++)
  {
      _temperature[componentName] = Math.Round(_temperature[componentName] + step, 1);
      await Task.Delay(6 * 1000);
  }

  TwinCollection completedReportedProperty = PnpConvention.CreateComponentWritablePropertyResponse(
      componentName,
      propertyName,
      _temperature[componentName],
      (int)StatusCode.Completed,
      desiredProperties.Version,
      "Successfully updated target temperature");

  await _deviceClient.UpdateReportedPropertiesAsync(completedReportedProperty);
}

```

`HandleMaxMinReportCommand` 메서드는 IoT Central에서 호출되는 구성 요소에 대한 명령을 처리합니다.

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
    try
    {
        string componentName = (string)userContext;
        DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
        var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

        if (_temperatureReadingsDateTimeOffset.ContainsKey(componentName))
        {

            Dictionary<DateTimeOffset, double> allReadings = _temperatureReadingsDateTimeOffset[componentName];
            Dictionary<DateTimeOffset, double> filteredReadings = allReadings.Where(i => i.Key > sinceInDateTimeOffset)
                .ToDictionary(i => i.Key, i => i.Value);

            if (filteredReadings != null && filteredReadings.Any())
            {
                var report = new
                {
                    maxTemp = filteredReadings.Values.Max<double>(),
                    minTemp = filteredReadings.Values.Min<double>(),
                    avgTemp = filteredReadings.Values.Average(),
                    startTime = filteredReadings.Keys.Min(),
                    endTime = filteredReadings.Keys.Max(),
                };

                byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
                return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
            }

            return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
        }

        return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
    }
    catch (JsonReaderException ex)
    {
        // ...
    }
}
```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>코드 실행

Visual Studio에서 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

1. **솔루션 탐색기** 에서 **PnpDeviceSamples > TemperatureController** 프로젝트 파일을 선택합니다.

1. **프로젝트 > TemperatureController 속성 > 디버그** 로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 이전에 기록해 둔 ID 범위 값입니다. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 이전에 기록해 둔 생성된 디바이스 키 값입니다. |

이제 Visual Studio에서 샘플을 실행하고 디버그할 수 있습니다.

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 샘플은 원격 분석 보내기를 시작합니다.

```output
[03/31/2021 14:43:17]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Press Control+C to quit the sample.
[03/31/2021 14:43:17]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set up the device client.
[03/31/2021 14:43:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Initializing via DPS
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for 'reboot' command.
[03/31/2021 14:43:27]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler for "getMaxMinReport" command.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Set handler to receive 'targetTemperature' updates.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component = 'deviceInformation', properties update is complete.
[03/31/2021 14:43:28]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - { "serialNumber": "SR-123456" } is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 34.2 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 34.2 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat2", { "temperature": 25.1 } in °C.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat2", { "maxTempSinceLastReboot": 25.1 } in °C is complete.
[03/31/2021 14:43:29]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - {"workingSet":31412} in KB.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

```output
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: Received - component="thermostat2", generating max, min and avg temperature report since 31/03/2021 06:00:00.
[03/31/2021 14:47:00]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Command: component="thermostat2", MaxMinReport since 31/03/2021 06:00:00: maxTemp=36.4, minTemp=36.4, avgTemp=36.4, startTime=31/03/2021 14:46:33, endTime=31/03/2021 14:46:55

...

[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Received - component="thermostat1", { "targetTemperature": 67°C }.
[03/31/2021 14:46:36]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is InProgress.
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Property: Update - component="thermostat1", {"targetTemperature": 67 } in °C is Completed
[03/31/2021 14:46:49]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
      Telemetry: Sent - component="thermostat1", { "temperature": 67 } in °C.
```
