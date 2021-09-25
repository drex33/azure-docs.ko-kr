---
title: Azure Data Box를 사용하여 온-프레미스 HDFS 저장소에서 Azure Storage로 마이그레이션
description: Data Box 디바이스를 사용하여 온-프레미스 HDFS 저장소의 데이터를 Azure Storage(Blob Storage 또는 Data Lake Storage Gen2)로 마이그레이션합니다.
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 2ac820aa7606ae310c5a7a3fd28869f34f846aaf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621189"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Azure Data Box를 사용하여 온-프레미스 HDFS 저장소에서 Azure Storage로 마이그레이션

Data Box 디바이스를 사용하여 Hadoop 클러스터의 온-프레미스 HDFS 저장소에서 Azure Storage(Blob Storage 또는 Data Lake Storage Gen2)로 데이터를 마이그레이션할 수 있습니다. Data Box Disk, 80TB Data Box 또는 770TB Data Box Heavy 중에서 선택할 수 있습니다.

이 문서에서는 다음 작업을 완료하는 방법을 보여 줍니다.

> [!div class="checklist"]
> - 데이터 마이그레이션을 준비합니다.
> - 데이터를 Data Box Disk, Data Box 또는 Data Box Heavy 디바이스로 복사합니다.
> - 디바이스를 Microsoft에 다시 배송합니다.
> - 파일 및 디렉터리에 대한 액세스 권한 적용(Data Lake Storage Gen2에만 해당)

## <a name="prerequisites"></a>사전 요구 사항

마이그레이션을 완료하려면 다음 항목이 필요합니다.

- Azure Storage 계정.

- 원본 데이터를 포함하는 온-프레미스 Hadoop 클러스터

