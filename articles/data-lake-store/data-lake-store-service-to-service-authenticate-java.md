---
title: 서비스-서비스 인증 - Data Lake Storage Gen2 – Java SDK
description: Java에서 Azure Active Directory 사용하여 Azure Data Lake Storage Gen2로 서비스 대 서비스 인증을 달성하는 방법을 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: normesta
ms.openlocfilehash: 8f36eaef8c84afd1010d6e4ab2714b0d538028b9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494553"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen2-using-java"></a>Java를 사용하여 Azure Data Lake Storage Gen2로 서비스 대 서비스 인증

> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

이 문서에서는 Java SDK를 사용하여 Azure Data Lake Storage Gen2로 서비스-서비스 인증을 수행하는 방법에 대해 알아봅니다. Java SDK를 사용하는 Data Lake Storage Gen2의 최종 사용자 인증은 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "웹" 애플리케이션 만들기**. Azure Active Directory 사용하여 Data Lake [Storage Gen2로 서비스 대 서비스 인증의](data-lake-store-service-to-service-authenticate-using-active-directory.md)단계를 완료해야 합니다.

* [Maven](https://maven.apache.org/install.html) 이 자습서에서는 빌드 및 프로젝트 종속성을 위해 Maven을 사용합니다. Maven 또는 Gradle과 같은 빌드 시스템을 사용하지 않고 빌드할 수 있지만 이러한 시스템을 사용하면 종속성을 훨씬 더 쉽게 관리할 수 있습니다.

* (선택 사항)[IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 또는 [Eclipse](https://www.eclipse.org/downloads/)나 유사한 IDE

## <a name="service-to-service-authentication"></a>서비스 간 인증

1. 명령줄에서 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)을 사용하거나 IDE에서 Maven 프로젝트를 만듭니다. IntelliJ를 사용하여 Java 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)를 참조하세요. Eclipse를 사용하여 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)를 참조하세요.

2. Maven **pom.xml** 파일에 다음 종속성을 추가합니다. **\</project>** 태그 앞에 다음 코드 조각을 추가합니다.

    ```xml
    <dependencies>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-storage-file-datalake</artifactId>
          <version>12.6.0</version>
      </dependency>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-identity</artifactId>
          <version>1.3.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    첫 번째 종속성은 Maven 리포지토리에서 Data Lake Storage Gen2 SDK( )를 사용하는 `azure-storage-file-datalake` 것입니다. 두 번째 종속성은 이 앱에 사용할 로깅 프레임워크( `slf4j-nop` )를 지정하는 것입니다. Data Lake Storage Gen2 SDK는 [log4j,](https://www.slf4j.org/) Java 로깅, 로그백 또는 로깅 없음과 같은 인기 있는 다양한 로깅 프레임워크 중에서 선택할 수 있는 slf4j 로깅 외관을 사용합니다. 이 예제에서 로깅을 비활성화하므로 **slf4j-nop** 바인딩을 사용합니다. 앱에서 다른 로깅 옵션을 사용하려면 [로깅을 위한 프로젝트 의존성 선언을](https://www.slf4j.org/manual.html#projectDep)참조하세요.

3. 애플리케이션에 다음 import 문을 추가합니다.

    ```java
    import com.azure.identity.ClientSecretCredential;
    import com.azure.identity.ClientSecretCredentialBuilder;
    import com.azure.storage.file.datalake.DataLakeDirectoryClient;
    import com.azure.storage.file.datalake.DataLakeFileClient;
    import com.azure.storage.file.datalake.DataLakeServiceClient;
    import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
    import com.azure.storage.file.datalake.DataLakeFileSystemClient;
    import com.azure.storage.file.datalake.models.ListPathsOptions;
    import com.azure.storage.file.datalake.models.PathAccessControl;
    import com.azure.storage.file.datalake.models.PathPermissions;
    ```

4. Java 앱에서 다음 조각을 사용하여 이전에 의 클래스 중 하나를 사용하여 만든 Active Directory 웹앱에 대한 토큰을 `StorageSharedKeyCredential` 얻습니다(다음 예제에서는 `credential` 사용). 토큰 공급자는 메모리에서 토큰을 가져오는 데 사용되는 자격 증명을 캐시하고 토큰이 만료될 경우 토큰을 자동으로 갱신합니다. 고객 코드에서 토큰을 가져오도록 자체 서브클래스를 만들 `StorageSharedKeyCredential` 수 있습니다. 하지만 지금은 SDK에 제공된 것을 사용하겠습니다.

    **FILL-IN-HERE** 를 Azure Active Directory 웹 애플리케이션의 실제 값으로 바꿉니다.

    ```java
    private static String clienttId = "FILL-IN-HERE";
    private static String tenantId = "FILL-IN-HERE";
    private static String clientSecret = "FILL-IN-HERE";
   
    ClientSecretCredential credential = new ClientSecretCredentialBuilder().clientId(clientId).tenantId(tenantId).clientSecret(clientSecret).build();
    ```

Data Lake Storage Gen2 SDK는 Data Lake Storage Gen2 계정과 대화하는 데 필요한 보안 토큰을 관리할 수 있는 편리한 방법을 제공합니다. 그러나 SDK는 이러한 메서드만 사용해야 하는 것은 아닙니다. [Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity) 또는 사용자 고유의 사용자 지정 코드를 사용하는 등 토큰을 가져오는 다른 방법도 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 최종 사용자 인증을 사용하여 Java SDK를 사용하여 Data Lake Storage Gen2로 인증하는 방법을 배웠습니다. 이제 Java SDK를 사용하여 Data Lake Storage Gen2로 작업하는 방법에 대해 다루는 다음 문서를 살펴볼 수 있습니다.

* [Java SDK를 사용하여 Data Lake Storage Gen2에서 데이터 작업](data-lake-store-get-started-java-sdk.md)
