---
title: 런타임 SDK 개요
description: Object Anchors 런타임 SDK에 관해 알아봅니다.
author: craigktreasure
manager: rgarcia
services: azure-object-anchors
ms.author: crtreasu
ms.date: 07/23/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: bc197af9be61a98be1cb22c7ff9fd0e97c31fcbe
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710744"
---
# <a name="runtime-sdk-overview"></a>런타임 SDK 개요

이 섹션에서는 Object Anchors 모델을 사용하여 개체를 검색하는 데 사용되는 Object Anchors 런타임 SDK를 대략적으로 설명합니다. 개체가 표현되는 방식과 다양한 구성 요소의 용도를 이해할 수 있습니다.

아래에 설명된 모든 유형은 **Microsoft.Azure.ObjectAnchors**, **Microsoft.Azure.ObjectAnchors.Diagnostics** 및 **Microsoft.Azure.ObjectAnchors.SpatialGraph** 네임스페이스 중 하나에서 찾을 수 있습니다.

## <a name="types"></a>형식

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel)은 물리적 개체의 기하 도형을 나타내며 검색 및 포즈 예측에 필요한 매개 변수를 인코딩합니다. [Object Anchors 서비스](../quickstarts/get-started-model-conversion.md)를 사용하여 만들어야 합니다. 그런 다음, 애플리케이션은 Object Anchors API를 사용하여 생성된 모델 파일을 로드하고 시각화를 위해 해당 모델에 포함된 메시를 쿼리할 수 있습니다.

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea)는 하나 이상의 개체를 찾을 공간을 지정합니다. 공간 그래프 노드 ID와 공간 그래프 노드 ID가 나타내는 좌표계의 공간 경계로 정의됩니다. Object Anchors 런타임 SDK는 **시야각**, **경계 상자**, **구**, **위치** 라는 네 가지 유형의 경계를 지원합니다.

### <a name="accountinformation"></a>AccountInformation

[AccountInformation](/dotnet/api/microsoft.azure.objectanchors.accountinformation)은 Azure Object Anchors 계정의 ID, 키 및 도메인을 저장합니다.

### <a name="objectanchorssession"></a>ObjectAnchorsSession

[ObjectAnchorsSession](/dotnet/api/microsoft.azure.objectanchors.objectanchorssession)은 실제 세계에서 개체를 감지하는 데 사용되는 ObjectObserver 인스턴스를 만드는 데 사용되는 Azure Object Anchor 세션을 나타냅니다.

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver)는 개체 모델을 로드하고, 해당 인스턴스를 검색하고, HoloLens 좌표계에서 각 인스턴스의 6-DoF 포즈를 보고합니다.

모든 개체 모델 또는 인스턴스는 **관찰자** 에서 생성되지만 해당 수명은 독립적입니다. 애플리케이션은 관찰자를 삭제하고 개체 모델 또는 인스턴스를 계속 사용할 수 있습니다.

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery)는 지정된 모델의 개체를 찾는 방법을 **개체 관찰자** 에게 알려 줍니다. 개체 모델에서 기본값을 검색할 수 있는 다음 튜닝 가능한 매개 변수를 제공합니다.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

[MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) 속성은 인스턴스를 검색된 것으로 간주할 값을 나타냅니다.

각 개체 후보의 경우 **관찰자** 는 변환된 개체 모델과 장면 간에 겹치는 표면의 비율을 계산한 다음, 적용 범위 비율이 지정된 임계값을 초과하는 경우에만 해당 후보를 애플리케이션에 보고합니다.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) 속성은 대상 개체가 접지 평면에 서 있어야 하는지 여부를 나타냅니다.

접지 평면은 검색 영역에서 가장 낮은 가로 바닥입니다. 가능한 개체 포즈에 대한 적절한 제약 조건을 제공합니다. 이 플래그를 켜면 **관찰자** 에게 제한된 공간에서 포즈를 예측하도록 안내가 제공되므로 정확도를 개선할 수 있습니다. 모델이 접지 평면에 서 있지 않아야 하는 경우 이 매개 변수는 무시됩니다.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) 속성은 개체 인스턴스 및 중력의 위쪽 방향 사이에 예상되는 최대 각도(도)를 나타냅니다.