- [Azure Data Box 디바이스](https://azure.microsoft.com/services/storage/databox/)

  - [Data Box](../../databox/data-box-deploy-ordered.md) 또는 [Data Box Heavy](../../databox/data-box-heavy-deploy-ordered.md)를 주문합니다.

  - [Data Box](../../databox/data-box-deploy-set-up.md) 또는 [Data Box Heavy](../../databox/data-box-heavy-deploy-set-up.md)를 온-프레미스 네트워크에 케이블로 연결합니다.

준비가 되면 시작해보겠습니다.

## <a name="copy-your-data-to-a-data-box-device"></a>Data Box 디바이스에 데이터 복사

데이터가 단일 Data Box 디바이스에 맞는 경우 데이터를 Data Box 디바이스로 복사합니다.

데이터 크기가 Data Box 디바이스의 용량을 초과하는 경우에는 [선택적 절차를 사용하여 데이터를 여러 Data Box 디바이스로 분할](#appendix-split-data-across-multiple-data-box-devices)한 다음 이 단계를 수행합니다.

온-프레미스 HDFS 저장소에서 Data Box 장치로 데이터를 복사하려면 몇 가지를 설정하고 [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 도구를 사용합니다.

다음 단계에 따라 Blob/개체 스토리지의 REST API를 통해 데이터를 Data Box 디바이스에 복사합니다. REST API 인터페이스는 디바이스가 클러스터에 HDFS 저장소로 표시되도록 합니다.

1. REST를 통해 데이터를 복사하기 전에 Data Box 또는 Data Box Heavy에서 REST 인터페이스에 연결할 보안 및 연결 기본 형식을 식별합니다. Data Box의 로컬 웹 UI에 로그인하고 **연결 및 복사** 페이지로 이동합니다. 디바이스의 Azure Storage 계정에 대해 **액세스 설정** 에서 **REST** 를 찾아 선택합니다.

    ![“연결 및 복사” 페이지](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 스토리지 계정 액세스 및 데이터 업로드 대화 상자에서 **Blob 서비스 엔드포인트** 및 **스토리지 계정 키** 를 복사합니다. Blob 서비스 엔드포인트에서 `https://` 및 후행 슬래시를 생략합니다.

    이 경우 엔드포인트는 `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`입니다. 사용할 URI의 호스트 부분은 `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`입니다. 예를 보려면 [http를 통해 REST에 연결](../../databox/data-box-deploy-copy-data-via-rest.md)하는 방법을 참조하세요.

     !["스토리지 계정 액세스 및 데이터 업로드" 대화 상자](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 엔드포인트와 Data Box 또는 Data Box Heavy 노드 IP 주소를 각 노드의 `/etc/hosts`에 추가합니다.

    ```
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS에 다른 메커니즘을 사용하는 경우 Data Box 엔드포인트를 확인할 수 있는지 확인해야 합니다.

4. 셸 변수 `azjars`를 `hadoop-azure` 및 `azure-storage` jar 파일의 위치로 설정합니다. 이러한 파일은 Hadoop 설치 디렉터리에서 찾을 수 있습니다.

    이러한 파일이 있는지 확인하려면 다음 명령을 사용합니다. `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. `<hadoop_install_dir>` 자리 표시자를 Hadoop을 설치한 디렉터리 경로로 바꿉니다. 정규화된 경로를 사용해야 합니다.

    예:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 데이터 복사에 사용하려는 스토리지 컨테이너를 만듭니다. 또한 이 명령의 일부로 대상 디렉터리를 지정해야 합니다. 이 시점에서 더미 대상 디렉터리가 될 수 있습니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    - `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 엔드포인트의 이름으로 바꿉니다.

    - `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

    - `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

    - `<destination_directory>` 자리 표시자를 데이터를 복사할 디렉터리 이름으로 바꿉니다.

6. list 명령을 실행하여 컨테이너 및 디렉터리를 만들었는지 확인합니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   - `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 엔드포인트의 이름으로 바꿉니다.

   - `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

   - `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

7. Hadoop HDFS의 데이터를 Data Box Blob Storage에서 이전에 만든 컨테이너로 복사합니다. 복사할 디렉터리를 찾을 수 없는 경우 이 명령은 자동으로 만듭니다.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    - `<blob_service_endpoint>` 자리 표시자를 Blob 서비스 엔드포인트의 이름으로 바꿉니다.

    - `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

    - `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

    - `<exlusion_filelist_file>` 자리 표시자를 파일 제외 목록을 포함하는 파일의 이름으로 바꿉니다.

    - `<source_directory>` 자리 표시자를 복사할 데이터가 포함된 디렉터리 이름으로 바꿉니다.

    - `<destination_directory>` 자리 표시자를 데이터를 복사할 디렉터리 이름으로 바꿉니다.

    `-libjars` 옵션은 `hadoop-azure*.jar` 및 종속 `azure-storage*.jar` 파일을 `distcp`에서 사용할 수 있도록 하는 데 사용됩니다. 이는 일부 클러스터에 대해 이미 발생할 수 있습니다.

    다음 예에서는 `distcp` 명령을 사용하여 데이터를 복사하는 방법을 보여 줍니다.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```

    복사 속도를 향상시키려면 다음을 수행합니다.

    - 매퍼 수를 변경해보세요. (위의 예는 `m` = 4 매퍼를 사용합니다.)

    - 여러 `distcp`를 병렬로 실행해 보세요.

    - 규모가 큰 파일은 규모가 작은 파일보다 성능이 더 좋습니다.

## <a name="ship-the-data-box-to-microsoft"></a>Microsoft에 Data Box 배송

Data Box 디바이스를 준비하고 Microsoft에 배송하려면 다음 단계를 수행합니다.

1. 먼저 [Data Box 또는 Data Box Heavy로 배송을 준비](../../databox/data-box-deploy-copy-data-via-rest.md)합니다.

2. 디바이스 준비가 완료되면 BOM 파일을 다운로드합니다. 나중에 이러한 BOM 또는 매니페스트 파일을 사용하여 Azure에 업로드된 데이터를 확인합니다.

3. 디바이스를 종료하고 케이블을 제거합니다.

4. UPS를 사용하여 픽업을 예약합니다.

    - Data Box 디바이스의 경우 [Data Box 배송](../../databox/data-box-deploy-picked-up.md)을 참조하세요.

    - Data Box Heavy 디바이스의 경우 [Data Box Heavy 배송](../../databox/data-box-heavy-deploy-picked-up.md)을 참조하세요.

5. Microsoft에서는 디바이스를 받은 후에 데이터 센터 네트워크에 연결되고 디바이스를 주문할 때 지정한 스토리지 계정에 데이터가 업로드됩니다. 모든 데이터가 Azure에 업로드되었는지 BOM 파일에 대해 확인합니다.

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>파일 및 디렉터리에 대한 액세스 권한 적용(Data Lake Storage Gen2에만 해당)

이미 Azure Storage 계정에 데이터가 있습니다. 이제 파일 및 디렉터리에 대한 액세스 권한을 적용합니다.

> [!NOTE]
> 이 단계는 Azure Data Lake Storage Gen2를 데이터 저장소로 사용하는 경우에만 필요합니다. 계층적 네임스페이스가 없는 Blob Storage 계정만 데이터 저장소로 사용하는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 계정에 대한 서비스 주체 만들기

서비스 주체를 만들려면 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

- 문서의 [애플리케이션을 역할에 할당](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

- 문서의 [로그인을 위한 값 가져오기](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 섹션에 있는 단계를 수행하는 경우 애플리케이션 ID 및 클라이언트 비밀 값을 텍스트 파일에 저장합니다. 곧 이 값들이 필요합니다.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>권한이 있는 복사된 파일의 목록 생성

온-프레미스 Hadoop 클러스터에서 다음 명령을 실행합니다.

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

이 명령은 해당 권한이 있는 복사된 파일의 목록을 생성합니다.

> [!NOTE]
> HDFS의 파일 수에 따라 이 명령을 실행하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>ID 목록을 생성하고 ADD(Azure Active Directory) ID에 매핑합니다.

1. `copy-acls.py` 스크립트를 다운로드합니다. 이 도움말의 [도우미 스크립트 다운로드 및 실행을 위한 에지 노드 설정](#download-helper-scripts) 섹션을 참조하세요.

2. 이 명령을 실행하여 고유한 ID 목록을 생성합니다.

   ```bash

   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   이 스크립트는 ADD 기반 ID에 맵핑해야 하는 ID가 포함된 `id_map.json` 파일을 생성합니다.

3. 텍스트 편집기에서 `id_map.json` 파일을 엽니다.

4. 파일에 나타나는 각 JSON 개체에 대해 AAD UPN(사용자 계정 이름) 또는 OID(ObjectId)의 `target` 속성을 적절한 매핑 ID를 사용하여 업데이트합니다. 작업을 완료한 후 파일을 저장합니다. 다음 단계에서 이 파일이 필요합니다.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>복사한 파일에 권한 적용 및 ID 매핑 적용

이 명령을 실행하여 Data Lake Storage Gen2 계정에 복사한 데이터에 권한을 적용합니다.

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

- `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

- `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

- `<application-id>` 및 `<client-secret>` 자리 표시자를 서비스 주체를 만들 때 수집한 애플리케이션 ID 및 클라이언트 비밀로 바꿉니다.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>부록: 여러 Data Box 디바이스에서 데이터 분할

데이터를 Data Box 디바이스로 이동하기 전에 일부 도우미 스크립트를 다운로드하고, 데이터가 Data Box 디바이스에 맞게 구성되어 있는지 확인하고, 불필요한 파일을 제외해야 합니다.

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>도우미 스크립트를 다운로드하고 에지 노드를 설정하여 실행합니다.

1. 온-프레미스 Hadoop 클러스터의 에지 또는 헤드 노드에서 다음 명령을 실행합니다.

   ```bash

   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   이 명령은 도우미 스크립트가 포함된 GitHub 리포지토리를 복제합니다.

2. 로컬 컴퓨터에 [jq](https://stedolan.github.io/jq/) 패키지가 설치되어 있는지 확인합니다.

   ```bash

   sudo apt-get install jq
   ```

3. [요청](https://2.python-requests.org/en/master/) python 패키지를 설치합니다.

   ```bash

   pip install requests
   ```

4. 필요한 스크립트에 대한 실행 권한을 설정합니다.

   ```bash

   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>데이터가 Data Box 디바이스에 맞게 구성되어 있는지 확인

데이터 크기가 단일 Data Box 디바이스의 크기를 초과하는 경우 여러 Data Box 디바이스에 저장할 수 있는 그룹으로 파일을 분할할 수 있습니다.

데이터가 단일 Data Box 디바이스의 크기를 초과하지 않으면 다음 섹션으로 진행할 수 있습니다.

1. 상승된 권한으로 이전 섹션의 지침에 따라 다운로드한 `generate-file-list` 스크립트를 실행합니다.

   명령 매개 변수에 대한 설명은 다음과 같습니다.

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 작업에 액세스할 수 있도록 생성된 파일 목록을 HDFS에 복사합니다.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>불필요한 파일 제외

DisCp 작업에서 일부 디렉터리를 제외해야 합니다. 예를 들어 클러스터를 계속 실행하는 상태 정보를 포함하는 디렉터리를 제외합니다.

DistCp 작업을 시작하려는 온-프레미스 Hadoop 클러스터에서 제외하려는 디렉터리 목록을 지정하는 파일을 만듭니다.

예를 들면 다음과 같습니다.

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2가 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. 자세한 내용은 [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
