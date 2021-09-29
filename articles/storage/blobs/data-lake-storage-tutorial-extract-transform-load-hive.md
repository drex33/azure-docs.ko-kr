---
title: '자습서: Azure HDInsight를 사용하여 데이터 추출, 변환 및 로드'
description: 이 자습서에서는 원시 CSV 데이터 세트에서 데이터를 추출하고, Azure HDInsight에서 Apache Hive를 사용하여 데이터를 변환한 다음, Sqoop을 사용하여 변환된 데이터를 Azure SQL Database로 로드하는 방법을 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6982cbc64e28b71566deb295dfe7d56aa8d24189
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588754"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>자습서: Azure HDInsight를 사용하여 데이터 추출, 변환 및 로드

이 자습서에서는 데이터 ETL(추출, 변환 및 로드) 작업을 수행합니다. 원시 CSV 데이터 파일을 추출하여 Azure HDInsight 클러스터로 가져오고, Apache Hive를 사용하여 변환하고, Apache Sqoop을 사용하여 Azure SQL Database에 로드합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - 데이터를 추출하여 HDInsight 클러스터에 로드합니다.
> - Apache Hive를 사용하여 데이터를 변환합니다.
> - Sqoop을 사용하여 Azure SQL Database에 데이터를 로드합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- **HDInsight에 대해 구성된 Azure Data Lake Storage Gen2 스토리지 계정**

    [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.

- **HDInsight의 Linux 기반 Hadoop 클러스터**

    [빠른 시작: Azure Portal을 사용하여 Azure HDInsight에서 Apache Hadoop 및 Apache Hive 시작](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)을 참조하세요.

- **Azure SQL Database**: Azure SQL Database를 대상 데이터 저장소로 사용합니다. SQL Database에 데이터베이스가 없는 경우 [Azure Portal의 Azure SQL Database에서 데이터베이스 만들기](../../azure-sql/database/single-database-create-quickstart.md)를 참조하세요.

- **Azure CLI**: 아직 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

- **SSH(보안 셸) 클라이언트**: 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="download-the-flight-data"></a>비행 데이터 다운로드

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)로 이동합니다.

2. 페이지에서 다음 값을 선택합니다.

   | Name | 값 |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | 필드 |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

   기타 모든 필드를 지웁니다.

3. **다운로드** 를 선택합니다. 선택한 데이터 필드와 함께 .zip 파일을 가져옵니다.

## <a name="extract-and-upload-the-data"></a>데이터 추출 및 업로드

이 섹션에서는 HDInsight 클러스터에 데이터를 업로드한 다음, Data Lake Storage Gen2 계정에 해당 데이터를 복사합니다.

1. 명령 프롬프트를 열고 다음 보안 복사(Scp) 명령을 사용하여 HDInsight 클러스터 헤드 노드에 .zip 파일을 업로드합니다.

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   - `<file-name>` 자리 표시자를 .zip 파일 이름으로 바꿉니다.
   - `<ssh-user-name>` 자리 표시자를 HDInsight 클러스터의 SSH 로그인으로 바꿉니다.
   - `<cluster-name>` 자리 표시자를 HDInsight 클러스터의 이름으로 바꿉니다.

   SSH 로그인을 인증하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다.

   공용 키를 사용하는 경우 `-i` 매개 변수를 사용하고 프라이빗 키와 일치하는 경로를 지정합니다. 예들 들어 `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`입니다.

2. 업로드를 완료한 후에 SSH를 사용하여 클러스터에 연결합니다. 명령 프롬프트에서 다음 명령을 입력합니다.

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. 다음 명령을 사용하여 .zip 파일의 압축을 풉니다.

   ```bash
   unzip <file-name>.zip
   ```

   이 명령은 **.csv** 파일을 추출합니다.

4. 다음 명령을 사용하여 Data Lake Storage Gen2 컨테이너를 만듭니다.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   `<container-name>` 자리 표시자를 컨테이너에 지정할 이름으로 바꿉니다.

   `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

5. 다음 명령을 사용하여 디렉터리를 만듭니다.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. 다음 명령을 사용하여 *.csv* 파일을 다음 디렉터리에 복사합니다.

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   파일 이름에 공백이나 특수 문자가 포함된 경우 파일 이름 주위에 따옴표를 사용합니다.

## <a name="transform-the-data"></a>데이터 변환

이 섹션에서는 Beeline을 사용하여 Apache Hive 작업을 실행합니다.

Apache Hive 작업의 일부로 .csv 파일의 데이터를 **delays** 라는 Apache Hive 테이블로 가져옵니다.

1. HDInsight 클러스터에 대해 이미 있는 SSH 프롬프트에서 다음 명령을 사용하여 **flightdelays.hql** 이라는 새 파일을 만들고 편집합니다.

   ```bash
   nano flightdelays.hql
   ```

2. `<container-name>` 및 `<storage-account-name>` 자리 표시자를 컨테이너 및 스토리지 계정 이름으로 바꿔서 다음 텍스트를 수정합니다. SHIFT 키를 누른 상태에서 마우스 오른쪽 단추를 클릭하여 텍스트를 복사하고 nano 콘솔에 붙여넣습니다.

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. CTRL+X 키를 누르고 프롬프트가 표시되면 `Y`를 입력하여 파일을 저장합니다.

4. Hive를 시작하고 `flightdelays.hql` 파일을 실행하려면 다음 명령을 사용합니다.

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. `flightdelays.hql` 스크립트 실행이 완료된 후에 다음 명령을 사용하여 대화형 Beeline 세션을 엽니다.

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
   ```

