---
title: Azure Purview 컬렉션 메타데이터 정책 및 역할 API에 대한 자세한 정보
description: 이 자습서에서는 사용자, 그룹 또는 서비스 주체의 Azure Purview 컬렉션에 대한 역할 기반 액세스 제어를 관리하는 방법을 설명합니다.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: cfb62050da2b1c72ec8e620f9a4222552f50daa2
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728687"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-on-azure-purview-collections"></a>자습서: REST API를 사용하여 Azure Purview 컬렉션에 대한 역할 기반 액세스 제어 관리 

Azure Purview의 액세스 제어는 2021년 8월에 Azure IAM(ID 및 액세스 관리)(컨트롤 플레인)에서 [Azure Purview 컬렉션](how-to-create-and-manage-collections.md)(데이터 평면)으로 이동되었습니다. 이 변경에 따라 엔터프라이즈 데이터 큐레이터와 관리자는 Azure Purview에서 스캔한 데이터 원본에 대한 더 정확하고 세분화된 액세스 제어를 제공할 수 있습니다. 또한 이 변경을 통해 조직에서 데이터의 적절한 액세스와 사용을 감사할 수 있습니다.

이 자습서에서는 Azure Purview 메타데이터 정책 API를 단계별로 사용하여 사용자, 그룹 또는 서비스 주체를 컬렉션에 추가하고 해당 컬렉션 내에서 해당 역할을 관리하거나 제거하는 방법을 안내합니다. REST API는 Azure Portal 또는 Azure Purview Studio를 사용하여 동일하게 세분화된 역할 기반 액세스 제어를 구현하는 대체 방법입니다.