이 매개 변수는 예상 포즈의 위쪽 방향에 대한 또 다른 제약 조건을 제공합니다. 예를 들어, 개체가 서 있는 경우 이 매개 변수는 0일 수 있습니다. Object Anchors는 모델과 다른 개체를 검색하지 않아야 합니다. 모델이 서 있는 경우 옆면이 아래쪽을 향하는 인스턴스를 검색하지 않습니다. 새 모델은 옆면이 아래쪽을 향하는 레이아웃에 사용됩니다.
관절에 동일한 규칙이 적용됩니다.

#### <a name="maxscalechange"></a>MaxScaleChange

[MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) 속성은 공간 매핑과 관련하여 최대 개체 배율 변경(0~1 이내)을 나타냅니다. 예상 배율은 원점에서 가운데 맞춤된 개체 꼭짓점과 축 정렬에 적용됩니다. 예상 배율은 CAD 모델과 물리적 표현 사이 실제 배율이 아니라 앱이 물리적 개체의 공간 매핑에 가까운 개체 모델을 렌더링할 수 있는 일부 값일 수 있습니다.

#### <a name="searchareas"></a>SearchAreas

[SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) 속성은 개체를 찾을 공간 경계 배열을 나타냅니다.

**관찰자** 는 쿼리에 지정된 모든 검색 영역의 공용 구조체 공간에서 개체를 찾습니다. 이 릴리스에서는 대기 시간을 줄이기 위해 신뢰도가 가장 높은 개체를 최대 하나만 반환합니다.

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance)는 지정된 모델의 인스턴스가 HoloLens 좌표계에 있을 수 있는 가상 위치를 나타냅니다. 각 인스턴스에는 예상 포즈가 얼마나 적절한지 나타내는 `SurfaceCoverage` 속성이 함께 제공됩니다.

인스턴스는 `ObjectObserver.DetectAsync` 메서드를 호출하여 만든 다음, 사용 중일 때 백그라운드에서 자동으로 업데이트됩니다. 애플리케이션은 특정 인스턴스에서 상태 변경 이벤트를 수신 대기하거나 추적 모드를 변경하여 업데이트를 일시 중지/다시 시작할 수 있습니다. 추적이 손실되면 인스턴스가 **관찰자** 에서 자동으로 제거됩니다.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession)은 진단을 기록하고 데이터를 보관에 씁니다.

진단 보관에는 장면 지점 클라우드, 관찰자 상태, 모델 정보가 포함됩니다. 이 정보는 가능한 런타임 문제를 식별하는 데 유용합니다. 자세한 내용은 [FAQ](../faq.md)을 참조하세요.

## <a name="runtime-sdk-usage-and-details"></a>런타임 SDK 사용 및 세부 정보

이 섹션에서는 런타임 SDK를 사용하는 방법의 기본 정보를 제공합니다. 샘플 애플리케이션을 탐색하여 Object Anchors가 전체적으로 사용되는 방식을 확인할 수 있는 충분한 컨텍스트를 제공합니다.

### <a name="initialization"></a>초기화

애플리케이션은 `ObjectObserver.IsSupported()` API를 호출하여 사용하기 전에 디바이스에서 Object Anchors가 지원되는지 확인해야 합니다. `ObjectObserver.IsSupported()`API가 `false`를 반환하는 경우 애플리케이션이 **spatialPerception** 기능을 사용하도록 설정했는지 확인하고\하거나 최신 HoloLens OS로 업그레이드합니다.

```cs
using Microsoft.Azure.ObjectAnchors;

if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
ObjectObserverAccessStatus status = await ObjectObserver.RequestAccessAsync();
if (status != ObjectObserverAccessStatus.Allowed)
{
    // Handle the error
}
```

