---
title: Dynamics에서 데이터 복사(Microsoft Dataverse)
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인의 복사 작업을 사용하여 Microsoft Dynamics CRM 또는 Microsoft Dynamics 365(Microsoft Dataverse)에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 Dynamics CRM 또는 Dynamics 365로 데이터를 복사하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6f95e117865ccf9d242d595ec98b66d7cd344a85
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597685"
---
# <a name="copy-data-from-and-to-dynamics-365-microsoft-dataverse-or-dynamics-crm"></a>Dynamics 365(Microsoft Dataverse) 또는 Dynamics CRM 간에 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
이 문서에서는 Azure Data Factory 또는 Synapse 파이프라인의 복사 작업을 사용하여 Microsoft Dynamics 365 및 Microsoft Dynamics CRM 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본 및 싱크 매트릭스](copy-activity-overview.md)를 사용하는 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Dynamics 365(Microsoft Dataverse) 또는 Dynamics CRM에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Dynamics 365(Microsoft Dataverse) 또는 Dynamics CRM에 복사할 수도 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

>[!NOTE]
>2020년 11월부터 Common Data Service는 [Microsoft Dataverse](/powerapps/maker/data-platform/data-platform-intro)로 이름이 변경되었습니다. 이 문서는 최신 용어를 반영하도록 업데이트되었습니다. 

이 Dynamics 커넥터는 온라인/온-프레미스에서 모두 Dynamics 버전 7~9를 지원합니다. 더 구체적으로 살펴보면 다음과 같습니다.
- 버전 7은 Dynamics CRM 2015에 매핑됩니다.
- 버전 8은 Dynamics CRM 2016 및 초기 버전의 Dynamics 365에 매핑됩니다.
- 버전 9는 최신 버전의 Dynamics 365에 매핑됩니다.


Dynamics 버전 및 제품에 지원되는 인증 유형 및 구성에 대한 다음 표를 참조하세요.