Azure Purview의 기본 제공 역할에 대한 자세한 내용은 [Azure Purview 권한 가이드](catalog-permissions.md#roles)를 참조하세요. 이 가이드는 역할을 사용자에게 부여된 액세스 권한 수준에 매핑합니다.

## <a name="metadata-policy-api-reference-summary"></a>메타데이터 정책 API 참조 요약
다음 표에서는 [Azure Purview 메타데이터 정책 API 참조에 대한 개요](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)를 제공합니다. 

이러한 API를 실행하기 전에 {pv-acc-name}을 Azure Purview 계정의 이름으로 바꿉니다. 예를 들어 Purview 계정 이름이 *FabrikamPurviewAccount* 인 경우 API 엔드포인트는 *FabrikamPurviewAccount.purview.azure.com* 이 됩니다.

| API&nbsp;함수 | REST&nbsp;메서드 | API&nbsp;엔드포인트 | 설명 | 
| :- | :- | :- | :- | 
| 모든 메타데이터 역할 읽기| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Azure Purview 계정에서 모든 메타데이터 역할을 읽습니다.| 
| 컬렉션 이름을 기준으로 메타데이터 정책 읽기| GET| https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| 지정된 컬렉션 이름(정책을 만들 때 Azure Purview에서 생성한 6자의 임의 이름)을 사용하여 메타데이터 정책을 읽습니다.| 
| PolicyID를 기준으로 메타데이터 정책 읽기| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 지정된 정책 ID를 사용하여 메타데이터 정책을 읽습니다. 정책 ID는 GUID 형식입니다.| 
| 모든 메타데이터 정책 읽기| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Azure Purview 계정에서 모든 메타데이터 정책을 읽습니다. 이 API에서 생성된 JSON 출력 목록에서 사용할 특정 정책을 선택할 수 있습니다.| 
| 메타데이터 정책 업데이트/PUT| PUT| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 지정된 정책 ID를 사용하여 메타데이터 정책을 업데이트합니다. 정책 ID는 GUID 형식입니다.|
| | | 

## <a name="azure-purview-catalog-collections-api-reference-summary"></a>Azure Purview 카탈로그 컬렉션 API 참조 요약
다음 표에서는 Azure Purview 컬렉션 API에 대한 개요를 제공합니다. 각 API에 대한 전체 문서를 보려면 왼쪽 열에서 API 작업을 선택합니다.

| 작업(Operation) | Description | 
| :- | :- | 
| [컬렉션 만들기 또는 업데이트](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | 컬렉션 엔터티를 만들거나 업데이트합니다. | 
| [컬렉션 삭제](/rest/api/purview/accountdataplane/collections/delete-collection) | 컬렉션 엔터티를 삭제합니다. | 
| [컬렉션 가져오기](/rest/api/purview/accountdataplane/collections/get-collection) | 컬렉션을 가져옵니다.| 
| [컬렉션 경로 가져오기](/rest/api/purview/accountdataplane/collections/get-collection-path) | 컬렉션 경로를 나타내는 부모 이름 및 표시 이름 체인을 가져옵니다.| 
| [자식 컬렉션 이름 나열](/rest/api/purview/accountdataplane/collections/list-child-collection-names) | 컬렉션의 자식 컬렉션 이름을 나열합니다.| 
| [컬렉션 나열](/rest/api/purview/accountdataplane/collections/list-collections) | 계정의 컬렉션을 나열합니다.| 


- API를 사용하는 경우 이 API를 성공적으로 실행하려면 Azure Purview에서 할당된 [컬렉션 관리자](how-to-create-and-manage-collections.md#check-permissions) 역할이 API를 실행하는 서비스 주체, 사용자 또는 그룹에 있어야 합니다.

- {collectionName}이 필요한 모든 Azure Purview API의 경우 *"friendlyName"* 이 아니라 *"name"* 을 사용해야 합니다. {collectionName}을 실제 6자 영숫자 컬렉션 이름 문자열로 바꿔야 합니다. 
   > [!NOTE]
   > 이 이름은 컬렉션을 만들 때 제공한 표시 이름과 다릅니다. {collectionName}이 없는 경우 [컬렉션 나열 API](/rest/api/purview/accountdataplane/collections/list-collections)를 사용하여 JSON 출력에서 ​​6자 컬렉션 이름을 선택합니다.

JSON 파일의 예제는 다음과 같습니다. 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>정책 JSON 설명

컬렉션 API에서 받은 JSON 출력의 중요한 식별자 중 일부는 다음과 같습니다.

**이름**: 정책의 이름입니다. 

**ID**: 정책의 고유 식별자입니다.

**버전**: 정책의 최신 버전 번호입니다. 
   > [!IMPORTANT]
   > Update-Metadata-Policy API가 호출될 때마다 버전 번호가 증가합니다. Get-Policy-by-Policy-ID API를 호출하여 정책의 최신 복사본을 가져와야 합니다. 항상 최신 버전의 JSON 파일을 사용할 수 있도록 정책 업데이트(PUT) API를 호출하기 전에 매번 이 새로 고침을 수행합니다.

**DecisionRules**: 이 정책의 규칙과 효과를 나열합니다. 메타데이터 정책의 경우 효과는 항상 *"허용"* 입니다.

## <a name="add-or-remove-users-from-a-collection-or-role"></a>컬렉션 또는 역할에서 사용자 추가 또는 제거

Azure Purview REST API를 사용하여 컬렉션 또는 역할에서 사용자, 그룹 또는 서비스 주체를 추가하거나 제거합니다. 자세한 API 사용법은 샘플 JSON 출력과 함께 제공됩니다. Azure Purview 메타데이터 정책 API를 가장 잘 이해하려면 다음 섹션의 지침을 순차적으로 따르는 것이 가장 좋습니다.

## <a name="get-all-metadata-roles"></a>모든 메타데이터 역할 가져오기

사용 가능한 모든 메타데이터 액세스 권한 역할을 나열하려면 다음 명령을 실행합니다.

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```

출력 JSON은 역할 및 관련 권한을 이 형식으로 설명합니다.

다음 표에는 기본 메타데이터 역할이 나와 있습니다.

| 역할 ID | 사용 권한 | 역할 설명 | 
| :- | :- | :- | 
| purviewmetadatarole\_builtin\_data-source-administrator| Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read| 다른 사람에게 컬렉션 읽기/쓰기, 데이터 원본 등록 및 스캔 트리거 권한을 부여합니다.| 
| purviewmetadatarole\_builtin\_collection-administrator| Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write| 컬렉션에서 사용자 및 SPN(서비스 사용자 이름) 추가 또는 제거, 관리 권한, 액세스 권한 부여 또는 취소를 포함하여 전체 컬렉션에 대한 관리자 수준의 모든 권한입니다. 경우에 따라 컬렉션 관리자가 컬렉션 작성자와 다를 수 있습니다.| 
| purviewmetadatarole\_builtin\_purview-reader| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read| 스캔 바인딩을 제외한 컬렉션의 분류, 민감도 레이블, 인사이트, 자산 읽기를 포함하여 데이터 처리 및 모든 메타데이터에 대한 읽기 액세스 권한만 부여합니다.| 
| purviewmetadatarole\_builtin\_data-curator| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read| 스캔 바인딩을 제외한 컬렉션의 분류, 민감도 레이블, 인사이트 및 자산 읽기를 포함하여 데이터 처리 및 모든 메타데이터에 대한 모든 권한을 부여합니다.| 
| purviewmetadatarole\_builtin\_data-share-contributor| Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write| 기여자로서 데이터 공유에 대한 액세스 권한을 부여합니다. | 

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>모든 메타데이터 정책 가져오기

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
위의 명령은 위쪽의 루트 컬렉션에서 모든 자식 정책에 이르기까지 전체 컬렉션 계층 구조에서 사용 가능한 모든 메타데이터 정책을 트리 형식으로 나열합니다. 각 자식 컬렉션에는 다음 수준의 각 자식이 포함됩니다.

예:

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-the-selected-metadata-policy"></a>선택한 메타데이터 정책 가져오기 

두 API 중 하나를 사용하여 {collectionName} 또는 {PolicyID}를 제공하여 특정 컬렉션의 메타데이터 정책 JSON 구조를 가져올 수 있습니다.

다음 두 섹션에서 설명한 대로 두 API는 모두 동일한 용도로 사용되며 두 API의 JSON 출력은 정확히 동일합니다.

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-collection-name"></a>컬렉션 이름을 사용하여 컬렉션의 메타데이터 정책 가져오기

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```

1. Azure Purview 계정 이름은 {your_purview_account_name}입니다. 이 이름을 Azure Purview 계정 이름으로 바꿉니다.

1. 이전 API의 JSON 출력인 "모든 메타데이터 정책 가져오기"에서 다음 섹션을 찾습니다. 

    { "type": "CollectionReference", "referenceName": "7xkdg2"}

1. API URL의 "{collectionName}"을 "referenceName" 값 "{6자-컬렉션-이름}"으로 바꿉니다. 예를 들어 6자 컬렉션 이름이 "7xkdg2"인 경우 API URL의 형식은 다음과 같이 지정됩니다. 

   https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01

1. 다음 API를 실행합니다. 

    ```json
    {
      "name": "policy_qu45fs",
      "id": "c6639bb2-9c41-4be0-912b-775750e725de",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "qu45fs"
                },
                {
                  "fromRule": "permission:qu45fs",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:qu45fs"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
            "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:qu45fs",
            "name": "permission:qu45fs",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
                }
              ],
              [
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "qu45fs"
        },
        "parentCollectionName": "fabrikampurview"
      }
    }
    ```

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-policy-id"></a>정책 ID를 사용하여 컬렉션의 메타데이터 정책 가져오기

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

1. Azure Purview 계정 이름은 {your_purview_account_name}입니다. 이 이름을 Azure Purview 계정 이름으로 바꿉니다.

1. 이전 API의 JSON 출력인 "모든 메타데이터 정책 가져오기"에서 다음 섹션을 찾습니다. 

    {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}

1. API URL의 "{policyId}"를 "id" 값으로 바꿉니다. 예를 들어 "{policy-guid}"가 "c6639bb2-9c41-4be0-912b-775750e725de"인 경우 API URL의 형식은 다음과 같이 지정됩니다.
 
   https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01

1. 이제 다음 API를 실행합니다. 

   > [!NOTE]
   > 이 API 호출과 이전 API 호출의 출력은 동일합니다. 앞에서 설명한 대로 둘 중 하나를 선택할 수 있습니다.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-the-collection-policy"></a>컬렉션 정책 업데이트

```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

이 섹션에서는 컬렉션에서 사용자, 그룹 또는 서비스 주체를 추가하거나 제거하여 이전 단계에서 얻은 정책 JSON을 업데이트합니다. 그런 다음, PUT REST 메서드를 사용하여 Azure Purview 서비스에 푸시합니다.

사용자, 그룹 또는 서비스 주체를 추가하거나 제거하는지에 관계없이 동일한 API 프로세스를 따릅니다.

1. 사용자, 그룹 또는 서비스 주체 개체 ID {guid}를 JSON의 "attributeValueIncludedIn" 배열에 제공합니다.

1. Get-Policy-by-ID API의 JSON 출력에서 이전 단계의 "attributeValueIncludedIn" 배열을 검색하고, 배열에서 사용자, 그룹 또는 서비스 주체 개체 ID를 추가하거나 제거합니다. 사용자, 그룹 또는 서비스 주체 개체 ID를 가져오는 방법을 잘 모르는 경우 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)를 참조하세요.

1. 네 가지 역할 각각에 대한 JSON 매핑에는 여러 섹션이 있습니다. 컬렉션 관리자 권한 역할의 경우 "purviewmetadatarole_builtin_collection-administrator"라는 "ID"가 있는 섹션을 사용합니다. 마찬가지로 다른 역할에 해당하는 섹션을 사용합니다.

1. 추가/제거 작업을 더 잘 이해하려면 이전 API의 JSON 출력과 다음 출력 간의 차이점을 주의 깊게 검토합니다. 다음 JSON 출력에서는 "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f" 사용자 ID가 컬렉션 관리자로 추가되었습니다.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-the-root-collection-administrator-role"></a>루트 컬렉션 관리자 역할 추가
기본적으로 Azure Purview 계정을 만든 사용자는 루트 컬렉션 관리자(즉, 컬렉션 계층 구조의 최상위 수준 관리자)입니다. 그러나 조직에서 API를 사용하여 루트 컬렉션 관리자를 변경해야 하는 경우가 있습니다. 예를 들어 현재 루트 컬렉션 관리자가 조직에 더 이상 존재하지 않을 수 있습니다. 이러한 경우 조직의 모든 사용자가 Azure Portal에 액세스하지 못할 수 있습니다. 이러한 이유로 API를 사용하여 새 루트 컬렉션 관리자를 할당하고 컬렉션 권한을 관리하는 것이 Azure Purview 계정에 대한 액세스 권한을 다시 얻는 유일한 방법이 됩니다.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
이전 명령을 실행하려면 새 루트 컬렉션 관리자의 개체 ID만 전달하면 됩니다. 앞에서 설명한 대로 개체 ID는 모든 사용자, 그룹 또는 서비스 주체의 ID일 수 있습니다.

```json
{"objectId": "{guid}"}
```

> [!NOTE]
> Azure Purview 계정에 대한 쓰기 작업을 실행하려면 이 API를 호출하는 사용자에게 해당 계정에 대한 소유자 또는 UAA(사용자 계정 및 인증) 권한이 있어야 합니다.

## <a name="additional-resources"></a>추가 자료

[PowerShell 유틸리티](https://aka.ms/purview-api-ps)를 사용하여 Azure Purview REST API를 실행하도록 선택할 수 있습니다. PowerShell 갤러리에서 쉽게 설치할 수 있습니다. 이 유틸리티를 사용하면 Windows PowerShell에서 동일한 명령을 모두 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
