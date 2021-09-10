---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 323e6960a111d8227c14cf38046807ad6c1cada6
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122261450"
---
IMDS(Azure Instance Metadata Service)는 현재 실행 중인 가상 머신 인스턴스에 대한 정보를 제공합니다. 이를 사용하여 가상 머신을 관리하고 구성할 수 있습니다.
이 정보에는 SKU, 스토리지, 네트워크 구성 및 예정된 유지 관리 이벤트가 포함됩니다. 사용 가능한 데이터의 전체 목록은 [엔드포인트 범주 요약](#endpoint-categories)을 참조하세요.

IMDS는 VM(가상 머신) 및 가상 머신 확장 집합 인스턴스를 실행하는 데 사용할 수 있습니다. 모든 엔드포인트는 [Azure Resource Manager](/rest/api/resources/)를 사용하여 만들고 관리하는 VM을 지원합니다. 인스턴스 범주의 증명된 범주 및 네트워크 부분만 클래식 배포 모델을 사용하여 만든 VM을 지원합니다. 증명된 엔드포인트는 제한된 범위에만 해당됩니다.

IMDS는 잘 알려진 라우팅할 수 없는 IP 주소(`169.254.169.254`)에서 사용할 수 있는 REST API입니다. VM 내에서만 액세스할 수 있습니다. VM과 IMDS 간의 통신은 호스트를 떠나지 않습니다.
IMDS를 쿼리할 때 HTTP 클라이언트에서 VM 내의 웹 프록시를 무시하도록 하고 `169.254.169.254`를 [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md)과 동일하게 처리합니다.

## <a name="usage"></a>사용법

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service에 액세스

IMDS에 액세스하려면 [Azure Resource Manager](/rest/api/resources/) 또는 [Azure Portal](https://portal.azure.com)에서 VM을 만들고 다음 샘플을 사용합니다.
자세한 예제는 [Azure Instance Metadata 샘플](https://github.com/microsoft/azureimds)을 참조하세요.

인스턴스에 대한 모든 메타데이터를 검색하는 샘플 코드는 다음과 같습니다. 특정 데이터 원본에 액세스하기 위해 사용 가능한 모든 기능에 대한 개요를 보려면 [엔드포인트 범주](#endpoint-categories)를 참조하세요.

**요청**

> [!IMPORTANT]
> 이 예제에서는 프록시를 무시합니다. IMDS를 쿼리할 때 프록시를 **무시해야 합니다**. 자세한 내용은 [프록시](#proxies)를 참조하세요.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -Depth 64
```

`-NoProxy`에는 PowerShell V6 이상이 필요합니다. 이전 PowerShell 버전의 예는 [샘플 리포지토리](https://github.com/microsoft/azureimds)를 참조하세요.

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | jq
```

---

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>보안 및 인증

Instance Metadata Service는 라우팅할 수 없는 IP 주소에서 실행 중인 가상 머신 인스턴스 내에서만 액세스할 수 있습니다. VM은 자체와 관련된 메타데이터/기능과 상호 작용하도록 제한됩니다. API는 HTTP 전용이며 호스트를 떠나지 않습니다.

IMDS에 대한 요청이 직접 의도된 것인지 확인하고, 의도하지 않거나 원치 않는 요청 리디렉션을 방지하기 위해 다음과 같이 요구합니다.
- `Metadata: true` 헤더를 **포함해야 함**
- `X-Forwarded-For` 헤더를 **포함하지 않아야 함**

이러한 두 요구 사항을 **모두** 충족하지 않는 요청은 서비스에서 거부됩니다.

> [!IMPORTANT]
> IMDS는 중요한 데이터를 위한 채널이 **아닙니다**. API는 인증되지 않았으며 VM의 모든 프로세스에 열려 있습니다. 이 서비스를 통해 노출되는 정보는 VM 내에서 실행 중인 모든 애플리케이션에 대한 공유 정보로 간주되어야 합니다.

VM의 모든 프로세스가 IMDS 엔드포인트에 액세스할 필요가 없는 경우 로컬 방화벽 규칙을 설정하여 액세스를 제한할 수 있습니다. 예를 들어 알려진 시스템 서비스만 Instance Metadata Service에 액세스해야 하는 경우 IMDS 엔드포인트에 방화벽 규칙을 설정하여 특정 프로세스만 액세스하도록 허용하거나 나머지 프로세스에 대해서는 액세스를 거부할 수 있습니다. 


## <a name="proxies"></a>프록시

IMDS는 프록시 뒤에서 사용할 수 없으며 지원되지 **않습니다**. 대부분의 HTTP 클라이언트는 요청 시 프록시를 사용하지 않도록 설정하는 옵션을 제공하며, 이 기능은 IMDS와 통신할 때 활용해야 합니다. 자세한 내용은 클라이언트 설명서를 참조하세요.

> [!IMPORTANT]
> 사용자 환경의 프록시 구성을 알지 못하는 경우에도 **여전히 기본 클라이언트 프록시 설정을 재정의해야 합니다**. 프록시 구성은 자동으로 검색할 수 있으며, 이러한 구성을 무시하지 못하면 나중에 컴퓨터의 구성을 변경해야 하는 경우 중단 위험에 노출됩니다.

## <a name="rate-limiting"></a>속도 제한

일반적으로 IMDS에 대한 요청은 초당 5개 요청으로 제한됩니다. 이 임계값을 초과하는 요청은 429 응답으로 거부됩니다. [관리 ID](#managed-identity) 범주에 대한 요청은 초당 20개 요청 및 5개 동시 요청으로 제한됩니다.

## <a name="http-verbs"></a>HTTP 동사

현재 지원되는 HTTP 동사는 다음과 같습니다.

| 동사 | Description |
|------|-------------|
| `GET` | 요청된 리소스를 검색합니다.

## <a name="parameters"></a>매개 변수

엔드포인트는 필수 및/또는 선택적 매개 변수를 지원할 수 있습니다. 자세한 내용은 해당 엔드포인트에 대한 [스키마](#schema) 및 설명서를 참조하세요.

### <a name="query-parameters"></a>쿼리 매개 변수

IMDS 엔드포인트는 HTTP 쿼리 문자열 매개 변수를 지원합니다. 예를 들어: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2021-01-01&format=json
```

매개 변수를 지정합니다.

| 이름 | 값 |
|------|-------|
| `api-version` | `2021-01-01`
| `format` | `json`

중복된 쿼리 매개 변수 이름이 있는 요청은 거부됩니다.

### <a name="route-parameters"></a>경로 매개 변수

더 큰 json Blob을 반환하는 일부 엔드포인트의 경우 경로 매개 변수를 요청 엔드포인트에 추가하여 응답의 하위 집합으로 필터링할 수 있습니다. 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
매개 변수는 구문 분석된 표현과 상호 작용할 때 json 개체를 탐색하는 데 사용되는 인덱스/키에 해당합니다.

예를 들어 `/metatadata/instance`는 json 개체를 반환합니다.
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

응답을 컴퓨팅 속성으로만 필터링하려면 다음 요청을 보냅니다. 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

마찬가지로 중첩된 속성 또는 특정 배열 요소로 필터링하려면 키를 계속 추가합니다. 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
`Network.interface` 속성에서 첫 번째 요소로 필터링하고 반환합니다.

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> 리프 노드로 필터링하는 경우 `format=json`이 작동하지 않습니다. 이러한 쿼리의 경우 기본 형식이 json이므로 `format=text`를 명시적으로 지정해야 합니다.

## <a name="schema"></a>스키마

### <a name="data-format"></a>데이터 형식

기본적으로 IMDS는 데이터를 JSON 형식(`Content-Type: application/json`)으로 반환합니다. 그러나 응답 필터링을 지원하는 엔드포인트([경로 매개 변수](#route-parameters) 참조)는 `text` 형식도 지원합니다.

기본이 아닌 응답 형식에 액세스하려면 요청된 형식을 요청의 쿼리 문자열 매개 변수로 지정합니다. 다음은 그 예입니다.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

json 응답에서 모든 기본 요소는 `string` 형식이며, 누락되거나 적용할 수 없는 값은 항상 포함되지만 빈 문자열로 설정됩니다.

### <a name="versioning"></a>버전 관리

IMDS에 대한 버전이 관리되므로 반드시 API 버전을 HTTP 요청에 지정해야 합니다. 이 요구 사항에 대한 유일한 예외는 사용 가능한 API 버전을 동적으로 검색하는 데 사용할 수 있는 [versions](#versions) 엔드포인트입니다.

새로운 버전이 추가되더라도 스크립트가 특정 데이터 형식에 종속성이 있는 경우 이전 버전에 여전히 액세스할 수 있습니다.

버전을 지정하지 않으면 지원되는 최신 버전 목록이 포함된 오류가 표시됩니다.
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>지원되는 API 버전
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01
- 2021-02-01
- 2021-03-01
- 2021-05-01

### <a name="swagger"></a>Swagger

IMDS에 대한 전체 Swagger 정의는 https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md 에서 제공됩니다.

## <a name="regional-availability"></a>국가별 가용성

이 서비스는 모든 Azure 클라우드에서 **일반 공급** 됩니다.

## <a name="root-endpoint"></a>루트 엔드포인트

루트 엔드포인트는 `http://169.254.169.254/metadata`입니다.

## <a name="endpoint-categories"></a>엔드포인트 범주

IMDS API에는 서로 다른 데이터 원본을 나타내는 여러 엔드포인트 범주가 포함되며, 각 범주에는 하나 이상의 엔드포인트가 포함됩니다. 자세한 내용은 각 범주를 참조하세요.

| 루트 범주 | Description | 도입된 버전 |
|---------------|-------------|--------------------|
| `/metadata/attested` | [증명된 데이터](#attested-data) 참조 | 2018-10-01
| `/metadata/identity` | [IMDS를 통한 관리 ID](#managed-identity) 참조 | 2018-02-01
| `/metadata/instance` | [인스턴스 메타데이터](#instance-metadata) 참조 | 2017-04-02
| `/metadata/loadbalancer` | [IMDS를 통한 Load Balancer 메타데이터 검색](#load-balancer-metadata) 참조 | 2020-10-01
| `/metadata/scheduledevents` | [IMDS를 통한 Scheduled Events](#scheduled-events) 참조 | 2017-08-01
| `/metadata/versions` | [버전](#versions) 참조 | 해당 없음

## <a name="versions"></a>버전

> [!NOTE]
> 이 기능은 2020-10-01 버전과 함께 릴리스되었으며, 현재 롤아웃되고 있지만 일부 지역에서 아직 사용할 수 없습니다.

### <a name="list-api-versions"></a>API 버전 나열

지원되는 API 버전 세트를 반환합니다.

```
GET /metadata/versions
```

#### <a name="parameters"></a>매개 변수

없음(이 엔드포인트에 대한 버전이 없음)

#### <a name="response"></a>응답

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>인스턴스 메타데이터

### <a name="get-vm-metadata"></a>VM 메타데이터 가져오기

컴퓨팅, 네트워크 및 스토리지를 포함하여 VM 인스턴스에 대한 중요한 메타데이터를 공개합니다. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>매개 변수

| 속성 | 필수/선택 | 설명 |
|------|-------------------|-------------|
| `api-version` | 필수 | 요청을 처리하는 데 사용되는 버전입니다.
| `format` | 선택 사항* | 응답의 형식(`json` 또는 `text`)입니다. *참고: 요청 매개 변수를 사용할 때 필요할 수 있습니다.

이 엔드포인트는 [경로 매개 변수](#route-parameters)를 통한 응답 필터링을 지원합니다.

#### <a name="response"></a>응답

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

스키마 분석:

**컴퓨팅**

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `azEnvironment` | VM이 실행되는 Azure 환경 | 2018-10-01
| `customData` | [IMDS에서는](#frequently-asked-questions) 이 기능이 사용되지 않으며 비활성화됩니다. `userData`로 대체되었습니다. | 2019-02-01
| `evictionPolicy` | [스폿 VM](../articles/virtual-machines/spot-vms.md)이 제거되는 방법을 설정합니다. | 2020-12-01
| `isHostCompatibilityLayerVm` | VM이 호스트 호환성 계층에서 실행되는지 여부를 식별합니다. | 2020-06-01
| `licenseType` | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)에 대한 라이선스 유형입니다. 이는 AHB 사용 VM에 대해서만 제공됩니다. | 2020-09-01
| `location` | VM을 실행하는 Azure 지역 | 2017-04-02
| `name` | VM의 이름 | 2017-04-02
| `offer` | VM 이미지에 대한 제품 정보이며 Azure 이미지 갤러리에서 배포된 이미지에만 있습니다. | 2017-04-02
| `osProfile.adminUsername` | 관리자 계정의 이름을 지정합니다. | 2020-07-15
| `osProfile.computerName` | 컴퓨터의 이름을 지정합니다. | 2020-07-15
| `osProfile.disablePasswordAuthentication` | 암호 인증을 사용할 수 없는지 여부를 지정합니다. 이는 Linux VM에 대해서만 제공됩니다. | 2020-10-01
| `osType` | Linux 또는or Windows | 2017-04-02
| `placementGroupId` | 가상 머신 확장 집합의 [배치 그룹](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | Azure Marketplace 이미지에 해당하는 VM의 이름, 제품 및 게시자를 포함하는 [계획](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
| `platformUpdateDomain` |  VM을 실행 중인 [업데이트 도메인](../articles/virtual-machines/availability.md) | 2017-04-02
| `platformFaultDomain` | VM을 실행 중인 [장애 도메인](../articles/virtual-machines/availability.md) | 2017-04-02
| `priority` | VM의 우선 순위입니다. 자세한 내용은 [스폿 VM](../articles/virtual-machines/spot-vms.md)을 참조하세요. | 2020-12-01
| `provider` | VM의 공급자 | 2018-10-01
| `publicKeys` | VM 및 경로에 할당된 [공개 키 컬렉션](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | VM 이미지 게시자 | 2017-04-02
| `resourceGroupName` | Virtual Machine에 대한 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | 리소스의 [정규화된](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM 이미지에 해당하는 SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | VM에서 UEFI 보안 부팅을 사용하도록 설정하는지 여부를 확인합니다. | 2020-06-01
| `securityProfile.virtualTpmEnabled` | VM에서 가상 TPM(신뢰할 수 있는 플랫폼 모듈)이 사용하도록 설정되어 있는지 여부를 식별합니다. | 2020-06-01
| `storageProfile` | 아래 '스토리지 프로필' 참조 | 2019-06-01
| `subscriptionId` | Virtual Machine에 대한 Azure 구독 | 2017-08-01
| `tags` | Virtual Machine에 대한 [태그](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | 원활한 프로그래매틱 구문 분석을 위해 JSON 배열로 형식이 지정된 태그  | 2019-06-04
| `userData` | 프로비저닝 중 또는 후에 사용하기 위해 VM을 만들 때 지정된 데이터 세트(Base64 인코딩)  | 2021-01-01
| `version` | VM 이미지의 버전 | 2017-04-02
| `vmId` | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | 가상 머신 확장 집합의 [가상 머신 확장 집합 이름](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM 크기](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | 가상 머신의 [가용성 영역](../articles/availability-zones/az-overview.md) | 2017-12-01

**스토리지 프로필**

VM의 스토리지 프로필은 이미지 참조, OS 디스크, 데이터 디스크의 세 가지 범주와 로컬 임시 디스크에 대한 추가 개체로 구분됩니다.

이미지 참조 개체에는 OS 이미지에 대한 다음 정보가 포함되어 있습니다.

| 데이터 | Description |
|------|-------------|
| `id` | 리소스 ID
| `offer` | 플랫폼의 제품 또는 마켓플레이스 이미지
| `publisher` | 이미지 게시자입니다.
| `sku` | 이미지 SKU
| `version` | 플랫폼의 버전 또는 마켓플레이스 이미지

OS 디스크 개체에는 VM에서 사용하는 OS 디스크에 대한 다음 정보가 포함됩니다.

| 데이터 | Description |
|------|-------------|
| `caching` | 캐싱 요구 사항
| `createOption` | VM이 생성된 방법에 대한 정보
| `diffDiskSettings` | 임시 디스크 설정
| `diskSizeGB` | 디스크 크기(GB)
| `image`   | 원본 사용자 이미지 가상 하드 디스크
| `managedDisk` | 관리 디스크 매개 변수
| `name`    | 디스크 이름
| `vhd`     | 가상 하드 디스크
| `writeAcceleratorEnabled` | writeAccelerator를 디스크에서 사용할 수 있는지 여부

데이터 디스크 배열에는 VM에 연결된 데이터 디스크 목록이 포함됩니다. 각 데이터 디스크 개체에는 다음 정보가 들어 있습니다.

데이터 | Description | 도입된 버전 |
|------|-----------|--------------------|
| `bytesPerSecondThrottle`* | 디스크 읽기/쓰기 할당량(바이트) | 2021-05-01
| `caching` | 캐싱 요구 사항 | 2019-06-01
| `createOption` | VM이 생성된 방법에 대한 정보 | 2019-06-01
| `diffDiskSettings` | 임시 디스크 설정 | 2019-06-01
| `diskCapacityBytes`* | 디스크 크기(바이트) | 2021-05-01
| `diskSizeGB` | 디스크 크기(GB) | 2019-06-01
| `encryptionSettings` | 디스크의 암호화 설정 | 2019-06-01
| `image` | 원본 사용자 이미지 가상 하드 디스크 | 2019-06-01
| `isSharedDisk`* | 리소스 간에 디스크가 공유되는지 식별 | 2021-05-01
| `isUltraDisk` | 데이터 디스크가 Ultra Disk인지 식별 | 2021-05-01
| `lun`     | 디스크의 논리 단위 번호 | 2019-06-01
| `managedDisk` | 관리 디스크 매개 변수 | 2019-06-01
| `name` | 디스크 이름 | 2019-06-01
| `opsPerSecondThrottle`* | IOPS의 디스크 읽기/쓰기 할당량 | 2021-05-01
| `osType` | 디스크에 포함된 OS 유형 | 2019-06-01
| `vhd` | 가상 하드 디스크 | 2019-06-01
| `writeAcceleratorEnabled` | writeAccelerator를 디스크에서 사용할 수 있는지 여부 | 2019-06-01

\* 이 필드는 Ultra Disk에 대해서만 채워지며 Ultra Disk가 아닌 경우 빈 문자열이 됩니다.

리소스 디스크 개체는 VM에 연결된 [로컬 임시 디스크](../articles/virtual-machines/managed-disks-overview.md#temporary-disk)의 크기(KB)를 포함합니다.
[VM에 대한 로컬 임시 디스크가 없으면](../articles/virtual-machines/azure-vms-no-temp-disk.yml) 이 값은 0입니다. 

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `resourceDisk.size` | VM에 대한 로컬 임시 디스크 크기(KB) | 2021-02-01

**Network**

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM의 로컬 IPv4 주소 | 2017-04-02
| `ipv4.publicIpAddress` | VM의 공용 IPv4 주소 | 2017-04-02
| `subnet.address` | VM의 서브넷 주소 | 2017-04-02
| `subnet.prefix` | 서브넷 접두사, 예:24 | 2017-04-02
| `ipv6.ipAddress` | VM의 로컬 IPv6 주소 | 2017-04-02
| `macAddress` | VM MAC 주소 | 2017-04-02

### <a name="get-user-data"></a>사용자 데이터 가져오기

새 VM을 만들 때는 VM 프로비저닝 중 또는 후에 사용할 데이터 세트를 지정하고 IMDS를 통해 검색할 수 있습니다. [여기](../articles/virtual-machines/user-data.md)에서 최종 사용자 데이터 환경을 확인하세요. 

사용자 데이터를 설정하려면 [여기](https://aka.ms/ImdsUserDataArmTemplate)에서 빠른 시작 템플릿을 활용합니다. 아래 샘플에서는 IMDS를 통해 이 데이터를 검색하는 방법을 보여줍니다. 이 기능은 버전 `2021-01-01` 이상으로 릴리스되었습니다.

> [!NOTE]
> 보안 알림: IMDS는 VM의 모든 애플리케이션에 공개되기 때문에 중요한 데이터를 사용자 데이터에 저장해서는 안 됩니다.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
$userData = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text"
[System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($userData))
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>샘플 1: Azure에서 실행 중인 VM 추적

서비스 공급자는 소프트웨어를 실행 중인 VM의 수를 추적하거나 VM의 고유성을 추적해야 하는 에이전트가 있어야 합니다. VM의 고유 ID를 가져오려면 Instance Metadata Service의 `vmId` 필드를 사용합니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**응답**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>샘플 2: 여러 데이터 복제본 배치

특정 시나리오의 경우 다양한 데이터 복제본 배치가 매우 중요합니다. 예를 들어 [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [오케스트레이터](https://kubernetes.io/docs/user-guide/node-selection/)를 통한 컨테이너 배치를 수행하려면 VM이 실행되는 `platformFaultDomain` 및 `platformUpdateDomain`을 알아야 할 수 있습니다.
인스턴스에 대해 [가용성 영역](../articles/availability-zones/az-overview.md)을 사용하여 이러한 결정을 내릴 수도 있습니다.
이 데이터는 IMDS를 통해 직접 쿼리할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**응답**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>샘플 3: VM 태그 가져오기

VM 태그는 인스턴스/컴퓨팅/태그 엔드포인트 아래 인스턴스 API에 포함되어 있습니다.
태그를 특정 분류법에 따라 논리적으로 정리하기 위해 Azure VM에 태그가 적용되었을 수 있습니다. VM에 할당된 태그는 아래 요청을 사용하여 검색할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

---

**응답**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags` 필드는 태그가 세미콜론으로 구분된 문자열입니다. 세미콜론이 태그 자체에 사용되는 경우 이 출력이 문제가 될 수 있습니다. 태그를 프로그래밍 방식으로 추출하도록 파서가 작성된 경우 `tagsList` 필드를 사용해야 합니다. `tagsList` 필드는 구분 기호가 없는 JSON 배열이며, 이에 따라 구문 분석이 더 쉽습니다. VM에 할당된 tagsList는 아래 요청을 사용하여 검색할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**응답**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>샘플 4: 지원 사례 중 VM에 대한 자세한 정보 가져오기

서비스 공급자는 지원 요청을 받을 수 있으며 이 때 VM에 대해 자세한 정보를 알아야 하는 경우가 있습니다. 고객에게 컴퓨팅 메타데이터를 공유하도록 요청하면 지원 전문가가 Azure에서 VM의 종류에 대해 알 수 있도록 기본 정보가 제공될 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/MicrosoftCompute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>샘플 5: VM이 실행되는 Azure 환경 가져오기

Azure에는 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)와 같은 다양한 소버린 클라우드가 있습니다. 경우에 따라 몇 가지 런타임 결정을 내리려면 Azure 환경이 필요합니다. 다음 샘플에서는 이러한 동작을 수행하는 방법을 보여줍니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**응답**

```
AzurePublicCloud
```

클라우드 및 Azure 환경의 값은 아래에 나와 있습니다.

| 클라우드 | Azure 환경 |
|-------|-------------------|
| [일반 공급되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure 독일](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>샘플 6: 네트워크 정보 검색

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**응답**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>샘플 7: 공용 IP 주소 검색

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>증명된 데이터

### <a name="get-attested-data"></a>증명된 데이터 가져오기

IMDS는 제공된 데이터가 Azure에서 제공되도록 보장하는 데 도움이 됩니다. Microsoft는 이 정보의 일부에 서명하므로 Azure Marketplace의 이미지가 Azure에서 실행되는 이미지인지 확인할 수 있습니다.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>매개 변수

| 속성 | 필수/선택 | 설명 |
|------|-------------------|-------------|
| `api-version` | 필수 | 요청을 처리하는 데 사용되는 버전입니다.
| `nonce` | 선택 사항 | 암호화 nonce 역할을 하는 10자리 문자열입니다. 값이 제공되지 않는 경우 IMDS는 현재 UTC 타임스탬프를 사용합니다.

#### <a name="response"></a>응답

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시된 nonce 값이 반환될 수 있습니다.

서명 Blob은 [pkcs7](https://aka.ms/pkcs7)로 서명된 버전의 문서입니다. 여기에는 특정 VM별 세부 정보와 함께 서명에 사용되는 인증서가 포함됩니다.

Azure Resource Manager를 사용하여 만든 VM의 경우 문서에는 문서 만들기 및 만료에 대한 `vmId`, `sku`, `nonce`, `subscriptionId`, `timeStamp` 및 이미지에 대한 계획 정보가 포함됩니다. 요금제 정보는 Azure Marketplace 이미지에 대해서만 채워집니다.

클래식 배포 모델을 사용하여 만든 VM의 경우 `vmId`만 채워집니다. 응답에서 인증서를 추출하고, 이를 사용하여 여 응답이 유효하고 Azure에서 제공되는지 확인할 수 있습니다.

디코딩된 문서에 포함되는 필드는 다음과 같습니다.

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `licenseType` | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)에 대한 라이선스 유형입니다. 이는 AHB 사용 VM에 대해서만 제공됩니다. | 2020-09-01
| `nonce` | 요청에 선택적으로 제공할 수 있는 문자열입니다. `nonce`가 제공되지 않은 경우 현재 협정 세계시 타임스탬프가 사용됩니다. | 2018-10-01
| `plan` | [Azure Marketplace 이미지 계획](/rest/api/compute/virtualmachines/createorupdate#plan)입니다. 계획 ID(이름), 제품 이미지 또는 제안(제품) 및 게시자 ID(게시자)를 포함합니다. | 2018-10-01
| `timestamp.createdOn` | 서명된 문서가 작성된 시점의 UTC 타임스탬프입니다. | 2018-20-01
| `timestamp.expiresOn` | 서명된 문서가 만료되는 시점의 UTC 타임스탬프입니다. | 2018-10-01
| `vmId` | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | Virtual Machine에 대한 Azure 구독 | 2019-04-30
| `sku` | VM 이미지에 해당하는 SKU | 2019-11-01

> [!NOTE]
> 클래식(비 Azure Resource Manager) VM의 경우 vmId만 채워집니다.

예제 문서:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>샘플 1: VM이 Azure에서 실행되는지 확인

Azure Marketplace의 공급업체는 자체 소프트웨어가 Azure에서만 실행되도록 사용이 허가되었는지 확인하려고 합니다. 누군가가 VHD를 온-프레미스 환경에 복사하는 경우 공급업체에서 이를 검색할 수 있어야 합니다. IMDS를 통해 이러한 공급업체는 Azure의 응답만 보장하는 서명된 데이터를 가져올 수 있습니다.

> [!NOTE]
> 이 샘플을 사용하려면 jq 유틸리티를 설치해야 합니다.

**유효성 검사**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

서명이 Microsoft Azure에서 제공된 것인지 확인하고, 인증서 체인에서 오류를 확인합니다.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**결과**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

서명이 Microsoft Azure에서 제공된 것인지 확인하고, 인증서 체인에서 오류를 확인합니다.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시된 `nonce` 값이 반환될 수 있습니다.

초기 요청에서 `nonce` 매개 변수를 제공한 경우 서명된 문서의 `nonce`를 비교할 수 있습니다.

> [!NOTE]
> 퍼블릭 클라우드 및 각 소버린 클라우드에 대한 인증서는 서로 다릅니다.

| 클라우드 | 인증서 |
|-------|-------------|
| [일반 공급되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure 독일](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 퍼블릭 클라우드에 대한 `metadata.azure.com`과 정확하게 일치하는 항목이 인증서에 없을 수 있습니다. 따라서 인증 유효성 검사는 `.metadata.azure.com` 하위 도메인의 일반 이름을 허용해야 합니다.

유효성을 검사하는 동안 네트워크 제약 조건으로 인해 중간 인증서를 다운로드할 수 없는 경우 중간 인증서를 고정할 수 있습니다. Azure는 표준 PKI 사례인 인증서를 롤오버합니다. 롤오버가 수행되는 경우 고정된 인증서를 업데이트해야 합니다. 중간 인증서 업데이트에 대한 변경이 계획될 때마다 Azure 블로그가 업데이트되고 Azure 고객에게 알려줍니다. 

중간 인증서는 [PKI 리포지토리](https://www.microsoft.com/pki/mscorp/cps/default.htm)에서 찾을 수 있습니다. 각 지역의 중간 인증서는 다를 수 있습니다.

> [!NOTE]
> Azure China 21Vianet에 대한 중간 인증서는 Baltimore가 아닌 DigiCert Global Root CA에서 제공됩니다.
루트 체인 인증 기관 변경의 일부로 Azure 중국에 대한 중간 인증서를 고정한 경우 해당 중간 인증서를 업데이트해야 합니다.


## <a name="managed-identity"></a>관리 ID

시스템이 할당한 관리 ID는 VM에서 사용하도록 설정할 수 있습니다. 하나 이상의 사용자가 할당한 관리 ID를 VM에 할당할 수도 있습니다.
그런 다음, IMDS에서 관리 ID에 대한 토큰을 요청할 수 있습니다. 이러한 토큰을 사용하여 Azure Key Vault와 같은 다른 Azure 서비스를 인증합니다.

이 기능을 사용하도록 설정하는 자세한 단계는 [액세스 토큰 획득](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

## <a name="load-balancer-metadata"></a>Load Balancer 메타데이터
Azure 표준 Load Balancer 내부에 가상 머신 또는 가상 머신 집합 인스턴스를 배치하는 경우 IMDS를 사용하여 부하 분산 장치 및 인스턴스와 관련된 메타데이터를 검색할 수 있습니다. 자세한 내용은 [부하 분산 장치 정보 검색](../articles/load-balancer/instance-metadata-service-load-balancer.md)을 참조하세요.

## <a name="scheduled-events"></a>예약된 이벤트
IMDS를 사용하여 예약된 이벤트의 상태를 가져올 수 있습니다. 그런 다음, 사용자는 이러한 이벤트에 대해 실행할 작업 세트를 지정할 수 있습니다. 자세한 내용은 [Linux의 예약된 이벤트](../articles/virtual-machines/linux/scheduled-events.md) 또는 [Windows의 예약된 이벤트](../articles/virtual-machines/windows/scheduled-events.md)를 참조하세요.


## <a name="sample-code-in-different-languages"></a>다양한 언어의 샘플 코드

다음 표에는 VM 내에서 다른 언어를 사용하여 IMDS를 호출하는 샘플이 나와 있습니다.

| 언어 | 예제 |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>오류 및 디버깅

찾을 수 없는 데이터 요소 또는 형식이 잘못된 요청이 있으면 Instance Metadata Service는 표준 HTTP 오류를 반환합니다. 다음은 그 예입니다.

| HTTP 상태 코드 | 이유 |
|------------------|--------|
| `200 OK` | 요청이 성공했습니다.
| `400 Bad Request` | 리프 노드를 쿼리할 때 `Metadata: true` 헤더 또는 `format=json` 매개 변수가 없습니다.
| `404 Not Found` | 요청된 요소가 없음
| `405 Method Not Allowed` | HTTP 메서드(동사)는 엔드포인트에서 지원되지 않습니다.
| `410 Gone` | 최대 70초 후 다시 시도
| `429 Too Many Requests` | API [속도 한도](#rate-limiting)를 초과했습니다.
| `500 Service Error` | 잠시 후 다시 시도하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

- `400 Bad Request, Required metadata header not specified` 오류가 발생했습니다. 무슨 의미인가요?
  - IMDS를 사용하려면 `Metadata: true` 헤더를 요청에 전달해야 합니다. REST 호출에서 이 헤더를 전달하면 IMDS에 액세스할 수 있습니다.

- VM에 대한 컴퓨팅 정보를 구할 수 없는 이유가 무엇인가요?
  - 현재 IMDS는 Azure Resource Manager를 사용하여 만든 인스턴스만 지원합니다.

- 최근에 Azure Resource Manager를 통해 VM을 만들었습니다. 컴퓨팅 메타데이터 정보가 왜 표시되지 않나요?
  - 2016년 9월 이후에 VM을 만든 경우 [태그](../articles/azure-resource-manager/management/tag-resources.md)를 추가하여 컴퓨팅 메타데이터 보기를 시작합니다. 2016년 9월 이전에 VM을 만든 경우 VM 인스턴스에서 확장 또는 데이터 디스크를 추가하거나 제거하여 메타데이터를 새로 고칩니다.

- 사용자 데이터는 사용자 지정 데이터와 동일한가요?
  - 사용자 데이터는 사용자 지정 데이터와 유사한 기능을 제공하여 VM 인스턴스에 사용자의 메타데이터를 전달할 수 있도록 합니다. 차이점은, 사용자 데이터는 IMDS를 통해 검색되며 VM 인스턴스의 수명 전체에 걸쳐 유지된다는 점입니다. 기존의 사용자 지정 데이터 기능은 [이 문서](../articles/virtual-machines/custom-data.md)에 설명된 대로 계속 작동합니다. 그러나 IMDS를 통해서가 아니라 로컬 시스템 폴더를 통해서만 사용자 지정 데이터를 가져올 수 있습니다.

- 새 버전의 모든 데이터가 채워지지 않는 이유는 무엇인가요?
  - 2016년 9월 이후에 VM을 만든 경우 [태그](../articles/azure-resource-manager/management/tag-resources.md)를 추가하여 컴퓨팅 메타데이터 보기를 시작합니다. 2016년 9월 이전에 VM을 만든 경우 VM 인스턴스에서 확장 또는 데이터 디스크를 추가하거나 제거하여 메타데이터를 새로 고칩니다.

- `500 Internal Server Error` 또는 `410 Resource Gone` 오류가 발생하는 이유는 무엇인가요?
  - 요청을 다시 시도합니다. 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요. 문제가 지속되면 Azure Portal에서 VM에 대한 지원 문제를 만듭니다.

- 가상 머신 확장 집합 인스턴스에서 작동하나요?
  - 예, IMDS는 가상 머신 확장 집합 인스턴스에 사용할 수 있습니다.

- 가상 머신 확장 집합에서 내 태그를 업데이트했지만 단일 인스턴스 VM과 달리 인스턴스에 표시되지 않습니다. 내가 무엇인가 잘못했나요?
  - 현재 가상 머신 확장 집합에 대한 태그는 다시 부팅, 이미지로 다시 설치 또는 인스턴스에 대한 디스크 변경 시에만 VM에 표시됩니다.

- `instance/compute` 세부 정보에서 내 VM에 대한 SKU 정보가 표시되지 않는 이유는 무엇인가요?
  - Azure Marketplace에서 만든 사용자 지정 이미지의 경우 Azure 플랫폼은 사용자 지정 이미지에 대한 SKU 정보 및 사용자 지정 이미지에서 만든 모든 VM에 대한 세부 정보를 유지하지 않습니다. 이는 의도적으로 설계된 것이므로 VM `instance/compute` 세부 정보에 표시되지 않습니다.

- 내 서비스 호출에 대한 요청 시간이 초과된 이유는 무엇인가요?
  - 메타데이터 호출은 VM의 기본 네트워크 카드에 할당된 기본 IP 주소에서 수행해야 합니다. 또한 경로를 변경한 경우 169.254.169.254/32 주소에 대한 경로가 VM의 로컬 라우팅 테이블에 있어야 합니다.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. 로컬 라우팅 테이블을 덤프하고 IMDS 항목을 찾습니다. 예를 들어:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. `169.254.169.254`에 대한 경로가 있는지 확인하고, 해당 네트워크 인터페이스(예: `172.16.69.7`)를 확인합니다.
    1. 인터페이스 구성을 덤프하고, MAC(물리적) 주소를 확인하여 라우팅 테이블에서 참조된 항목에 해당하는 인터페이스를 찾습니다.
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. 인터페이스가 VM의 기본 NIC 및 기본 IP에 해당하는지 확인합니다. 기본 NIC 및 IP는 Azure Portal에서 네트워크 구성을 확인하거나 Azure CLI를 통해 조회하여 확인할 수 있습니다. 개인 IP(및 CLI를 사용하는 경우 MAC 주소)를 확인합니다. PowerShell CLI 예제는 다음과 같습니다.
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. 일치하지 않는 경우 기본 NIC 및 IP가 대상으로 지정되도록 라우팅 테이블을 업데이트합니다.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. `netstat -r`과 같은 명령을 사용하여 로컬 라우팅 테이블을 덤프하고 IMDS 항목을 찾습니다. 예를 들어 다음과 같습니다.
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. `169.254.169.254`에 대한 경로가 있는지 확인하고, 해당 네트워크 인터페이스(예: `eth0`)를 확인합니다.
    1. 라우팅 테이블의 해당 인터페이스에 대한 인터페이스 구성을 덤프합니다(구성 파일의 정확한 이름은 다를 수 있음).
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. 동적 IP를 사용하는 경우 MAC 주소를 확인합니다. 고정 IP를 사용하는 경우 나열된 IP 및/또는 MAC 주소를 확인할 수 있습니다.
    1. 인터페이스가 VM의 기본 NIC 및 기본 IP에 해당하는지 확인합니다. 기본 NIC 및 IP는 Azure Portal에서 네트워크 구성을 확인하거나 Azure CLI를 통해 조회하여 확인할 수 있습니다. 개인 IP(및 CLI를 사용하는 경우 MAC 주소)를 확인합니다. PowerShell CLI 예제는 다음과 같습니다.
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. 일치하지 않는 경우 기본 NIC 및 IP가 대상으로 지정되도록 라우팅 테이블을 업데이트합니다.

    ---

- Windows Server의 장애 조치(failover) 클러스터링
  - 장애 조치(failover) 클러스터링을 사용하여 IMDS를 쿼리하는 경우 경로를 라우팅 테이블에 추가해야 하는 경우가 있습니다. 방법은 다음과 같습니다.

    1. 관리자 권한으로 명령 프롬프트를 엽니다.

    1. 다음 명령을 실행하고, IPv4 라우팅 테이블의 네트워크 대상 인터페이스(`0.0.0.0`) 주소를 확인합니다.

    ```bat
    route print
    ```

    > [!NOTE]
    > 다음 예제 출력은 장애 조치(failover) 클러스터가 사용하도록 설정된 Windows Server VM에서 제공한 것입니다. 간단히 하기 위해 출력에는 IPv4 라우팅 테이블만 포함되었습니다.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    다음 명령을 실행하고, 네트워크 대상의 인터페이스 주소(`0.0.0.0`)를 사용합니다(이 예제에서는 `10.0.1.10`).

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>지원

여러 번 시도한 후에도 메타데이터 응답을 받을 수 없는 경우 Azure Portal에서 지원 문제를 만들 수 있습니다.

## <a name="product-feedback"></a>제품 사용자 의견

[여기](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)의 Virtual Machines > Instance Metadata Service 아래에서 제품 피드백 및 아이디어를 사용자 피드백 채널에 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [VM에 대한 액세스 토큰 획득](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Linux의 예약된 이벤트](../articles/virtual-machines/linux/scheduled-events.md)

- [Windows의 예약된 이벤트](../articles/virtual-machines/windows/scheduled-events.md)
