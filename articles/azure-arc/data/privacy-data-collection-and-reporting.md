---
title: 데이터 수집 및 보고 | Azure Arc 지원 데이터 서비스
description: Arc 지원 데이터 서비스에서 Microsoft로 전송되는 데이터 형식에 대해 설명합니다.
author: dnethi
ms.author: dinethi
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
ms.date: 07/30/2021
ms.custom: template-concept
ms.openlocfilehash: 189021997362da8508d2e60c23cc3acb1238ca55
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535725"
---
# <a name="azure-arc-data-services-data-collection-and-reporting"></a>Azure Arc 데이터 서비스 데이터 수집 및 보고

이 문서에서는 Azure Arc 지원 데이터 서비스가 Microsoft에 전송하는 데이터에 대해 설명합니다. 


## <a name="related-products"></a>관련 제품

Azure Arc 지원 데이터 서비스는 다음 제품의 일부 또는 전부를 사용할 수 있습니다.

- SQL MI – Azure Arc 
- PostgreSQL 하이퍼스케일 – Azure Arc
- Azure Data Studio

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- Azure CLI(az)
- Azure Data CLI(`azdata`) 

## <a name="directly-connected"></a>직접 연결됨

클러스터가 Azure에 직접 연결되도록 구성된 경우 일부 데이터가 자동으로 Microsoft에 전송됩니다. 

다음 표에서는 데이터 형식, 전송 방법, 요구 사항에 대해 설명합니다.  