다음으로, 애플리케이션은 개체 관찰자를 만들고 [Object Anchors 모델 변환 서비스](../quickstarts/get-started-model-conversion.md)에서 생성된 필요한 모델을 로드합니다.

```cs
using Microsoft.Azure.ObjectAnchors;

// Note that you need to provide the Id, Key and Domain for your Azure Object
// Anchors account.
Guid accountId = new Guid("[your account id]");
string accountKey = "[your account key]";
string accountDomain = "[your account domain]";

AccountInformation accountInformation = new AccountInformation(accountId, accountKey, accountDomain);
ObjectAnchorsSession session = new ObjectAnchorsSession(accountInformation);
ObjectObserver observer = session.CreateObjectObserver();

// Load a model into a byte array. The model could be a file, an embedded
// resource, or a network stream.
byte[] modelAsBytes;
ObjectModel model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed
// into a centered coordinate system for the ease of computing the object pose.
// The rigid transform can be retrieved through the `OriginToCenterTransform`
// property.
```

애플리케이션은 공간 내에서 해당 모델의 인스턴스를 검색하는 쿼리를 만듭니다.

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.SpatialGraph;
using Microsoft.Azure.ObjectAnchors.Unity;
using UnityEngine;

// Get the coordinate system.
SpatialGraphCoordinateSystem? coordinateSystem = null;

#if WINDOWS_UWP
SpatialCoordinateSystem worldOrigin = ObjectAnchorsWorldManager.WorldOrigin;
if (worldOrigin != null)
{
    coordinateSystem = await Task.Run(() => worldOrigin.TryToSpatialGraph());
}
#endif

if (!coordinateSystem.HasValue)
{
    Debug.LogError("no coordinate system?");
    return;
}

// Get the search area.
SpatialFieldOfView fieldOfView = new SpatialFieldOfView
{
    Position = Camera.main.transform.position.ToSystem(),
    Orientation = Camera.main.transform.rotation.ToSystem(),
    FarDistance = 4.0f, // Far distance in meters of object search frustum.
    HorizontalFieldOfViewInDegrees = 75.0f, // Horizontal field of view in
                                            // degrees of object search frustum.
    AspectRatio = 1.0f // Aspect ratio (horizontal / vertical) of object search
                       // frustum.
};

ObjectSearchArea searchArea = ObjectSearchArea.FromFieldOfView(coordinateSystem.Value, fieldOfView);

// Optionally change the parameters, otherwise use the default values embedded
// in the model.
ObjectQuery query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchArea);

// Detection could take a while, so we run it in a background thread.
IList<ObjectInstance> detectedObjects = await observer.DetectAsync(query);
```

기본적으로 검색된 각 인스턴스는 **관찰자** 에 의해 자동으로 추적됩니다. 이 인스턴스는 필요에 따라 추적 모드를 변경하거나 상태 변경 이벤트를 수신 대기하여 조작할 수 있습니다.

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition"    - Consumes less CPU cycles thus fast to
    //                                 update the state.
    // "HighLatencyAccuratePosition" - (Not yet implemented) Consumes more CPU
    //                                 cycles thus potentially taking longer
    //                                 time to update the state.
    // "Paused"                      - Stops to update the state until mode
    //                                 changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    // instance.Dispose();
}
```

상태 변경 이벤트에서 최신 상태를 쿼리하거나 추적이 손실된 경우 인스턴스를 삭제할 수 있습니다.

```cs
using Microsoft.Azure.ObjectAnchors;

var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    ObjectInstanceState? state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in
        // the same order to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance
        // and dispose it.
        sender.Dispose();
    }
});
```

또한 애플리케이션은 필요에 따라 오프라인 디버깅을 위해 하나 이상의 진단 세션을 기록할 수 있습니다.

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.Diagnostics;

string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
ObjectDiagnosticsSession diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance
// acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

마지막으로, 모든 개체를 삭제하여 리소스를 해제합니다.

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```