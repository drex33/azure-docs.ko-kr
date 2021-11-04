---
title: CLI(v2) YAML 스키마 개요
titleSuffix: Azure Machine Learning
description: CLI(v2) YAML 스키마의 개요 및 색인입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 541cb9049be64977858a10cd486605f9572b3f30
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561350"
---
# <a name="cli-v2-yaml-schemas"></a>CLI(v2) YAML 스키마

Azure CLI에 대한 확장인 Azure Machine Learning CLI(v2)는 특정 스키마가 있는 YAML 파일을 자주 사용하고 필요로 하는 경우가 있습니다. 이 문서에는 YAML 파일에 대한 참조 문서와 원본 스키마가 나열되어 있습니다. 예제는 개별 문서에 인라인으로 포함 되어 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="workspace"></a>작업 영역

| 참조 | URI |
| - | - |
| [작업 영역](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json |

## <a name="environment"></a>환경

| 참조 | URI |
| - | - |
| [환경](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json |

## <a name="dataset"></a>데이터 세트

| 참조 | URI |
| - | - |
| [데이터 세트](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json |

## <a name="model"></a>모델

| 참조 | URI |
| - | - |
| [모델](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json |

## <a name="compute"></a>Compute

| 참조 | URI |
| - | - |
| [계산 클러스터 (AmlCompute)](reference-yaml-compute-aml.md) | https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json |
| [컴퓨팅 인스턴스](reference-yaml-compute-instance.md) | https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json |
| [연결 된 가상 컴퓨터](reference-yaml-compute-vm.md) | https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json |

## <a name="job"></a>작업

| 참조 | URI |
| - | - |
| [명령](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json |
| [Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json |
| [파이프라인](reference-yaml-job-pipeline.md) | https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json |

## <a name="datastore"></a>데이터 저장소

| 참조 | URI |
| - | - |
| [Azure Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json |
| [Azure 파일](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json |
| [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json |
| [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json |

## <a name="endpoint"></a>엔드포인트

| 참조 | URI |
| - | - |
| [온라인(실시간) 관리](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json |
| [Batch](reference-yaml-endpoint-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json |

## <a name="deployment"></a>배포

| 참조 | URI |
| - | - |
| [온라인(실시간) 관리](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json |
| [Batch](reference-yaml-deployment-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json |

## <a name="component"></a>구성 요소

| 참조 | URI |
| - | - |
| [명령](reference-yaml-component-command.md) | https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json |

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
