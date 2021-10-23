---
title: Apache Spark용 Scala 및 Java 라이브러리 관리
description: Azure Synapse Analytics에서 Scala 및 Java 라이브러리를 추가하고 관리하는 방법을 알아봅니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 952e0b325fe37345db42d1dcd8d6c521f8235366
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225401"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Apache Spark용 Scala 및 Java 패키지 관리

라이브러리는 프로그램 또는 프로젝트에 포함하는 것이 좋은 재사용 가능한 코드를 제공합니다. 

여러 가지 이유로 서버리스 Apache Spark 풀 환경을 업데이트해야 할 수 있습니다. 예를 들어 다음을 확인할 수 있습니다.
- 핵심 종속성 중 하나가 새 버전을 출시했습니다.
- 기계 학습 모델을 학습하거나 데이터를 준비하기 위한 추가 패키지가 필요합니다.
- 더 나은 패키지를 찾아서 이전 패키지가 더 이상 필요하지 않습니다.

애플리케이션에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 서버리스 Apache Spark 풀 또는 Notebook 세션 중 하나에 라이브러리를 설치하면 됩니다. 이 문서에서는 Scala 및 Java 패키지를 관리하는 방법을 다룹니다.

## <a name="default-installation"></a>기본 설치
Azure Synapse Analytics의 Apache Spark에는 일반적인 데이터 엔지니어링, 데이터 준비, 기계 학습 및 데이터 가시화 작업을 위한 전체 라이브러리 집합이 있습니다. 전체 라이브러리 목록은 [Apache Spark 버전 지원](apache-spark-version-support.md)에서 찾을 수 있습니다. 

Spark 인스턴스가 시작되면 라이브러리가 자동으로 포함됩니다. Spark 풀 및 세션 수준에서 추가 Scala/Java 패키지를 추가할 수 있습니다.

## <a name="workspace-packages"></a>작업 영역 패키지
작업 영역 패키지는 사용자 지정되거나 프라이빗 jar 파일일 수 있습니다. 이러한 패키지를 작업 영역에 업로드하고 나중에 특정 Spark 풀에 할당할 수 있습니다.

작업 영역 패키지를 추가하려면:
1. **관리** > **작업 영역 패키지** 탭으로 이동합니다.
2. 파일 선택기를 사용하여 jar 파일을 업로드합니다.
3. 파일이 Azure Synapse 작업 영역에 업로드된 후에는 지정된 Apache Spark 풀에 이러한 jar 파일을 추가할 수 있습니다.

![작업 영역 패키지를 강조하는 스크린샷.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "작업 영역 패키지 보기")

## <a name="pool-libraries"></a>풀 라이브러리
Spark 애플리케이션에 사용할 Scala 및 Java 패키지를 확인한 후에는 Spark 풀에 설치할 수 있습니다. 풀 수준 라이브러리는 풀에서 실행되는 모든 Notebook 및 작업에 사용할 수 있습니다.

Synapse Studio 또는 Azure Portal로 이동하여 Spark 풀 라이브러리를 업데이트할 수 있습니다. 여기에서 설치할 작업 영역 라이브러리를 선택할 수 있습니다. 

변경 내용이 저장되면 Spark 작업은 설치를 실행하고 나중에 다시 사용할 수 있도록 결과 환경을 캐시합니다. 작업이 완료되면 새 Spark 작업 또는 Notebook 세션에서 업데이트된 풀 라이브러리를 사용합니다. 

> [!IMPORTANT]
> - 설치하는 패키지가 크거나 설치하는 데 시간이 오래 걸리는 경우 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - PySpark, Python, Scala/Java, .NET 또는 Spark 버전을 변경하는 것은 지원되지 않습니다.

#### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>Synapse Studio 또는 Azure Portal에서 패키지 관리
Spark 풀 라이브러리는 Synapse Studio 또는 Azure Portal에서 관리할 수 있습니다. 

