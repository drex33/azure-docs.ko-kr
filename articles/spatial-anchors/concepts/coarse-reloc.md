---
title: 광역 위치 재결정
description: 광역 위치 재결정을 사용하는 경우와 방법에 대해 알아봅니다. 광역 위치 재결정을 사용하면 가까운 앵커를 찾을 수 있습니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 51c7be1a2ce16b4725bc5aa8e294f8b91a71fa0a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722661"
---
# <a name="coarse-relocalization"></a>광역 위치 재결정

광역 위치 재결정은 다음과 같은 질문에 대한 대략적인 대답을 제공하여 대규모 위치 결정를 가능하게 해 주는 기능입니다. 
- *내 디바이스는 어디에 있나요?* 
- *관찰해야 하는 콘텐츠는 무엇인가요?* 
 
응답은 정확하지 않습니다. 다음 형식으로 되어 있습니다. *다음 앵커에 가깝습니다. 이 중 하나를 찾아보세요*.

광역 위치 재결정은 나중에 빠른 쿼리에 사용되는 다양한 디바이스 센서 판독값을 통해 앵커에 태그를 지정하는 방식으로 작동합니다. 옥외 시나리오에서 센서 데이터는 일반적으로 디바이스의 GPS(위성 항법 시스템) 위치입니다. 실내에서와 같이 GPS를 사용할 수 없거나 신뢰할 수 없는 경우 센서 데이터는 범위 내 Wi-Fi 액세스 지점과 Bluetooth 비콘으로 구성됩니다. 수집된 센서 데이터는 디바이스에 가까운 앵커를 신속하게 확인하기 위해 Azure Spatial Anchors에서 사용되는 공간 인덱스를 유지 관리하는 데 기여합니다.

## <a name="when-to-use-coarse-relocalization"></a>광역 위치 재결정을 사용하는 경우

테니스 경기장보다 큰 공간에서 35개 이상의 공간 앵커를 처리하려는 경우 광역 위치 재결정 공간 인덱싱이 유용할 수 있습니다.

광역 위치 재결정으로 설정된 앵커의 빠른 조회는 지리적으로 분산된 수백만 개의 앵커와 같이 세계적인 규모의 컬렉션에서 지원되는 애플리케이션 개발을 간소화하도록 설계되었습니다. 공간 인덱싱의 복잡성은 모두 숨겨져 있으므로 애플리케이션 로직에 집중할 수 있습니다. 모든 어려운 작업은 Azure Spatial Anchors에 의해 백그라운드에서 수행됩니다.

## <a name="using-coarse-relocalization"></a>광역 위치 재결정 사용

광역 위치 재결정을 사용하여 Azure Spatial Anchors를 만들고 쿼리하는 일반적인 워크플로는 다음과 같습니다.
1.  센서 지문 공급자를 만들고 구성하여 원하는 센서 데이터를 수집합니다.
2.  Azure Spatial Anchors 세션을 시작하고 앵커를 만듭니다. 센서 지문 인식을 사용하도록 설정되어 있으므로 앵커는 광역 위치 재결정에 의해 공간적으로 인덱싱됩니다.
3.  Spatial Anchors 세션에서 전용 검색 조건을 통한 광역 이치 재결정을 사용하여 주변 앵커를 쿼리합니다.

