---
title: 컨테이너 앱 미리 보기 ARM 템플릿 API 사양
description: 컨테이너 앱 ARM 템플릿에서 사용할 수 있는 속성을 탐색 합니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: reference
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f59046a431e872c11f7b03ffff30240beb330f37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053115"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>컨테이너 앱 미리 보기 ARM 템플릿 API 사양

Azure Container Apps 배포는 ARM (Azure Resource Manager) 템플릿으로 구동 됩니다. 다음 표에서는 컨테이너 앱 ARM 템플릿에서 사용할 수 있는 속성을 설명 합니다.

[사용 예제에 대 한 샘플 ARM 템플릿](#example)

## <a name="resources"></a>리소스

`resources`ARM 템플릿 배열의 항목에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 데이터 형식 |
|---|---|--|
| `name` | 컨테이너 앱 응용 프로그램 이름입니다. | 문자열 |
| `location` | 컨테이너 앱 인스턴스가 배포 되는 Azure 지역입니다. | 문자열 |
| `tags` | 컨테이너 앱과 연결 된 Azure 태그의 컬렉션입니다. | array |
| `type` | 항상 `Microsoft.Web/containerApps` ARM 끝점은 전달할 API를 결정 합니다.  | 문자열 |

이 예제에서는 대괄호로 묶은 자리 표시자 토큰 대신 값을 입력 합니다 `<>` .

## <a name="properties"></a>properties

리소스의 개체에는 `properties` 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 데이터 형식 | 읽기 전용 |
|---|---|---|---|
| `provisioningState` | 장기 실행 작업의 상태입니다 (예: 새 컨테이너 수정이 생성 된 경우). 가능한 값에는 프로 비전, 프로 비전 됨, 실패 됨이 있습니다. 앱이 실행 중인지 확인 합니다. | 문자열 | Yes |
| `environmentId` | 컨테이너 앱에 대 한 환경 ID입니다. **필수 속성입니다.** | 문자열 | No |
| `latestRevisionName` | 최신 수정 버전의 이름입니다. | 문자열 | Yes |
| `latestRevisionFqdn` | 최신 수정 버전의 URL입니다. | 문자열 | Yes |

`environmentId`값은 다음 형식을 사용 합니다.

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

이 예제에서는 대괄호로 묶은 자리 표시자 토큰 대신 값을 입력 합니다 `<>` .

## <a name="propertiesconfiguration"></a>속성. 구성

리소스의 개체에는 `properties.configuration` 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 데이터 형식 |
|---|---|---|
| `activeRevisionsMode` | 로 설정 `multiple` 하면 여러 개의 수정 버전을 유지할 수 있습니다. 를로 설정 하면 `single` 오래 된 수정 버전을 자동으로 비활성화 하 고, 최신 수정 버전만 활성 상태로 유지 합니다. | 문자열 |
| `secrets` | 컨테이너 앱에서 비밀 값을 정의 합니다. | 개체 |
| `ingress` | 컨테이너 앱에 대 한 공용 액세스 가능성 구성을 정의 하는 개체입니다. | 개체 |
| `registries` | 개인 컨테이너 레지스트리에 대 한 자격 증명을 참조 하는 구성 개체입니다. 로 정의 된 항목 `secretref` 은 암호 구성 개체를 참조 합니다. | 개체 |

섹션에 대 한 변경 내용은 `configuration` [응용 프로그램 범위 변경](revisions.md#application-scope-changes)내용으로, 새 수정 버전을 트리거하지 않습니다.

## <a name="propertiestemplate"></a>properties. 템플릿

리소스의 개체에는 `properties.template` 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 데이터 형식 |
|---|---|---|
| `revisionSuffix` | 수정 버전에 대 한 친숙 한 이름입니다. 런타임이 기존 수정 이름 접미사 값과의 충돌을 모두 거부 하므로이 값은 고유 해야 합니다. | 문자열 |
| `containers` | 컨테이너 앱에 포함 되는 컨테이너 이미지를 정의 하는 구성 개체입니다. | 개체 |
| `scale` | 컨테이너 앱에 대 한 크기 조정 규칙을 정의 하는 구성 개체입니다. | 개체 |
| `dapr` | 컨테이너 앱에 대 한 4Apr 설정을 정의 하는 구성 개체입니다. | 개체  |

섹션에 대 한 변경 내용은 `template` 새 수정 버전을 트리거하는 [수정 버전 범위 변경](revisions.md#revision-scope-changes)내용입니다.

## <a name="example"></a>예제

다음은 컨테이너 앱을 배포 하는 데 사용 되는 ARM 템플릿 예제입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "containerappName": {
            "defaultValue": "mycontainerapp",
            "type": "String"
        },
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "myenvironment",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2021-03-01",
            "type": "Microsoft.Web/containerApps",
            "name": "[parameters('containerappName')]",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "secrets": [
                        {
                            "name": "mysecret",
                            "value": "thisismysecret"
                        }
                    ],
                    "ingress": {
                        "external": true,
                        "targetPort": 80,
                        "allowInsecure": false,
                        "traffic": [
                            {
                                "latestRevision": true,
                                "weight": 100
                            }
                        ]
                    }
                },
                "template": {
                    "revisionSuffix": "myrevision",
                    "containers": [
                        {
                            "name": "nginx",
                            "image": "nginx",
                            "env": [
                                {
                                    "name": "HTTP_PORT",
                                    "value": "80"
                                },
                                {
                                    "name": "SECRET_VAL",
                                    "secretRef": "mysecret"
                                }
                            ],
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 3
                    }
                }
            }
        }
    ]
}
```
