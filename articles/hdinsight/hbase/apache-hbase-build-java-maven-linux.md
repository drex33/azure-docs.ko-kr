---
title: Apache Maven을 사용하여 Azure HDInsight용 Java HBase 클라이언트 빌드
description: Apache Maven을 사용하여 Java 기반 Apache HBase 애플리케이션을 빌드한 다음 Azure HDInsight의 HBase에 배포하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 3d37c60159efbcffe87defbc4ea2b0ac0d02070c
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286596"
---
# <a name="build-java-applications-for-apache-hbase"></a>Apache HBase에 대한 Java 애플리케이션 빌드

Java에서 [Apache HBase](https://hbase.apache.org/) 애플리케이션을 만드는 방법을 알아봅니다. 그런 다음 Azure HDInsight의 HBase에서 애플리케이션을 사용합니다.

이 문서에 나온 단계에서는 [Apache Maven](https://maven.apache.org/)을 사용하여 프로젝트를 만들고 빌드합니다. Maven은 Java 프로젝트용 소프트웨어, 문서화 및 보고를 빌드할 수 있는 소프트웨어 프로젝트 관리 및 종합 도구입니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache HBase 클러스터. [Apache HBase 시작](./apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* [JDK(Java Developer Kit) 버전 8](/azure/developer/java/fundamentals/java-support-on-azure).

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html)[Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* PowerShell을 사용하는 경우 [AZ 모듈](/powershell/azure/)이 필요합니다.

* 텍스트 편집기. 이 문서는 Microsoft 메모장을 사용합니다.

## <a name="test-environment"></a>테스트 환경

이 문서에 사용된 환경은 Windows 10을 실행하는 컴퓨터였습니다.  명령은 명령 프롬프트에서 실행되었으며 다양한 파일이 메모장을 사용하여 편집되었습니다. 사용자 환경에 맞게 수정합니다.

명령 프롬프트에서 아래 명령을 입력하여 작업 환경을 만듭니다.

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

1. 다음 명령을 입력하여 **hbaseapp** 이라는 Maven 프로젝트를 만듭니다.

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    이 명령은 기본 Maven 프로젝트를 포함하는 현재 위치에 `hbaseapp`라는 디렉터리를 만듭니다. 두 번째 명령은 작업 디렉터리를 `hbaseapp`으로 변경합니다. 세 번째 명령은 나중에 사용될 새 디렉터리 `conf`를 만듭니다. `hbaseapp` 디렉터리에는 다음과 같은 항목이 포함됩니다.

    * `pom.xml`: [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)(프로젝트 개체 모델)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.
    * `src\main\java\com\microsoft\examples`: 애플리케이션 코드를 포함합니다.
    * `src\test\java\com\microsoft\examples`: 애플리케이션에 대한 테스트를 포함합니다.

2. 생성된 예제 코드를 제거합니다. 아래의 명령을 입력하여 생성된 테스트 및 애플리케이션 파일 `AppTest.java` 및 `App.java`를 삭제합니다.

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>프로젝트 개체 모델 업데이트

pom.xml 파일에 대한 전체 참조를 보려면 https://maven.apache.org/pom.html을 참조하세요.  아래 명령을 입력하여 `pom.xml`을 엽니다.

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>종속성 추가

`pom.xml`에서 `<dependencies>` 섹션에 다음 텍스트를 추가합니다.

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

이 섹션에서는 프로젝트에 **hbase-client** 및 **phoenix-core** 구성 요소가 필요하다는 점을 나타냅니다. 컴파일 시 이러한 종속성이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 중앙 리포지토리 검색](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar)을 사용하여 이 종속성에 대한 자세한 정보를 확인할 수 있습니다.

> [!IMPORTANT]  
> hbase-client의 버전 번호는 HDInsight 클러스터를 제공하는 Apache HBase 버전과 일치해야 합니다. 다음 표를 사용하여 올바른 버전 번호를 찾으세요.

| HDInsight 클러스터 버전 | 사용할 Apache HBase 버전 |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

HDInsight 버전 및 구성 요소에 대한 자세한 내용은 [HDInsight에서 사용할 수 있는 다양한 Apache Hadoop 구성 요소](../hdinsight-component-versioning.md)를 참조하세요.

### <a name="build-configuration"></a>빌드 구성

Maven 플러그 인을 사용하면 프로젝트의 빌드 단계를 사용자 지정할 수 있습니다. 이 섹션은 플러그 인, 리소스 및 다른 빌드 구성 옵션을 추가하는 데 사용됩니다.

`pom.xml` 파일에 다음 코드를 추가한 다음, 파일을 저장하고 닫습니다. 이 텍스트는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어 `</dependencies>`와 `</project>` 사이에 있어야 합니다.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

이 섹션은 HBase에 대한 구성 정보를 포함하는 리소스(`conf/hbase-site.xml`)를 구성합니다.

> [!NOTE]  
> 또한 코드를 통해 구성 값을 설정할 수도 있습니다. `CreateTable` 예제에서 주석을 참조하세요.

이 섹션에서는 [Apache Maven 컴파일러 플러그 인](https://maven.apache.org/plugins/maven-compiler-plugin/) 및 [Apache Maven 음영 플러그 인](https://maven.apache.org/plugins/maven-shade-plugin/)도 구성합니다. 컴파일러 플러그 인은 토폴로지를 컴파일하는 데 사용됩니다. 음영 플러그 인은 Maven으로 빌드된 JAR 패키지에서 라이선스 중복을 방지하는 데 사용됩니다. 이 플러그 인은 HDInsight 클러스터에서 런타임에 "중복 라이선스 파일" 오류가 발생하지 않도록 하는 데 사용됩니다. `ApacheLicenseResourceTransformer` 구현에서 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

또한 maven-shade-plugin은 애플리케이션에 필요한 모든 종속성을 포함하는 uber jar도 생성합니다.

### <a name="download-the-hbase-sitexml"></a>hbase-site.xml 다운로드

다음 명령을 사용하여 HBase 클러스터에서 `conf` 디렉터리로 HBase 구성을 복사합니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꾼 후, 다음 명령을 입력합니다.

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>애플리케이션 만들기

### <a name="implement-a-createtable-class"></a>CreateTable 클래스 구현

아래 명령을 입력하여 새 파일 `CreateTable.java`를 만들고 엽니다. 프롬프트에서 **예** 를 선택하여 새 파일을 만듭니다.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

그런 다음, 아래 java 코드를 복사하여 새 파일에 붙여넣습니다. 그 다음 파일을 닫습니다.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

이 코드는 `CreateTable` 클래스이며, `people`이라는 테이블을 만들고 미리 정의된 사용자로 채웁니다.

### <a name="implement-a-searchbyemail-class"></a>SearchByEmail 클래스 구현

아래 명령을 입력하여 새 파일 `SearchByEmail.java`를 만들고 엽니다. 프롬프트에서 **예** 를 선택하여 새 파일을 만듭니다.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

그런 다음, 아래 java 코드를 복사하여 새 파일에 붙여넣습니다. 그 다음 파일을 닫습니다.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail` 클래스를 사용하여 이메일 주소로 행을 쿼리할 수 있습니다. 정규식 필터를 사용하므로, 이 클래스를 사용할 때 문자열 또는 정규식을 제공할 수 있습니다.

### <a name="implement-a-deletetable-class"></a>DeleteTable 클래스 구현

아래 명령을 입력하여 새 파일 `DeleteTable.java`를 만들고 엽니다. 프롬프트에서 **예** 를 선택하여 새 파일을 만듭니다.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

그런 다음, 아래 java 코드를 복사하여 새 파일에 붙여넣습니다. 그 다음 파일을 닫습니다.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable` 클래스는 `CreateTable` 클래스에서 생성된 테이블을 비활성화하고 제거하여 이 예제에서 만든 HBase 테이블을 정리합니다.

## <a name="build-and-package-the-application"></a>애플리케이션 빌드 및 패키지화

1. `hbaseapp` 디렉터리에서 다음 명령을 사용하여 애플리케이션을 포함하는 JAR 파일을 빌드합니다.

    ```cmd
    mvn clean package
    ```

    이 명령은 .jar 파일에 애플리케이션을 빌드하고 패키지합니다.

2. 명령이 완료되면 `hbaseapp/target` 디렉터리는 `hbaseapp-1.0-SNAPSHOT.jar`라는 파일을 포함합니다.

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` 파일은 uber jar입니다. 애플리케이션을 실행 는 데 필요한 모든 종속성을 포함합니다.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR 업로드 및 작업 실행(SSH)

다음 단계에서는 `scp`를 사용하여 HDInsight 클러스터에서 Apache HBase의 기본 헤드 노드에 JAR을 복사합니다. 그런 후 `ssh` 명령은 클러스터에 연결하고 헤드 노드에서 직접 예제를 실행하는 데 사용됩니다.

1. jar을 클러스터에 업로드합니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꾼 후, 다음 명령을 입력합니다.

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. HBase 클러스터에 연결합니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꾼 후, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Java 애플리케이션을 사용하여 HBase 테이블을 만들려면 열린 ssh 연결에서 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    이 명령은 **people** 이라는 HBase 테이블을 만들고 데이터로 채웁니다.

4. 테이블에 저장된 이메일 주소를 검색하려면 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    다음과 같은 결과가 표시됩니다.

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. 테이블을 삭제하려면 다음 명령을 사용합니다.

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>JAR 업로드 및 작업 실행(PowerShell)

다음 단계에서는 Azure PowerShell [AZ 모듈](/powershell/azure/new-azureps-module-az)을 사용하여 Apache HBase 클러스터용 기본 스토리지에 JAR을 업로드합니다. HDInsight cmdlet은 예제를 원격으로 실행하는 데 사용됩니다.

1. AZ 모듈을 설치 및 구성한 후 `hbase-runner.psm1`이라는 파일을 만듭니다. 이 파일의 내용으로 다음 텍스트를 사용합니다.

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    이 파일에는 다음 두 모듈이 포함됩니다.

   * **Add-HDInsightFile** - 클러스터에 파일을 업로드하는 데 사용합니다.
   * **Start-HBaseExample** - 이전에 생성한 클래스를 실행하는 데 사용합니다.

2. `hbase-runner.psm1` 파일을 `hbaseapp` 디렉터리에 저장합니다.

3. Azure PowerShell을 사용하여 모듈을 등록합니다. 새 Azure PowerShell 창을 열고 `CLUSTERNAME`을 클러스터 이름으로 바꾸어 아래 명령을 편집합니다. 그런 후, 다음 명령을 입력합니다.

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. 다음 명령을 사용하여 클러스터에 `hbaseapp-1.0-SNAPSHOT.jar`을 업로드합니다.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다. 이 명령은 `hbaseapp-1.0-SNAPSHOT.jar`을 클러스터용 기본 스토리지에서 `example/jars` 위치에 업로드합니다.

5. `hbaseapp`을 사용하는 테이블을 만들려면 다음 명령을 사용합니다.

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다.

    이 명령은 HDInsight 클러스터에 HBase의 **people** 이라는 테이블을 만듭니다. 이 명령은 콘솔 창에 출력을 표시하지 않습니다.

6. 테이블에서 항목을 검색하려면 다음 명령을 사용합니다.

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    메시지가 표시되면 클러스터 로그인(관리자) 이름 및 암호를 입력합니다.

    이 명령은 `SearchByEmail` 클래스를 사용하여 `contactinformation` 열 패밀리 및 `email` 열에 `contoso.com` 문자열이 포함된 모든 행을 검색합니다. 다음과 같은 결과가 표시됩니다.

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    `-emailRegex` 값에 **fabrikam.com** 을 사용하면 메일 필드에 **fabrikam.com** 을 포함하는 사용자가 반환됩니다. 검색 용어로 정규식을 사용할 수도 있습니다. 예를 들어, **^r** 은 'r' 문자로 시작하는 전자 메일 주소를 반환합니다.

7. 테이블을 삭제하려면 다음 명령을 사용합니다.

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Start-HBaseExample을 사용할 경우 결과가 없거나 예기치 않은 결과가 표시됨

`-showErr` 매개 변수를 사용하여 작업을 실행하는 동안 생성된 표준 오류(STDERR)을 확인합니다.

## <a name="next-steps"></a>다음 단계

[Apache HBase에서 SQLLine을 사용하는 방법 알아보기](apache-hbase-query-with-phoenix.md)