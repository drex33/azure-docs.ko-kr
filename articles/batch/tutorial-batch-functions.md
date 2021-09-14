---
title: 자습서 - Azure Functions를 사용한 Batch 작업 트리거
description: 자습서 - 스캔한 문서가 Storage Blob에 추가되면 OCR 적용
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 02379ee6872564b73441f6756479965912f3925f
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449102"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>자습서: Azure Functions를 사용한 Batch 작업 트리거

이 자습서에서는 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 Batch 작업을 트리거하는 방법을 알아봅니다. Azure Storage Blob 컨테이너에 추가된 문서에 Azure Batch를 통해 OCR(광학 문자 인식)이 적용된 예를 살펴봅니다. OCR 처리를 간소화하기 위해 파일이 Blob 컨테이너에 추가될 때마다 Batch OCR을 실행하는Azure 함수를 구성합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Batch Explorer를 사용하여 풀과 작업 만들기
> * Storage Explorer를 사용하여 Blob 컨테이너 및 SAS(공유 액세스 서명) 만들기
> * Blob 트리거 Azure Function 만들기
> * 입력 파일을 Storage에 업로드
> * 태스크 실행 모니터링
> * 출력 파일 검색

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Batch 계정 및 연결된 Azure Storage 계정. 계정 만들기 및 연결 방법에 대한 자세한 내용은 [Batch 계정 만들기](quick-create-portal.md#create-a-batch-account)를 참조하세요.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Batch Explorer를 사용하여 Batch 풀 및 Batch 작업 만들기

이 섹션에서는 Batch Explorer를 사용하여 OCR 작업을 실행할 Batch 풀 및 Batch 작업을 만듭니다.

### <a name="create-a-pool"></a>풀 만들기

1. Azure 자격 증명을 사용하여 Batch Explorer에 로그인합니다.
1. 왼쪽 메뉴 모음에서 **풀** 을 선택한 다음, 검색 양식 위의 **추가** 버튼을 선택하여 풀을 만듭니다. 
    1. ID와 표시 이름을 선택합니다. 이 예제에서는 `ocr-pool`을 사용합니다.
    1. 확장 유형을 **고정 크기** 로 설정하고 전용 노드 수를 3으로 설정합니다.
    1. 운영 체제로 **Ubuntuserver** > **18.04-lts** 를 선택합니다.
    1. 가상 머신 크기에 `Standard_f2s_v2`를 선택합니다.
    1. 시작 작업을 사용하도록 설정하고 `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` 명령을 추가합니다. 시작 작업에서 `sudo` 명령을 포함할 수 있도록 사용자 ID를 **작업 사용자(관리자)** 로 설정해야 합니다.
    1. **확인** 을 선택합니다.
  
### <a name="create-a-job"></a>작업 만들기

1. 왼쪽 메뉴 모음에서 **작업** 을 선택한 다음, 검색 양식 위의 **추가** 버튼을 선택하여 풀에 작업을 만듭니다.
   1. ID와 표시 이름을 선택합니다. 이 예제에서는 `ocr-job`을 사용합니다.
   1. 풀을 `ocr-pool` 또는 풀에 대해 선택한 이름으로 설정합니다.
   1. **확인** 을 선택합니다.

## <a name="create-blob-containers"></a>Blob 컨테이너 만들기

여기에서는 OCR Batch 작업에 대한 입력 및 출력 파일을 저장하는 Blob 컨테이너를 만듭니다. 이 예제에서 입력 컨테이너 이름은 `input`이며 OCR이 없는 모든 문서가 처리되기 위해 처음 업로드되는 위치입니다. 출력 컨테이너 이름은 `output`이며 Batch 작업이 OCR로 처리된 문서를 기록하는 위치입니다.

1. Azure 자격 증명을 사용하여 [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)에 로그인합니다.
1. Batch 계정에 연결된 스토리지 계정을 사용하여 [Blob 컨테이너 만들기](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)의 단계에 따라 두 개의 Blob 컨테이너(입력 파일용 1개, 출력 파일용 1개)를 만듭니다.
1. 출력 컨테이너를 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기...** 를 선택하여 Storage Explorer에서 출력 컨테이너에 대한 공유 액세스 서명을 만듭니다. **사용 권한** 에서 **쓰기** 를 선택합니다. 다른 권한은 필요하지 않습니다.  

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 섹션에서는 파일이 입력 컨테이너에 업로드될 때마다 OCR Batch 작업을 트리거하는 Azure Function을 만듭니다.

1. [Azure Blob Storage가 트리거하는 함수 만들기](../azure-functions/functions-create-storage-blob-triggered-function.md)의 단계에 따라 함수를 만듭니다.
    1. **런타임 스택** 에 .NET을 선택합니다. Batch.NET SDK를 활용하기 위해 함수를 C#으로 작성합니다.
    1. **호스팅** 아래에서 스토리지 계정을 입력하라는 메시지가 표시되면 Batch 계정에 연결한 것과 동일한 스토리지 계정을 사용합니다.
    1. Azure Blob 스토리지 계정 트리거를 만드는 동안 입력 컨테이너 이름과 일치하도록 경로를 `input/{name}`으로 설정해야 합니다.
1. Blob 트리거 함수가 만들어지면 **코드 + 테스트** 를 선택합니다. Function에서 GitHub의 [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) 및 [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj)를 사용합니다. `function.proj`는 기본적으로 존재하지 않으므로 **업로드** 단추를 선택하여 개발 작업 영역에 업로드합니다.
    * `run.csx`는 새 Blob이 입력 Blob 컨테이너에 추가될 때 실행됩니다.
    * `function.proj`는 Batch .NET SDK 등, Function 코드의 외부 라이브러리를 나열합니다.
1. Batch 및 스토리지 자격 증명을 반영하여 `run.csx` 파일의 `Run()` 함수에서 변수 자리 표시자 값을 변경합니다. Batch 계정의 **키** 섹션에서 Azure Portal의 Batch 및 스토리지 계정 자격 증명을 확인할 수 있습니다.
    * Batch 계정의 **키** 섹션에서 Azure Portal의 Batch 및 스토리지 계정 자격 증명을 검색합니다. 


## <a name="trigger-the-function-and-retrieve-results"></a>함수 트리거 및 결과 검색

GitHub의 [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files)에서 스캔한 일부 또는 모든 파일을 입력 컨테이너에 업로드합니다. Batch Explorer를 모니터링하여 각 파일에 대해 작업이 `ocr-pool`에 추가되었는지 확인합니다. 몇 초 후 OCR이 적용된 파일이 출력 컨테이너에 추가됩니다. 그러면 Storage Explorer에서 파일을 보고 검색할 수 있습니다.

또한 Azure Functions 웹 편집기 창의 맨 아래에 있는 로그 파일에서 입력 컨테이너에 업로드한 파일마다 다음과 같은 메시지를 표시합니다.

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Storage Explorer에서 로컬 머신으로 출력 파일을 다운로드하려면 먼저 파일을 선택한 다음, 위쪽 리본 메뉴에서 **다운로드** 를 선택합니다.

> [!TIP]
> PDF Reader로 열면 다운로드한 파일을 검색할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

작업이 예약되지 않은 경우에도 노드가 실행되는 동안은 풀에 대한 요금이 부과됩니다. 풀이 더 이상 필요하지 않으면 다음 단계를 수행하여 삭제합니다.

1. 계정 보기에서 **풀** 과 해당 풀의 이름을 선택합니다.
1. **삭제** 를 선택합니다.

풀을 삭제하면 노드의 모든 태스크 출력이 삭제됩니다. 그러나 출력 파일은 스토리지 계정에 남아 있습니다. 더 이상 필요하지 않은 경우 Batch 계정과 스토리지 계정을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

.NET API를 사용하여 Batch 워크로드를 예약하고 처리하는 방법에 대한 예를 더 보려면 GitHub의 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [Batch C# 샘플](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
