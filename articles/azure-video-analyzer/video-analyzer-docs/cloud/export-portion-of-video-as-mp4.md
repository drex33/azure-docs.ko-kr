---
title: Azure Video Analyzer 녹화 비디오의 일부를 MP4 파일로 내보내기
description: 이 자습서에서는 Video Analyzer 계정 에코시스템 외부에서 다운로드하고 사용할 수 있는 Video Analyzer 비디오로 저장된 Video Analyzer 녹화 비디오의 일부를 MP4로 내보내는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74b46b6c46e3ae6cb6ae9f4567b20bcee3e37ebe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053592"
---
# <a name="tutorial-export-portion-of-recorded-video-as-an-mp4-file"></a>자습서: 녹화된 비디오의 일부를 MP4 파일로 내보내기

[!INCLUDE [header](includes/cloud-env.md)]

이 자습서에서는 Azure Video Analyzer 계정에 녹화된 비디오의 일부를 내보내는 방법을 알아봅니다.  이 비디오의 내보낸 부분은 Video Analyzer 계정 외부에서 다운로드하고 사용할 수 있는 MP4 파일로 저장됩니다.

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하세요.

* [Azure Video Analyzer 개요](../overview.md)
* [Azure Video Analyzer 용어](../terminology.md)
* [Video Analyzer 파이프라인 개념](../pipeline.md)
* [이벤트 기반 비디오 녹화](../event-based-video-recording-concept.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 다음과 같습니다.

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Video Analyzer 계정](../create-video-analyzer-account.md)
* [빠른 시작: 동작 감지, Video Analyzer에 비디오 녹화](../edge/detect-motion-record-video-clips-cloud.md) 또는 비디오 싱크에 기록하는 Video Analyzer 파이프라인을 완료했습니다.

## <a name="overview"></a>개요

Video Analyzer는 RTSP 원본 및 Video Analyzer 계정의 비디오를 녹화할 수 있으며 이러한 비디오는 분할된 보관에 녹화됩니다.  분할된 보관 형식은 비디오 녹화의 무제한 기간을 허용합니다. 그러나 경우에 따라 비디오의 일부를 MP4로 저장하여 Video Analyzer 에코시스템 외부에서 개별적으로 보관, 다운로드 또는 재생할 수 있도록 해야 합니다.

이 자습서에서는 다음에 대해 알아봅니다.

* 일괄 처리 파이프라인 토폴로지 및 일괄 처리 파이프라인 작업 정보
* 일괄 처리 토폴로지를 만드는 방법입니다.
* 지정된 시간 값(최대 24시간)을 포함하는 MP4 파일을 만드는 Video Analyzer 비디오 보관에서 일괄 처리 파이프라인 작업을 만드는 방법입니다.

## <a name="pipeline-topology-of-batch-kind"></a>**일괄 처리** 종류의 파이프라인 토폴로지