다음 자습서 중 하나를 참조하여 애플리케이션에서 광역 위치 재결정을 설정할 수 있습니다.
* [Unity의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-unity.md)
* [Objective-C의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-objc.md)
* [Swift의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-swift.md)
* [Java의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-java.md)
* [C++/NDK의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [C++/WinRT의 광역 위치 재결정](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>센서 및 플랫폼

### <a name="platform-availability"></a>사용 가능한 플랫폼

다음 유형의 센서 데이터를 앵커 서비스로 보낼 수 있습니다.

* GPS 위치: 위도, 경도, 고도
* 범위 내 Wi-Fi 액세스 지점의 신호 강도
* 범위 내 Bluetooth 비콘의 신호 강도

다음 표에는 지원되는 플랫폼에서 센서 데이터의 가용성이 요약되어 있으며 알고 있어야 하는 정보를 제공합니다.

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | 아니요<sup>1</sup>  | 예<sup>2</sup> | 예<sup>3</sup> |
| **Wi-Fi**        | 예<sup>4</sup> | 예<sup>5</sup> | No  |
| **BLE 비콘** | 예<sup>6</sup> | 예<sup>6</sup> | 예<sup>6</sup>|


<sup>1</sup> 외부 GPS 디바이스를 HoloLens와 연결할 수 있습니다. 외부 GPS 추적기에서 HoloLens 사용하는 경우 [UpdatedSensorFingerprintRequired](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updatedsensorfingerprintrequired) 이벤트를 처리하여 [GeoLocation](/dotnet/api/microsoft.azure.spatialanchors.geolocation) 판독값을 제출합니다.<br/>
<sup>2</sup> [LocationManager][3] API를 통해 지원됩니다(GPS 및 NETWORK 모두).<br/>
<sup>3</sup> [CLLocationManager][4] API를 통해 지원됩니다.<br/>
<sup>4</sup> 약 3초마다 한 번 검색의 속도로 지원됩니다. <br/>
<sup>5</sup> API 수준 28부터 Wi-Fi 스캔은 2분마다 4번의 호출로 제한됩니다. Android 10부터 **개발자 설정** 메뉴에서 이 제한을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [Android 설명서][5]를 참조하세요.<br/>
<sup>6</sup> [Eddystone][1] 및 [ibeacon][2]으로 제한됩니다.

### <a name="which-sensor-to-enable"></a>사용할 센서

센서 선택은 개발 중인 애플리케이션 및 플랫폼에 따라 달라집니다.
아래 다이어그램은 위치 결정 시나리오에 따라 사용할 수 있는 센서의 조합을 결정하기 위한 시작점을 제공합니다.

![다양한 시나리오에 사용할 수 있는 센서를 보여 주는 다이어그램](media/coarse-relocalization-enabling-sensors.png)

다음 섹션에서는 각 센서 유형의 이점 및 제한 사항에 대한 자세한 정보를 제공합니다.

### <a name="gps"></a>GPS

GPS는 실외 시나리오를 위해 준비된 옵션입니다.
애플리케이션에서 GPS를 사용하는 경우 하드웨어에서 제공하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 저주파수(1Hz 미만)
* 불안정/잡음 있음(평균, 7m 표준 편차)

일반적으로 디바이스 OS 및 Spatial Anchors는 이러한 문제를 완화하기 위해 원시 GPS 신호의 일부 필터링 및 추정 작업을 수행합니다. 이러한 추가 처리를 위해서는 수렴 시간이 필요하므로 최상의 결과를 위해 다음을 수행해야 합니다.

* 애플리케이션에서 가능한 한 빨리 센서 지문 공급자를 하나 만듭니다.
* 센서 지문 공급자를 여러 세션 간에 활성 상태로 유지합니다.
* 센서 지문 공급자를 여러 세션 간에 공유합니다.

소비자 등급 GPS 디바이스는 일반적으로 정확하지 않습니다. [Zandenbergen과 Barbeau(2011)][6]의 연구에서는 GPS(A-GPS)를 지원하는 휴대폰의 중간 정확도를 약 7미터로 보고합니다. 무시하기에는 큰 값입니다. 이러한 측정 오류를 고려하여 서비스는 앵커를 GPS 공간의 확률 분포로 처리합니다. 따라서 앵커는 알려지지 않은 진정한 GPS 위치를 포함할 가능성(95% 이상)이 높은 공간 영역입니다.

GPS를 사용하여 쿼리하는 경우에도 동일한 추론을 적용합니다. 디바이스는 알려지지 않은 진정한 GPS 위치 주변의 다른 공간 신뢰 지역으로 표시됩니다. 주변 앵커를 검색하면 여기에 설명된 대로 디바이스 신뢰도 영역에 *충분히 가까운* 신뢰 지역이 있는 앵커를 찾습니다.

![GPS를 사용하여 앵커 후보를 찾는 방법을 보여 주는 다이어그램](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

HoloLens 및 Android에서 Wi-Fi 신호 강도는 실내 광역 위치 재결정을 사용하도록 설정하는 좋은 방법일 수 있습니다.
이러한 혜택은 추가 설정이 필요 없는 Wi-Fi 액세스 지점(예: 사무실 공간 및 쇼핑몰)을 잠재적으로 즉시 활용할 수 있다는 것입니다.

> [!NOTE]
> iOS는 Wi-Fi 신호 강도를 읽기 위한 API를 제공하지 않으므로 Wi-Fi를 통해 사용하도록 설정된 광역 위치 재결정에 사용할 수 없습니다.

애플리케이션에서 Wi-Fi를 사용하는 경우 하드웨어에서 제공하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 저주파수(0.1Hz 미만)
* 잠재적으로 OS 수준에서 제한됨
* 불안정/잡음 있음(평균, 3-dBm 표준 편차)

Spatial Anchors는 이러한 문제를 완화하기 위해 세션 중에 Wi-Fi 신호 강도의 필터링된 맵을 빌드합니다. 최상의 결과를 위해 다음을 시도합니다.

* 첫 번째 앵커를 배치하기 전에 세션을 만듭니다.
* 세션을 최대한 오랫동안 활성 상태로 유지합니다. 즉, 한 세션에서 모든 앵커와 쿼리를 만듭니다.

### <a name="bluetooth-beacons"></a>Bluetooth 비콘
<a name="beaconsDetails"></a>

Bluetooth 비콘을 신중하게 배포하는 것은 GPS가 없거나 부정확한 대규모 광역 위치 재결정 시나리오에 적합한 방법입니다. 세 플랫폼 모두에서 지원되는 유일한 실내 메서드이기도 합니다.

비콘은 일반적으로 UUID 및 MAC 주소를 포함하여 모든 항목을 구성할 수 있는 다양한 디바이스입니다. Azure Spatial Anchors에는 해당 UUID에 의해 고유하게 식별되는 비콘이 필요합니다. 이러한 고유성이 보장되지 않을 경우 잘못된 결과가 발생할 수 있습니다. 최상의 결과를 얻으려면

* 고유 UUID를 비콘에 할당합니다.
* 공간을 균일하게 처리하는 방식으로 비콘을 배포하여 공간의 모든 지점에서 세 개 이상의 비콘에 연결할 수 있습니다.
* 고유한 비콘 UUID 목록을 센서 지문 공급자에게 전달합니다.

Bluetooth의 신호와 같은 라디오 신호는 장애물의 영향을 받으며 다른 라디오 신호를 방해할 수 있습니다. 공간이 일관되게 적용되는지 추측하기 어려울 수 있습니다. 더 나은 고객 환경을 보장하기 위해 비콘 적용 범위를 수동으로 테스트하는 것이 좋습니다. 후보 디바이스를 사용하여 공간을 탐색하고 범위 내 Bluetooth를 표시하는 애플리케이션을 사용하여 테스트를 수행할 수 있습니다. 적용 범위를 테스트하는 동안 공간의 모든 전략적 위치에서 세 개 이상의 비콘에 연결할 수 있는지 확인합니다. 비콘이 너무 많으면 그 사이에 더 많은 간섭을 초래하고 광역 위치 재결정의 정확도를 향상시키지 못할 수 있습니다.

일반적으로 Bluetooth 비콘은 공간에 장애물이 없는 경우 80미터를 처리합니다.
따라서 장애가 크지 않은 공간의 경우 40미터마다 그리드 패턴으로 비콘을 배포할 수 있습니다.

배터리가 부족한 비콘은 결과에 영향을 미치므로 배터리량이나 충전 상태에 대해 주기적으로 배포를 모니터링해야 합니다.

Azure Spatial Anchors는 알려진 비콘 근접 UUID 목록에 있는 Bluetooth 비콘만 추적합니다. 하지만 허용 목록에 추가된 UUID를 포함하도록 프로그래밍된 악성 비콘은 서비스 품질에 부정적인 영향을 줄 수 있습니다. 따라서 비콘 배포를 제어할 수 있는 조정된 공간에서 최상의 결과를 얻을 수 있습니다.

### <a name="sensor-accuracy"></a>센서 정확도

앵커 생성 및 쿼리 중에 GPS 신호의 정확도는 반환되는 앵커 집합에 상당한 영향을 미칩니다. 반면, Wi-Fi/비콘을 기반으로 하는 쿼리는 쿼리와 공통적으로 하나 이상의 액세스 지점/비콘을 포함하는 모든 앵커를 고려합니다. 이러한 의미에서 Wi-Fi/비콘을 기반으로 하는 쿼리 결과는 주로 액세스 지점/비콘 및 환경적 장애물의 물리적 범위에 따라 결정됩니다.
아래 표에서는 각 센서 유형에 대해 예상되는 검색 공간을 예측합니다.

| 센서      | 검색-공간 반지름(근사치) | 세부 정보 |
|-------------|:-------:|---------|
| **GPS**         | 20m ~ 30m | 다른 요인 중에서 GPS 불확실성에 의해 결정됩니다. A-GPS를 사용한 휴대폰의 예상 중앙 GPS 정확도는 7미터로 보고됩니다. |
| **Wi-Fi**        | 50m ~ 100m | 무선 액세스 지점의 범위에 따라 결정됩니다. 주파수, 전송기 강도, 물리적 장애물, 간섭 등에 따라 달라집니다. |
| **BLE 비콘** |  70m | 비콘 범위에 따라 결정됩니다. 주파수, 전송 강도, 물리적 장애물, 간섭 등에 따라 달라집니다. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
