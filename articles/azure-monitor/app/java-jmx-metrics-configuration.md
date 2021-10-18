---
title: JMX 메트릭을 구성하는 방법 - Java용 Azure Monitor Application Insights
description: Azure Monitor Application Insights Java 에이전트에 대한 추가 JMX 메트릭 수집 구성
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 932ce7191aa64850dcf276f5906a038a0352ee80
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133883"
---
# <a name="configuring-jmx-metrics"></a>JMX 메트릭 구성

Application Insights Java 3.x는 기본적으로 일부 JMX 메트릭을 수집하지만 대부분의 경우 이것으로는 충분하지 않습니다. 이 문서에서는 JMX 구성 옵션에 대해 자세히 설명합니다.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>추가 JMX 메트릭을 수집하는 방법은 무엇인가요?

JMX 메트릭 수집은 applicationinsights.json 파일에 ```"jmxMetrics"``` 섹션을 추가하여 구성할 수 있습니다. 메트릭의 이름은 Azure Portal의 Application Insights 리소스에 표시하고 싶은 대로 지정할 수 있습니다. 수집하려는 각 메트릭에 대해 개체 이름과 특성을 정의해야 합니다.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>어떤 메트릭을 구성에 사용할 수 있는지 아는 방법은 무엇인가요?

중요한 질문입니다. 개체 이름 및 특성을 알고 계셔야 합니다. 해당 속성은 라이브러리, 프레임워크 및 애플리케이션 서버별로 다르고 제대로 문서화되지 않는 경우가 많습니다. 개체 이름 및 특성을 확인하려면 MBean 트리를 봐야 합니다. MBean은 디바이스, 애플리케이션 또는 리소스를 나타낼 수 있는 관리형 Java 개체이며 일련의 특성을 가지고 있습니다. 

사용 가능한 메트릭을 확인하고 해당 메트릭을 찾아보려면 [Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html)을 사용하는 것이 좋습니다.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>올바른 메트릭을 확인하기 위해 Java Mission Control로 이동하는 방법은 무엇인가요?

Java Mission Control 도구를 실행할 때 왼쪽에 사용 가능한 JVM으로 구성된 선택 영역이 나타나면 ‘VM 브라우저’ 탭 아래에서 관련 프로세스를 클릭합니다. JMC가 프로세스에 대한 대시보드를 로드할 때까지 기다린 후 하단에서 'MBean 브라우저' 탭을 선택합니다(아래 참조). JMC는 JVM과 동일한 폴더에 위치해야 하며 프로세스/앱이 실행 중이어야 합니다.

![JMC MBean 브라우저의 스크린샷](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>원하는 메트릭과 필요한 특성을 가져오는 방법은 무엇인가요?

MBean 브라우저에서는 확장할 수 있는 범주 목록이 포함된 MBean 트리가 열립니다. 왼쪽에서 범주를 선택하면 오른쪽에 특성 목록이 열립니다. 아래는 메트릭, 해당 개체 이름 및 특성의 예입니다. 특성은 아래 예에서와 같이 중첩될 수 있습니다.

![JMC MBean 트리의 스크린샷](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>구성 예

위 이미지에 표시된 대로 선택한 특성으로 몇 가지 메트릭을 구성해 보겠습니다. 첫 번째는 중첩된 메트릭인 `LastGcInfo`의 예이며 이 메트릭에는 몇 가지 속성이 있는데 이 중에 `GcThreadCount`를 캡처하려고 합니다.

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>수집된 메트릭의 형식과 사용 가능한 구성 옵션은 무엇인가요?

숫자 및 부울 JMX 메트릭은 지원되지만 다른 형식은 지원되지 않고 무시됩니다. 

현재 와일드카드 및 집계된 특성은 지원되지 않으므로 모든 특성 '개체 이름'/' 특성' 쌍을 별도로 구성해야 합니다. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Application Insights에서 JMX 메트릭을 찾을 수 있는 위치는 어디인가요?

애플리케이션이 실행 중이고 JMX 메트릭이 수집되는 경우 Azure Portal로 가서 Application Insights 리소스로 이동하면 해당 메트릭을 볼 수 있습니다. 메트릭 탭에서 아래 표시된 것처럼 드롭다운을 선택하면 해당 메트릭이 표시됩니다.

![포털에서 메트릭의 스크린샷](media/java-ipa/jmx/jmx-portal.png)