일괄 처리 종류의 파이프라인 토폴로지를 사용하면 세 개의 상호 연결된 노드를 통해 사용자 지정 요구 사항에 따라 내보내기 위해 녹화된 비디오를 처리하는 방법을 설명할 수 있습니다.  일괄 처리 종류의 파이프라인 토폴로지는 파이프라인 작업에 사용되는 기준입니다.  파이프라인 작업은 일괄 처리 종류의 파이프라인 토폴로지의 개별 인스턴스입니다.  파이프라인 작업은 녹화된 Video Analyzer 비디오를 가져와서 다운로드 가능한 MP4 파일로 Video Analyzer의 스토리지 계정에 저장합니다.  일괄 처리 종류의 파이프라인 토폴로지는 [인코더 프로세서 노드](../pipeline.md#encoder-processor)에 연결한 다음 [비디오 싱크 노드](../pipeline.md#video-sink)에 연결하는 [비디오 원본 노드](../pipeline.md#video-source)를 사용합니다.

> [!NOTE]
> 원본, 프로세서 및 싱크에 대한 자세한 내용은 [원본, 프로세서 및 싱크](../pipeline.md#sources-processors-and-sinks)를 참조하세요. 파이프라인 작업에 대한 자세한 내용은 [파이프라인 작업](../pipeline.md#batch-pipeline)을 참조하세요.

## <a name="create-a-pipeline-job-from-videos"></a>파이프라인 작업 만들기(비디오에서)

1. Azure Portal에서 Video Analyzer 계정으로 이동합니다.
1. `Video Analyzer` 섹션 아래에서 **비디오** 를 선택한 다음 비디오의 일부를 내보내는 데 사용할 비디오 스트림을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-analyzer-video.png" alt-text="비디오 선택 사항을 강조 표시하는 Azure Video Analyzer의 Video Analyzer 메뉴 섹션 이미지":::
1. 비디오 위젯 플레이어 블레이드의 위쪽에서 **작업 만들기** 를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/export-portion-of-video-as-mp4/create-job.png" alt-text="작업 만들기 선택 사항을 강조 표시하는 Azure Video Analyzer의 비디오 위젯 블레이드 이미지":::
1. `Create Job` 플라이아웃 블레이드에서 다음을 선택합니다.

   1. `Batch topology`에 대해 **샘플에서 만들기** 를 선택합니다.
   1. `Batch topology name` 드롭다운 목록에서 **비디오 내보내기** 샘플 토폴로지를 선택합니다.
   1. `Batch topology name`에 이름을 입력하여 토폴로지의 이름을 다음 이름으로 저장합니다.

        > [!NOTE]
        > 샘플 토폴로지는 위에서 입력한 이름으로 저장됩니다.  다음에 비디오를 내보낼 때 다시 사용할 수 있습니다.
1. `Name your job` 섹션에서 작업 이름 필드에 **작업 이름** 을 입력하고, 설명 필드(설명은 선택 사항임)에 작업에 대한 **설명** 을 입력합니다.
1. `Define parameters` 섹션에서 다음 작업을 수행합니다.
    1. `sourceVideoName` 필드에 녹화된 Video Analyzer 비디오의 이름을 입력합니다.
    1. `videoSourceTimeSequenceParameter` 필드에서 캘린더 아이콘을 클릭하고 각 값의 날짜를 선택하여 시작 및 종료 날짜를 선택합니다.  시간 필드에 비디오 클립을 만드는 데 사용할 파이프라인 작업의 시작 및 종료 시간을 입력합니다.
        > [!NOTE]
        > 지정된 녹화된 비디오의 시간 값은 비디오 위젯 플레이어의 오른쪽 위에 표시됩니다.  이 시간 값은 빨간색 상자가 있는 아래 이미지에 표시됩니다.  캘린더 아이콘은 녹색 상자가 있는 아래 이미지에도 표시됩니다.

    1. `exportedVideoName` 필드에 내보낸 MP4 파일의 이름을 입력합니다.
  
      > [!div class="mx-imgBorder"]
      > :::image type="content" source="./media/export-portion-of-video-as-mp4/video-widget-job-creation.png" alt-text="캘린더 아이콘 주위의 빨간색 상자와 녹색 상자의 비디오 타임스탬프를 강조 표시하는 Azure Video Analyzer의 비디오 플레이어 위젯 및 파이프라인 작업 플라이아웃 블레이드 이미지":::
1. `Create a job` 플라이아웃 블레이드 아래쪽의 **만들기** 를 클릭합니다.  
    
    파이프라인 작업을 모니터링하려면 `Batch Jobs tab`로 이동합니다.
1. `Video Analyzer` 섹션에서 **일괄 처리** 를 선택합니다.
1. 일괄 처리 블레이드 위쪽의 `Jobs` 탭을 클릭합니다.
  
    일괄 처리 작업은 처리 상태를 입력한 다음, 성공적으로 완료되면 상태를 `Completed`(으)로 변경합니다.  연결된 MP4 비디오 파일을 보려면 다음을 수행합니다.
1. `Video Analyzer` 섹션 아래에서 **비디오** 를 클릭합니다.
1. 이전에 5단계에서 사용한 일괄 처리 작업 이름과 일치하는 비디오 이름을 클릭합니다.  

비디오 위젯 플레이어가 MP4 파일 재생을 시작해야 합니다.  MP4 파일을 다운로드하려면 블레이드 위쪽에서 `Download video`을(를) 클릭합니다.  그러면 새 브라우저 탭에서 MP4 파일이 열립니다. 비디오를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장** 을 클릭합니다.

## <a name="cancel-a-pipeline-job"></a>파이프라인 작업 취소

파이프라인 작업이 처리 상태를 입력하면 파이프라인 작업을 취소할 수 있습니다.  파이프라인 작업을 취소하려면 다음을 수행합니다.

1. Video Analyzer 계정으로 이동하고 **Video Analyzer** 섹션에서 **일괄 처리** 를 선택합니다.
1. 일괄 처리 블레이드의 위쪽에 있는 **작업** 탭을 선택합니다.
1. 작업 탭 아래에서 여러 상태에 있는 작업 목록을 찾을 수 있습니다. 처리 상태에서 취소하려는 작업을 찾고 일괄 처리 파이프라인 작업 탭의 오른쪽에서 **취소** 를 선택한 다음 **예** 를 클릭합니다.

    > [!NOTE]
    > 실패한 파이프라인 작업은 취소할 수 없습니다.

## <a name="delete-a-pipeline-job"></a>파이프라인 작업 삭제

파이프라인 작업이 완료된 상태 또는 실패한 상태를 입력하면 파이프라인 작업을 삭제할 수 있습니다.  파이프라인 작업을 삭제하려면 다음을 수행합니다.

1. Video Analyzer 계정으로 이동하고 **Video Analyzer** 섹션에서 **일괄 처리** 를 선택합니다.
1. 일괄 처리 블레이드의 위쪽에 있는 **작업** 탭을 선택합니다.
1. 작업 탭 아래에서 여러 상태에 있는 작업 목록을 찾을 수 있습니다.  삭제하려는 작업(취소, 완료 또는 실패 상태)을 찾고 오른쪽 파이프라인 작업에서 **삭제** 를 선택한 다음 **삭제** 를 클릭합니다.

## <a name="delete-a-pipeline-topology-of-batch-kind"></a>일괄 처리 종류의 파이프라인 토폴로지 삭제

일괄 처리 종류의 파이프라인 토폴로지 삭제하려면 파이프라인 토폴로지와 연결된 모든 파이프라인 작업을 삭제해야 합니다.  일괄 처리 종류의 파이프라인 토폴로지를 삭제하려면 다음을 수행합니다.

1. Video Analyzer 계정으로 이동합니다.
2. `Video Analyzer` 섹션 아래에서 **일괄 처리** 를 클릭합니다.
3. 토폴로지 탭 아래에서 삭제할 일괄 처리 종류의 파이프라인 토폴로지를 찾아 일괄 처리 종류의 파이프라인 토폴로지 오른쪽에 있는 ' **...** '을 클릭합니다.
4. **토폴로지 삭제** 를 클릭합니다.

    > [!NOTE]
    > 일괄 처리 종류의 파이프라인 토폴로지를 삭제하기 전에 일괄 처리 종류의 파이프라인 토폴로지에서 모든 파이프라인 작업을 삭제해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 유지합니다. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* [클라우드 파이프라인](../pipeline.md)을 사용하여 비디오를 캡처하고 녹화하려면 [카메라를 클라우드에 직접 연결](./connect-cameras-to-cloud.md)
* [RTSP 프로토콜을 통해 비디오 패킷에 대한 투명 게이트웨이 역할을 하는 Video Analyzer 에지 모듈을 통해 Video Analyzer의 서비스에 카메라 연결](./use-remote-device-adapter.md)
