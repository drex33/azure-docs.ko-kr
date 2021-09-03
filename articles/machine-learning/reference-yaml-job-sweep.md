---
title: CLI(v2) 스윕 작업 YAML 스키마
titleSuffix: Azure Machine Learning
description: CLI(v2) 스윕 작업 YAML 스키마에 대한 참조 문서입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 5c0520b37c98395845ef1adf074e380b264a09d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528876"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>CLI(v2) 스윕 작업 YAML 스키마

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>스키마

원본 JSON 스키마는 https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json 에서 찾을 수 있습니다. 스키마는 편의를 위해 JSON 및 YAML 형식으로 아래에 제공됩니다.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/sweepJob.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/sweepJob.schema.yml":::

---

## <a name="remarks"></a>설명

`az ml job` 명령은 Azure Machine Learning 작업을 관리하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [CLI(v2) 설치 및 사용](how-to-configure-cli.md)
