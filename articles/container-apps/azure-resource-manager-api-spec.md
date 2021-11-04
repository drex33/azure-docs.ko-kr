---
title: Container Apps 미리 보기 ARM 템플릿 API 사양
description: Container Apps ARM 템플릿에서 사용 가능한 속성을 탐색합니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: reference
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a0bc640e046074af94462c9e429102dc43ad442f
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578238"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>Container Apps 미리 보기 ARM 템플릿 API 사양

Azure Container Apps 배포는 ARM(Azure Resource Manager) 템플릿을 통해 구동됩니다. 다음 표에서는 컨테이너 앱 ARM 템플릿에서 사용할 수 있는 속성에 대해 설명합니다.

사용 [예제에 대한 샘플 ARM 템플릿입니다.](#example)

## <a name="resources"></a>리소스

`resources`ARM 템플릿의 배열에 있는 항목에는 다음과 같은 속성이 있습니다.

| 속성 | Description | 데이터 형식 |
|---|---|--|
| `name` | Container Apps 애플리케이션 이름입니다. | 문자열 |
| `location` | Container Apps 인스턴스가 배포되는 Azure 지역입니다. | 문자열 |
| `tags` | 컨테이너 앱과 연결된 Azure 태그의 컬렉션입니다. | array |
| `type` | 항상 `Microsoft.Web/containerApps` ARM 엔드포인트는 전달할 API를 결정합니다.  | 문자열 |

이 예제에서는 대괄호로 묶인 자리 표시자 토큰 대신 값을 `<>` 배치합니다.

## <a name="properties"></a>properties

리소스의 `properties` 개체에는 다음과 같은 속성이 있습니다.

| 속성 | Description | 데이터 형식 | 읽기 전용 |
|---|---|---|---|
| `provisioningState` | 장기 실행 작업의 상태(예: 새 컨테이너 수정 버전이 만들어지는 경우)입니다. 가능한 값은 프로비전, 프로비전, 실패입니다. 앱이 실행 중인지 확인합니다. | 문자열 | Yes |
| `environmentId` | 컨테이너 앱의 환경 ID입니다. **필수 속성입니다.** | 문자열 | No |
| `latestRevisionName` | 최신 수정 버전 이름입니다. | 문자열 | Yes |
| `latestRevisionFqdn` | 최신 수정 버전 URL입니다. | 문자열 | Yes |

`environmentId`값은 다음 형식을 취합니다.

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

이 예제에서는 대괄호로 묶인 자리 표시자 토큰 대신 값을 `<>` 배치합니다.

## <a name="propertiesconfiguration"></a>properties.configuration

리소스의 `properties.configuration` 개체에는 다음과 같은 속성이 있습니다.

| 속성 | Description | 데이터 형식 |
|---|---|---|
| `activeRevisionsMode` | 를 로 `multiple` 설정하면 여러 수정 버전을 유지할 수 있습니다. 를 로 설정하면 `single` 이전 수정 버전이 자동으로 비활성화되고 최신 수정 버전만 활성 상태로 유지됩니다. | 문자열 |
| `secrets` | 컨테이너 앱에서 비밀 값을 정의합니다. | 개체 |
| `ingress` | 컨테이너 앱의 공용 접근성 구성을 정의하는 개체입니다. | 개체 |
| `registries` | 프라이빗 컨테이너 레지스트리에 대한 자격 증명을 참조하는 구성 개체입니다. 로 정의된 `secretref` 항목은 비밀 구성 개체를 참조합니다. | 개체 |

섹션의 변경 `configuration` 내용은 새 수정 버전을 트리거하지 않는 애플리케이션 범위 변경 [내용입니다.](revisions.md#application-scope-changes)

## <a name="propertiestemplate"></a>properties.template

리소스의 `properties.template` 개체에는 다음과 같은 속성이 있습니다.

| 속성 | Description | 데이터 형식 |
|---|---|---|
| `revisionSuffix` | 수정 버전에 대한 이름입니다. 이 값은 런타임이 기존 수정 버전 이름 접미사 값과의 충돌을 거부하기 때문에 고유해야 합니다. | 문자열 |
| `containers` | 컨테이너 앱에 포함되는 컨테이너 이미지를 정의하는 구성 개체입니다. | 개체 |
| `scale` | 컨테이너 앱에 대한 크기 조정 규칙을 정의하는 구성 개체입니다. | 개체 |
| `dapr` | 컨테이너 앱에 대한 Dapr 설정을 정의하는 구성 개체입니다. | 개체  |

섹션에 대한 변경 `template` 내용은 새 수정 버전을 트리거하는 수정 범위 변경 [내용입니다.](revisions.md#revision-scope-changes)

## <a name="example"></a>예제

다음은 컨테이너 앱을 배포하는 데 사용되는 ARM 템플릿 예제입니다.

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
