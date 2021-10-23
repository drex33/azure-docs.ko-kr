---
title: Metrics Advisor에 서로 다른 데이터 원본 연결
titleSuffix: Azure Cognitive Services
description: Metrics Advisor에 다른 데이터 피드 추가
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 9d5d111e86623b3de63bc9aa5d77f5a14c37fabd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216773"
---
# <a name="how-to-connect-different-data-sources"></a>방법: 여러 데이터 원본 연결

이 문서를 사용하여 다양한 유형의 데이터 원본을 Azure Metrics Advisor에 연결하기 위한 설정 및 요구 사항을 찾을 수 있습니다. Metrics Advisor에서 데이터를 사용하는 방법에 대한 자세한 내용은 [데이터 온보딩](how-tos/onboard-your-data.md)을 참조하세요. 

## <a name="supported-authentication-types"></a>지원되는 인증 형식

| 인증 형식 | Description |
| ---------------------|-------------|
|**기본** | 데이터 원본에 액세스하기 위한 기본 매개 변수를 제공할 수 있어야 합니다. 예를 들어, 연결 문자열 또는 암호를 사용할 수 있습니다. 데이터 피드 관리자는 이러한 자격 증명을 볼 수 있습니다. |
| **Azure 관리 ID** | Azure 리소스에 대한 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD(Azure Active Directory)의 기능입니다. Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하여 Azure AD 인증을 지원하는 모든 서비스에 대해 인증할 수 있습니다.|
| **Azure SQL 연결 문자열**| Metrics Advisor에서 Azure SQL 연결 문자열을 자격 증명 엔터티로 저장하고 메트릭 데이터를 가져올 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다. |
| **Azure Data Lake Storage Gen2 공유 키**| 데이터 레이크 계정 키를 Metrics Advisor에 자격 증명 엔터티로 저장하고 메트릭 데이터를 가져올 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다.|
| **서비스 주체**| [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)를 Metrics Advisor에 자격 증명 엔터티로 저장하고 메트릭 데이터를 가져올 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다.|
| **키 자격 증명 모음의 서비스 주체**|[Key Vault의 서비스 주체](/azure-stack/user/azure-stack-key-vault-store-credentials)를 Metrics Advisor에 자격 증명 엔터티로 저장하고 메트릭 데이터를 가져올 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 자격 증명을 볼 수 있지만 보기 권한자는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다. |


## <a name="data-sources-and-corresponding-authentication-types"></a>데이터 원본 및 해당 인증 유형

