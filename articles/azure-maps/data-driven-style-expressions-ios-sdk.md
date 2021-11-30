---
title: IOS 맵의 데이터 기반 스타일 식
titleSuffix: Microsoft Azure Maps
description: 데이터 기반 스타일 식에 대해 알아봅니다. Azure Maps iOS SDK에서 이러한 식을 사용 하 여 맵의 스타일을 조정 하는 방법을 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 3fc0c4e5524a84b0b11a03feea3a127c17f0f154
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219061"
---
# <a name="data-driven-style-expressions-in-the-ios-sdk-preview"></a>IOS SDK의 데이터 기반 스타일 식 (미리 보기)

계층에서는 조건자와 식을 사용 하 여 표시할 항목과 형식을 지정 하는 방법을 결정 합니다. _조건자_ 는 `NSPredicate` 핵심 데이터의 결과를 필터링 하는 동일한 클래스 또는 `NSArray` 목적-C에서의 항목으로 표현 됩니다.
조건자는 클래스에 의해 표현 되는 _식을_ 기반으로 `NSExpression` 합니다.

이 문서에서는이 SDK에서 지 원하는 조건자와 식 구문의 특정 하위 집합에 대해 설명 합니다. 조건자와 식에 대 한 일반적인 소개는 Apple 개발자 설명서의 _[조건자 프로그래밍 가이드](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Predicates/AdditionalChapters/Introduction.html)_ 를 참조 하세요.

## <a name="using-predicates-to-filter-vector-data"></a>조건자를 사용 하 여 벡터 데이터 필터링

대부분의 레이어 클래스는 `Feature` 기능 속성에 따라 표시 하거나 숨길 수 있는 개체를 표시 합니다. 옵션을 사용 `filter` 하 여 원본 계층에서 정의한 조건을 충족 하는 기능만 포함 합니다.

### <a name="operators"></a>연산자

지원 되는 비교 연산자는 다음과 같습니다.

`NSPredicateOperatorType`                     | 서식 문자열 구문
----------------------------------------------|---------------------
`NSEqualToPredicateOperatorType`              | `key = value`<br />`key == value`
`NSGreaterThanOrEqualToPredicateOperatorType` | `key >= value`<br />`key => value`
`NSLessThanOrEqualToPredicateOperatorType`    | `key <= value`<br />`key =< value`
`NSGreaterThanPredicateOperatorType`          | `key > value`
`NSLessThanPredicateOperatorType`             | `key < value`
`NSNotEqualToPredicateOperatorType`           | `key != value`<br />`key <> value`
`NSBetweenPredicateOperatorType`              | `key BETWEEN { 32, 212 }`

기능에 특정 속성이 있는지 여부를 테스트 하려면 특성을 `NULL` 또는와 비교 합니다 `NIL` . 이니셜라이저를 사용 하 여 만든 조건자 `NSPredicate(value:)` 도 지원 됩니다. 문자열 연산자 및 사용자 지정 연산자는 지원 되지 않습니다.

지원 되는 복합 연산자는 다음과 같습니다.

`NSCompoundPredicateType` | 서식 문자열 구문
--------------------------|---------------------
`NSAndPredicateType`      | `predicate1 AND predicate2`<br />`predicate1 && predicate2`
`NSOrPredicateType`       | `predicate1 OR predicate2`<br />`predicate1 \|\| predicate2`
`NSNotPredicateType`      | `NOT predicate`<br />`!predicate`

지원 되는 집계 연산자는 다음과 같습니다.

`NSPredicateOperatorType`         | 서식 문자열 구문
----------------------------------|---------------------
`NSInPredicateOperatorType`       | `key IN { 'iOS', 'macOS', 'tvOS', 'watchOS' }`
`NSContainsPredicateOperatorType` | `{ 'iOS', 'macOS', 'tvOS', 'watchOS' } CONTAINS key`

및 연산자를 사용 하 여 `IN` `CONTAINS` 값이 컬렉션에 표시 되는지 여부, 문자열이 큰 문자열의 부분 문자열 인지 여부 또는 계산 된 기능 ( `SELF` )이 지정 된 내에 있는지 여부를 테스트할 수 있습니다 `Feature` .

다음과 같은 비교 연산자와 한정자 조합이 지원 됩니다.

`NSComparisonPredicateModifier` | `NSPredicateOperatorType`           | 서식 문자열 구문
--------------------------------|-------------------------------------|---------------------
`NSAllPredicateModifier`        | `NSNotEqualToPredicateOperatorType` | `ALL haystack != needle`
`NSAnyPredicateModifier`        | `NSEqualToPredicateOperatorType`    | `ANY haystack = needle`<br />`SOME haystack = needle`

