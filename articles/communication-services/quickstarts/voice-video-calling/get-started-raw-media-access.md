---
title: 빠른 시작 - 앱에 RAW 미디어 액세스 추가(Android)
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Services를 사용하여 앱에 원시 미디어 액세스 통화 기능을 추가하는 방법을 알아봅니다.
author: LaithRodan
ms.author: larodan
ms.date: 11/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: e1e8ceb5b6be90359ca2fe62777d5447e9822682
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132904378"
---
# <a name="raw-media-access"></a>원시 미디어 액세스

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

이 빠른 시작에서는 Android용 Azure Communication Services Calling SDK를 원시 미디어 액세스를 구현하는 방법을 알아봅니다.

## <a name="outbound-virtual-video-device"></a>아웃바운드 가상 비디오 디바이스

ACS Calling SDK는 앱이 원격 참가자에 게 보낼 고유의 비디오 프레임을 생성할 수 있도록 하는 API를 제공합니다.

이 빠른 시작은 Android용 [빠른 시작: 앱에 1:1 영상 통화 추가](./get-started-with-video-calling.md?pivots=platform-android)를 기반으로 합니다.


## <a name="overview"></a>개요

아웃바운드 가상 비디오 디바이스를 만든 후에는 DeviceManager를 사용하여 컴퓨터 또는 휴대폰에 연결된 다른 웹캠처럼 작동하는 새 가상 비디오 디바이스를 만듭니다.

앱은 비디오 프레임을 생성하므로 앱은 앱이 생성할 수 있는 비디오 형식에 대해 ACS Calling SDK에 알려야 합니다. 이는 ACS Calling SDK가 지정된 시간에 네트워크 조건에 따라 최상의 비디오 형식 구성을 선택할 수 있도록 하는 데 필요합니다.

앱은 비디오 프레임 생성을 시작하거나 중지해야 하는 경우에 대한 정보를 얻기 위해 대리자를 등록해야 합니다. 대리자 이벤트는 현재 네트워크 상태에 더 적합한 비디오 형식을 앱에 알립니다.

아웃바운드 가상 비디오 디바이스를 만드는 데 필요한 단계에 대한 개요는 다음과 같습니다.

1. 새 아웃바운드 가상 비디오 디바이스에 대한 기본 ID 정보를 사용하여 `VirtualDeviceIdentification`을 만듭니다.

    ```java
    VirtualDeviceIdentification deviceId = new VirtualDeviceIdentification();
    deviceId.setId("QuickStartVirtualVideoDevice");
    deviceId.setName("My First Virtual Video Device");
    ```

2. 앱에서 지원하는 비디오 형식으로 `VideoFormat`의 배열을 만듭니다. 하나의 비디오 형식만 지원해도 되지만 제공된 비디오 형식 중 하나 이상은 `MediaFrameKind::VideoSoftware` 형식이어야 합니다. 여러 형식을 제공하는 경우 목록에 있는 형식의 순서는 사용할 항목에 영향을 주거나 우선 순위를 지정하지 않습니다. 선택한 형식은 네트워크 대역폭과 같은 외부 요소를 기반으로 합니다.

    ```java
    ArrayList<VideoFormat> videoFormats = new ArrayList<VideoFormat>();

    VideoFormat format = new VideoFormat();
    format.setWidth(1280);
    format.setHeight(720);
    format.setPixelFormat(PixelFormat.RGBA);
    format.setMediaFrameKind(MediaFrameKind.VIDEO_SOFTWARE);
    format.setFramesPerSecond(30);
    format.setStride1(1280 * 4); // It is times 4 because RGBA is a 32-bit format.

    videoFormats.add(format);
    ```

3. `OutboundVirtualVideoDeviceOptions`를 만들고 이전에 만든 개체를 사용하여 `DeviceIdentification` 및 `VideoFormats`를 설정합니다.

    ```java
    OutboundVirtualVideoDeviceOptions m_options = new OutboundVirtualVideoDeviceOptions();

    // ...

    m_options.setDeviceIdentification(deviceId);
    m_options.setVideoFormats(videoFormats);
    ```

4. `OutboundVirtualVideoDeviceOptions::OnFlowChanged` 대리자가 정의되어 있는지 확인합니다. 이 대리자는 앱이 비디오 프레임 생성을 시작하거나 중지해야 하는 이벤트에 대해 수신기에 알립니다. 이 빠른 시작에서 `m_mediaFrameSender`는 프레임 생성을 시작할 시기를 앱에 알리는 트리거로 사용됩니다. 앱의 모든 메커니즘을 트리거로 자유롭게 사용할 수 있습니다.

    ```java
    private MediaFrameSender m_mediaFrameSender;

    // ...

    m_options.addOnFlowChangedListener(virtualDeviceFlowControlArgs -> {
        if (virtualDeviceFlowControlArgs.getMediaFrameSender().getRunningState() == VirtualDeviceRunningState.STARTED) {
            // Tell the app's frame generator to start producing frames.
            m_mediaFrameSender = virtualDeviceFlowControlArgs.getMediaFrameSender();
        } else {
            // Tell the app's frame generator to stop producing frames.
            m_mediaFrameSender = null;
        }
    });
    ```

