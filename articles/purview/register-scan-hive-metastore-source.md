---
title: Hive Metastore 데이터베이스에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 Hive Metastore 데이터베이스에 연결하고 Purview의 기능을 사용하여 Hive Metastore 데이터베이스 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 5784ae0b74762ae4dbcd512e92df4f81f9f70ec5
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871512"
---
# <a name="connect-to-and-manage-hive-metastore-databases-in-azure-purview"></a>Azure Purview에서 Hive Metastore 데이터베이스에 연결 및 관리

이 문서에서는 Hive Metastore 데이터베이스를 등록하는 방법과 Azure Purview에서 Hive Metastore 데이터베이스를 인증하고 상호 작용하는 방법을 간략하게 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 아니요 | 아니요 | 아니요 | 아니요| 예** |

\**데이터 세트가 [데이터 팩터리 복사 작업에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다](how-to-link-azure-data-factory.md). 

> [!Important]
> 지원되는 플랫폼은 Apache Hadoop, Cloudera, Hortonworks 및 Databricks입니다.
> 지원되는 Hive 버전은 2.x~3.x입니다. 지원되는 Databricks 버전은 8.0 이상입니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

* [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

* 자체 호스팅 통합 런타임이 실행되고 있는 머신에 Hive Metastore 데이터베이스의 JDBC 드라이버를 다운로드하여 설치합니다. 예를 들어, 사용되는 데이터베이스가 mssql인 경우 [SQL Server용 Microsoft JDBC 드라이버](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)를 다운로드해야 합니다.

    > [!Note]
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에서 Hive Metastore 데이터베이스를 등록하는 방법에 대해 설명합니다.

Hive 메타스토어 데이터베이스에 대해 유일하게 지원되는 인증은 **기본 인증** 입니다.

### <a name="steps-to-register"></a>등록 단계

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. **등록** 을 선택합니다.

1. 원본 등록에서 Hive **메타스토어** 를 선택합니다. **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Hive 원본 등록" border="true":::

원본 등록(Hive 메타스토어) 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

1. **Hive 클러스터 URL** 을 입력합니다. Ambari URL 또는 Databricks 작업 영역 URL에서 클러스터 URL을 가져올 수 있습니다. 예를 들면 hive.azurehdinsight.net 또는 adb-19255636414785.5.azuredatabricks.net과 같습니다.

1. **Hive 메타스토어 서버 URL** 을 입력합니다. 예를 들면 sqlserver://hive.database.windows.net 또는 jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443과 같습니다.

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Hive 원본 구성" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 Hive Metastore 데이터베이스를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md]에 설명된 단계를 사용하여 자체 호스팅 통합 런타임을 설정합니다.

1. **원본** 으로 이동합니다.

1. 등록된 **Hive 메타스토어** 데이터베이스를 선택합니다.

1. **+ 새 검사** 를 선택합니다.

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.

       * 자격 증명을 만드는 동안 기본 인증을 선택합니다.
       * 사용자 이름 입력 필드에 메타스토어 사용자 이름을 입력합니다.
       * 비밀 키에 메타스토어 암호를 저장합니다.

       자격 증명에 대한 자세한 내용은 [여기](manage-credentials.md)에 있는 링크를 참조하세요.

       **Databricks 사용법**: Databricks 클러스터 -> 앱 -> 웹 터미널 시작으로 이동합니다. cmdlet **cat/databricks/hive/conf/hive-site.xml** 을 실행합니다.

       사용자 이름과 암호는 아래와 같이 두 가지 속성에서 액세스할 수 있습니다.

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **메타스토어 JDBC 드라이버 위치**: VM에서 자체 호스팅 통합 런타임이 실행 중인 JDBC 드라이버 위치의 경로를 지정합니다. 이 경로는 유효한 JAR 폴더 위치에 대한 경로여야 합니다.

       Databricks를 검색하는 경우 아래 Databricks에 대한 섹션을 참조하세요.

       > [!Note]
       > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

    1. **메타스토어 JDBC 드라이버 클래스**: 연결 드라이버 클래스 이름을 제공합니다. 예를 들면 \com.microsoft.sqlserver.jdbc.SQLServerDriver와 같습니다.

       **Databricks 사용법**: Databricks 클러스터 -> 앱 -> 웹 터미널 시작으로 이동합니다. cmdlet **cat/databricks/hive/conf/hive-site.xml** 을 실행합니다.

       드라이버 클래스는 아래와 같이 속성에서 액세스할 수 있습니다.

        :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **메타스토어 JDBC URL**: 연결 URL 값을 제공하고 메타스토어 DB 서버 URL에 대한 연결을 정의합니다. 예: `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`

       **Databricks 사용법**: Databricks 클러스터 -> 앱 -> 웹 터미널 시작으로 이동합니다. cmdlet **cat/databricks/hive/conf/hive-site.xml** 을 실행합니다.

       JDBC URL은 아래와 같이 연결 URL 속성에서 액세스할 수 있습니다.

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

       > [!NOTE]
       > *hive-site.xml* 에서 URL을 복사할 때 문자열에서 `amp;`를 제거해야 합니다. 그렇지 않으면 검사가 실패합니다. 이 URL에 대해 VM에서 SSL 인증서가 배치된 위치에 경로를 추가합니다. [SSL 인증서를 다운로드](../mysql/howto-configure-ssl.md)할 수 있습니다. Windows 경로 구분 문자를 `\`에서 `/`로 변경해야 합니다. 예를 들어, MariaDB JAR 파일이 *C:\mariadb-jdbc.jar* 인 경우 *C:/mariadb-jdbc.jar* 로 변경합니다. Metastore JDBC URL `sslCA` 매개 변수를 동일하게 변경합니다. *D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem* 에서 *D:/Drivers/SSLCert/BaltimoreCyberTrustRoot.crt.pem* 으로 변경합니다.

       **메타스토어 JDBC URL** 은 다음 예제와 같습니다.

       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&useSSL=true&sslCA=D:/Drivers/SSLCert/BaltimoreCyberTrustRoot.crt.pem`

    1. **메타스토어 데이터베이스 이름**: Hive 메타스토어 데이터베이스 이름을 제공합니다.

       Databricks를 검색하는 경우 아래 Databricks에 대한 섹션을 참조하세요.

       **Databricks 사용법**: Databricks 클러스터 -> 앱 -> 웹 터미널 시작으로 이동합니다. cmdlet **cat/databricks/hive/conf/hive-site.xml** 을 실행합니다.

       다음과 같이 JDBC URL 속성에서 데이터베이스 이름에 액세스할 수 있습니다. 예: organization1829255636414785

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **스키마**: 가져올 Hive 스키마 목록을 지정합니다. 예: schema1; schema2.

        목록이 비어 있으면 모든 사용자 스키마를 가져옵니다. 모든 시스템 스키마(예: SysAdmin) 및 개체는 기본적으로 무시됩니다.

        목록이 비어 있는 경우 모든 사용 가능한 스키마를 가져옵니다. SQL LIKE 식 구문을 사용하는 허용 가능한 스키마 이름 패턴에는 % 사용이 포함됩니다. 예를 들어 A%; %B; %C%; D입니다.

        * A로 시작합니다. 또는
        * B로 끝납니다. 또는
        * C를 포함합니다. 또는
        * D와 같습니다.

        NOT 및 특수 문자는 허용되지 않습니다.

    1. **사용 가능한 최대 메모리**: 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 검색할 Hive 메타스토어 데이터베이스의 크기에 따라 달라집니다.

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="Hive 원본 검색" border="true":::

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
