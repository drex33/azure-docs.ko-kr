---
title: Databricks Python을 사용하여 데이터 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 Databricks Python 작업을 실행 하 여 데이터를 처리 하거나 변환 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 09/09/2021
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-python, synapse
ms.openlocfilehash: b178a54d71d7f004f66e15f455c0b618842d765e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806353"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Azure Databricks에서 Python 작업을 실행하여 데이터 변환
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[파이프라인](concepts-pipelines-activities.md) 의 Azure Databricks python 작업은 Azure Databricks 클러스터에서 python 파일을 실행 합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다.

11분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python 작업 정의

Databricks Python 작업에 대한 샘플 JSON 정의는 다음과 같습니다.

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python 작업 속성

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다.

|속성|설명|필수|
|---|---|---|
|name|파이프라인의 작업 이름입니다.|예|
|description|작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.|예|
|type|Databricks Python 작업의 경우 작업 형식은 DatabricksSparkPython입니다.|예|
|linkedServiceName|Python 작업이 실행되는 Databricks 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요.|예|
|pythonFile|실행할 Python 파일의 URI입니다. DBFS 경로만이 지원됩니다.|예|
|매개 변수|Python 파일에 전달되는 명령줄 매개 변수입니다. 문자열의 배열입니다.|예|
|라이브러리|작업을 실행할 클러스터에 설치할 라이브러리의 목록입니다. <문자열, 개체>의 배열일 수 있습니다.|예|

## <a name="supported-libraries-for-databricks-activities"></a>Databricks 활동에 지원되는 라이브러리

위의 Databricks 활동 정의에서 *jar*, *egg*, *maven*, *pypi*, *cran* 라이브러리 유형을 지정합니다.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

자세한 내용은 라이브러리 유형에 대한 [Databricks 설명서](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary)를 참조하세요.

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks에서 라이브러리를 업로드하는 방법

### <a name="you-can-use-the-workspace-ui"></a>작업 영역 UI를 사용할 수 있습니다.

1. [Databricks 작업 영역 UI 사용](/azure/databricks/libraries/#create-a-library)

2. UI를 사용하여 추가된 라이브러리의 dbfs 경로를 얻으려면 [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)를 사용하면 됩니다.

   일반적으로 Jar 라이브러리는 UI를 사용하는 동안 dbfs:/FileStore/jars 아래에 저장됩니다. *databricks fs ls dbfs:/FileStore/job-jars* CLI를 통해 모두 나열할 수 있습니다.

### <a name="or-you-can-use-the-databricks-cli"></a>또는 Databricks CLI를 사용할 수 있습니다.

1. [Databricks CLI를 사용하여 라이브러리 복사](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)를 따르세요.

2. Databricks CLI [(설치 단계)](/azure/databricks/dev-tools/cli/#install-the-cli)를 사용합니다.

   예를 들어, JAR를 DBFS에 복사하려면 `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`을 수행합니다.