Spark 풀에 라이브러리를 업데이트하거나 추가하려면:
1. Azure Portal에서 Azure Synapse Analytics 작업 영역으로 이동합니다.

    **Azure Portal** 에서 업데이트하는 경우:

    - **Synapse 리소스** 섹션 아래에서 **Apache Spark 풀** 탭을 선택하고 목록에서 Spark 풀을 선택합니다.
     
    - **패키지** 를 Spark 풀의 **설정** 섹션에서 선택합니다.
  
    ![환경 구성 파일 업로드 단추를 강조하는 스크린샷.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python 라이브러리 추가")
   
    **Synapse Studio** 에서 업데이트하는 경우:
    - 기본 탐색 패널에서 **관리** 를 선택한 다음 **Apache Spark 풀** 을 선택합니다.

    - 특정 Spark 풀에 대한 **패키지** 섹션을 선택합니다.
    ![스튜디오에서의 업로드 환경 구성 옵션을 강조하는 스크린샷.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "스튜디오에서 Python 라이브러리 추가")
   
2. Jar 파일을 추가하려면 **작업 영역 패키지** 섹션으로 이동하여 풀에 추가합니다. 
3. 변경 내용을 저장하면 시스템 작업이 트리거되어 지정된 라이브러리를 설치하고 캐시합니다. 이 프로세스를 통해 전체 세션 시작 시간을 줄일 수 있습니다. 
4. 작업이 성공적으로 완료되면 모든 새 세션에서 업데이트된 풀 라이브러리를 선택합니다.

> [!IMPORTANT]
> **새 설정을 강제 적용** 하는 옵션을 선택하면 선택한 Spark 풀의 모든 현재 세션이 종료됩니다. 세션이 종료되면 풀이 다시 시작될 때까지 기다려야 합니다. 
>
> 이 설정을 선택하지 않으면 현재 Spark 세션이 종료될 때까지 기다리거나 수동으로 중지해야 합니다. 세션이 종료되면 풀을 다시 시작하도록 해야 합니다.

#### <a name="track-installation-progress"></a>설치 진행률 추적 
시스템 예약 Spark 작업은 새 라이브러리 집합으로 풀이 업데이트될 때마다 시작됩니다. 이 Spark 작업은 라이브러리 설치 상태를 모니터링하는 데 유용합니다. 라이브러리 충돌이나 기타 문제로 인해 설치가 실패하는 경우 Spark 풀이 이전 또는 기본 상태로 되돌아갑니다. 

또한 사용자는 설치 로그를 검사하여 종속성 충돌을 식별하거나 풀 업데이트 중에 설치된 라이브러리를 확인할 수도 있습니다.

이 로그를 보려면:
1. **모니터** 탭에서 Spark 애플리케이션 목록으로 이동합니다. 
2. 풀 업데이트에 해당하는 시스템 Spark 애플리케이션 작업을 선택합니다. 이 시스템 작업은 *SystemReservedJob-LibraryManagement* 제목으로 실행됩니다.
   ![시스템 예약 라이브러리 작업을 강조 표시하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "시스템 라이브러리 작업 보기")
3. 보기를 전환하여 **드라이버** 및 **stdout** 로그를 봅니다. 
4. 결과 내에서 패키지의 설치와 관련된 로그를 확인합니다.
    ![시스템 예약 라이브러리 작업 결과를 강조하는 스크린샷.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "시스템 라이브러리 작업 진행률 보기")

## <a name="session-scoped-libraries"></a>세션 범위 라이브러리 
풀 수준 라이브러리 외에도 Notebook 세션의 시작 부분에 세션 범위 라이브러리를 지정할 수 있습니다.  세션 범위 라이브러리를 사용하면 Notebook 세션 내에서 독점적으로 jar 패키지를 지정하고 사용할 수 있습니다. 

세션 범위 라이브러리를 사용하는 경우 다음 사항을 염두에 두어야 합니다.
   - 세션 범위 라이브러리를 설치할 때 현재 Notebook만이 지정된 라이브러리에 액세스할 권한이 있습니다. 
   - 이러한 라이브러리는 동일한 Spark 풀을 사용하는 다른 세션 또는 작업에 영향을 주지 않습니다. 
   - 이러한 라이브러리는 기본 런타임 및 풀 수준 라이브러리 위에 설치됩니다. 
   - Notebook 라이브러리는 우선 순위가 가장 높습니다.

세션 범위 Java 또는 Scala 패키지를 지정하려면 ```%%configure``` 옵션을 사용할 수 있습니다.

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Notebook의 시작 부분에서 %% configure를 실행하는 것이 좋습니다. 유효한 매개 변수의 전체 목록은 이 [문서](https://github.com/cloudera/livy#request-body)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
- 라이브러리 설치 오류 문제 해결: [라이브러리 오류 문제 해결](apache-spark-troubleshoot-library-errors.md)