조건자에 사용 되는 비교 연산자와 집계 연산자에는 다음과 같은 비교 조건자 옵션이 지원 됩니다.

`NSComparisonPredicateOptions`          | 서식 문자열 구문
----------------------------------------|---------------------
`NSCaseInsensitivePredicateOption`      | `'QUEBEC' =[c] 'Quebec'`
`NSDiacriticInsensitivePredicateOption` | `'Québec' =[d] 'Quebec'`

기타 비교 조건자 옵션은 지원 되지 않습니다 (예를 들어, `l` 로캘 민감도의 경우) 및 `n` (정규화의 경우). 비교는 대/소문자 또는 분음 부호를 구분 하지 않는 한 로캘에 따라 구분 됩니다. 비교 조건자 옵션은 및과 같은 비교 한정자와 함께 사용할 수 없습니다 `ALL` `ANY` .

### <a name="operands"></a>피연산자

조건자의 피연산자는 [변수](#variables), [키 경로](#key-paths)또는 [식 내부](#using-expressions-to-configure-layer-options)에 나타날 수 있는 거의 모든 것이 될 수 있습니다.

자동 형식 캐스팅이 수행 되지 않습니다. 따라서 해당 속성의 값이 조건자에 지정 된 값과 동일한 형식인 경우에만 기능이 조건자와 일치 합니다. 연산자를 사용 `CAST()` 하 여 키 경로 또는 변수를 일치 하는 형식으로 변환 합니다.

* 값을 숫자로 캐스팅 하려면를 사용 `CAST(key, 'NSNumber')` 합니다.
* 값을 문자열로 캐스팅 하려면를 사용 `CAST(key, 'NSString')` 합니다.
* 값을 색으로 캐스팅 하려면를 사용 `CAST(key, 'UIColor')` 합니다.
* 개체를 배열로 캐스팅 하려면를 `UIColor` 사용 `CAST(noindex(color), 'NSArray')` 합니다.

조건자 형식 문자열 구문에 대 한 자세한 내용은 Apple 개발자 설명서의 _[조건자 프로그래밍 가이드](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Predicates/AdditionalChapters/Introduction.html)_ 의 "조건자 형식 문자열 구문" 장을 참조 하세요.

### <a name="operands-examples"></a>피연산자 예

거품형 및 기호 계층은 기본적으로 데이터 원본에 있는 모든 기 하 도형의 좌표를 렌더링 합니다. 이 동작은 다각형의 꼭짓점 또는 선을 강조 표시할 수 있습니다. `filter`계층의 옵션은 조건자 내에서를 사용 하 여 렌더링 하는 기능의 기 하 도형 형식을 제한 하는 데 사용할 수 있습니다 `NSExpression.geometryTypeAZMVariable` . 다음 예에서는 `Point` 기능만 렌더링되도록 거품형 계층을 제한합니다.

```swift
let layer = BubbleLayer(source: source, options: [
    .filter(
        from: NSPredicate(format: "%@ == \"Point\"", NSExpression.geometryTypeAZMVariable)
    )
])
```

다음 예에서는 `Point` 및 `Polygon` 기능을 모두 렌더링할 수 있습니다.

```swift
let layer = BubbleLayer(source: source, options: [
    .filter(
        from: NSCompoundPredicate(orPredicateWithSubpredicates: [
            NSPredicate(format: "%@ == \"Point\"", NSExpression.geometryTypeAZMVariable),
            NSPredicate(format: "%@ == \"Polygon\"", NSExpression.geometryTypeAZMVariable)
        ])
    )
])
```

## <a name="using-expressions-to-configure-layer-options"></a>식을 사용 하 여 계층 옵션 구성

식에는 다양 한 형식의 하위 식이 포함 될 수 있습니다. 지원 되는 각 식 유형은 아래에 설명 되어 있습니다.

문서의이 섹션에 나오는 일부 예제에서는 다음 기능을 사용 하 여 이러한 식을 사용할 수 있는 여러 가지 방법을 보여 줍니다.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

다음 코드는 앱에서 이 GeoJSON 기능을 수동으로 만드는 방법을 보여 줍니다.

```swift
// Create a point feature.
let feature = Feature(Point(CLLocationCoordinate2D(latitude: 45, longitude: -100)))

// Add properties to the feature.
feature.addProperty("id", value:  123)
feature.addProperty("entityType", value: "restaurant")
feature.addProperty("revenue", value:  12345)
feature.addProperty("subTitle", value: "Building 40")
feature.addProperty("temperature", value:  64)
feature.addProperty("title", value: "Cafeteria")
feature.addProperty("zoneColor", value: "purple")
feature.addProperty("abcArray", value: ["a", "b", "c"])
feature.addProperty("array2d", value: [["a", "b"], ["x", "y"]])
feature.addProperty("_style", value: ["fillColor": "red"])
```

다음 코드는 JSON 개체의 문자열화된 버전을 앱의 GeoJSON 기능으로 역직렬화하는 방법을 보여 줍니다.

```swift
let featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

let feature = Feature.fromJson(featureString)
```

### <a name="json-based"></a>JSON 기반

Azure Maps 웹 SDK는 JSON 배열을 사용하여 표현되는 데이터 기반 스타일 식도 지원합니다. 이러한 동일한 식은 iOS SDK의 네이티브 클래스를 사용 하 여 다시 만들 수 있습니다 `NSExpression` . 또는 이러한 JSON 기반 식을 ios 앱 (예: 응답에 연결)에 공유 하 고 메서드에 전달할 수 있습니다 `NSExpression(azmJSONObject:)` . 예를 들어, 다음 JSON 식을 사용합니다.

```js
var exp = ['get','title'];
```

위의 식의 swift 버전은 `["get", "title"]` 이며 다음과 같이 IOS SDK로 읽을 수 있습니다.

```swift
let jsonResponse = ["get", "title"]
let exp = NSExpression(azmJSONObject: jsonResponse)
```

이 접근 방식을 사용하면 Azure Maps를 사용하는 모바일 앱과 웹앱 간에 스타일 식을 쉽게 다시 사용할 수 있습니다.

이 비디오에서는 Azure Maps의 데이터 기반 스타일 개요를 제공합니다.

>[Azure Maps를 사용 하 여 사물 인터넷 표시 Data-Driven 스타일 지정](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny)

### <a name="constant-values"></a>상수 값

상수 값은 다음 형식 중 하나일 수 있습니다.

* `UIColor`
* `String`
* `NSNumber.boolValue`
* `NSNumber.doubleValue`
* `[Float]`
* `[String]`
* `NSValue.cgVectorValue`
* `NSValue.uiEdgeInsetsValue`

#### <a name="constant-values-example"></a>상수 값 예

```swift
NSExpression(forConstantValue: UIColor.red)
NSExpression(forConstantValue: "constant")
NSExpression(forConstantValue: true)
NSExpression(forConstantValue: 20)
NSExpression(forConstantValue: [20, 21, 22])
NSExpression(forConstantValue: ["constant1", "constant2"])
NSExpression(forConstantValue: NSValue(cgVector: CGVector(dx: 2, dy: -4)))
NSExpression(forConstantValue: NSValue(uiEdgeInsets: UIEdgeInsets(top: 8, left: 0, bottom: 8, right: 0)))
```

### <a name="key-paths"></a>키 경로

키 경로 식은 `Feature` 표시를 위해 계산 되는 개체의 속성을 참조 합니다. 예를 들어 다각형의 사전에 `Feature.properties` 키가 포함 된 경우 `floorCount` 키 경로는 `floorCount` `floorCount` 특정 다각형을 평가할 때 속성의 값을 참조 합니다.

일부 문자는 서식 문자열에서 키 경로의 일부로 직접 사용할 수 없습니다. 예를 들어, 기능의 속성이로 지정 된 경우의 `ISO 3166-1:2006` 식 서식 문자열 `lowercase(ISO 3166-1:2006)` 또는의 조건자 서식 문자열은 예외를 `ISO 3166-1:2006 == 'US-OH'` 발생 시킵니다. 대신 `%K` 자리 표시자 나 이니셜라이저를 사용 합니다 `NSExpression(forKeyPath:)` .

```swift
NSPredicate(format: "%K == 'US-OH'", "ISO 3166-1:2006")
NSExpression(
   forFunction: "lowercase:",
   arguments: [NSExpression(forKeyPath: "ISO 3166-1:2006")]
)
```

### <a name="functions"></a>Functions

[ `NSExpression(forFunction:arguments:)` 메서드에](https://developer.apple.com/documentation/foundation/nsexpression/1413747-init)의해 [미리 정의 된 함수의](https://developer.apple.com/documentation/foundation/nsexpression/1413747-init#discussion) 경우 계층 옵션에서 다음 하위 집합이 지원 됩니다.

이니셜라이저 매개 변수 | 서식 문자열 구문
----------------------|---------------------
`average:`            | `average({1, 2, 2, 3, 4, 7, 9})`
`sum:`                | `sum({1, 2, 2, 3, 4, 7, 9})`
`count:`              | `count({1, 2, 2, 3, 4, 7, 9})`
`min:`                | `min({1, 2, 2, 3, 4, 7, 9})`
`max:`                | `max({1, 2, 2, 3, 4, 7, 9})`
`add:to:`             | `1 + 2`
`from:subtract:`      | `2 - 1`
`multiply:by:`        | `1 * 2`
`divide:by:`          | `1 / 2`
`modulus:by:`         | `modulus:by:(1, 2)`
`sqrt:`               | `sqrt(2)`
`log:`                | `log(10)`
`ln:`                 | `ln(2)`
`raise:toPower:`      | `2 ** 2`
`exp:`                | `exp(0)`
`ceiling:`            | `ceiling(0.99999)`
`abs:`                | `abs(-1)`
`trunc:`              | `trunc(6378.1370)`
`floor:`              | `floor(-0.99999)`
`uppercase:`          | `uppercase('Elysian Fields')`
`lowercase:`          | `lowercase('DOWNTOWN')`
`noindex:`            | `noindex(0 + 2 + c)`
`length:`             | `length('Wapakoneta')`
`castObject:toType:`  | `CAST(ele, 'NSString')`<br>`CAST(ele, 'NSNumber')`

다음 미리 정의 된 함수는 지원 **되지 않습니다** .

이니셜라이저 매개 변수 | 서식 문자열 구문
----------------------|---------------------
`median:`             | `median({1, 2, 2, 3, 4, 7, 9})`
`mode:`               | `mode({1, 2, 2, 3, 4, 7, 9})`
`stddev:`             | `stddev({1, 2, 2, 3, 4, 7, 9})`
`random`              | `random()`
`randomn:`            | `randomn(10)`
`now`                 | `now()`
`bitwiseAnd:with:`    | `bitwiseAnd:with:(5, 3)`
`bitwiseOr:with:`     | `bitwiseOr:with:(5, 3)`
`bitwiseXor:with:`    | `bitwiseXor:with:(5, 3)`
`leftshift:by:`       | `leftshift:by:(23, 1)`
`rightshift:by:`      | `rightshift:by:(23, 1)`
`onesComplement:`     | `onesComplement(255)`
`distanceToLocation:fromLocation:` | `distanceToLocation:fromLocation:(there, here)`

### <a name="conditionals"></a>조건부

조건부는 기본 제공 메서드 및 연산자를 통해 지원 됩니다 `NSExpression(forAZMConditional:trueExpression:falseExpression:)` `TERNARY()` . 여러 사례 ("else-if")를 표시 해야 하는 경우 조건부 내에 조건부를 중첩 하거나 [일치 식을](#match-expression)사용할 수 있습니다.

#### <a name="conditionals-example"></a>조건 예

다음 예에서는로 계산 되는 조건자를 찾은 `true` 다음 해당 true 식을 반환 하기 전까지 다른 조건자를 단계별로 진행 합니다. 조건자가로 계산 되지 않으면 `true` 마지막 false 식이 반환 됩니다.

```swift
let layer = BubbleLayer(
    source: source,
    options: [
        .bubbleColor(
            from: NSExpression(
                // Check to see whether a feature has a zoneColor property
                forAZMConditional: NSPredicate(format: "zoneColor != NIL"),
                // If it has a zoneColor property, use its value as a color.
                trueExpression: NSExpression(forKeyPath: "zoneColor"),
                // if not, do another check
                falseExpression: NSExpression(
                    // Check to see whether it has a temperature property with a value greater than or equal to 100.
                    forConditional: NSPredicate(format: "temperature != nil && temperature >= 100"),
                    // If it has, use red color.
                    trueExpression: NSExpression(forConstantValue: UIColor.red),
                    // If not, use green color.
                    falseExpression: NSExpression(forConstantValue: UIColor.green)
                )
            )
        )
    ]
)
```

### <a name="aggregates"></a>집계

집계 식은 식의 배열을 포함할 수 있습니다. 경우에 따라 집계 식에 배열을 래핑하는 대신 배열 자체를 사용할 수 있습니다.

### <a name="variables"></a>variables

다음 변수는 계층 옵션과 함께 사용 하기 위해이 SDK에 의해 정의 됩니다.

#### <a name="feature-identifier"></a>기능 식별자

기능 ID입니다. 이 변수는 속성에 해당 `NSExpression.featureIdentifierAZMVariable` 합니다.

_서식 문자열 구문_: `$featureIdentifier`

#### <a name="feature-properties"></a>기능 속성

기능의 속성 개체입니다. 이 변수는 속성에 해당 `NSExpression.featurePropertiesAZMVariable` 합니다.

_서식 문자열 구문_: `$featureAttributes`

#### <a name="feature-accumulated"></a>누적 된 기능

지금까지 누적 된 클러스터 속성의 값입니다.
클러스터형 `DataSource` 원본의 옵션 `clusterProperties`에서만 사용할 수 있습니다.
이 변수는 속성에 해당 `NSExpression.featureAccumulatedAZMVariable` 합니다.

_서식 문자열 구문_: `$featureAccumulated`

#### <a name="geometry-type"></a>기하 도형 유형

기능이 나타내는 기 하 도형 형식입니다. 기능의 형식은 다음 문자열 중 하나입니다.

* `Point` point 기능의 경우 클래스에 해당 `Point` 합니다.
* `MultiPoint` 요소 컬렉션 기능의 경우 클래스에 해당 `PointCollection` 합니다.
* `LineString` 다중선 기능의 경우 클래스에 해당 `Polyline` 합니다.
* `MultiLineString` 다중 폴리라인 기능의 경우 클래스에 해당 `MultiPolyline` 합니다.
* `Polygon` 다각형 기능의 경우 클래스에 해당 `Polygon` 합니다.
* `MultiPolygon` 여러 다각형 기능의 경우 클래스에 해당 `MultiPolygon` 합니다.
* `GeometryCollection` 기 하 도형 컬렉션 기능의 경우 클래스에 해당 `GeometryCollection` 합니다.

이 변수는 속성에 해당 `NSExpression.geometryTypeAZMVariable` 합니다.

_서식 문자열 구문_: `$geometryType`

##### <a name="variables-example"></a>변수 예

[피연산자 예](#operands-examples)를 참조 하세요.

#### <a name="zoom"></a>Zoom

현재 확대/축소 수준입니다.
계층의 스타일 옵션에서이 변수는 최상위 보간 또는 단계 식의 대상 으로만 표시 될 수 있습니다.
이 변수는 속성에 해당 `NSExpression.zoomLevelAZMVariable` 합니다.

_서식 문자열 구문_: `$zoomLevel`

##### <a name="zoom-example"></a>확대/축소 예제

기본적으로 열 지도 계층에 렌더링 되는 데이터 요소의 반지름에는 모든 확대/축소 수준에 대 한 고정 점 반지름이 있습니다. 지도의 확대/축소 수준이 변경되면 데이터가 함께 집계되고 열 지도 계층이 다르게 보입니다. 각 데이터 포인트가 지도의 동일한 실제 영역을 포함하도록 각 확대/축소 수준에 대한 반경의 크기를 조정하는 데 `zoom` 식을 사용할 수 있습니다. 열 지도 계층을 더 정적이고 일관되게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준 만큼 세로 및 가로로 두 배의 점이 있습니다. 각 확대/축소 수준마다 두 배가 되도록 반경 크기를 조정하면 모든 확대/축소 수준에서 일관되게 보이는 열 지도를 만듭니다. 식에 식을 사용 하 여이를 수행할 수 있습니다 `zoom` `base 2 exponential interpolation` . 이때 최소 확대/축소 수준에 대해 점 반지름이 설정 되 고 아래와 같이 계산 된 최대 확대/축소 수준에 대해 배율이 조정 된 반지름이 사용 됩니다 `pow(2, maxZoom - minZoom) * radius` .

```swift
let layer = HeatMapLayer(
   source: source,
   options: [
      .heatmapRadius(
         from: NSExpression(
            forAZMInterpolating: .zoomLevelAZMVariable,
            curveType: .exponential,
            parameters: NSExpression(forConstantValue: 2),
            stops: NSExpression(forConstantValue: [
               // For zoom level 1 set the radius to 2 points.
               1: 2,

               // Between zoom level 1 and 19, exponentially scale the radius from 2 points to 2 * 2^(maxZoom - minZoom) points.
               19: pow(2, 19 - 1) * 2
            ])
         )
      )
   ]
)
```

#### <a name="heatmap-density"></a>열 지도 밀도

열 지도 계층에 있는 화면의 [커널 밀도 추정](https://en.wikipedia.org/wiki/Kernel_density_estimation) 즉, 특정 픽셀을 기준으로 하는 데이터 요소 수를 상대적으로 측정 합니다. 이 변수는 0에서 1 사이의 숫자로 계산 됩니다.
`interpolation` 또는 `step` 식과 함께 사용되어 열 지도의 색을 지정하는 데 사용되는 색 그라데이션을 정의합니다.
`heatmapColor`열 지도 계층의 옵션에만 사용할 수 있습니다.
이 변수는 속성에 해당 `NSExpression.heatmapDensityAZMVariable` 합니다.

_서식 문자열 구문_: `$heatmapDensity`

> [!TIP]
> 보간 식에서 인덱스 0의 색상 또는 단계 색의 기본 색은 데이터가 없는 영역의 색을 정의합니다. 인덱스 0에 있는 색은 배경색을 정의하는 데 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

##### <a name="heatmap-density-example"></a>열 지도 밀도 예

이 예에서는 선형 보간 식을 사용하여 열 지도를 렌더링하기 위한 부드러운 색 그라데이션을 만듭니다.

```swift
let layer = HeatMapLayer(
   source: source,
   options: [
      .heatmapColor(
         from: NSExpression(
            forAZMInterpolating: .heatmapDensityAZMVariable,
            curveType: .linear,
            parameters: nil,
            stops: NSExpression(forConstantValue: [
               0: UIColor.magenta.withAlphaComponent(0),
               0.01: UIColor.magenta,
               0.5: UIColor(red: 251 / 255, green: 0, blue: 251 / 255, alpha: 1),
               1: UIColor(red: 0, green: 195 / 255, blue: 1, alpha: 1)
            ])
         )
      )
   ]
)
```

부드러운 그라데이션을 사용 하 여 열 지도를 색상화 하는 것 외에도 단계 식을 사용 하 여 범위 집합 내에서 색을 지정할 수 있습니다. 열 지도를 색으로 변환 하는 단계 식을 사용 하면 밀도를 윤곽선 또는 방사형 스타일 맵과 유사한 범위로 시각적으로 나눌 있습니다.

```swift
let layer = HeatMapLayer(
   source: source,
   options: [
      .heatmapColor(
         from: NSExpression(
            forAZMStepping: .heatmapDensityAZMVariable,
            from: NSExpression(forConstantValue: UIColor.clear),
            stops: NSExpression(forConstantValue: [
               0.01: UIColor(red: 0, green: 0, blue: 128 / 255, alpha: 1),
               0.25: UIColor.cyan,
               0.5: UIColor.green,
               0.75: UIColor.yellow,
               1: UIColor.red
            ])
         )
      )
   ]
)
```

자세한 내용은 [열 지도 계층 추가](add-heat-map-layer-ios.md) 설명서를 참조하세요.

#### <a name="line-progress"></a>줄 진행

줄의 지정 된 지점에서 선을 따라 상대적인 거리를 나타내는 숫자입니다.
이 변수는 줄의 시작 부분에서 0으로 계산 되며 줄의 끝에서 1로 계산 됩니다.
이 `LineLayerOption.strokeGradient` 옵션은 선 계층의 옵션에만 사용할 수 있습니다.
에 해당 하는 것은 `NSExpression.lineProgressAZMVariable` 속성입니다.

_서식 문자열 구문_: `$lineProgress`

> [!NOTE]
> 선 계층의 `strokeGradient` 옵션을 사용하려면 데이터 원본의 `lineMetrics` 옵션을 `true`로 설정해야 합니다.

##### <a name="line-progress-example"></a>줄 진행률 예

이 예에서는 `NSExpression.lineProgressAZMVariable` 식을 사용하여 선의 스트로크에 색 그라데이션을 적용합니다.

```swift
let layer = LineLayer(
   source: source,
   options: [
      .strokeGradient(
         from: NSExpression(
            forAZMInterpolating: NSExpression.lineProgressAZMVariable,
            curveType: .linear,
            parameters: nil,
            stops: NSExpression(forConstantValue: [
               0: UIColor.blue,
               0.1: UIColor(red: 0.25, green: 0.41, blue: 1, alpha: 1), // Royal Blue
               0.3: UIColor.cyan,
               0.5: UIColor(red: 0, green: 1, blue: 0, alpha: 1), // Lime
               0.7: UIColor.yellow,
               1: UIColor.red
            ])
         )
      )
   ]
)
```

[라이브 예 참조](add-line-layer-map-ios.md)

### <a name="azuremaps-specific-methods"></a>AzureMaps 관련 메서드

다음 섹션에서는 클래스를 확장 하는 AzureMaps 메서드를 구체적으로 보여 줍니다. `NSExpression`

#### <a name="match-expression"></a>Match 식

일치 식은 논리와 같이 switch 문을 제공 하는 조건식의 유형입니다. 입력은 문자열이나 숫자를 반환하는 `NSExpression(forKeyPath: "entityType")` 등의 모든 식일 수 있습니다. 일치 하는 식은 단일 문자열이 나 숫자 또는 모든 문자열의 배열 또는 모든 숫자 및 값을 식으로 평가 하는 식으로 키를 포함 해야 하는 사전입니다. 입력 식 형식이 키 형식과 일치 하지 않으면 기본 대체 값이 반환 됩니다.

##### <a name="match-expression-examples"></a>일치 식 예

다음 예에서는 `entityType` `Point` 거품형 계층에서 기능의 속성을 살펴보고 일치 항목을 검색 합니다. 일치 항목이 발견되면 지정된 값이 반환되거나 대체 값이 반환됩니다.

```swift
let layer = BubbleLayer(
    source: source,
    options: [
        .bubbleColor(
            from: NSExpression(
                // Get the input value to match.
                forAZMMatchingKey: NSExpression(forKeyPath: "entityType"),
                // List the keys to match and the value to return for each match.
                in: [
                    // If value is "restaurant" return red color.
                    NSExpression(forConstantValue: "restaurant"): NSExpression(forConstantValue: UIColor.red),
                    // If value is "park" return green color.
                    NSExpression(forConstantValue: "park"): NSExpression(forConstantValue: UIColor.green)
                ],
                // Specify a default value to return if no match is found.
                default: NSExpression(forConstantValue: UIColor.black)
            )
        )
    ]
)
```

다음 예에서는 문자열 배열로 계산 되는 식을 사용 하 여 모두 동일한 값을 반환 해야 하는 레이블 집합을 지정 합니다. 이 방법은 각 레이블을 개별적으로 나열하는 것보다 훨씬 효율적입니다. 이 경우 `entityType` 속성이 `"restaurant"` 또는 이면 `"grocery_store"` 빨강 색이 반환 됩니다.

```swift
let layer = BubbleLayer(
    source: source,
    options: [
        .bubbleColor(
            from: NSExpression(
                // Get the input value to match.
                forAZMMatchingKey: NSExpression(forKeyPath: "entityType"),
                // List the keys to match and the value to return for each match.
                in: [
                    // If value is "restaurant" or "grocery_store" return red color.
                    NSExpression(forConstantValue: ["restaurant", "grocery_store"]): NSExpression(forConstantValue: UIColor.red),
                    // If value is "park" return green color.
                    NSExpression(forConstantValue: "park"): NSExpression(forConstantValue: UIColor.green)
                ],
                // Specify a default value to return if no match is found.
                default: NSExpression(forConstantValue: UIColor.black)
            )
        )
    ]
)
```

#### <a name="coalesce-expression"></a>Coalesce 식

병합 식에서는 첫 번째 null이 아닌 값을 가져와 값을 반환할 때까지 식 집합을 단계별로 진행 합니다.

##### <a name="coalesce-expression-example"></a>병합 식 예제

다음 예에서는 병합 식을 사용 하 여 `textField` 기호 계층의 옵션을 설정 합니다. `title` 속성이 기능에서 누락되었거나 `nil`로 설정된 경우에는 식이 `subTitle` 속성을 찾으려고 시도하고, 누락되었거나 `nil`인 경우에는 빈 문자열로 대체합니다.

```swift
let layer = SymbolLayer(
    source: source,
    options: [
        .textField(
            from: NSExpression(forAZMFunctionCoalesce: [
                // Try getting the title property.
                NSExpression(forKeyPath: "title"),

                // If there is no title, try getting the subTitle.
                NSExpression(forKeyPath: "subTitle"),

                // Default to an empty string.
                NSExpression(forConstantValue: "")
            ])
        )
    ]
)
```

#### <a name="join-expression"></a>조인 식

여러 문자열을 결합 합니다. 각 값은 문자열 또는 숫자 여야 합니다.

##### <a name="join-expression-example"></a>Join 식 예제

다음 예에서는 `temperature` point 기능의 속성과를 조인 합니다 `"°F"` .

```swift
let layer = SymbolLayer(
    source: source,
    options: [
        .textField(
            from: NSExpression(forAZMFunctionJoin: [
                NSExpression(forKeyPath: "temperature"),
                NSExpression(forConstantValue: "°F")
            ])
        ),

        // Some additional style options.
        .textOffset(CGVector(dx: 0, dy: -1.5)),
        .textSize(12),
        .textColor(.white)
    ]
)
```

위의 식은 `"64°F"` 아래 이미지에 표시 된 것 처럼 맨 위에 겹쳐진 텍스트가 있는 지도에서 핀을 렌더링 합니다.

:::image type="content" source="./media/ios-sdk/data-driven-style-expressions-ios/join-expression.png" alt-text="Join 식 예제":::

#### <a name="interpolation-and-step-expressions"></a>보간 및 단계 식

보간 및 단계 식은 보간된 곡선이 나 step 함수를 따라 값을 계산 하는 데 사용할 수 있습니다. 이러한 식은 숫자를 입력으로 반환 하는 식 (예:)을 사용 `NSExpression(forKeyPath: "temperature")` 합니다. 입력 값은 입력 및 출력 값 쌍에 대해 평가되어 보간된 곡선이나 단계 함수에 가장 적합한 값을 결정합니다. 출력 값은 "stops"라고 합니다. 각 중지의 입력 값은 숫자여야 하며 오름차순이어야 합니다. 출력 값은 숫자, 숫자 배열 또는 색 이어야 합니다.

##### <a name="interpolation-expression"></a>보간 식

보간 식을 사용 하 여 중지 값 사이에 보간 값의 연속 되는 부드러운 값 집합을 계산할 수 있습니다.

보간 식에 사용할 수 있는 보간 방법에는 다음 세 가지 유형이 있습니다.

| 이름 | Description | 매개 변수 |
|------|-------------|------------|
| `ExpressionInterpolationMode.linear` | 중지 쌍 사이에 선형으로 보간합니다. | `nil` |
| `ExpressionInterpolationMode.exponential` | 중지 사이에 지수로 보간합니다. 밑이 지정 되 고 출력이 늘어나는 속도를 제어 합니다. 값이 높을수록 출력은 범위의 높은 쪽 끝에서 증가합니다. 1에 가까운 밑 값은 보다 선형으로 향상 되는 출력을 생성 합니다.| 지 수 보간의 밑을 지정 하는 숫자로 계산 되는 식입니다. |
| `ExpressionInterpolationMode.cubicBezier` | 지정된 제어 지점에서 정의하는 [입방형 베지어 곡선](https://developer.mozilla.org/docs/Web/CSS/timing-function)을 사용하여 보간합니다. | 각각 숫자로 계산 되는 네 개의 식을 포함 하는 배열 또는 집계 식입니다. 네 개의 숫자는 입방 형 3 차원 곡선의 제어점입니다. |

다음은 이러한 여러 유형의 보간의 예입니다.

| 선형  | 지수 | 입방형 3차원 |
|---------|-------------|--------------|
| ![선형 보간 그래프](./media/ios-sdk/data-driven-style-expressions-ios/linear-interpolation.png) | ![지수 보간 그래프](./media/ios-sdk/data-driven-style-expressions-ios/exponential-interpolation.png) | ![입방형 3차원 보간 그래프](./media/ios-sdk/data-driven-style-expressions-ios/bezier-curve-interpolation.png) |

###### <a name="interpolation-expression-example"></a>보간 식 예제

다음 예에서는 선형 보간 식을 사용 하 여 `bubbleColor` point 기능의 속성에 따라 거품 계층의 속성을 설정 합니다 `temperature` . `temperature`값이 60 보다 작은 경우 파란색 색이 반환 됩니다. 60에서 70 미만 사이의 값인 경우 "yellow"가 반환됩니다. 70 ~ 80 보다 작은 경우 주황색이 반환 됩니다. 80 이상인 경우 빨간색이 반환 됩니다.

```swift
let layer = BubbleLayer(
    source: source,
    options: [
        .bubbleColor(
            from: NSExpression(
                forAZMInterpolating: NSExpression(forKeyPath: "temperature"),
                curveType: .linear,
                parameters: nil,
                stops: NSExpression(forConstantValue: [
                    50: UIColor.blue,
                    60: UIColor.yellow,
                    70: UIColor.orange,
                    80: UIColor.red
                ])
            )
        )
    ]
)
```

다음 이미지는 위의 식에서 색을 선택하는 방법을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/data-driven-style-expressions-ios/interpolate-expression-example.png" alt-text="Interpolate 식 예":::

##### <a name="step-expression"></a>Step 식

Step 식은 중지로 정의 된 [부분 일정 분포 상수 함수](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) 를 평가 하 여 불연속의 단계별 결과 값을 계산 하는 데 사용할 수 있습니다.

단계 식은 입력 값 바로 앞에 있는 stop의 출력 값을 반환 하거나 입력이 첫 번째 중지 보다 작은 경우에는 from 값을 반환 합니다.

###### <a name="step-expression-example"></a>Step 식 예

다음 예에서는 step 식을 사용 하 여 `bubbleColor` point 기능의 속성을 기반으로 거품 계층의 속성을 설정 합니다 `temperature` . `temperature`값이 60 보다 작은 경우 파란색이 반환 됩니다. 60에서 70 미만 사이의 값인 경우 "yellow"가 반환됩니다. 70 ~ 80 보다 작은 경우 주황색이 반환 됩니다. 80 이상인 경우 빨간색이 반환 됩니다.

```swift
let layer = BubbleLayer(
    source: source,
    options: [
        .bubbleColor(
            from: NSExpression(
                forAZMStepping: NSExpression(forKeyPath: "temperature"),
                from: NSExpression(forConstantValue: UIColor.blue),
                stops: NSExpression(forConstantValue: [
                    50: UIColor.blue,
                    60: UIColor.yellow,
                    70: UIColor.orange,
                    80: UIColor.red
                ])
            )
        )
    ]
)
```

다음 이미지는 위의 식에서 색을 선택하는 방법을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/data-driven-style-expressions-ios/step-expression-example.png" alt-text="Step 식 예":::

## <a name="additional-information"></a>추가 정보

식을 지원하는 계층에 관해 자세히 알아보세요.

* [기호 계층 추가](add-symbol-layer-ios.md)
* [거품형 계층 추가](add-bubble-layer-map-ios.md)
* [선 계층 추가](add-line-layer-map-ios.md)
* [다각형 계층 추가](add-polygon-layer-map-ios.md)
