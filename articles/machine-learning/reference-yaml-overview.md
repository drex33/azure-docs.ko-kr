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
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 4d98ebbebbb25e2ab903e06162114f8fa026fbda
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529338"
---
# <a name="cli-v2-yaml-schemas"></a>CLI(v2) YAML 스키마

Azure CLI에 대한 확장인 Azure Machine Learning CLI(v2)는 특정 스키마가 있는 YAML 파일을 자주 사용하고 필요로 하는 경우가 있습니다. 이 문서에는 YAML 파일에 대한 참조 문서와 원본 스키마가 나열되어 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>자산

참조 | URI
- | -
[작업 영역](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[컴퓨팅](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[환경](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[데이터 세트](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[모델](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>데이터 저장소

참조 | URI
- | -
[Azure Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Azure 파일](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>작업

참조 | URI
- | -
[명령](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>엔드포인트

참조 | URI
- | -
[온라인(실시간) 관리](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[관리되는 일괄 처리](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes(k8s) 온라인(실시간)](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>배포

참조 | URI
- | -
[온라인(실시간) 관리](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[관리되는 일괄 처리](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes(k8s) 온라인(실시간)](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnelineDeployment.schema.json

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
