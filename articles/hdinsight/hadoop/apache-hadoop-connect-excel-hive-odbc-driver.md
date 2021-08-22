---
title: ODBC(Open Database Connectivity) 드라이버를 사용하는 Excel 및 Apache Hadoop - Azure HDInsight
description: Excel용 Microsoft Hive ODBC 드라이버를 설정하고 Microsoft Excel의 HDInsight 클러스터에서 데이터를 쿼리하는 데 사용하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 10bc1d2841e7858697a60dfbd1d093f403617a59
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300086"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC 드라이버로 Azure HDInsight의 Apache Hadoop에 Excel 연결

[!INCLUDE [ODBC-JDBC-selector](../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft의 빅 데이터 솔루션은 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 HDInsight에 배포한 Apache Hadoop 클러스터와 통합합니다. 예를 들면 Excel을 Hadoop 클러스터의 Hive 데이터 웨어하우스에 연결하는 기능과 같습니다. Microsoft Hive ODBC(Open Database Connectivity) 드라이버를 사용하여 연결합니다.

Excel용 Microsoft 파워 쿼리 추가 기능이 있는 Excel의 HDInsight 클러스터에 연결된 데이터를 연결할 수 있습니다. 자세한 내용은 [파워 쿼리로 HDInsight에 Excel 연결](./apache-hadoop-connect-excel-power-query.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* HDInsight Hadoop 클러스터. 만들려면 [Azure HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* Office 2010 Professional Plus 이상 또는 Excel 2010 이상을 포함한 워크스테이션.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC 드라이버 설치

[Microsoft Hive ODBC 드라이버](https://www.microsoft.com/download/details.aspx?id=40886)를 다운로드하고 설치합니다. ODBC 드라이버를 사용할 애플리케이션 버전과 일치하는 버전을 선택합니다.  이 문서에서는 Office Excel용 드라이버를 사용합니다.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC 데이터 원본 만들기

다음 단계에 따라 Hive ODBC 데이터 원본을 만들 수 있습니다.

1. Windows의 경우 **시작 > Windows 관리 도구 > ODBC 데이터 원본 (32비트)/(64비트)** 로 이동합니다.  이 작업을 수행하면 **OBDC 데이터 원본 관리자** 창이 열립니다.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="OBDC 데이터 원본 관리자" border="true":::

1. **사용자 DSN** 탭에서 **추가** 를 선택하여 **새 데이터 원본 만들기** 창을 엽니다.

1. **Microsoft Hive ODBC 드라이버** 를 선택한 다음, **마침** 을 선택하여 **Microsoft Hive ODBC 드라이버 DSN 설정** 창을 엽니다.

1. 다음 값을 입력하거나 선택합니다.

   | 속성 | Description |
   | --- | --- |
   |  데이터 원본 이름 |데이터 원본에 이름 지정 |
   |  호스트 |`HDInsightClusterName.azurehdinsight.net`를 입력합니다. 예들 들어 `myHDICluster.azurehdinsight.net`입니다. 참고: `HDInsightClusterName-int.azurehdinsight.net`은 클라이언트 VM이 동일한 가상 네트워크에 피어링되는 동안 지원됩니다. |
   |  포트 |**443** 을 사용합니다. (이 포트는 563에서 443으로 변경됨) |
   |  데이터베이스 |**기본값** 을 사용합니다. |
   |  메커니즘 |**Windows Azure HDInsight Service** 를 선택합니다. |
   |  사용자 이름 |HDInsight 클러스터 HTTP 사용자의 사용자 이름을 입력합니다. 기본 사용자 이름은 **admin** 입니다. |
   |  암호 |HDInsight 클러스터 사용자 암호 입력 **암호 저장(암호화됨)** 확인란을 선택합니다.|

1. 선택 사항: **고급 옵션...** 을 선택합니다.  

   | 매개 변수 | Description |
   | --- | --- |
   |  Use Native Query |선택하면 ODBC 드라이버가 TSQL을 HiveQL로 변환하지 않습니다. 순수 HiveQL 문을 제출하는 것이 100% 확실한 경우에만 사용합니다. SQL Server 또는 Azure SQL Database에 연결하는 경우에는 이 옵션을 선택 취소한 상태로 둬야 합니다. |
   |  Rows fetched per block |많은 수의 레코드를 가져오는 경우 최적의 성능을 위해 이 매개 변수를 조정해야 할 수 있습니다. |
   |  Default string column length, Binary column length, Decimal column scale |데이터 형식 길이 및 정밀도는 데이터가 반환되는 방식에 영향을 줄 수 있습니다. 이 경우 정밀도 손실 또는 잘림으로 인해 잘못된 정보가 반환됩니다. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="고급 DSN 구성 옵션" border="true":::

1. **테스트** 를 선택하여 데이터 원본을 테스트합니다. 데이터 원본이 올바르게 구성된 경우 테스트 결과가 **성공!** 으로 표시됩니다.

1. **확인** 을 선택하여 테스트 창을 닫습니다.  

1. **확인** 을 선택하여 **Microsoft Hive ODBC Driver DSN 설정** 창을 닫습니다.  

1. **확인** 을 선택하여 **ODBC 데이터 원본 관리자** 창을 닫습니다.  

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight에서 Excel로 데이터 가져오기

다음 단계는 이전 섹션에서 만든 ODBC 데이터 원본을 사용하여 Hive 테이블에서 Excel 통합 문서로 데이터를 가져오는 방법을 설명합니다.

1. Excel에서 새 통합 문서나 기존 통합 문서를 엽니다.

2. **데이터** 탭에서 **데이터 가져오기** > **기타 원본에서** > **ODBC에서** 로 이동하여 **ODBC에서** 창을 시작합니다.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Excel 데이터 연결 마법사 열기" border="true":::

3. 드롭다운 목록에서 마지막 섹션에서 만든 데이터 원본 이름을 선택한 다음, **확인** 을 선택합니다.

4. 처음 사용하는 경우 **ODBC 드라이버** 대화 상자가 열립니다. 왼쪽 메뉴에서 **창** 을 선택합니다. 그런 다음 **연결** 을 선택하여 **탐색기** 창을 엽니다.

5. **탐색기** 에서 **HIVE** > **기본값** > **hivesampletable** 로 이동한 다음, **로드** 를 선택합니다. 데이터를 Excel로 가져올 때까지 잠시 기다립니다.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="HDInsight Excel Hive ODBC 탐색기" border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight Service에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight Service에서 SQL Database로 데이터를 가져올 수 있습니다. HDInsight 서비스에 데이터를 업로드할 수도 있습니다. 자세한 내용은 다음을 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)