6. `jdbc:hive2://localhost:10001/>` 프롬프트를 수신하면, 다음 쿼리를 사용하여 가져온 비행 지연 데이터에서 데이터를 검색합니다.

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

   이 쿼리는 평균 지연 시간과 함께 날씨 지연이 발생된 도시 목록이 검색된 후 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`에 저장됩니다. 나중에 Sqoop는 이 위치에서 데이터를 읽어 Azure SQL Database로 내보냅니다.

7. Beeline을 종료하려면 프롬프트에 `!quit`을 입력합니다.

## <a name="create-a-sql-database-table"></a>SQL Database 테이블 만들기

이 작업을 수행하려면 SQL Database의 서버 이름이 필요합니다. 다음 단계를 완료하여 서버 이름을 찾습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

2. **SQL Databases** 를 선택합니다.

3. 사용하려는 데이터베이스의 이름을 필터링합니다. 서버 이름은 **서버 이름** 열에 나열됩니다.

4. 사용할 데이터베이스의 이름을 필터링합니다. 서버 이름은 **서버 이름** 열에 나열됩니다.

    ![Azure SQL 서버 세부 정보 가져오기](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL 서버 세부 정보 가져오기")

    여러 가지 방법으로 SQL Database에 연결하고 테이블을 생성할 수 있습니다. 다음 단계는 HDInsight 클러스터의 [FreeTDS](https://www.freetds.org/)를 사용합니다.

5. FreeTDS를 설치하려면 클러스터에 대한 SSH 연결에서 다음 명령을 사용합니다.

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. 설치가 완료되면 다음 명령을 사용하여 SQL Database에 연결합니다.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```

   - `<server-name>` 자리 표시자를 논리 SQL 서버 이름으로 바꿉니다.

   - `<admin-login>` 자리 표시자를 SQL Database의 관리 로그인으로 바꿉니다.

   - `<database-name>` 자리 표시자를 데이터베이스 이름으로 바꿉니다.

   메시지가 표시되면 SQL Database 관리자 로그인의 암호를 입력합니다.

   다음 텍스트와 비슷한 출력이 표시됩니다.

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. `1>` 프롬프트에서 다음 명령문을 입력합니다.

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. `GO` 문을 입력하면 이전 문이 평가됩니다.

   이 쿼리는 클러스터형 인덱스가 있는 **delays** 라는 테이블을 만듭니다.

9. 다음 쿼리를 사용하여 테이블이 생성되었는지 확인합니다.

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   다음 텍스트와 유사하게 출력됩니다.

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. `exit` at the `1>`를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="export-and-load-the-data"></a>데이터 내보내기 및 로드

이전 섹션에서는 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` 위치에서 변환된 데이터를 복사했습니다. 이 섹션에서는 Sqoop을 사용하여 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`의 데이터를 Azure SQL Database에 만든 테이블로 내보냅니다.

1. 다음 명령을 사용하여 Sqoop이 SQL Database를 볼 수 있는지 확인합니다.

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   이 명령은 **delays** 테이블을 만든 데이터베이스를 포함한 데이터베이스 목록을 반환합니다.

2. 다음 명령을 사용하여 **hivesampletable** 테이블에서 **delays** 테이블로 데이터를 내보냅니다.

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop은 **delays** 테이블을 포함하는 데이터베이스에 연결한 다음, `/tutorials/flightdelays/output` 디렉터리에서 **delays** 테이블로 데이터를 내보냅니다.

3. `sqoop` 명령이 완료되면 tsql 유틸리티를 사용하여 데이터베이스에 연결합니다.

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. 다음 명령문을 사용하여 데이터가 **delays** 테이블로 내보내졌는지 확인합니다.

   ```sql
   SELECT * FROM delays
   GO
   ```

   테이블에 데이터 목록이 표시됩니다. 테이블은 도시 이름 및 해당 도시에 대한 평균 비행 지연 시간을 포함합니다.

5. `exit`를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에 사용된 모든 리소스는 이미 있는 것들입니다. 따라서 정리가 필요 없습니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 데이터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