| Dynamics 버전 | 인증 형식 | 연결된 서비스 샘플 |
|:--- |:--- |:--- |
| Dataverse <br/><br/> Dynamics 365 온라인 <br/><br/> Dynamics CRM 온라인 | Azure AD(Azure Active Directory) 서비스 사용자 <br/><br/> Office 365 | [Dynamics 온라인 및 Azure AD 서비스 사용자 또는 Office 365 인증](#dynamics-365-and-dynamics-crm-online) |
| IFD(인터넷 연결 배포)를 사용하는 Dynamics 365 온-프레미스 <br/><br/> IFD로 Dynamics CRM 2016 온-프레미스 <br/><br/> IFD로 Dynamics CRM 2015 온-프레미스 | IFD | [IFD 및 IFD 인증을 사용하는 Dynamics 온-프레미스](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

>[!NOTE]
>[지역 검색 서비스 지원 중단](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)에 따라 서비스가 업그레이드되어 Office 365 인증을 사용하는 동안 [전역 검색 서비스](/powerapps/developer/data-platform/webapi/discover-url-organization-web-api#global-discovery-service)를 활용하게 되었습니다.

> [!IMPORTANT]
>테넌트 및 사용자가 [조건부 액세스](../active-directory/conditional-access/overview.md) 및/또는 Multi-Factor Authentication을 위해 Azure Active Directory에 구성되어 있는 경우 Office 365 인증 유형을 사용할 수 없습니다. 이러한 상황에서는 Azure AD(Azure Active Directory) 서비스 주체 인증을 사용해야 합니다.

구체적으로 Dynamics 365에 대해 다음 애플리케이션 유형이 지원됩니다.
- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing 이 커넥터는 재무, 운영, 인재 같은 다른 애플리케이션 유형을 지원하지 않습니다.

>[!TIP]
>Dynamics 365 Finance and Operations에서 데이터를 복사하려면 [Dynamics AX 커넥터](connector-dynamics-ax.md)를 사용하면 됩니다.

이 Dynamics 커넥터는 [Dynamics XRM 도구](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)를 기반으로 빌드됩니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure AD 서비스 사용자 인증을 통해 이 커넥터를 사용하려면 Dataverse 또는 Dynamics에서 S2S(서버 간) 인증을 설정해야 합니다. 먼저 Azure Active Directory에 애플리케이션 사용자(서비스 사용자)를 등록합니다. 이 작업을 수행하는 방법은 [여기](../active-directory/develop/howto-create-service-principal-portal.md)에서 찾을 수 있습니다. 애플리케이션을 등록하는 동안 Dataverse 또는 Dynamics에서 해당 사용자를 만들고 권한을 부여해야 합니다. 이러한 권한은 Dataverse 또는 Dynamics에서 사용 권한이 부여된 팀에 애플리케이션 사용자를 추가하여 직접 또는 간접적으로 부여할 수 있습니다. 애플리케이션 사용자를 설정하여 Dataverse로 인증하는 방법에 대한 자세한 내용은 [여기](/powerapps/developer/data-platform/use-single-tenant-server-server-authentication)에서 찾을 수 있습니다. 


## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-dynamics-365-using-ui"></a>UI를 사용하여 Dynamics 365에 연결된 서비스 만들기

다음 단계에 따라 Azure Portal UI에서 Dynamics 365에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Dynamics를 검색하고 Dynamics 365 커넥터를 선택합니다.

    :::image type="content" source="media/connector-dynamics-crm-office-365/dynamics-crm-office-365-connector.png" alt-text="Dynamics 365 커넥터 스크린샷":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-dynamics-crm-office-365/configure-dynamics-crm-office-365-linked-service.png" alt-text="Dynamics 365의 연결된 서비스 구성 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Dynamics에 한정된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics 연결 서비스에 다음 속성이 지원됩니다.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 및 Dynamics CRM Online

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 "Dynamics", "DynamicsCrm" 또는 "CommonDataServiceForApps"로 설정되어야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. Dynamics 온라인의 값은 "Online"이어야 합니다. | 예 |
| serviceUri | 브라우저에서 액세스하는 것과 동일한 Dynamics 인스턴스의 서비스 URL입니다. 예를 들면 "https://\<organization-name>.crm[x].dynamics.com"입니다. | 예 |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. 유효한 값은 "AADServicePrincipal" 및 "Office365"입니다. | 예 |
| servicePrincipalId | Azure AD 애플리케이션의 클라이언트 ID입니다. | 인증이 "AADServicePrincipal"인 경우 예 |
| servicePrincipalCredentialType | 서비스 사용자 인증에 사용할 자격 증명 유형입니다. 유효한 값은 "ServicePrincipalKey" 및 "ServicePrincipalCert"입니다. | 인증이 "AADServicePrincipal"인 경우 예 |
| servicePrincipalCredential | 서비스 사용자 자격 증명입니다. <br/><br/>"ServicePrincipalKey"를 자격 증명 유형으로 사용하는 경우 `servicePrincipalCredential`은 연결된 서비스 배포 시 서비스에서 암호화하는 문자열일 수 있습니다. 또는 Azure Key Vault의 비밀에 대한 참조일 수 있습니다. <br/><br/>"ServicePrincipalCert"를 자격 증명으로 사용하는 경우 `servicePrincipalCredential`은(는) Azure Key Vault의 인증서에 대한 참조여야 합니다. | 인증이 "AADServicePrincipal"인 경우 예 |
| 사용자 이름 | Dynamics에 연결할 사용자 이름입니다. | 인증이 "Office365"인 경우 예 |
| password | 사용자 이름으로 지정한 사용자 계정의 암호입니다. 이 필드를 "SecureString"으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 인증이 "Office365"인 경우 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 값이 지정되지 않은 경우 속성은 기본 Azure Integration Runtime을 사용합니다. | 예 |

>[!NOTE]
>Dynamics 커넥터는 이전에 선택적 **organizationName** 속성을 사용하여 Dynamics CRM 또는 Dynamics 365 Online 인스턴스를 식별했습니다. 해당 속성이 계속 작동하는 동안 인스턴스 검색 성능을 높이려면 대신 새 **serviceUri** 속성을 지정하는 것이 좋습니다.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>예: Azure AD 서비스 사용자 및 키 인증을 사용하는 Dynamics 온라인

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>예: Azure AD 서비스 사용자 및 인증서 인증을 사용하는 Dynamics 온라인

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```
#### <a name="example-dynamics-online-using-office-365-authentication"></a>예: Office365 인증을 사용하는 Dynamics 온라인

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD로 Dynamics 365 및 Dynamics CRM 온-프레미스

Dyanmics 온라인과 비교되는 추가 속성은 **hostName** 및 **port** 입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 "Dynamics", "DynamicsCrm" 또는 "CommonDataServiceForApps"로 설정되어야 합니다. | 예. |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. IFD를 사용하는 Dynamics 온-프레미스의 값은 "OnPremisesWithIfd"여야 합니다.| 예. |
| hostName | 온-프레미스 Dynamics 서버의 호스트 이름입니다. | 예. |
| 포트 | 온-프레미스 Dynamics 서버의 포트입니다. | 아니요. 기본값은 443입니다. |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | 예. |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 "Ifd"를 지정합니다. | 예. |
| 사용자 이름 | Dynamics에 연결할 사용자 이름입니다. | 예. |
| password | 사용자 이름에 지정한 사용자 계정의 암호입니다. 이 필드를 "SecureString"으로 표시하여 안전하게 저장할 수 있습니다. 또는 Key Vault에 암호를 저장하고 데이터를 복사할 때 여기에서 복사 작업을 끌어올 수 있습니다. [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)에서 자세히 알아봅니다. | 예. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 값이 지정되지 않은 경우 속성은 기본 Azure Integration Runtime을 사용합니다. | 예 |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>예제: IFD 인증을 사용하여 IFD로 Dynamics 온-프레미스

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Dynamics 간에 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 "DynamicsEntity", "DynamicsCrmEntity" 또는 "CommonDataServiceForAppsEntity"로 설정되어야 합니다. |예 |
| entityName | 검색할 엔터티의의 논리적 이름입니다. | 작업 원본이 "query"로 지정된 경우 아니요, 싱크의 경우 예 |

#### <a name="example"></a>예제

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 원본 및 싱크 형식에서 지원하는 속성의 목록을 제공합니다.

### <a name="dynamics-as-a-source-type"></a>원본 유형인 Dynamics

Dynamics에서 데이터를 복사하려는 경우 복사 작업 **원본** 섹션에서는 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 유형 속성은 "DynamicsSource", "DynamicsCrmSource" 또는 "CommonDataServiceForAppsSource"로 설정되어야 합니다. | 예 |
| Query | FetchXML은 재산적 가치가 있는 쿼리 언어로, Dynamics 온라인 및 온-프레미스에서 사용됩니다. 다음 예제를 참조하세요. 자세한 내용을 알아보려면 [FeachXML로 쿼리 작성](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8))을 참조하세요. | 데이터 세트에 `entityName`이(가) 지정되지 않은 경우 아니요 |

>[!NOTE]
>PK 열은 FetchXML 쿼리에서 구성한 열 프로젝트에 포함되지 않은 경우에도 항상 복사됩니다.

> [!IMPORTANT]
>- Dynamics에서 데이터를 복사할 때 Dynamics에서 싱크로의 명시적 열 매핑은 선택 사항입니다. 하지만 결정적인 복사 결과를 보장하려면 매핑을 사용하는 것이 좋습니다.
>- 서비스가 작성 UI에서 스키마를 가져올 때 스키마를 유추합니다. 이때 Dynamics 쿼리 결과에서 상위 행을 샘플링하여 원본 열 목록을 초기화합니다. 이 경우 상위 행에 값이 없는 열은 생략됩니다. 명시적 매핑이 없는 경우 동일한 동작이 복사 실행에 적용됩니다. 복사 런타임에 적용되는 추가 열을 검토하고 매핑에 추가할 수 있습니다.

#### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>샘플 FetchXML 쿼리

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>싱크 형식인 Dynamics

Dynamics에 데이터를 복사하려는 경우 복사 작업 **싱크** 섹션에서는 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 유형 속성은 "DynamicsSink", "DynamicsCrmSink" 또는 "CommonDataServiceForAppsSink"로 설정되어야 합니다. | 예. |
| writeBehavior | 작업의 쓰기 동작입니다. 값은 "Upsert"여야 합니다. | 예 |
| alternateKeyName | upsert를 수행하기 위해 엔터티에 정의된 대체 키 이름입니다. | 아니요. |
| writeBatchSize | 각 일괄 처리에서 Dynamics에 작성된 데이터의 행 수입니다. | 아니요. 기본값은 10입니다. |
| ignoreNullValues | 쓰기 작업 중에 키 필드를 제외한 입력 데이터에서 null 값을 무시할지 여부입니다.<br/><br/>유효한 값은 **TRUE** 및 **FALSE** 입니다.<ul><li>**TRUE**: upsert 또는 업데이트 작업을 수행할 때 대상 개체의 데이터를 변경하지 않고 유지합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.</li><li>**FALSE**: upsert 또는 업데이트 작업을 수행할 때 대상 개체의 데이터를 Null로 업데이트합니다. 삽입 작업을 수행할 때 Null 값을 삽입합니다.</li></ul> | 아니요. 기본값은 **FALSE** 입니다. |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

>[!NOTE]
>Dynamics 싱크에 대한 싱크 **writeBatchSize** 및 복사 작업 **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** 의 기본값은 모두 10입니다. 따라서 기본적으로 레코드 100개가 Dynamics에 동시 제출됩니다.

Dynamics 365 Online의 경우 [조직당 동시 일괄 처리 호출 2개](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations)라는 제한이 있습니다. 이 제한을 초과하면 첫 번째 요청이 실행되기도 전에 "서버 작업 중" 예외가 throw됩니다. 이러한 동시 호출의 제한을 방지하려면 **writeBatchSize** 를 10개 이하로 유지합니다.

최적의 **writeBatchSize**/**parallelCopies** 조합은 엔터티의 스키마에 따라 달라집니다. 스키마 요소에는 열 수, 행 크기 및 이러한 호출에 연결된 플러그 인, 워크플로 또는 워크플로 활동의 수가 포함됩니다. **writeBatchSize**(10) &times; **parallelCopies**(10)의 기본 설정은 Dynamics 서비스에 따라 권장되는 사항입니다. 이 값은 최상의 성능을 제공하지 않을 수도 있지만 대부분의 Dynamics 엔터티에서 작동합니다. 복사 작업 설정의 조합을 조정하여 성능을 튜닝할 수 있습니다.

#### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="retrieving-data-from-views"></a>보기에서 데이터 검색

Dynamics 보기에서 데이터를 검색하려면 저장된 보기 쿼리를 가져온 다음 쿼리를 사용해 데이터를 가져와야 합니다.

서로 다른 유형의 보기를 저장하는 엔터티가 두 개 있습니다. "저장된 쿼리"는 시스템 보기를, "사용자 쿼리"는 사용자 보기를 저장합니다. 보기 정보를 가져오려면 다음 FetchXML 쿼리를 참조하여 "TARGETENTITY"를 `savedquery` 또는 `userquery`(으)로 바꿉니다. 각 엔터티 형식에는 필요에 따라 쿼리에 추가할 수 있는 사용 가능한 특성이 더 많이 있습니다. [savedquery 엔터티](/dynamics365/customer-engagement/web-api/savedquery) 및 [userquery 엔터티](/dynamics365/customer-engagement/web-api/userquery)에 대해 자세히 알아보세요.

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

또한 필터를 추가하여 보기를 필터링할 수 있습니다. 예를 들어 계정 엔터티에서 "내 활성 계정"이라는 보기를 가져오려면 다음 필터를 추가합니다.

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics에 대한 데이터 형식 매핑

Dynamics에서 데이터를 복사하는 경우, 다음 표에서 Dynamics 데이터 형식에서 서비스 내의 중간 데이터 형식으로의 매핑을 볼 수 있습니다. 복사 작업이 원본 스키마에 매핑되고 데이터 형식이 싱크에 매핑되는 방법을 알아보려면 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

다음의 매핑 표를 사용하여 원본 Dynamics 데이터 형식을 기준으로 데이터 세트 구조에 해당 중간 데이터 형식을 구성합니다.

| Dynamics 데이터 형식 | 서비스 중간 데이터 형식 | 원본으로 지원됨 | 싱크로 지원됨 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 부울 | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓([참고 자료](#writing-data-to-a-lookup-field) 참조) |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓([참고 자료](#writing-data-to-a-lookup-field) 참조) |
| AttributeType.ManagedProperty | 부울 | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓([참고 자료](#writing-data-to-a-lookup-field) 참조) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics 데이터 형식 **AttributeType.CalendarRules**, **AttributeType.MultiSelectPicklist** 및 **AttributeType.PartyList** 는 지원되지 않습니다.

## <a name="writing-data-to-a-lookup-field"></a>조회 필드에 데이터 쓰기

고객 및 소유자와 같은 여러 대상이 포함된 조회 필드에 데이터를 쓰려면 이 참고 자료와 예제를 따릅니다.

1. 원본에 필드 값과 해당 대상 엔터티 이름이 모두 포함되도록 합니다.
   - 모든 레코드가 동일한 대상 엔터티에 매핑되는 경우 다음 조건 중 하나를 확인합니다.
      - 원본 데이터에는 대상 엔터티 이름을 저장하는 열이 하나 있습니다.
      - 대상 엔터티를 정의하기 위해 복사 작업 원본에 다른 열을 추가했습니다.
   - 다른 레코드가 다른 대상 엔터티에 매핑되는 경우, 원본 데이터에 해당 대상 엔터티 이름을 저장하는 열이 하나 있는지 확인합니다.

1. 값 및 엔터티-참조 열을 모두 원본에서 싱크로 매핑합니다. 엔터티-참조 열은 `{lookup_field_name}@EntityReference` 특수 명명 패턴을 사용하여 가상 열에 매핑되어야 합니다. 이 열이 Dynamics에 실제로 존재하지는 않습니다. 이는 해당 열이 지정된 멀티 타기팅 조회 필드의 메타데이터 열인지를 나타내는 데 사용됩니다.

예를 들어 원본에 다음의 두 열이 있다고 가정해 보겠습니다.

- Dynamics 대상 엔터티의 기본 키 값인 **GUID** 형식의 **Customerfield** 열
- 대상 엔터티의 논리적 이름인 **String** 형식의 **Target** 열

또한 이러한 데이터를 **Customer** 형식의 싱크 Dynamics 엔터티 필드인 **CustomerField** 로 복사하려는 경우를 가정해 보겠습니다.

복사-작업 열 매핑에서 두 열을 다음과 같이 매핑합니다.

- **CustomerField** 를 **CustomerField** 로 매핑. 이 매핑은 표준 필드 매핑입니다.
- **Target** 을 **CustomerField\@EntityReference** 로 매핑. 싱크 열은 엔터티 참조를 나타내는 가상의 열입니다. 스키마를 가져오면 표시되지 않으므로 매핑에 이러한 필드 이름을 입력합니다.

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png" alt-text="Dynamics 조회-필드 열 매핑":::

모든 원본 레코드가 동일한 대상 엔터티에 매핑되고 원본 데이터가 대상 엔터티 이름을 포함하지 않는 경우, 여기에 바로 가기(복사 작업 원본에서 다른 열 추가)가 있습니다. `{lookup_field_name}@EntityReference` 패턴을 사용하여 새 열의 이름을 지정하고 값을 대상 엔터티 이름으로 설정한 다음, 평소와 같이 열 매핑을 진행합니다. 원본 및 싱크 열 이름이 동일한 경우 기본적으로 복사 작업에서는 이름을 기준으로 열을 매핑하기 때문에 명시적 열 매핑을 건너뛸 수도 있습니다.

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png" alt-text="엔터티-참조 열을 추가하는 Dynamics 조회-필드":::

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 알아보려면 [조회 작업](control-flow-lookup-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

지원되는 데이터 목록의 경우 복사 작업은 원본 및 싱크로 저장합니다. [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