5. `DeviceManager::CreateOutboundVirtualVideoDevice`를 사용하여 아웃바운드 가상 비디오 디바이스를 만듭니다. 앱이 가상 비디오 디바이스로 계속 작동해야 하는 한 반환되는 `OutboundVirtualVideoDevice`는 활성 상태로 유지되어야 합니다. 앱당 여러 아웃바운드 가상 비디오 디바이스를 등록해도 됩니다.

    ```java
    private OutboundVirtualVideoDevice m_outboundVirtualVideoDevice;

    // ...

    m_outboundVirtualVideoDevice = m_deviceManager.createOutboundVirtualVideoDevice(m_options).get();
    ```

6. 호출 시 최근에 만든 가상 카메라를 사용하도록 디바이스 관리자에게 지시합니다.

    ```java
    private LocalVideoStream m_localVideoStream;

    // ...

    for (VideoDeviceInfo videoDeviceInfo : m_deviceManager.getCameras())
    {
        String deviceId = videoDeviceInfo.getId();
        if (deviceId.equalsIgnoreCase("QuickStartVirtualVideoDevice")) // Same id used in step 1.
        {
            m_localVideoStream = LocalVideoStream(videoDeviceInfo, getApplicationContext());
        }
    }
    ```

7.  앱의 비 UI 스레드 또는 루프에서 `MediaFrameSender`를 `VideoFormat`의 `MediaFrameKind` 속성에 의해 정의된 적절한 형식으로 캐스팅합니다. 예를 들어 `SoftwareBasedVideoFrame`으로 캐스팅한 다음, MediaFormat에 의해 정의된 평면 수에 따라 `send` 메서드를 호출합니다.
이후에 필요한 경우 비디오 프레임을 지원하는 ByteBuffer를 만듭니다. 그런 다음, 비디오 프레임의 콘텐츠를 업데이트합니다. 마지막으로 `sendFrame` API를 사용하여 다른 참가자에게 비디오 프레임을 보냅니다.

    ```java
    java.nio.ByteBuffer plane1 = null;
    Random rand = new Random();
    byte greyValue = 0;

    // ...
    java.nio.ByteBuffer plane1 = null;
    Random rand = new Random();

    while (m_outboundVirtualVideoDevice != null) {
        while (m_mediaFrameSender != null) {
            if (m_mediaFrameSender.getMediaFrameKind() == MediaFrameKind.VIDEO_SOFTWARE) {
                SoftwareBasedVideoFrame sender = (SoftwareBasedVideoFrame) m_mediaFrameSender;
                VideoFormat videoFormat = sender.getVideoFormat();

                // Gets the timestamp for when the video frame has been created.
                // This allows better synchronization with audio.
                int timeStamp = sender.getTimestamp();

                // Adjusts frame dimensions to the video format that network conditions can manage.
                if (plane1 == null || videoFormat.getStride1() * videoFormat.getHeight() != plane1.capacity()) {
                    plane1 = ByteBuffer.allocateDirect(videoFormat.getStride1() * videoFormat.getHeight());
                    plane1.order(ByteOrder.nativeOrder());
                }

                // Generates random gray scaled bands as video frame.
                int bandsCount = rand.nextInt(15) + 1;
                int bandBegin = 0;
                int bandThickness = videoFormat.getHeight() * videoFormat.getStride1() / bandsCount;

                for (int i = 0; i < bandsCount; ++i) {
                    byte greyValue = (byte)rand.nextInt(254);
                    java.util.Arrays.fill(plane1.array(), bandBegin, bandBegin + bandThickness, greyValue);
                    bandBegin += bandThickness;
                }

                // Sends video frame to the other participants in the call.
                FrameConfirmation fr = sender.sendFrame(plane1, timeStamp).get();

                // Waits before generating the next video frame.
                // Video format defines how many frames per second app must generate.
                Thread.sleep((long) (1000.0f / videoFormat.getFramesPerSecond()));
            }
        }

        // Virtual camera hasn't been created yet.
        // Let's wait a little bit before checking again.
        // This is for demo only purposes.
        // Feel free to use a better synchronization mechanism.
        Thread.sleep(100);
    }
    ```
