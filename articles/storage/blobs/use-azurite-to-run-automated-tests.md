---
title: Azurite를 사용하여 자동화된 테스트 실행
titleSuffix: Azure Storage
description: Azurite를 사용하여 Azure Blob Storage용 프라이빗 엔드포인트에 대해 자동화된 테스트를 쓰는 방법을 알아봅니다.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: 21a0d2157cdb8b6b2c31520596327b3b433ef93a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674550"
---
# <a name="run-automated-tests-by-using-azurite"></a>Azurite를 사용하여 자동화된 테스트 실행

Azurite 스토리지 에뮬레이터를 사용하여 Azure Blob Storage용 프라이빗 엔드포인트에 대해 자동화된 테스트를 쓰는 방법을 알아봅니다.

## <a name="run-tests-on-your-local-machine"></a>로컬 컴퓨터에서 테스트 실행

1. 최신 버전의 [Python](https://www.python.org/)을 설치합니다.

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 설치합니다.

1. [Azurite](../common/storage-use-azurite.md)를 설치하고 실행합니다.

   **옵션 1:** npm을 사용하여 설치하고 Azurite를 로컬로 실행합니다.

   ```bash
   # Install Azurite
   npm install -g azurite

   # Create an Azurite directory
   mkdir c:\azurite

   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **옵션 2:** Docker를 사용하여 Azurite를 실행합니다.

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Azure Storage Explorer에서 **로컬 에뮬레이터에 연결** 을 선택합니다.

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Azure Storage 소스에 연결하는 Azure Storage Explorer의 스크린샷":::

1. **표시 이름** 및 **Blob 포트** 번호를 제공하여 Azurite를 연결하고 Azure Storage Explorer를 사용하여 로컬 Blob 스토리지를 관리합니다.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="로컬 에뮬레이터에 연결하는 Azure Storage Explorer의 스크린샷":::

1. 가상 Python 환경 만들기

   ```bash
   python -m venv .venv
   ```

1. 컨테이너를 만들고 환경 변수를 초기화합니다. [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/latest/how-to/writing_plugins.html#conftest-py-plugins) 파일을 사용하여 테스트를 생성합니다. 다음은 conftest.py 파일의 예입니다.

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > `AZURE_STORAGE_CONNECTION_STRING`에 표시되는 값은 Azurite의 기본값이며 프라이빗 키가 아닙니다.

1. [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) 파일에 나열된 종속성을 설치합니다.

   ```bash
   pip install -r requirements.txt
   ```

1. 테스트를 실행합니다.

   ```bash
   python -m pytest ./tests
   ```

테스트를 실행한 후 Azure Storage Explorer를 사용하여 Azurite Blob 스토리지의 파일을 볼 수 있습니다.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="테스트에 의해 생성된 파일을 보여 주는 Azure Storage Explorer의 스크린샷":::

## <a name="run-tests-on-azure-pipelines"></a>Azure Pipelines에서 테스트 실행

테스트를 로컬로 실행한 후에는 테스트가 [Azure Pipelines](/azure/devops/pipelines)에서 통과하는지 확인합니다. Docker Azurite 이미지를 Azure의 호스트된 에이전트로 사용하거나 npm을 사용하여 Azurite를 설치합니다. 다음 Azure Pipelines 예제에서는 npm을 사용하여 Azurite를 설치합니다.

```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'

- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Azure Pipelines 테스트를 실행한 후 다음과 유사한 출력이 표시됩니다.

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Azure Pipelines 테스트 결과를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용](../common/storage-use-azurite.md)
- [샘플: Azurite를 사용하여 Azure DevOps 파이프라인에서 Blob 스토리지 테스트 실행](https://github.com/Azure-Samples/automated-testing-with-azurite)
