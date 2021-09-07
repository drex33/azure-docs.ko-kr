---
title: 지원되는 데이터 플랫폼
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에서 지원되는 데이터 플랫폼 및 도구에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 04/29/2021
ms.openlocfilehash: 13da66fbe5b79026028dc6c525283e40f1d55dad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528969"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine에서 지원되는 데이터 플랫폼

DSVM(Data Science Virtual Machine)을 사용하여 광범위한 데이터 플랫폼에 대한 분석을 빌드할 수 있습니다. 원격 데이터 플랫폼에 대 한 인터페이스를 외에도 DSVM 신속 하 게 개발 및 프로토타입 생성에 대 한 로컬 인스턴스를 제공합니다.

다음은 DSVM에서 지원되는 데이터 플랫폼 도구입니다.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 관계형 데이터베이스 인스턴스      |
| 지원되는 DSVM 버전      | Windows 2019, Ubuntu 18.04(SQL Server 2019)   |
| 일반적인 사용 용도      | <ul><li>더 작은 데이터 세트를 사용하여 로컬에서 빠른 개발</li><li>In-database R 실행</li></ul> |
| 샘플에 대한 링크      | <ul><li>뉴욕 시 데이터 세트의 작은 샘플이 다음 SQL 데이터베이스로 로드됩니다.<br/>  `nyctaxi`</li><li>Microsoft Machine Learning Server 및 데이터베이스 내 분석을 보여 주는 Jupyter 샘플은 다음에서 찾을 수 있습니다.<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM의 관련 도구       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC 드라이버</li><li>pyodbc, RODBC</li></ul> |

> [!NOTE]
> SQL Server Developer 버전은 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스나 SQL Server VM 중 하나가 필요합니다.

> [!NOTE]
> Machine Learning Server 독립 실행형에 대한 지원은 2021년 7월 1일에 종료됩니다. 6월 30일 이후에 DSVM 이미지에서 이를 제거합니다. 기존 배포는 계속해서 소프트웨어에 액세스할 수 있지만, 2021년 7월 1일 이후에는 지원이 종료되기 때문에 이를 지원하지 않습니다.

> [!NOTE]
> 2021년 11월 말까지 DSVM 이미지에서 SQL Server Developer Edition을 제거할 예정입니다. 기존 배포는 SQL Server Developer Edition을 계속 설치할 수 있습니다. 새 배포에서 Docker 지원을 통해 설치하고 사용할 수 있는 SQL Server Developer Edition에 액세스하려면 [빠른 시작: Docker에서 SQL Server 컨테이너 이미지 실행](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-)을 참조하세요.

### <a name="windows"></a>Windows

#### <a name="setup"></a>설치 프로그램

데이터베이스 서버는 이미 미리 구성되었고 SQL Server와 관련된 Windows 서비스(예: `SQL Server (MSSQLSERVER)`)는 자동으로 실행되도록 설정됩니다. 수동 단계는 Microsoft Machine Learning Server를 사용하여 데이터베이스 내 분석을 사용하도록 설정하는 것뿐입니다. SSMS(SQL Server Management Studio)에서 일회성 작업으로 다음 명령을 실행하여 분석을 사용하도록 설정할 수 있습니다. 컴퓨터 관리자로 로그인한 후 이 명령을 실행하여 SSMS에서 새 쿼리를 열고 선택한 데이터베이스가 `master`인지 확인합니다.

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(%COMPUTERNAME%을 VM 이름으로 바꿉니다.)

SQL Server Management Studio를 실행하려면 프로그램 목록에서 'SQL Server Management Studio'를 검색하거나 Windows 검색을 사용하여 그것을 찾아 실행할 수 있습니다. 자격 증명을 묻는 메시지가 나타나면 **Windows 인증** 을 선택하고 **SQL Server 이름** 필드에 컴퓨터 이름 또는 ```localhost```를 사용합니다.

#### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법

기본 데이터베이스 인스턴스가 설치된 데이터베이스 서버는 기본적으로 자동 실행됩니다. Microsoft SQL Server 데이터베이스에 로컬로 액세스하려면 VM에서 SQL Server Management Studio와 같은 도구를 사용할 수 있습니다. 로컬 관리자 계정에는 데이터베이스에 대한 관리자 액세스 권한이 있습니다.

또한 DSVM은 Python, Machine Learning Server를 포함하여 여러 언어로 작성된 애플리케이션에서 SQL Server, Azure SQL 데이터베이스, Azure Synapse Analytics와 통신하는 ODBC 및 JDBC 드라이버가 함께 제공됩니다.

#### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에서 구성 및 설치 방법 

 SQL Server는 표준 방식으로 설치됩니다. 그것은 `C:\Program Files\Microsoft SQL Server`에서 찾을 수 있습니다. 데이터베이스 내 Machine Learning Server 인스턴스는 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`에 있습니다. 또한 DSVM에는 `C:\Program Files\Microsoft\R Server\R_SERVER`에 설치되는 별도의 독립 실행형 Machine Learning Server 인스턴스가 있습니다. 이러한 두 Machine Learning Server 인스턴스는 라이브러리를 공유하지 않습니다.


### <a name="ubuntu"></a>Ubuntu

Ubuntu DSVM에서 SQL Server Developer 버전을 사용하려면 먼저 설치해야 합니다. [빠른 시작: Ubuntu에 SQL Server 설치 및 데이터베이스 만들기](/sql/linux/quickstart-install-connect-ubuntu)를 통해 방법을 알 수 있습니다.



## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (독립 실행형)

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 빠른 대규모 데이터 처리 및 기계 학습을 위한 시스템인, 많이 사용되는 Apache Spark 플랫폼의 독립 실행형(단일 노드 In Process) 인스턴스     |
| 지원되는 DSVM 버전      | Linux     |
| 일반적인 사용 용도      | <ul><li>더 작은 데이터 세트를 사용하여 로컬에서 빠르게 Spark/PySpark 애플리케이션을 개발하고 나중에 Azure HDInsight와 같은 큰 Spark 클러스터에 배포</li><li>Microsoft Machine Learning Server Spark 컨텍스트를 테스트</li><li>SparkML 또는 Microsoft의 오픈 소스인 [MMLSpark](https://github.com/Azure/mmlspark) 라이브러리를 사용하여 ML 애플리케이션을 빌드</li></ul> |
| 샘플에 대한 링크      |    Jupyter 샘플:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server(Spark 컨텍스트): /dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| DSVM의 관련 도구       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark 커널)</li><li>Microsoft Machine Learning Server, SparkR, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>사용 방법
명령줄에서 `spark-submit` 또는 `pyspark` 명령을 실행하여 Spark 작업을 제출할 수 있습니다. 또한 Spark 커널로 새 노트북을 만듦으로써 Jupyter 노트북을 만들 수 있습니다.

DSVM에서 사용할 수 있는 SparkR, Sparklyr, Microsoft Machine Learning Server 같은 라이브러리를 사용하여 R에서 Spark를 사용할 수 있습니다. 앞의 표에서 샘플에 대한 포인터를 참조하세요.

### <a name="setup"></a>설치 프로그램
Ubuntu Linux DSVM 버전에서 Microsoft Machine Learning Server에 Spark 컨텍스트를 실행하기 전에 일회성 설정 단계를 수행하여 로컬 단일 노드 Hadoop HDFS 및 Yarn 인스턴스를 사용하도록 설정해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. 이러한 서비스를 사용하도록 설정하려면 처음에 다음 명령을 루트로 실행해야 합니다.

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop 관련 서비스는 더 이상 필요하지 않은 경우 ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```을 실행하여 중지할 수 있습니다.

원격 Spark 컨텍스트(DSVM의 독립 실행형 Spark 인스턴스)에서 MRS를 개발하고 테스트하는 방법을 보여 주는 샘플은 `/dsvm/samples/MRS` 디렉터리에서 사용할 수 있습니다.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에서 구성 및 설치 방법 
|플랫폼|설치 위치($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Microsoft MMLSpark 기계 학습 라이브러리를 사용하여 Azure Blob Storage 또는 Azure Data Lake Storage(ADLS)의 데이터에 액세스하기 위한 라이브러리는 $SPARK_HOME/jars에 미리 설치되어 있습니다. 이러한 JAR은 Apache Spark가 시작될 때 자동으로 로드됩니다. 기본적으로 Spark는 로컬 디스크의 데이터를 사용합니다. 

DSVM의 Spark 인스턴스가 Blob Storage 또는 Azure Data Lake Storage에 저장된 데이터에 액세스하려면 $SPARK_HOME/conf/core-site.xml.template에 있는 템플릿을 기반으로 `core-site.xml` 파일을 만들고 구성해야 합니다. 또한 Blob Storage 및 Azure Data Lake Storage에 액세스하는 데 적절한 자격 증명이 있어야 합니다. (템플릿 파일은 Blob Storage 및 Azure Data Lake Storage 구성에 자리 표시자를 사용합니다.)

Azure Data Lake Storage 서비스 자격 증명을 만드는 방법에 대한 자세한 내용은 [Azure Data Lake Storage Gen1으로 인증](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)을 참조하세요. Blob Storage 또는 Azure Data Lake Storage에 대한 자격 증명을 core-site.xml 파일에 입력하고 나면 URI 접두사 wasb:// 또는 adl://을 통해 해당 원본에 저장된 데이터를 참조할 수 있습니다.