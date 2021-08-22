---
title: Ambari REST API를 사용하여 Hadoop 모니터링 및 관리 - Azure HDInsight
description: Ambari를 사용하여 Azure HDInsight에서 Hadoop 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari REST API를 사용하는 방법을 배웁니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurepowershell
ms.date: 04/29/2020
ms.openlocfilehash: 0b8b57164d97007695d28efdccbf873d6fc71674
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299636"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리

[!INCLUDE [ambari-selector](includes/hdinsight-ambari-selector.md)]

Apache Ambari REST API를 사용하여 Azure HDInsight에서 Apache Hadoop 클러스터를 관리하고 모니터링하는 방법에 대해 알아봅니다.

## <a name="what-is-apache-ambari"></a>Apache Ambari란?

Apache Ambari는 [REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)가 지원되는 손쉬운 Web UI 사용을 제공하여 Apache Hadoop 클러스터의 관리 및 모니터링을 간소화합니다.  Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Hadoop 클러스터 [Linux에서 HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

* Windows 10의 Ubuntu에 있는 Bash.  이 문서의 예제에서는 Windows 10의 Bash 셸을 사용합니다. [Windows 10을 위한 Linux용 Windows 하위 시스템 설치 가이드](/windows/wsl/install-win10)에서 설치 단계를 참조하세요.  다른 [Unix 셸](https://www.gnu.org/software/bash/)도 작동합니다.  약간의 수정을 거치면 예제도 Windows 명령 프롬프트에서 작업할 수 있습니다.  또는 Windows PowerShell을 사용할 수 있습니다.

* 간단한 jq 명령줄 JSON 프로세서.  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)을 참조하세요.

* Windows PowerShell.  또는 Bash를 사용할 수 있습니다.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Ambari Rest API에 대한 기본 URI(Uniform Resource Identifier)

 HDInsight의 Ambari REST API에 대한 기본 URI(Uniform Resource Identifier)는 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`입니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.  URI의 클러스터 이름은 **대/소문자를 구분** 합니다.  URI(`CLUSTERNAME.azurehdinsight.net`)의 FQDN(정규화된 도메인 이름) 부분에 있는 클러스터 이름은 대/소문자를 구분하지 않지만 URI의 다른 항목은 대/소문자를 구분합니다.

## <a name="authentication"></a>인증

HTTPS를 요구하는 HDInsight에서 Ambari로 연결 클러스터 만들기 중 입력한 관리자 계정 이름(기본값은 **admin**) 및 암호를 사용합니다.

Enterprise Security Package 클러스터의 경우 `admin` 대신 `username@domain.onmicrosoft.com`과 같이 정규화된 사용자 이름을 사용합니다.

## <a name="examples"></a>예

### <a name="setup-preserve-credentials"></a>설치(자격 증명 유지)

각 예제에 대해 자격 증명을 다시 입력할 필요가 없도록 자격 증명을 유지합니다.  클러스터 이름은 별도의 단계에서 유지됩니다.

**A. Bash**  
`PASSWORD`를 실제 암호로 대체하여 아래 스크립트를 편집합니다.  그런 후 명령을 입력합니다.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>대/소문자가 올바르게 입력된 클러스터 이름을 식별합니다.

클러스터 이름의 실제 대/소문자 구분은 예상과 다를 수 있습니다.  여기의 단계는 실제 대/소문자 구분을 표시한 다음 모든 이후 예제에서 이를 변수에 저장합니다.

아래 스크립트를 편집하여 `CLUSTERNAME`을 클러스터 이름으로 바꿉니다. 그런 후 명령을 입력합니다. (FQDN의 클러스터 이름은 대/소문자를 구분하지 않습니다.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>JSON 데이터 구문 분석

다음 예제에서는 [jq](https://stedolan.github.io/jq/) 또는 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json)을 사용하여 JSON 응답 문서를 구문 분석하고 결과 중에서 `health_report` 정보만 표시합니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>클러스터 노드의 FQDN 가져오기

클러스터 노드의 FQDN(정규화된 도메인 이름)에 대해 알아야 할 수도 있습니다. 다음 예제를 사용하여 클러스터의 다양한 노드에 대한 FQDN을 쉽게 검색할 수 있습니다.

**모든 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**헤드 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**작업자 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper 노드**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>클러스터 노드의 내부 IP 주소 가져오기

이 섹션의 예제에서 반환된 IP 주소는 인터넷을 통해 직접 액세스할 수 없습니다. HDInsight 클러스터를 포함하는 Azure Virtual Network 내에서만 액세스할 수 있습니다.

HDInsight 및 가상 네트워크로 작업하는 방법에 대한 자세한 내용은 [HDInsight에 대한 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md)을 참조하세요.

IP 주소를 찾으려면 클러스터 노드의 내부 FQDN(정규화된 도메인 이름)을 알아야 합니다. FQDN을 알고 있으므로 호스트의 IP 주소를 얻을 수 있습니다. 다음 예제에서는 먼저 Ambari를 쿼리하여 모든 호스트 노드의 FQDN을 알아낸 다음 다시 Ambari를 쿼리하여 각 호스트의 IP 주소를 알아냅니다.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>기본 스토리지 가져오기

HDInsight 클러스터는 Azure Storage 계정 또는 Data Lake Storage를 기본 스토리지로 사용해야 합니다. 클러스터를 만든 후 Ambari를 사용하여 이 정보를 검색할 수 있습니다. 예를 들어 HDInsight 외부 컨테이너에 데이터를 읽고 쓰려는 경우가 여기에 해당합니다.

다음 예제에서는 클러스터에서 기본 스토리지 구성을 검색합니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> 이러한 예제는 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후에 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 합니다.

반환 값은 다음 예제 중 하나와 유사합니다.

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 이 값은 클러스터에서 기본 스토리지에 Azure Storage 계정을 사용하고 있음을 나타냅니다. `ACCOUNTNAME` 값은 스토리지 계정의 이름입니다. `CONTAINER` 부분은 스토리지 계정에서 blob 컨테이너의 이름입니다. 이 컨테이너는 클러스터에 대한 HDFS 호환 스토리지의 루트입니다.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - 이 값은 클러스터가 기본 스토리지에 Azure Data Lake Storage Gen2를 사용하고 있음을 나타냅니다. `ACCOUNTNAME` 및 `CONTAINER` 값은 앞에서 언급한 Azure Storage에서 동일한 의미를 갖습니다.

* `adl://home` - 이 값은 클러스터가 기본 스토리지에 Azure Data Lake Storage Gen1을 사용하고 있음을 나타냅니다.

    Data Lake Storage 계정 이름을 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    반환 값은 `ACCOUNTNAME.azuredatalakestore.net`와 비슷합니다. 여기서 `ACCOUNTNAME`은 Data Lake Storage 계정의 이름입니다.

    Data Lake Storage 내에서 클러스터에 대한 스토리지를 포함하는 디렉터리를 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    반환 값은 `/clusters/CLUSTERNAME/`과 비슷합니다. 이 값은 Data Lake Storage 계정 내의 경로입니다. 이 경로는 클러스터에 대한 HDFS 호환 파일 시스템의 루트입니다.  

> [!NOTE]  
> [Azure PowerShell](/powershell/azure/)에서 제공하는 [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet 또한 클러스터에 대한 스토리지 정보를 반환합니다.

### <a name="get-all-configurations"></a>모든 구성 가져오기

클러스터에 사용할 수 있는 구성을 가져옵니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

이 예제는 설치된 구성 요소에 대한 현재 구성을 포함하는 JSON 문서를 반환합니다. *태그* 값을 참조하세요. 다음 예제는 Spark 클러스터 형식에서 반환된 데이터에서 발췌한 것입니다.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>특정 구성 요소에 대한 구성 가져오기

관심 있는 구성 요소에 대한 구성을 가져옵니다. 다음 예제에서는 이전 요청에서 반환된 태그 값으로 `INITIAL`을 바꿉니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

이 예제는 `livy2-conf` 구성 요소에 대한 현재 구성을 포함하는 JSON 문서를 반환합니다.

### <a name="update-configuration"></a>구성 업데이트

1. `newconfig.json`를 만듭니다.  
   수정한 다음 아래 명령을 입력합니다.

   * `livy2-conf`를 새 구성 요소로 대체합니다.
   * [모든 구성 가져오기](#get-all-configurations)에서 `INITIAL`을 `tag`에 대해 검색된 실제 값으로 대체합니다.

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShell 스크립트는 [jq](https://stedolan.github.io/jq/)를 사용합니다.  아래 `C:\HD\jq\jq-win64`를 편집하여 [jq](https://stedolan.github.io/jq/)의 실제 경로와 버전을 반영합니다.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq는 HDInsight에서 검색한 데이터를 새 구성 템플릿으로 반환하는 데 사용됩니다. 특히, 이러한 예제에서는 다음 작업을 수행합니다.

   * 문자열 "version" 및 날짜를 포함하는 고유 값을 만듭니다. 이 값은 `newtag`에 저장됩니다.

   * 새 구성의 루트 문서를 만듭니다.

   * `.items[]` 배열의 내용을 가져와서 **desired_config** 요소에 추가합니다.

   * `href`, `version`, and `Config` 요소는 새 구성을 제출하는 데 필요하지 않으므로 삭제합니다.

   * 값이 `version#################`인 `tag` 요소를 추가합니다. 숫자 부분은 현재 날짜를 기반으로 합니다. 각 구성에 고유한 태그가 있어야 합니다.

     마지막으로 데이터가 `newconfig.json` 문서에 저장됩니다. 문서 구조는 다음 예제와 유사하게 표시되어야 합니다.

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. `newconfig.json`을 편집합니다.  
   `newconfig.json` 문서를 열고 `properties` 개체의 값을 수정/추가합니다. 다음 예제는 `"livy.server.csrf_protection.enabled"` 값을 `"true"`에서 `"false"`로 변경합니다.

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    수정을 완료했으면 파일을 저장합니다.

3. `newconfig.json`을 제출합니다.  
   다음 명령을 사용하여 업데이트된 구성을 Ambari에 제출합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    이러한 명령은 **newconfig.json** 파일의 내용을 새 구성으로 클러스터에 제출합니다. 이 요청은 JSON 문서를 반환합니다. 이 문서의 **versionTag** 요소는 제출한 버전과 일치해야 하며, **configs** 개체에는 요청한 구성 변경 내용이 포함됩니다.

### <a name="restart-a-service-component"></a>서비스 구성 요소 다시 시작

이제 새 구성을 적용하려면 먼저 Spark 서비스를 다시 시작해야 한다는 메시지가 Ambari 웹 UI에 표시됩니다. 다음 단계를 사용하여 서비스를 다시 시작합니다.

1. 다음을 사용하여 Spark2 서비스에 대한 유지 관리 모드를 사용하도록 설정합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. 유지 관리 모드를 확인합니다.  

    이러한 명령은 서버에 JSON 문서를 보내 유지 관리 모드를 켭니다. 이제 다음 요청을 사용하여 서비스가 유지 관리 모드인지 확인할 수 있습니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    반환 값은 `ON`입니다.

3. 그런 후 다음을 사용하여 Spark2 서비스를 해제합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    응답은 다음 예제와 유사합니다.

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > 이 URI에서 반환된 `href` 값은 클러스터 노드의 내부 IP 주소를 사용합니다. 클러스터 외부에서 이를 사용하려면 `10.0.0.18:8080` 부분을 클러스터의 FQDN으로 바꿉니다.  

4. 요청을 확인합니다.  
    `29`를 이전 단계에서 반환된 `id`의 실제 값으로 대체하여 아래 명령을 편집합니다.  다음 명령은 요청의 상태를 검색합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    응답 `COMPLETED`는 요청이 완료되었음을 나타냅니다.

5. 이전 요청이 완료되면 다음을 사용하여 Spark2 서비스를 시작합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    이제 서비스는 새 구성을 사용합니다.

6. 마지막으로, 다음을 사용하여 유지 관리 모드를 해제합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>다음 단계

REST API의 모든 참조 문서를 보려면 [Apache Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.  [사용자에게 Apache Ambari Views에 대한 권한 부여](./hdinsight-authorize-users-to-ambari.md)도 참조하세요.