| 데이터 원본 | 인증 형식 |
|-------------| ---------------------|
|[Application Insights](#appinsights) | 기본 |
|[Azure Blob Storage(JSON)](#blob) | 기본<br>관리 ID |
|[Azure Cosmos DB(SQL)](#cosmosdb) | 기본 |
|[Azure Data Explorer(Kusto)](#kusto) | 기본<br>관리 ID<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체 |
|[Azure Data Lake Storage Gen2](#adl) | 기본<br>Data Lake Storage Gen2 공유 키<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체 |
|[Azure Event Hubs](#eventhubs) | 기본 |
|[Azure Monitor 로그](#log) | 기본<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체 |
|[Azure SQL Database / SQL Server](#sql) | 기본<br>관리 ID<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체<br>Azure SQL 연결 문자열 |
|[Azure Table Storage](#table) | 기본 | 
|[InfluxDB(InfluxQL)](#influxdb) | 기본 |
|[MongoDB](#mongodb) | 기본 |
|[MySQL](#mysql) | 기본 |
|[PostgreSQL](#pgsql) | 기본|
|[로컬 파일(CSV)](#csv) | 기본|

다음 섹션에서는 다른 데이터 원본 시나리오 내의 모든 인증 유형에 필요한 매개 변수를 지정합니다. 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **애플리케이션 ID**: Application Insights API를 사용할 때 이 애플리케이션을 식별하는 데 사용합니다. 애플리케이션 ID를 가져오려면 다음 단계를 수행합니다.

   1. Application Insights 리소스에서 **API 액세스** 를 선택합니다.
   
      ![Application Insights 리소스에서 애플리케이션 ID를 얻는 방법을 보여 주는 스크린샷](media/portal-app-insights-app-id.png)

   2. 생성된 애플리케이션 ID를 Metrics Advisor에서 **애플리케이션 ID** 필드에 복사합니다. 

* **API 키**: 브라우저 외부의 애플리케이션에서 이 리소스에 액세스하는 데 사용됩니다. API 키를 가져오려면 다음 단계를 수행합니다.

   1. Application Insights 리소스에서 **API 액세스** 를 선택합니다.

   2. **API 키 만들기** 를 선택합니다.

   3. 간단한 설명을 입력하고 **원격 분석 읽기** 옵션을 선택한 다음, **키 생성** 단추를 선택합니다.

      ![Azure Portal에서 API 키를 얻는 방법을 보여 주는 스크린샷](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > 이 API 키를 복사하고 저장합니다. 이 키는 다시 표시되지 않습니다. 이 키를 분실하면 새로 만들어야 합니다.

   4. API 키를 Metrics Advisor의 **API 키** 필드에 복사합니다.

* **쿼리**: Application Insights 로그는 Azure Data Explorer에서 빌드되고, Azure Monitor 로그 쿼리는 동일한 Kusto 쿼리 언어의 한 버전을 사용합니다. [Kusto 쿼리 언어 문서](/azure/data-explorer/kusto/query)는 Application Insights에 대한 쿼리 작성을 위한 주요 리소스로 참조해야 합니다. 

    샘플 쿼리:

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    [자습서: 올바른 쿼리 작성](tutorials/write-a-valid-query.md)에서 보다 구체적인 예제를 참조할 수 있습니다.
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage(JSON)</span>

* **연결 문자열**: Azure Blob Storage(JSON)에 대한 두 가지 인증 유형이 있습니다.

    * **기본**: 이 문자열을 가져오는 방법은 [Azure Storage 연결 문자열 구성](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account)을 참조하세요. 또한 Azure Blob Storage 리소스에 대한 Azure Portal을 방문하여 **설정** > **액세스 키** 에서 직접 연결 문자열을 찾을 수 있습니다.
    
    * **관리 ID**: Azure 리소스에 대한 관리 ID를 사용하여 Blob 및 큐 데이터에 대한 액세스 권한 부여할 수 있습니다. 이 기능은 Azure VM(가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행되는 애플리케이션에서 Azure AD 자격 증명을 사용합니다. 
    
        Azure Blob Storage 리소스에 대한 Azure Portal에서 관리 ID를 만들 수 있습니다. **액세스 제어(IAM)** 에서 **역할 할당** 을 선택한 후 **추가** 를 선택합니다 제안된 역할 유형은 **Storage Blob 데이터 읽기 권한자** 입니다. 자세한 내용은 [관리 ID를 사용하여 Azure Storage에 액세스](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1)를 참조하세요.
    
        ![관리 ID를 Blob을 보여 주는 스크린샷](media/managed-identity-blob.png)
    

* **컨테이너**: Metrics Advisor는 단일 컨테이너 아래에 Blob 파일(타임스탬프당 하나의 Blob)로 저장된 시계열 데이터를 예상합니다. 이는 컨테이너 이름 필드입니다.

* **Blob 템플릿**: Metrics Advisor는 경로를 사용하여 Blob Storage에서 JSON 파일을 찾습니다. 다음은 Blob Storage에서 JSON 파일을 찾는 데 사용되는 Blob 파일 템플릿의 예입니다. `%Y/%m/FileName_%Y-%m-%d-%h-%M.json` `%Y/%m`은 경로이고 경로에 `%d`가 있는 경우 `%m` 뒤에 추가할 수 있습니다. JSON 파일의 이름을 날짜별로 지정할 경우 `%Y-%m-%d-%h-%M.json`을 사용할 수도 있습니다.

   지원되는 매개 변수는 다음과 같습니다.
   
   * `%Y`는 `yyyy` 형식의 연도입니다.
   * `%m`은 `MM` 형식의 월입니다.
   * `%d`는 `dd` 형식의 일입니다.
   * `%h`는 `HH` 형식의 시간입니다.
   * `%M`은 `mm` 형식의 분입니다.
  
   예를 들어, 다음 데이터 세트에서 Blob 템플릿은 `%Y/%m/%d/00/JsonFormatV2.json`이어야 합니다.
  
   ![Blob 템플릿을 보여 주는 스크린샷](media/blob-template.png)
  

* **JSON 형식 버전**: JSON 파일의 데이터 스키마를 정의합니다. Metrics Advisor는 다음 버전을 지원합니다. 하나를 선택하여 필드를 채울 수 있습니다.
  
   * **v1**(기본값)

      메트릭 *이름* 과 *값* 만 허용됩니다. 예를 들면 다음과 같습니다.
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      메트릭 *차원* 과 *타임스탬프* 도 허용됩니다. 예를 들면 다음과 같습니다.
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   JSON 파일당 타임스탬프는 하나만 허용됩니다. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB(SQL)</span>

* **연결 문자열**: Azure Cosmos DB에 액세스하기 위한 연결 문자열입니다. 이 문자열은 Azure Portal의 Azure Cosmos DB 리소스에 있는 **키** 에서 찾을 수 있습니다. 자세한 내용은 [Azure Cosmos DB에서 데이터 액세스 보호](../../cosmos-db/secure-access-to-data.md)를 참조하세요.
* **데이터베이스**: 쿼리할 데이터베이스입니다. Azure Portal의 **컨테이너** 에서 **찾아보기** 로 이동하여 데이터베이스를 찾습니다.
* **연결 ID**: 쿼리할 컬렉션 ID입니다. Azure Portal의 **컨테이너** 에서 **찾아보기** 로 이동하여 컬렉션 ID를 찾습니다.
* **SQL 쿼리**: 데이터를 가져와서 다차원 시계열 데이터로 작성하는 SQL 쿼리입니다. 쿼리에서 `@IntervalStart` 및 `@IntervalEnd` 변수를 사용할 수 있습니다. 형식은 `yyyy-MM-ddTHH:mm:ssZ`와 같이 지정해야 합니다.

    샘플 쿼리:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer(Kusto)</span>

* **연결 문자열**: Azure Data Explorer(Kusto)에 대한 네 가지 인증 유형: 기본, 서비스 주체, Key Vault의 서비스 주체 및 관리 ID. 연결 문자열의 데이터 원본은 "https"로 시작하는 URI 형식이어야 합니다. Azure Portal에서 URI를 찾을 수 있습니다.
    
    * **기본**: Metrics Advisor는 Azure AD 애플리케이션 인증을 사용하여 Azure Data Explorer(Kusto)에 액세스할 수 있도록 지원합니다. Azure AD 애플리케이션을 만들고 등록한 다음 Azure Data Explorer 데이터베이스에 대한 액세스 권한을 부여해야 합니다. 자세한 내용은 [Azure Data Explorer에서 Azure AD 앱 등록 만들기](/azure/data-explorer/provision-azure-ad-app)를 참조하세요. 다음은 연결 문자열의 예입니다.
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **서비스 주체**: 서비스 주체는 애플리케이션 개체에서 만든 구체적인 인스턴스입니다. 서비스 주체는 해당 애플리케이션 개체의 특정 속성을 상속합니다. 서비스 사용자 개체는 앱이 특정 테넌트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의합니다. Metrics Advisor에서 서비스 주체를 사용하려면 다음을 수행합니다.
    
        1. Azure AD 애플리케이션 등록을 만듭니다. 자세한 내용은 [Azure Data Explorer에서 Azure AD 앱 등록 만들기](/azure/data-explorer/provision-azure-ad-app)를 참조하세요.

        1. Azure Data Explorer 데이터베이스 권한을 관리합니다. 자세한 내용은 [Azure Data Explorer 데이터베이스 권한 관리](/azure/data-explorer/manage-database-permissions)를 참조하세요. 

        1. Metrics Advisor에서 자격 증명 엔터티를 만듭니다. 서비스 주체 인증 유형에 대한 데이터 피드를 추가할 때 해당 엔터티를 선택할 수 있도록 Metrics Advisor에서 [자격 증명 엔터티를 만드는](how-tos/credential-entity.md) 방법을 참조하세요. 
        
        다음은 연결 문자열의 예입니다.
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Key Vault의 서비스 주체**: Azure Key Vault는 클라우드 앱 및 서비스에서 사용하는 암호화 키와 비밀 값을 보호하는 데 도움이 됩니다. Key Vault를 사용하여 키와 비밀 값을 암호화할 수 있습니다. 먼저 서비스 주체를 만든 다음, Key Vault 내에 서비스 주체를 저장해야 합니다. 자세한 내용은 [Key Vault에서 서비스 주체에 대한 자격 증명 엔터티 만들기](how-tos/credential-entity.md#sp-from-kv)를 참조하여 Key Vault에서 서비스 주체를 설정하는 자세한 절차를 따르세요. 다음은 연결 문자열의 예입니다. 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **관리 ID**: Azure 리소스에 대한 관리 ID를 사용하여 Blob 및 큐 데이터에 대한 액세스 권한 부여할 수 있습니다. 관리 ID는 Azure 가상 머신, 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행되는 애플리케이션에서 Azure AD 자격 증명을 사용합니다. Azure AD 인증과 함께 Azure 리소스에 대한 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다. [관리 ID로 권한 부여](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm) 방법을 알아봅니다. 
    
        Azure Data Explorer(Kusto)에 대한 Azure Portal에서 관리 ID를 만들 수 있습니다. **권한** > **추가** 를 선택합니다. 제안되는 역할 유형은 **관리자/보기 권한자** 입니다.
        
        ![Kusto의 관리 ID를 보여 주는 스크린샷](media/managed-identity-kusto.png)

        다음은 연결 문자열의 예입니다. 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **쿼리**: 데이터를 가져와 다차원 시계열 데이터로 작성하려면 [Kusto 쿼리 언어](/azure/data-explorer/kusto/query)를 참조하세요. 쿼리에서 `@IntervalStart` 및 `@IntervalEnd` 변수를 사용할 수 있습니다. 형식은 `yyyy-MM-ddTHH:mm:ssZ`와 같이 지정해야 합니다.

    샘플 쿼리:
    
    ``` Kusto
   [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
   ```

    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **계정 이름**: Azure Data Lake Storage Gen2의 인증 유형은 기본, Azure Data Lake Storage Gen2 공유 키, 서비스 주체 및 Key Vault의 서비스 주체입니다.
    
    * **기본**: Azure Data Lake Storage Gen2의 **계정 이름** 입니다. **액세스 키** 의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 이 이름을 찾을 수 있습니다. 

    * **Azure Data Lake Storage Gen2 공유 키**: 먼저 Azure Data Lake Storage Gen2에 액세스하기 위한 계정 키를 지정합니다(기본 인증 유형의 계정 키와 동일함). **액세스 키** 의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 이 이름을 찾을 수 있습니다. 그런 다음, Azure Data Lake Storage Gen2 공유 키 형식에 대한 [자격 증명 엔터티를 만들고](how-tos/credential-entity.md) 계정 키를 입력합니다. 

        계정 이름은 기본 인증 유형과 동일합니다.
    
    * **서비스 주체**: ‘서비스 주체’는 서비스 사용자는 애플리케이션 개체에서 만든 구체적인 인스턴스이고 해당 애플리케이션 개체의 특정 속성을 상속합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 앱 개체를 참조합니다. 서비스 사용자 개체는 앱이 특정 테넌트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의합니다.

        계정 이름은 기본 인증 유형과 동일합니다.
    
        **1단계:** Azure AD 애플리케이션을 만들고 등록한 다음, 데이터베이스에 액세스하도록 권한을 부여합니다. 자세한 내용은 [Azure AD 앱 등록 만들기](/azure/data-explorer/provision-azure-ad-app)를 참조하세요.

        **2단계:** 역할을 할당합니다.
        
        1. Azure Portal에서 **스토리지 계정** 서비스로 이동합니다.
        
        2. 이 애플리케이션 등록에 사용할 Azure Data Lake Storage Gen2 계정을 선택합니다.

        3. **Access Control(IAM)** 을 선택합니다.

        4. **+추가** 를 선택한 다음, 메뉴에서 **역할 할당 추가** 를 선택합니다.

        5. **선택** 필드를 Azure AD 애플리케이션 이름으로 설정하고 역할을 **Storage Blob 데이터 기여자** 로 설정합니다. 그런 다음 **저장** 을 선택합니다.
        
        ![역할을 할당하는 단계를 보여 주는 스크린샷](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **3단계:** 서비스 주체 인증 유형에 대한 데이터 피드를 추가할 때 해당 엔터티를 선택할 수 있도록 Metrics Advisor에서 [자격 증명 엔터티를 만듭니다](how-tos/credential-entity.md). 
        
    * **Key Vault의 서비스 주체**: Key Vault는 클라우드 앱 및 서비스에서 사용하는 암호화 키와 비밀 값을 보호하는 데 도움이 됩니다. Key Vault를 사용하여 키와 비밀 값을 암호화할 수 있습니다. 먼저 서비스 주체를 만든 다음, Key Vault 내에 서비스 주체를 저장합니다. 자세한 내용은 [Key Vault의 서비스 주체에 대한 자격 증명 엔터티 만들기](how-tos/credential-entity.md#sp-from-kv)를 참조하세요. 계정 이름은 기본 인증 유형과 동일합니다.

* **계정 키**(기본 인증 유형에만 필요): Azure Data Lake Storage Gen2에 액세스하기 위한 계정 키를 지정합니다. **액세스 키** 의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 이 이름을 찾을 수 있습니다.

* **파일 시스템 이름(컨테이너)** : Metrics Advisor의 경우 시계열 데이터를 단일 컨테이너 아래에 Blob 파일(타임스탬프당 하나의 Blob)로 저장합니다. 이는 컨테이너 이름 필드입니다. 이 필드는 Azure Storage 계정(Azure Data Lake Storage Gen2) 인스턴스에서 찾을 수 있습니다. **Data Lake Storage** 에서 **컨테이너** 를 선택하고 컨테이너 이름을 확인합니다.

* **디렉터리 템플릿**: Blob 파일의 디렉터리 템플릿입니다. 지원되는 매개 변수는 다음과 같습니다.

   * `%Y`는 `yyyy` 형식의 연도입니다.
   * `%m`은 `MM` 형식의 월입니다.
   * `%d`는 `dd` 형식의 일입니다.
   * `%h`는 `HH` 형식의 시간입니다.
   * `%M`은 `mm` 형식의 분입니다.

   일별 메트릭에 대한 쿼리 샘플: `%Y/%m/%d`

   시간별 메트릭에 대한 쿼리 샘플: `%Y/%m/%d/%h`

* **파일 템플릿**: Metrics Advisor는 경로를 사용하여 Blob Storage에서 JSON 파일을 찾습니다. 다음은 Blob Storage에서 JSON 파일을 찾는 데 사용되는 Blob 파일 템플릿의 예입니다. `%Y/%m/FileName_%Y-%m-%d-%h-%M.json` `%Y/%m`은 경로이고 경로에 `%d`가 있는 경우 `%m` 뒤에 추가할 수 있습니다. 
   
   지원되는 매개 변수는 다음과 같습니다.
   
   * `%Y`는 `yyyy` 형식의 연도입니다.
   * `%m`은 `MM` 형식의 월입니다.
   * `%d`는 `dd` 형식의 일입니다.
   * `%h`는 `HH` 형식의 시간입니다.
   * `%M`은 `mm` 형식의 분입니다.

   Metrics Advisor는 다음 예제와 같이 JSON 파일의 데이터 스키마를 지원합니다.

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure Event Hubs</span>

* **제한 사항**: 통합과 관련하여 다음과 같은 제한 사항을 알고 있어야 합니다.

   * Event Hubs와의 Metrics Advisor 통합은 현재 퍼블릭 미리 보기의 한 Metrics Advisor 인스턴스에서 4개 이상의 활성 데이터 피드를 지원하지 않습니다.
   * Metrics Advisor는 일시 중지된 데이터 피드를 다시 활성화하는 경우를 포함하여 항상 최신 오프셋에서 메시지 소비를 시작합니다.
   
      * 데이터 피드 일시 중지 기간 동안 메시지가 손실됩니다.
      * 데이터 피드 수집 시작 시간은 데이터 피드를 만들 때 자동으로 현재 협정 세계시 타임스탬프로 설정됩니다. 이 시간은 참조용으로만 사용됩니다.

   * 소비자 그룹당 하나의 데이터 피드만 사용할 수 있습니다. 삭제된 다른 데이터 피드의 소비자 그룹을 다시 사용하려면 삭제 후 10분 이상 기다려야 합니다.
   * 데이터 피드를 만든 후에는 연결 문자열 및 소비자 그룹을 수정할 수 없습니다.
   * Event Hubs 메시지의 경우 JSON만 지원되며 JSON 값은 중첩된 JSON 개체일 수 없습니다. 최상위 요소는 JSON 개체 또는 JSON 배열일 수 있습니다.
    
    유효한 메시지는 다음과 같습니다.

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **연결 문자열**: Event Hubs의 인스턴스로 이동합니다. 그런 다음, 새 정책을 추가하거나 기존 공유 액세스 정책을 선택합니다. 팝업 패널에서 연결 문자열을 복사합니다.
    ![Event Hubs 스크린샷](media/datafeeds/entities-eventhubs.jpg)
    
    ![공유 액세스 정책의 스크린샷](media/datafeeds/shared-access-policies.jpg)

    다음은 연결 문자열의 예입니다. 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **소비자 그룹**: [소비자 그룹](../../event-hubs/event-hubs-features.md#consumer-groups)은 전체 Event Hub의 보기(상태, 위치, 또는 오프셋)입니다.
Azure Event Hubs 인스턴스의 **소비자 그룹** 메뉴에서 찾을 수 있습니다. 소비자 그룹은 하나의 데이터 피드만 제공할 수 있습니다. 각 데이터 피드에 대한 새 소비자 그룹을 만듭니다.
* **타임스탬프**(선택 사항): Metrics Advisor는 사용자 데이터 원본에 타임스탬프 필드가 없는 경우 Event Hubs 타임스탬프를 이벤트 타임스탬프로 사용합니다. 타임스탬프 필드는 선택 사항입니다. 타임스탬프 열을 선택하지 않으면 서비스에서는 큐에 넣은 시간을 타임스탬프로 사용합니다.

    타임스탬프 필드는 다음 두 형식 중 하나와 일치해야 합니다.
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * `1970-01-01T00:00:00Z` epoch에서의 초 또는 밀리초 수입니다.
    
    타임스탬프는 세분성에 따라 왼쪽으로 맞춤됩니다. 예를 들어, 타임스탬프가 `2019-01-01T00:03:00Z`이고 세분성이 5분이면 Metrics Advisor는 타임스탬프를 `2019-01-01T00:00:00Z`에 맞춥니다. 이벤트 타임스탬프가 `2019-01-01T00:10:00Z`이면 Metrics Advisor는 타임스탬프를 맞춤 없이 직접 사용합니다. 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Azure Monitor 로그</span>

Azure Monitor 로그는 인증 유형으로 기본, 서비스 주체 및 Key Vault의 서비스 주체를 제공합니다.
* **기본**: **테넌트 ID**, **클라이언트 ID**, **클라이언트 비밀** 및 **작업영역 ID** 를 입력해야 합니다.
   **테넌트 ID**, **클라이언트 ID**, **클라이언트 암호** 를 가져오려면 [앱 또는 웹 API 등록](../../active-directory/develop/quickstart-register-app.md)을 참조하세요. Azure Portal에서 **작업 영역 ID** 를 찾을 수 있습니다.
   
    ![Azure Portal에서 작업 영역 ID를 찾을 수 있는 위치를 보여 주는 스크린샷](media/workspace-id.png)
    
* **서비스 주체**: 서비스 주체는 서비스 사용자는 애플리케이션 개체에서 만든 구체적인 인스턴스이고 해당 애플리케이션 개체의 특정 속성을 상속합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 앱 개체를 참조합니다. 서비스 사용자 개체는 앱이 특정 테넌트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의합니다.
    
    **1단계:** Azure AD 애플리케이션을 만들고 등록한 다음, 데이터베이스에 액세스하도록 권한을 부여합니다. 자세한 내용은 [Azure AD 앱 등록 만들기](/azure/data-explorer/provision-azure-ad-app)를 참조하세요.

    **2단계:** 역할을 할당합니다.
    1. Azure Portal에서 **스토리지 계정** 서비스로 이동합니다.
    2. **Access Control(IAM)** 을 선택합니다.
    3. **+추가** 를 선택한 다음, 메뉴에서 **역할 할당 추가** 를 선택합니다.
    4. **선택** 필드를 Azure AD 애플리케이션 이름으로 설정하고 역할을 **Storage Blob 데이터 기여자** 로 설정합니다. 그런 다음 **저장** 을 선택합니다.
    
        ![역할 할당 방법을 보여 주는 스크린샷](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **3단계:** 서비스 주체 인증 유형에 대한 데이터 피드를 추가할 때 해당 엔터티를 선택할 수 있도록 Metrics Advisor에서 [자격 증명 엔터티를 만듭니다](how-tos/credential-entity.md). 
        
* **Key Vault의 서비스 주체**: Key Vault는 클라우드 앱 및 서비스에서 사용하는 암호화 키와 비밀 값을 보호하는 데 도움이 됩니다. Key Vault를 사용하여 키와 비밀 값을 암호화할 수 있습니다. 먼저 서비스 주체를 만든 다음, Key Vault 내에 서비스 주체를 저장합니다. 자세한 내용은 [Key Vault의 서비스 주체에 대한 자격 증명 엔터티 만들기](how-tos/credential-entity.md#sp-from-kv)를 참조하세요. 

* **쿼리**: 쿼리를 지정합니다. 자세한 내용은 [Azure Monitor의 로그 쿼리](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

    샘플 쿼리:

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **연결 문자열**: Azure SQL Database 및 SQL Server 인증 유형은 기본, 관리 ID, Azure SQL 연결 문자열, 서비스 주체 및 Key Vault의 서비스 주체입니다.
    
    * **기본**: Metrics Advisor는 SQL Server 데이터 원본에 대해 [ADO.NET 스타일 연결 문자열](/dotnet/framework/data/adonet/connection-string-syntax)을 허용합니다.
    다음은 연결 문자열의 예입니다. 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**관리 ID**</span>: Azure 리소스에 대한 관리 ID를 사용하여 Blob 및 큐 데이터에 대한 액세스 권한 부여할 수 있습니다. 이 관리 ID는 Azure 가상 머신, 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행되는 애플리케이션에서 Azure AD 자격 증명을 사용합니다. Azure AD 인증과 함께 Azure 리소스에 대한 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다. [관리 엔터티를 사용하도록 설정하려면](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 다음 단계를 수행합니다.
    1. 한 번의 클릭으로 시스템 할당 관리 ID를 활성화할 수 있습니다. Azure Portal에서 Metrics Advisor 작업 영역에 대해 **설정** > **ID** > **시스템 할당** 으로 이동합니다. 그런 후 상태를 **켜짐** 으로 설정합니다. 
    
        ![상태를 켜짐으로 설정하는 방법을 보여 주는 스크린샷](media/datafeeds/set-identity-status.png)

    1. Azure AD 인증을 사용하도록 설정합니다. Azure Portal에서 데이터 원본에 대해 **설정** > **Active Directory 관리자** 로 이동합니다. **관리자 설정** 을 선택하고 서버의 관리자로 지정할 **Azure AD 사용자 계정** 을 선택합니다. 그런 다음, **선택** 을 선택합니다.
    
        ![관리자를 설정하는 방법을 보여 주는 스크린샷](media/datafeeds/set-admin.png)

    1. Metrics Advisor에서 관리 ID를 사용하도록 설정합니다. 데이터베이스 관리 도구 또는 Azure Portal에서 쿼리를 편집할 수 있습니다.
    
        **관리 도구**: 데이터베이스 관리 도구에서 **Active Directory - MFA 지원을 통한 유니버설 인증** 을 선택합니다. **사용자 이름** 필드에 2단계에서 서버 관리자로 설정한 Azure AD 계정의 이름을 입력합니다. 예를 들어 `test@contoso.com`일 수 있습니다.
    
        ![연결 세부 정보 설정 방법을 보여 주는 스크린샷](media/datafeeds/connection-details.png)
        
        **Azure Portal**: SQL 데이터베이스에서 **쿼리 편집기** 를 선택하고 관리자 계정에 로그인합니다.
        ![Azure Portal에서 쿼리를 편집하는 방법을 보여 주는 스크린샷](media/datafeeds/query-editor.png)

        그런 다음, 쿼리 창에서 다음을 실행합니다(관리 도구 메서드에 대해 동일함).
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > `MI Name`은 Metrics Advisor에서 관리 ID 이름입니다(서비스 주체의 경우 서비스 주체 이름으로 대체해야 함). 자세한 내용은 [관리 ID로 권한 부여](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm)를 참조하세요. 
            
        다음은 연결 문자열의 예입니다. 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Azure SQL 연결 문자열**: 
      

        다음은 연결 문자열의 예입니다. 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **서비스 주체**: 서비스 주체는 서비스 사용자는 애플리케이션 개체에서 만든 구체적인 인스턴스이고 해당 애플리케이션 개체의 특정 속성을 상속합니다. 서비스 주체는 애플리케이션이 사용되는 각 테넌트에 만들어지고 전역적으로 고유한 앱 개체를 참조합니다. 서비스 사용자 개체는 앱이 특정 테넌트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의합니다.
    
        **1단계:** Azure AD 애플리케이션을 만들고 등록한 다음, 데이터베이스에 액세스하도록 권한을 부여합니다. 자세한 내용은 [Azure AD 앱 등록 만들기](/azure/data-explorer/provision-azure-ad-app)를 참조하세요.

        **2단계:** [SQL Server의 관리 ID](#jump)에서 이전에 설명한 단계를 수행합니다. 

        **3단계:** 서비스 주체 인증 유형에 대한 데이터 피드를 추가할 때 해당 엔터티를 선택할 수 있도록 Metrics Advisor에서 [자격 증명 엔터티를 만듭니다](how-tos/credential-entity.md). 

        다음은 연결 문자열의 예입니다. 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Key Vault의 서비스 주체**: Key Vault는 클라우드 앱 및 서비스에서 사용하는 암호화 키와 비밀 값을 보호하는 데 도움이 됩니다. Key Vault를 사용하여 키와 비밀 값을 암호화할 수 있습니다. 먼저 서비스 주체를 만든 다음, Key Vault 내에 서비스 주체를 저장합니다. 자세한 내용은 [Key Vault의 서비스 주체에 대한 자격 증명 엔터티 만들기](how-tos/credential-entity.md#sp-from-kv)를 참조하세요. **설정** > **연결 문자열** 에서 Azure SQL Server 리소스의 연결 문자열을 찾을 수도 있습니다.
        
        다음은 연결 문자열의 예입니다. 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **쿼리**: 데이터를 가져와서 다차원 시계열 데이터로 작성하는 SQL 쿼리입니다. 쿼리에서 `@IntervalStart` 및 `@IntervalEnd`를 사용하여 간격의 예상되는 메트릭 값을 가져올 수 있습니다. 형식은 `yyyy-MM-ddTHH:mm:ssZ`와 같이 지정해야 합니다.


    샘플 쿼리:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **연결 문자열**: SAS(공유 액세스 서명) URL을 만든 후 여기에 입력합니다. SAS URL을 만드는 가장 간단한 방법은 Azure Portal을 사용하는 것입니다. 먼저 **설정** 에서 액세스하려는 스토리지 계정으로 이동합니다. 그런 후 **공유 액세스 서명** 을 선택합니다. **테이블** 및 **개체** 확인란을 선택한 다음, **SAS 및 연결 문자열 생성** 을 선택합니다. Metrics Advisor 작업 영역에서 **Table service SAS URL** 을 복사하여 텍스트 상자에 붙여넣습니다.

    ![Azure Table Storage에서 공유 액세스 서명을 생성하는 방법을 보여 주는 스크린샷](media/azure-table-generate-sas.png)

* **테이블 이름**: 쿼리할 테이블을 지정합니다. Azure Storage 계정 인스턴스에서 찾을 수 있습니다. **Table service** 섹션에서 **테이블** 을 선택합니다.

* **쿼리**: 쿼리에서 `@IntervalStart` 및 `@IntervalEnd`를 사용하여 간격의 예상되는 메트릭 값을 가져올 수 있습니다. 형식은 `yyyy-MM-ddTHH:mm:ssZ`와 같이 지정해야 합니다.

    샘플 쿼리:
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB(InfluxQL)</span>

* **연결 문자열**: InfluxDB에 액세스하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.

    샘플 쿼리:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.

* **사용자 이름**: 인증을 위한 선택 사항입니다. 
* **암호**: 인증을 위한 선택 사항입니다. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **연결 문자열**: MongoDB에 액세스하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 명령입니다. [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html)에서 명령을 확인합니다.

    샘플 쿼리:

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **연결 문자열**: MySQL DB에 액세스하기 위한 연결 문자열입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.

    샘플 쿼리:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **연결 문자열**: PostgreSQL DB에 액세스하기 위한 연결 문자열입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.

    샘플 쿼리:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    자세한 내용은 [유효한 쿼리 작성에 대한 자습서](tutorials/write-a-valid-query.md)를 참조하세요.
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">로컬 파일(CSV)</span>

> [!NOTE]
> 이 기능은 변칙 검색에 초점을 맞춘 빠른 시스템 평가에만 사용됩니다. 로컬 CSV의 정적 데이터만 허용하고 단일 시계열 데이터에 대해 변칙 검색을 수행합니다. 실시간 데이터 수집, 변칙 알림, 근본 원인 분석 및 메트릭 간 인시던트 분석을 비롯한 다차원 메트릭을 분석하는 경우 지원되는 다른 데이터 원본을 사용합니다.

**CSV의 데이터에 대한 요구 사항:**
- 분석할 측정값을 나타내는 하나 이상의 열이 있습니다. 더 빠르고 더 나은 사용자 환경을 위해 타임스탬프 열과 메트릭 열의 두 열이 포함된 CSV 파일을 사용해 보세요. 타임스탬프 형식은 `2021-03-30T00:00:00Z`와 같아야 하며 `seconds` 파트는 `:00Z`로 지정하는 것이 가장 좋습니다. 모든 레코드 간의 시간 세분성은 동일해야 합니다.
- 타임스탬프 열은 선택 사항입니다. 타임스탬프가 없으면 Metrics Advisor는 오늘(`00:00:00` 협정 세계시)부터 시작되는 타임스탬프를 사용합니다. 서비스는 1시간 간격으로 행의 각 측정값을 매핑합니다.
- 데이터 수집 중에는 순서를 다시 정렬하거나 간격을 채우지 않습니다. CSV 파일의 데이터가 타임스탬프 순서의 **ASC(오름차순)** 로 정렬되었는지 확인합니다.
 
## <a name="next-steps"></a>다음 단계

* 메트릭 데이터가 시스템으로 수집될 때까지 기다리는 동안 [데이터 피드 구성 관리 방법](how-tos/manage-data-feeds.md)을 읽어 보세요.
* 메트릭 데이터가 수집되면 [메트릭을 구성하고 검색 구성을 미세 조정](how-tos/configure-metrics.md)할 수 있습니다.