|데이터 범주|어떤 데이터가 전송되나요?|어떻게 전송되나요?|전송이 필요한가요?
|:----|:----|:----|:----|
|운영 데이터|메트릭 및 로그|자동으로 구성된 경우 자동|아니요
청구 및 인벤토리 데이터|인스턴스 수와 같은 인벤토리 및 사용된 vCore 수와 같은 사용량|자동 |예
진단|문제 해결을 위한 진단 정보|수동으로 Microsoft 지원에 내보내기 및 제공|문제 해결 범위에만 해당하며 표준 [개인 정보 정책](https://privacy.microsoft.com/privacystatement)을 따릅니다.
CEIP(Customer Experience Improvement Program)|[CEIP 요약](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|자동(허용되는 경우)|아니요

## <a name="indirectly-connected"></a>간접적으로 연결됨

클러스터가 Azure에 직접 연결되도록 구성되지 않은 경우 운영 또는 청구 및 인벤토리 데이터가 Microsoft에 자동으로 전송되지 않습니다. Microsoft로 데이터를 전송하려면 내보내기를 구성해야 합니다. 

다음 표에서는 데이터 형식, 전송 방법, 요구 사항에 대해 설명합니다.  

|데이터 범주|어떤 데이터가 전송되나요?|어떻게 전송되나요?|전송이 필요한가요?
|:----|:----|:----|:----|
|운영 데이터|메트릭 및 로그|수동|아니요
청구 및 인벤토리 데이터|인스턴스 수와 같은 인벤토리 및 사용된 vCore 수와 같은 사용량|설명서 |예
진단|문제 해결을 위한 진단 정보|수동으로 Microsoft 지원에 내보내기 및 제공|문제 해결 범위에만 해당하며 표준 [개인 정보 정책](https://privacy.microsoft.com/privacystatement)을 따릅니다.
CEIP(Customer Experience Improvement Program)|[CEIP 요약](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|자동(허용되는 경우)|아니요

## <a name="detailed-description-of-data"></a>데이터에 대한 자세한 설명

이 섹션에서는 Azure Arc 지원 데이터 서비스가 Microsoft에 전송하는 정보에 대해 자세히 설명합니다.

### <a name="operational-data"></a>운영 데이터

모든 데이터베이스 인스턴스 및 Arc 지원 데이터 서비스 플랫폼 자체에 대한 운영 데이터가 수집됩니다. 운영 데이터에는 다음 두 가지 유형이 있습니다. 

- 메트릭 – Arc 지원 데이터 서비스의 일부로 제공되는 유입 DB에 수집되는 성능 및 용량 관련 메트릭입니다. 제공된 Grafana 대시보드에서 이러한 메트릭을 볼 수 있습니다. 

- 로그 – 오류, 경고, 정보 이벤트를 비롯한 모든 구성 요소에서 내보낸 로그는 Arc 지원 데이터 서비스의 일부로 제공되는 Elasticsearch 데이터베이스에 수집됩니다. 제공된 Kibana 대시보드에서 로그를 볼 수 있습니다. 

로컬로 저장된 운영 데이터의 경우 Grafana/Kibana에서 이 데이터를 볼 수 있는 관리자 권한이 필요합니다. 

내보내기/업로드를 선택(간접 연결 모드)하거나 데이터를 Azure Monitor/Log Analytics로 자동으로 전송하도록 선택(직접 연결 모드)하지 않는 한 운영 데이터는 환경을 벗어나지 않습니다. 데이터는 제어하는 Log Analytics 작업 영역으로 이동합니다. 

데이터가 Azure Monitor 또는 Log Analytics로 전송되는 경우 Log Analytics 작업 영역이 위치하는 Azure 지역 또는 데이터 센터를 선택할 수 있습니다. 그런 다음 다른 위치에서 보거나 복사하는 액세스 권한을 제어할 수 있습니다. 

### <a name="billing-and-inventory-data"></a>청구 및 인벤토리 데이터 

청구 데이터는 청구 가능한 사용량을 추적하는 데 사용됩니다. 이 데이터는 서비스 실행에 필수적이며 모든 모드에서 수동 또는 자동으로 전송되어야 합니다. 

모든 데이터베이스 인스턴스와 데이터 컨트롤러 자체는 Azure Resource Manager의 Azure 리소스로 Azure에 반영됩니다. 

리소스 종류로는 다음 3가지가 있습니다. 

- Azure Arc 지원 SQL Managed Instance 
- Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 
- Azure Arc 사용 서버의 SQL Server 
- 데이터 컨트롤러 

다음 섹션에서는 각 리소스 종류에 대해 수집 및 저장되는 속성, 형식, 설명을 보여 줍니다. 

### <a name="sql-server-on-azure-arc-enabled-servers"></a>Azure Arc 사용 서버의 SQL Server 
- SQL Server 에디션입니다. 
   - `string: Edition` 
- 컨테이너 리소스의 리소스 ID입니다(서버의 경우 Azure Arc). 
   - `string: ContainerResourceId` 
- 리소스가 만들어진 시간입니다. 
   - `string: CreateTime` 
- SQL Server 인스턴스에서 사용하는 논리 프로세서의 수입니다.
   - `string: VCore` 
- 클라우드 연결 상태입니다. 
   - `string: Status` 
- SQL Server 업데이트 수준입니다. 
   - `string: PatchLevel` 
- SQL Server 데이터 정렬입니다. 
   - `string: Collation`
- SQL Server 현재 버전입니다.
   - `string: CurrentVersion`
- SQL Server 인스턴스 이름입니다. 
   - `string: InstanceName`
- SQL Server에서 사용되는 동적 TCP 포트입니다. 
   - `string: TcpDynamicPorts`
- SQL Server에서 사용되는 정적 TCP 포트입니다.
   - `string: TcpStaticPorts` 
- SQL Server 제품 ID입니다.
   - `string: ProductId`
- SQL Server 프로비전 상태입니다.
   - `string: ProvisioningState`

### <a name="data-controller"></a>데이터 컨트롤러 

- 위치 정보
   - `public OnPremiseProperty OnPremiseProperty` 
- 원시 Kubernetes 정보(`kubectl get datacontroller`) 
   - `object: K8sRaw` 
- 온-프레미스 클러스터에서 마지막으로 업로드된 데이터입니다.
   - `System.DateTime: LastUploadedDate` 
- 데이터 컨트롤러 상태입니다.
   - `string: ProvisioningState` 

### <a name="postgresql-hyperscale-server-group"></a>PostgreSQL 하이퍼스케일 서버 그룹 

- 데이터 컨트롤러 ID
   - `string: DataControllerId`
- 인스턴스 관리자 이름
   - `string: Admin`
- 기본 인증을 위한 사용자 이름 및 암호
   - `public: BasicLoginInformation BasicLoginInformation` - 원시 Kubernetes 정보(`kubectl get postgres12`) 
   - `object: K8sRaw` - 온-프레미스 클러스터에서 마지막으로 업로드된 데이터입니다. 
   - `System.DateTime: LastUploadedDate` 
- 그룹 프로비전 상태
   - `string: ProvisioningState` 

### <a name="sql-managed-instance"></a>SQL Managed Instance 

- 관리형 인스턴스 ID
   - `public string: DataControllerId` 
- 인스턴스 관리자 사용자 이름 
   - `string: Admin` 
- 인스턴스 시작 시간 
   - `string: StartTime`
- 인스턴스 종료 시간 
   - `string: EndTime` 
- 원시 kubernetes 정보(`kubectl get sqlmi`) 
   - `object: K8sRaw` 
- 기본 인증을 위한 사용자 이름 및 암호입니다. 
   - `public: BasicLoginInformation BasicLoginInformation`
- 온-프레미스 클러스터에서 마지막으로 업로드된 데이터입니다. 
   - `public: System.DateTime LastUploadedDate` 
- SQL 관리형 인스턴스 프로비전 상태
   - `public string: ProvisioningState` 

### <a name="examples"></a>예

구독에서 Azure 리소스를 만들기 위해 Azure로 전송되는 리소스 인벤토리 데이터 JSON 문서의 예시입니다. 

```json
{ 

        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711", 

        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373", 

        "instanceName": "sqlInstance001", 

        "instanceNamespace": "arc", 

        "instanceType": "<resource>", 

        "location": "eastus", 

        "resourceGroupName": "production-resources", 

        "subscriptionId": "<subscription_id>", 

        "isDeleted": false, 

        "externalEndpoint": "32.191.39.83:1433", 

        "vCores": "2", 

        "createTimestamp": "05/29/2020 23:13:17", 

        "updateTimestamp": "05/29/2020 23:13:17" 

    } 
```

 

청구 데이터는 지정된 인스턴스의 시작 시간(‘생성’) 및 종료 시간(‘삭제’)뿐 아니라 지정된 인스턴스(‘코어 한도’)에 사용할 수 있는 코어 수가 변경될 때마다 시작 및 시간을 캡처합니다. 

```json
{ 

          "requestType": "usageUpload", 

          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d", 

          "name": "DataControllerTestName", 

          "subscriptionId": "<subscription_id>", 

          "resourceGroup": "production-resources", 

          "location": "eastus", 

          "uploadRequest": { 

            "exportType": "usages", 

            "dataTimestamp": "2020-06-17T22:32:24Z", 

            "data": "[{\"name\":\"sqlInstance001\", 

                       \"namespace\":\"arc\", 

                       \"type\":\"<resource type>\", 

                       \"eventSequence\":1,  

                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\", 

                       \"startTime\":\"2020-06-17T19:11:47.7533333\", 

                       \"endTime\":\"2020-06-17T19:59:00\", 

                       \"quantity\":1, 

                       \"id\":\"<subscription_id>\"}]", 

           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK" 

```

### <a name="diagnostic-data"></a>진단 데이터

지원 상황에서 데이터베이스 인스턴스 로그, Kubernetes 로그, 기타 진단 로그를 제공하라는 메시지가 표시될 수 있습니다. 지원 팀은 업로드할 수 있는 안전한 위치를 제공합니다. DMV(동적 관리 뷰)에서 진단 데이터가 제공될 수도 있습니다. 사용되는 DMV 또는 쿼리에는 데이터베이스 스키마 메타데이터 세부 정보가 포함될 수 있지만 일반적으로 고객 데이터는 포함되지 않습니다. 진단 데이터에는 암호, 클러스터 IP 또는 개별적으로 식별할 수 있는 데이터가 포함되지 않습니다. 이러한 데이터는 정리되며 가능한 경우 익명으로 저장됩니다. 자동으로 전송되지 않으며 관리자가 수동으로 업로드해야 합니다. 

|필드 이름  |메모  |
|---------|---------|
|오류 로그 |오류를 캡처하는 로그 파일에 고객이 포함되거나 사용자가 개인 데이터(아래 참조)를 제한 및 공유할 수 있습니다. |
|DMV      |동적 관리 뷰는 쿼리 및 쿼리 계획을 포함할 수 있지만 사용자가 제한 및 공유할 수 있습니다.     |
|보기    |보기는 고객 데이터를 포함할 수 있지만 사용자가 제한 및 공유할 수 있습니다.     |
|크래시 덤프 - 고객 데이터 | 크래시 덤프 최대 30일 보존 – 액세스 제어 데이터를 포함할 수 있습니다. <br/><br/> 고객 크래시 덤프에 통계 개체, 행 내 데이터 값, 쿼리 텍스트가 있을 수 있습니다.    |
|크래시 덤프 – 개인 데이터 | 머신, 로그인/사용자 이름, 메일, 위치 정보, 고객 ID – 사용자 동의가 포함되어야 함  |

## <a name="next-steps"></a>다음 단계
[Azure Monitor에 사용량 데이터 업로드](upload-usage-data.md)
