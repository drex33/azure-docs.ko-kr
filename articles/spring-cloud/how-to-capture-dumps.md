---
title: Azure 스프링 클라우드에서 힙 덤프, 스레드 덤프 및 JFR을 수동으로 캡처하는 방법
description: 힙 덤프, 스레드 덤프 또는 시작 JFR를 수동으로 캡처하는 방법에 대해 알아봅니다.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5f34dce5e177650b11858cabf770b7d63dc9b593
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000813"
---
# <a name="how-to-manually-capture-heap-dump-thread-dump-and-jfr-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 힙 덤프, 스레드 덤프 및 JFR을 수동으로 캡처하는 방법

**이 문서는 다음에 적용 됩니다.** ✔️ Java ❌ C #

효과적인 문제 해결-고객이 프로덕션 환경에서 적시에 문제를 해결 하는 데 중요 한 것은 비즈니스를 항상 온라인으로 유지 합니다. 현재 Azure 스프링 클라우드에서는이 필드의 고객을 지원 하기 위해 응용 프로그램 로그 스트리밍/쿼리, 다양 한 메트릭 내보내기, 경고, 분산 추적 등을 이미 제공 하 고 있습니다. 그러나 대기 시간이 긴 요청, JVM 힙 누수 또는 높은 CPU 사용량에 대 한 경고를 수신 하는 고객의 경우에는 최신 마일 솔루션이 없습니다. 따라서 수동으로 힙 덤프 생성, 스레드 덤프 생성 또는 JFR 시작을 사용 하도록 설정 했습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 연습을 완료하려면 다음이 필요합니다.

* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.
* 생성 된 진단 파일을 저장 하기 위해 이미 하나 이상의 [영구 저장소가 앱에 바인딩되어](how-to-built-in-persistent-storage.md) 있습니다.

**고객이 탑재 경로 아래에 경로를 사용 하려면 하위 경로가 이미 만들어져 있는지 확인 하세요.**

## <a name="generate-a-heap-dump"></a>힙 덤프 생성
Azure 스프링 클라우드에서 앱의 힙 덤프를 생성 합니다.
```heap dump command
   az spring-cloud app deployment generate-heap-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="generate-a-thread-dump"></a>스레드 덤프 생성
Azure 스프링 클라우드에서 앱의 스레드 덤프를 생성 합니다.
```thread dump command
   az spring-cloud app deployment generate-thread-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="start-jfr"></a>JFR 시작
Azure 스프링 클라우드에서 앱의 JFR을 시작 합니다.
```JFR command
   az spring-cloud app deployment start-JFR -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path> --duration <duration-of-JFR>
```
Duration의 기본값은 60 초입니다.
## <a name="get-your-diagnostic-files"></a>진단 파일 가져오기
영구 저장소의 대상 파일 경로로 이동 하 여 dump/JFR을 찾습니다. 로컬 컴퓨터에 다운로드할 수 있습니다. 생성 된 파일의 이름은 힙 덤프의 경우 ' {appInstance} \_ heapdump \_ {timestamp}. hprof '이 고, 스레드 덤프에는 ' {appinstance} \_ threaddump \_ {timestamp} .txt '이 고, jfr의 경우 ' {appinstance} \_ jfr \_ {timestamp}. jfr '와 같습니다.
