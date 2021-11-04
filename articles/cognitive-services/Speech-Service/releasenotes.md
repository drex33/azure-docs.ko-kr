---
title: 릴리스 정보 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service 기능 릴리스, 향상된 기능, 버그 수정 및 알려진 문제의 실행 로그입니다.
services: cognitive-services
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4b291006795728348aa694e56c3e84a88d8e65c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469961"
---
# <a name="speech-service-release-notes"></a>Speech Service 릴리스 정보

## <a name="speech-sdk-1190-2021-nov-release"></a>Speech SDK 1.19.0: 2021-Nov 릴리스  

 

**참고**. Speech SDK [여기](speech-sdk.md#get-the-speech-sdk)에서 시작을 해보십시오. Windows Speech SDK는 Visual Studio 공유 Microsoft Visual C++ 재배포 가능 Visual Studio 따라 달라집니다. [여기](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)에서 다운로드하세요.  
  

#### <a name="highlights"></a>주요 항목 

- Speaker Recognition 서비스는 현재 GA(일반 제공)입니다. Speech SDK API는 C++, C#, Java 및 Javascript에서 사용할 수 있습니다. 화자 인식을 사용하면 고유한 음성 특성으로 화자를 정확하게 확인하고 식별할 수 있습니다. 자세한 내용은 [설명서](speaker-recognition-overview.md)를 참조하세요. 

- Azure DevOps 및 Github와 함께 Ubuntu 16.04에 대한 지원을 중단했습니다. Ubuntu 16.04는 2021년 4월에 수명이 종료되었습니다. Ubuntu 16.04 워크플로를 Ubuntu 18.04 이상으로 마이그레이션하세요.   

- Linux 이진에서 OpenSSL 연결이 동적으로 변경되었습니다. Linux 이진 크기가 약 50% 감소했습니다. 

- Mac M1 ARM 기반 실리콘 지원이 추가되었습니다. 

 

#### <a name="new-features"></a>새로운 기능 

- **C++/C#/Java:** Microsoft Audio Stack을 사용하여 음성 입력에 대한 오디오 처리 지원을 사용할 수 있도록 새로운 API가 추가되었습니다. [여기](audio-processing-overview.md)서 설명함

- **C++**: 고급 패턴 일치를 용이하게 하는 의도 인식을 위한 새로운 API입니다. 여기에는 목록 및 미리 작성된 정수 엔터티뿐만 아니라 의도 및 엔터티를 모델로 그룹화하기 위한 지원이 포함됩니다(설명서, 업데이트 및 샘플은 개발 중이며 조만간 게시될 예정임). 

- **Mac:** GitHub 문제 1244와 관련된 Cocoapod, Python, Java 및 NuGet 패키지에 대한 [ARM64(M1)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/1244)기반 실리콘 지원

- **iOS/Mac:** 이제 iOS 및 MacOS 이진이 GitHub 문제 [919와](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/919)관련된 xcframework로 패키지됩니다.

- **iOS/Mac:** [GitHub 문제 1171과](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/1171)관련된 Mac용 지원 

- **Linux:** CentOS7 Speech [SDK에](speech-sdk.md)대한 새 tar 패키지가 추가되었습니다.

- **Javascript:** VoiceProfile & SpeakerRecognizer API를 async/awaitable로 만들었습니다. 

- **Javascript:** 미국 정부 Azure 지역에 대한 지원이 추가되었습니다. 

- **Windows:** UWP(유니버설 Windows 플랫폼)에서 재생에 대한 지원이 추가되었습니다. 

  

#### <a name="bug-fixes"></a>버그 수정 

- **Android: Android** 패키지용 OpenSSL 보안 업데이트(버전 1.1.1l로 업데이트됨) 

- **Python: Python에서** 화자 디바이스를 선택하는 데 실패하는 버그가 해결되었습니다. 

- **코어:** 연결 시도가 실패하면 자동으로 다시 연결합니다. 

- **iOS:** Gstreamer를 사용할 때 불안정성 및 비트코드 빌드 문제로 인해 iOS 패키지에서 오디오 압축이 사용되지 않습니다. 자세한 내용은 [GitHub 문제 1209를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/1209)통해 사용할 수 있습니다.

 

#### <a name="samples-github"></a>샘플 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

- **Mac/iOS:** xcframework 패키지를 사용하도록 샘플 및 빠른 시작이 업데이트되었습니다. 

- **.NET: .NET** Core 3.1 버전을 사용하도록 샘플이 업데이트되었습니다. 

- **Javascript:** 음성 도우미에 대한 샘플이 추가되었습니다. 

 
## <a name="text-to-speech-2021-october-release"></a>Text-to-Speech 2021-10월 릴리스
**신경망 TTS에 대한 새로운 언어 및 음성 추가**
- **49개의 새로운 언어 및 변형이 도입되었습니다.** 49개의 새 로케일로 98개의 새로운 음성이 신경망 TTS 언어 목록에 추가됩니다. Adri in `af-ZA` Afrikaans(남아프리카 공화국), `af-ZA` Afrikaans의 Willem (남아프리카 공화국), `am-ET` 앰하릭의 Mekdes(아랍어), `am-ET` Amharic의 아메하(폴란드), `ar-AE` 아랍어의 Fatima(미국령 까지), `ar-AE` 아랍어의 함메야어(영국령, `ar-BH` 아랍어로 나타남), 아랍어의 Laila(아랍어), 아랍어의 `ar-BH` Ali(아랍어),  `ar-DZ` 아랍어의 Amina(아랍어), 아랍어로 된 `ar-DZ` Ismael(아랍어), 아랍어로 된 란다(아랍어), 아랍어로 된 `ar-IQ` `ar-IQ` 카셀(아랍어), 아랍어로 된 `ar-JO` 사나(아랍어), 아랍어로 된 `ar-JO` 자말(아랍어), 아랍어로 된 `ar-KW` 누라(아랍어), 아랍어로 페이싱된(아랍어), 아랍어로 된 `ar-KW` 이만(아랍어), 아랍어로 된 모나(아랍어), 아랍어로 자말(아랍어), 아랍어로 된 `ar-LY` `ar-LY` `ar-MA` `ar-MA` 자말(아랍어), `ar-QA` 아랍어의 `ar-QA` 모아(아랍어)  Amany in `ar-SY` Arabic (Arabic), Laith in `ar-SY` Arabic (Arabic),Reem in `ar-TN` Arabic (Arabic),Hedi in `ar-TN` Arabic (Arabic(Arabic), `ar-YE` Saleh in Arabic (In Arabic( 2016), `ar-YE` Saleh in Arabic (Arabic), #A0 (2016), Asilia in English (Asilia in English ),2016; English (20), Ezinne in English `bn-BD` `bn-BD` `en-KE` `en-KE` `en-NG` `en-NG` (In English), Imani in `en-TZ` English (Ezimu in English), Elimu in `en-TZ` English (2)  스페인어로 된 `es-BO` 에스페인어(스웨덴), 스페인어로 된 `es-BO` Marcelo(스웨덴), 스페인어로 된 `es-CL` Catalina(스웨덴), 스페인어로 된 Lorenzo(스페인어), `es-CL` 스페인어로 된 `es-CR` Maria(에카), 스페인어로 된 `es-CR` 에키즈(에피카), 스페인어로 된 힌키스(스페인어), 스페인어로 된 람마어(스페인), 스페인어로 된 `es-CU` `es-CU` `es-DO` 에밀도(스페인), 스페인어로 된 `es-DO` `es-EC` 에필리초(에파리아), 스페인어로 된 `es-EC` Luis(에갈루어), 스페인어로 된 `es-GQ` Luis(적도)  `es-GQ` 스페인어의 Javier(적도)의 Javier, 스페인어로 된 `es-GT` Marta(에필), 스페인어의 Andres(스페인어), 스페인어로 된 `es-GT` `es-HN` Andres(에리스), 스페인어로 된 `es-HN` 카나(에리스), `es-NI` 스페인어의 Yolanda(Nicaragua), 스페인어로 페더레이션된 페더리코(Nicaragua), `es-NI` 스페인어로 마가리타(스페인어), 스페인어로 된 `es-PA` `es-PA` 마술라(에필라), 스페인어로 된 `es-PE` `es-PE` Alex(에메라인), 스페인어로 된 `es-PR` 카니나(2차), 스페인어로 된 카리아나(2차), `es-PR` 와다나  `es-PY` 스페인어(Paraguay), `es-PY` 스페인어(Paraguay), 스페인어로 된 `es-SV` Lorena(El Spanish), 스페인어로 된 로레나(El `es-SV` Spanish), 스페인어의 Valentina(필론), 스페인어로 된 `es-UY` `es-UY` 가폴라(스페인어), 스페인어로 된 Paola(필), 덴마크의 `es-VE` `es-VE` Dilara(필러), `fa-IR` `fa-IR` 에리스의 파리스카(에메인), 에브리어의 에리스카(에메인), `fil-PH` `fil-PH` 율리치어의 `gl-ES` 에필라(스페인), Roi `gl-ES`  갈리시아어(스페인), `jv-ID` Javanese의 Siti(포르투갈), `jv-ID` Javanese의 Dimas(포르투갈), `km-KH` Khmer의 Subamom(표시), `km-KH` 크메르어의 Piseth(표시), Nilar in `my-MM` Burmese(표시), `my-MM` Burmese의 히하(필어), Uba `so-SO` 2016년 3월 `so-SO` `su-ID` `su-ID` `sw-TZ` `sw-TZ` `ta-LK` 12일, 2016년 12월 31일, 3월 12일 30009년 12월 31일 3000만 30000018년 12월 21일  `ta-LK` 타밀어(스리루), Venba in `ta-SG` 타밀어(싱가포르), `ta-SG` 타밀어의 Anbu(싱가포르),Urdu의 `ur-IN` Gul(인도), `ur-IN` Urdu(인도), `uz-UZ` Uzbek의 무디나(유니버키), `uz-UZ` Uzbek의 Sardor(남아프리카 공화국), `zu-ZA` Zulu의 Thando(남아프리카), `zu-ZA` Zulu(남아프리카)의 세노어.

## <a name="text-to-speech-2021-september-release"></a>Text-to-Speech 2021-9월 릴리스
- **의 새 챗봇 `en-US` 음성 영어(미국)**: 챗봇 시나리오에 가장 적합하고 더 간소하게 말하는 여성 성인을 나타내는 영문입니다. 
- **에 대해 추가된 `ja-JP` 새 스타일 일본어 음성 Nanami: 이제 Nanami에서** 채팅, 고객 서비스 및 싱어의 세 가지 새로운 스타일을 사용할 수 있습니다.
- **과도 발음 개선** 사항: 의 유지, 의 `id-ID` Premwadee, `th-TH` 의 경우, 의 경우 `da-DK` HoaiMy 및 NamMinh의 경우 `vi-VN`
- **의 두 가지 `zh-CN` 새로운 음성 중국어(중국어, 중국) 미리 보기:** 힌조 음성 및 고객 서비스 시나리오에 최적화된 & 중국어입니다.

## <a name="text-to-speech-2021-july-release"></a>텍스트 음성 변환 2021년 7월 릴리스

**인공 신경망 TTS 업데이트**
- 히브리어로 발음 오류가 20%로 줄어듭니다.

**Speech Studio 업데이트**
- **사용자 지정 신경망 음성** 은 어쿠스틱 모델을 위한 학습 시간이 50% 감소하는 동안 모델 품질이 향상된 UniTTSv3 학습 파이프라인을 업데이트 했습니다. 
- **오디오 콘텐츠 만들기** 는 "내보내기" 성능 문제 및 사용자 지정 음성 선택에 대한 버그를 수정했습니다.  

## <a name="speech-sdk-1180-2021-july-release"></a>Speech SDK 1.18.0: 2021년 7월 릴리스

**참고**. Speech SDK [여기](speech-sdk.md#get-the-speech-sdk)에서 시작을 해보십시오.

**하이라이트 요약**
- Ubuntu 16.04는 2021년 4월의 수명 끝에 도달했습니다. Azure DevOps 및 GitHub 함께 2021년 9월에 16.04에 대한 지원을 중단할 예정입니다.  그 전에 ubuntu-16.04 워크플로를 ubuntu-18.04 이상으로 마이그레이션 하세요. 

#### <a name="new-features"></a>새로운 기능

- **C++** 는 의도 인식기를 사용하여 간단한 언어 패턴 일치를 통해 [간단한 의도 인식 시나리오](./get-started-intent-recognition.md?pivots=programming-language-cpp)를 보다 쉽게 구현할 수 있습니다.
- **C++/C#/Java** 에서는 독립적인 인식 시나리오에 대한 발표자 인식 등록 단계에서 유효한 활성화 문구 목록을 수신하기 위해 `GetActivationPhrasesAsync()`를 `VoiceProfileClient`클래스에 새 API를 추가했습니다. 
    - **중요**. 발표자 인식 기능은 미리보기 상태입니다. 미리보기로 만든 모든 음성 프로필은 화자 인식 기능이 미리 보기에서 일반 공급으로 전환된 후 90 일 후에 중단됩니다. 이 시점에서 미리보기 음성 프로필의 작동이 중지됩니다.
- **Python** 에는 기존의 `SpeechRecognizer`와`TranslationRecognizer`개체들에 대한 [자동 언어 식별 (LID)에 대한 지원](./how-to-automatic-language-detection.md?pivots=programming-language-python)이 추가되었습니다. 
- **Python**: 한번 또는 연속 LID(인식 또는 번역없음)을 수행하는`SourceLanguageRecognizer`라는 [새 Python 개체](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.sourcelanguagerecognizer)를 추가했습니다. 
- **JavaScript** 에는 독립적인 인식 시나리오에 대한 발표자 인식 등록 단계에서 유효한 활성화 문구 목록을 받기 위한 `getActivationPhrasesAsync`API가 `VoiceProfileClient` 클래스에 추가되었습니다. 
- **JavaScript** 는`VoiceProfileClient`의`enrollProfileAsync` API는 이제 비동기 대기중입니다. 사용 예제는 [독립 식별 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/node/speaker-recognition/identification/independent-identification.js)를 참조하세요.

#### <a name="improvements"></a>향상 된 기능

- **Java**: 많은 Java 개체에 **AutoCloseable** 지원이 추가되었습니다. 이제 리소스를 사용하여 리소스를 해제할 수 있습니다. [‘리소스로 시도’를 사용하는 이 샘플을](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java#L28)참조하세요. 또한 이 패턴에 대해 알아보려면 [‘리소스로 시도’ 설명문](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)대한 Oracle Java 자습서를 참조하십시오.
- 많은 플랫폼과 아키텍처에서 **디스크 공간이** 크게 줄었습니다. 이진 파일`Microsoft.CognitiveServices.Speech.core`에 대한 예시로 X64 Linux는 475KB 작고(8.0% 감소) ARM64 Windows UWP는 464kb 보다 작습니다(11.5% 감소). x86 Windows는 343kb 보다 작으며(17.5% 감소) x64 Windows 451KB 더 작습니다(19.4% 감소).

#### <a name="bug-fixes"></a>버그 수정

- **Java** 는 합성 텍스트가 서로게이트 문자를 포함하는 경우의 합성 오류를 해결했습니다. 자세한 내용은 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/1118)를 참조하세요. 
- **JavaScript** 에서 브라우저 마이크 오디오 처리는 이제 `AudioWorkletNode`를 사용하지 않는 대신 `ScriptProcessorNode`를 사용합니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/391)를 참조하세요.
- **JavaScript** 는 장기적 대화 번역 상황에서 올바르게 대화를 유지합니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/389)를 참조하세요.
- **JavaScript** 의 연속 인식할 때 mediastream에 대한 인식기 재연결 문제를 해결했습니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/385)를 참조하세요.
- **JavaScript**: 연속 인식할 때 pushStream에 대한 인식기 재연결 문제를 해결했습니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/399)를 참조하세요.
- **JavaScript**: 자세한 인식 결과에서 단어 수준 오프셋 계산을 수정했습니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/394)를 참조하세요.

#### <a name="samples"></a>샘플

- Java 빠른 시작 샘플은 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java)에 업데이트 되었습니다.
- JavaScript 스피커 화자 인식 샘플은 `enrollProfileAsync()`의 새로운 사용을 표시 하도록 업데이트 되었습니다. [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/node)에서 샘플을 참조합니다.

## <a name="text-to-speech-2021-june-release"></a>텍스트 음성 변환 2021년 6월 릴리스

**Speech Studio 업데이트**

- **사용자 지정 신경망 음성** 는 동남 아시아를 지원 하도록 확장된 사용자 지정 신경망 음성 학습입니다. 지원을 데이터 업로드 상태 확인을 위한 새로운 기능이 출시되었습니다. 
- **오디오 콘텐츠 만들기** 는 사용자 지정 어휘를 지원하는 새 기능을 릴리스했습니다. 이 기능을 사용하면 사용자가 쉽게 어휘집 파일을 만들고 사용자 지정된 음성 출력에 대한 사용자 지정 발음을 정의할 수 있습니다. 

## <a name="text-to-speech-2021-may-release"></a>텍스트 음성 변환 2021년 5월 릴리스

**인공신경망 TTS에 새 언어 및 음성 추가**

- **10개의 새로운 언어 도입** - 10개의 새로운 로캘에서 20개의 새로운 음성이 인공신경망 TTS 언어 목록에 추가되었습니다. Yan - `en-HK` 영어(홍콩), Sam - `en-HK` 영어(홍콩), Molly - `en-NZ` 영어(뉴질랜드), Mitchell - `en-NZ` 영어(뉴질랜드), Luna - `en-SG` 영어(싱가포르), Wayne - `en-SG` 영어(싱가포르), Leah - `en-ZA` 영어(남아프리카), Luke - `en-ZA` 영어(남아프리카), Dhwani - `gu-IN` 구자라트어(인도), Niranjan - `gu-IN` 구자라트어(인도), Aarohi - `mr-IN` 마라티어(인도), Manohar - `mr-IN` 마라티어(인도), Elena - `es-AR` 스페인어(아르헨티나), Tomas - `es-AR` 스페인어(아르헨티나), Salome - `es-CO` 스페인어(콜롬비아), Gonzalo - `es-CO` 스페인어(콜롬비아), Paloma - `es-US` 스페인어(미국), Alonso - `es-US` 스페인어(미국), Zuri - `sw-KE` 스와힐리어(케냐), Rafiki - `sw-KE` 스와힐리어(케냐).

- **미리 보기에서 11개의 새로운 en-US 음성** - 미리 보기에서 11개의 새로운 en-US 음성이 미국 영어에 추가되었습니다. Ashley, Amber, Ana, Brandon, Christopher, Cora, Elizabeth, Eric, Michelle, Monica, Jacob입니다.

- **5개의 `zh-CN` 중국어(북경어, 간체) 음성이 일반 공급됩니다.** - 5개의 중국어(북경어, 간체) 음성이 미리 보기에서 일반 공급으로 변경되었습니다. Yunxi, Xiaomo, Xiaoman, Xiaoxuan, Xiaorui입니다. 이제 이러한 음성은 모든 [지역](regions.md#neural-and-standard-voices)에서 사용할 수 있습니다. Yunxi는 채팅 봇 및 음성 에이전트에 적합한 새로운 '도우미' 스타일로 추가되었습니다. Xiaomo의 음성 스타일은 보다 자연스럽고 기능적으로 개선되었습니다.

## <a name="speech-sdk-1170-2021-may-release"></a>Speech SDK 1.17.0: 2021년 5월 릴리스

>[!NOTE]
>[여기](speech-sdk.md#get-the-speech-sdk)에서 Speech SDK 시작

**하이라이트 요약**

- 사용 공간 축소 - Speech SDK 및 해당 구성 요소에 사용되는 메모리와 디스크 공간을 계속해서 줄이고 있습니다.
- 새로운 독립 실행형 언어 식별 API를 사용하면 사용 중인 언어를 인식할 수 있습니다.
- macOS에서 Unity를 사용하여 음성 지원 혼합 현실 및 게임 애플리케이션을 개발합니다.
- 이제 Go 프로그래밍 언어의 음성 인식 외에도 텍스트 음성 변환을 사용할 수 있습니다.
- 소중한 고객의 문제를 해결하기 위한 여러 버그 수정이 GitHub에 플래깅되었습니다. 감사합니다. 앞으로도 피드백을 계속 보내주세요.

#### <a name="new-features"></a>새로운 기능

- **C++/C#** : `SourceLanguageRecognizer` API를 통한 새로운 독립 실행형 1단계/시작 시 언어 및 지속적인 언어 식별. 오디오 콘텐츠에서 사용 중인 언어만 감지하려는 경우 이를 수행하는 API입니다.
- **C++/C#** : 이제 음성 인식 및 번역 인식은 1단계 및 지속적인 언어 식별을 모두 지원하므로 텍스트를 전사하거나 번역하기 전에 사용 중인 언어를 프로그래밍 방식으로 확인할 수 있습니다. [음성 인식](how-to-automatic-language-detection.md) 및 [음성 번역](get-started-speech-translation.md) 문서를 참조하세요.
- **C#** : macOS(x64)에 Unity 지원이 추가되었습니다. 이를 통해 혼합 현실 및 게임에서 음성 인식 및 음성 합성을 사용할 수 있습니다.
- **Go**: 더 많은 사용 사례에서 음성 합성을 사용할 수 있도록 Go 프로그래밍 언어에 음성 합성/텍스트 음성 변환 지원을 추가했습니다. [빠른 시작](get-started-text-to-speech.md?tabs=windowsinstall&pivots=programming-language-go) 또는 [참조 문서](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go)를 참조하세요.
- **C++/C#/Java/Python/Objective-C/Go**: 이제 음성 합성기가 `connection` 개체를 지원합니다. 이를 통해 음성 서비스에 대한 연결을 관리하고 모니터링할 수 있으며 특히 사전 연결을 통해 대기 시간을 줄일 수 있습니다. [여기](how-to-lower-speech-synthesis-latency.md) 설명서를 참조하세요.
- **C++/C#/Java/Python/Objective-C/Go**: 이제 `SpeechSynthesisResult`에서 지연 시간과 언더런 시간을 공개하여 음성 합성 지연 문제를 모니터링하고 진단할 수 있습니다. [C++](/cpp/cognitive-services/speech/speechsynthesisresult), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult), [Objective-C](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) 및 [Go](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go#readme-reference)에 대한 세부 정보를 참조하세요.
- **C++/C#/Java/Python/Objective-C**: 이제 텍스트 음성 변환은 사용할 음성을 지정하지 않는 경우 기본적으로 [신경망 음성을 사용](text-to-speech.md#core-features)합니다. 이렇게 하면 기본적으로 더 높은 품질의 출력이 제공되지만 [기본 가격도 증가](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/#pricing)합니다. [70개가 넘는 표준 음성](language-support.md#standard-voices) 또는[130개가 넘는 신경망 음성](language-support.md#neural-voices)을 지정하여 기본값을 변경할 수 있습니다.
- **C++/C#/Java/Python/Objective-C/Go**: 성별에 따라 음성을 더 쉽게 선택할 수 있도록 합성 음성 정보에 성별 속성을 추가했습니다. 따라서 [GitHub #1055 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/1055)도 해결됩니다.
- **C++, C#, Java, JavaScript**: 이제 스피커 인식에서 `retrieveEnrollmentResultAsync`, `getAuthorizationPhrasesAsync` 및 `getAllProfilesAsync()`를 지원하여 지정된 계정에 대한 모든 음성 프로필의 사용자 관리가 용이합니다. [C++](/cpp/cognitive-services/speech/speaker-voiceprofileclient), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speaker.voiceprofileclient), [Java](/java/api/com.microsoft.cognitiveservices.speech.voiceprofileclient), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient)에 대한 문서를 참조하세요. 따라서 [GitHub #338 문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/338)도 해결됩니다.
- **JavaScript**: JavaScript 기반 음성 애플리케이션을 더욱 강력하게 만드는 연결 실패에 대한 재시도를 추가했습니다.

#### <a name="improvements"></a>향상 된 기능

- Linux 및 Android Speech SDK 이진 파일이 최신 버전의 OpenSSL(1.1.1k)을 사용하도록 업데이트되었습니다.
- 코드 크기 개선:
    - 이제 언어 이해는 별도의 "lu" 라이브러리로 분할됩니다.
    - Windows x64 코어 바이너리 크기가 14.4% 감소했습니다.
    - Android ARM64 코어 바이너리 크기가 13.7% 감소했습니다.
    - 다른 구성 요소도 크기가 감소했습니다.

#### <a name="bug-fixes"></a>버그 수정

- **모두**: ServiceTimeout에 대한 [GitHub 문제 #842](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/842)가 수정되었습니다. 이제 이 오류로 종료되는 서비스에 대한 연결 없이 Speech SDK를 사용하여 매우 긴 오디오 파일을 전사할 수 있습니다. 그러나 긴 파일에는 [일괄 전사](batch-transcription.md)를 사용하는 것이 좋습니다.
- **C#** : 음성 입력이 없으면 앱이 잘못된 상태가 될 수 있는 [GitHub 문제 #947](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/947)이 수정되었습니다.
- **Java**: 네트워크 연결이나 잘못된 구독 키 없이 DialogServiceConnector를 사용할 때 Java Speech SDK 1.16이 충돌하는 [GitHub 문제 #997](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/997)이 수정되었습니다.
- 음성 인식을 갑자기 중지(예: 콘솔 앱에서 CTRL+C 사용)할 때 발생하는 충돌을 수정했습니다.
- **Java**: Java Speech SDK를 사용할 때 Windows에서 임시 파일을 삭제하는 수정 사항이 추가되었습니다.
- **Java**: `DialogServiceConnector.stopListeningAsync`를 호출하면 오류가 발생할 수 있는 [GitHub 문제 #994](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/994)가 수정되었습니다.
- **Java**: [가상 도우미 빠른 시작](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/virtual-assistant)에서 고객 문제가 수정되었습니다.
- **JavaScript**: `ConversationTranslator`에서 'this.cancelSpeech가 함수가 아님' 오류를 throw한 [GitHub 문제 #366](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/366)이 수정되었습니다.
- **JavaScript**: '메모리 내 스트림으로 결과 가져오기' 샘플이 소리내어 재생되는 [GitHub 문제 #298](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/298)가 수정되었습니다.
- **JavaScript**: `AudioConfig`를 호출하면 'ReferenceError: MediaStream이 정의되지 않음'이 발생할 수 있는 [GitHub 문제 #350](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/350)이 수정되었습니다.
- **JavaScript**: 장기 실행 세션에 대한 Node.js의 UnhandledPromiseRejection 경고가 수정되었습니다.

#### <a name="samples"></a>샘플

- [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk)에서 macOS용 Unity 샘플 문서가 업데이트되었습니다.
- 이제 Cognitive Services 음성 인식 서비스에 대한 React Native 샘플을 [여기](https://github.com/microsoft/cognitive-services-sdk-react-native-example)에서 사용할 수 있습니다.

## <a name="speech-cli-also-known-as-spx-2021-may-release"></a>Speech CLI(SPX라고도 함): 2021년 5월 릴리스

>[!NOTE]
>[여기](spx-basics.md)서 Azure Speech Service CLI(명령줄 인터페이스)를 시작하세요. CLI를 사용하면 코드를 작성하지 않고도 Azure Speech Service를 사용할 수 있습니다.

#### <a name="new-features"></a>새로운 기능

- 이제 SPX는 프로필, 스피커 ID 및 스피커 확인을 지원합니다. SPX 명령줄에서 `spx profile` 및 `spx speaker`를 실행해 보세요.
- 또한 대화 상자 지원을 추가했습니다. SPX 명령줄에서 `spx dialog`를 실행해 보세요.
- SPX 도움말 개선. [GitHub 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)를 열어 이 도움말이 어떻게 작동하는지에 대한 의견을 보내주세요.
- SPX.NET 도구 설치의 크기를 줄였습니다.

**코로나19로 테스트 축소**:

지속되는 전염병으로 인해 엔지니어들이 계속 집에서 근무하게 되면서, 전염병 발생 이전에 비해 수동 확인 단계가 크게 축소되었습니다. 테스트할 수 있는 디바이스와 구성의 수가 줄었기 때문에 환경별 버그를 놓칠 가능성이 높아질 수 있습니다. 저희는 수많은 자동화를 통해 여전히 엄격한 유효성 검사를 수행하고 있습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="text-to-speech-2021-april-release"></a>텍스트 음성 변환 2021년 4월 릴리스

**인공신경망 TTS를 21개 지역에서 사용할 수 있음**

- **12개의 새로운 지역이 추가됨** - 이제 새로운 12개 지역에서 인공신경망 TTS를 사용할 수 있습니다. `Japan East`, `Japan West`, `Korea Central`, `North Central US`, `North Europe`, `South Central US`, `Southeast Asia`, `UK South`, `west Central US`, `West Europe`, `West US`, `West US 2` 21개 지원 지역의 전체 목록은 [여기](regions.md#text-to-speech)를 확인하세요.

## <a name="text-to-speech-2021-march-release"></a>텍스트 음성 변환 2021년 3월 릴리스

**인공신경망 TTS에 새 언어 및 음성 추가**

- **6개 새 언어 도입** - 다음과 같은 6개 새 로캘의 12개 새 음성이 인공신경망 TTS 언어 목록에 추가되었습니다. `cy-GB` 웨일스어(영국)를 사용하는 Nia, `cy-GB` 웨일스어(영국)를 사용하는 Aled, `en-PH` 영어(필리핀)를 사용하는 Rosa, `en-PH` 영어(필리핀)를 사용하는 James, `fr-BE` 프랑스어(벨기에)를 사용하는 Charline, `fr-BE` 프랑스어(벨기에)를 사용하는 Gerard, `nl-BE` 네덜란드어(벨기에)를 사용하는 Dena, `nl-BE` 네덜란드어(벨기에)를 사용하는 Arnaud, `uk-UA` 우크라이나어(우크라이나)를 사용하는 Polina, `uk-UA` 우크라이나어(우크라이나)를 사용하는 Ostap, `ur-PK` 우르두어(파키스탄)를 사용하는 Uzma, `ur-PK` 우르두어(파키스탄)를 사용하는 Asad.

- **미리 보기에서 GA로 전환되는 5개 언어** - 2020년 11월에 도입된 다음과 같은 5개 로캘의 10개 음성이 이제 GA로 전환됩니다. `et-EE` 에스토니아어(에스토니아)를 사용하는 Kert, `ga-IE` 아일랜드어(아일랜드)를 사용하는 Colm, `lv-LV` 라트비아어(라트비아)를 사용하는 Nils, `lt-LT` 리투아니아어(리투아니아)를 사용하는 Leonas, `mt-MT` 몰타어(몰타)를 사용하는 Joseph.

- **프랑스어(캐나다)에 새로운 남성 음성 추가** - `fr-CA` 프랑스어(캐나다)에 새 음성 Antoine가 추가되었습니다.

- **품질 향상** - 발음 오류율이 `hu-HU` 헝가리어 - 48.17%, `nb-NO` 노르웨이어 - 52.76%, `nl-NL` 네덜란드어(네덜란드) - 22.11%로 낮아졌습니다.

이번 릴리스에서는 60개 언어/로캘의 총 142개 인공신경망 음성을 지원합니다. 또한 70개가 넘는 표준 음성이 49개 언어/로캘로 제공됩니다. 전체 목록을 보려면 [언어 지원](language-support.md#text-to-speech)을 방문하세요.

**문자에 애니메이션 효과를 주는 얼굴 포즈 이벤트 받기**

이제 인공신경망 텍스트 음성 변환에 [viseme 이벤트](how-to-speech-synthesis-viseme.md)가 포함됩니다. Viseme 이벤트를 통해 사용자는 합성된 음성과 함께 일련의 얼굴 포즈를 가져올 수 있습니다. Viseme은 2D 및 3D 아바타 모델의 움직임을 제어하는 데 사용할 수 있으며 입 움직임을 합성된 음성과 일치시킵니다. Viseme 이벤트는 현재 `en-US-AriaNeural` 음성에만 사용할 수 있습니다.

**SSML(Speech Synthesis Markup Language)에 책갈피 요소 추가**

[책갈피 요소](speech-synthesis-markup.md#bookmark-element)를 사용하면 SSML로 사용자 지정 표식을 삽입하여 오디오 스트림에 있는 각 표식의 오프셋을 얻을 수 있습니다. 텍스트 또는 태그 시퀀스의 특정 위치를 참조하는 데 사용할 수 있습니다.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: 2021년 3월 릴리스

> [!NOTE]
> Windows의 Speech SDK는 Visual Studio 2015, 2017 및 2019용 공유 Microsoft Visual C++ 재배포 가능 패키지를 사용합니다. [여기](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)에서 다운로드하세요.

#### <a name="new-features"></a>새로운 기능

- **C++/C#/Java/Python**: Windows, Linux 및 Android에서 모든 미디어 형식을 지원하도록 최신 버전의 GStreamer(1.18.3)로 이동되었습니다. [여기](how-to-use-codec-compressed-audio-input-streams.md) 설명서를 참조하세요.
- **C++/C#/Java/Objective-C/Python**: 압축된 TTS/합성된 오디오를 SDK로 디코딩하는 지원이 추가되었습니다. 출력 오디오 형식을 PCM으로 설정하고 시스템에서 GStreamer를 사용할 수 있으면 SDK는 서비스에서 압축된 오디오를 자동으로 요청하여 대역폭을 절약하고 클라이언트에서 오디오를 디코딩합니다. 이 기능을 사용하지 않으려면 `SpeechServiceConnection_SynthEnableCompressedAudioTransmission`을 `false`로 설정합니다. [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.propertyid), [Java](/java/api/com.microsoft.cognitiveservices.speech.propertyid), [Objective-C](/objectivec/cognitive-services/speech/spxpropertyid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid)에 대한 세부 정보를 제공합니다.
- **JavaScript**: 이제 Node.js 사용자는 [`AudioConfig.fromWavFileInput` API](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromWavFileInput_File_)를 사용할 수 있습니다. 따라서 [GitHub #252 문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252)도 해결됩니다.
- **C++/C#/Java/Objective-C/Python**: 사용 가능한 모든 합성 음성을 반환하는 TTS에 대한 `GetVoicesAsync()` 메서드가 추가되었습니다. [C++](/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer#methods), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer#methods), [Objective-C](/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync) 및 [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#methods)에 대한 세부 정보를 제공합니다.
- **C++/C#/Java/JavaScript/Objective-C/Python**: 동기 viseme 애니메이션을 반환하는 TTS/음성 합성에 대한 `VisemeReceived` 이벤트가 추가되었습니다. [여기](./how-to-speech-synthesis-viseme.md) 설명서를 참조하세요.
- **C++/C#/Java/JavaScript/Objective-C/Python**: TTS에 대한 `BookmarkReached` 이벤트가 추가되었습니다. 입력 SSML에서 책갈피를 설정하고 각 책갈피에 대한 오디오 오프셋을 가져올 수 있습니다. [여기](./speech-synthesis-markup.md#bookmark-element) 설명서를 참조하세요.
- **Java**: 화자 인식 API에 대한 지원이 추가되었습니다. 자세한 내용은 [여기](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer)를 참조하세요.
- **C++/C#/Java/JavaScript/Objective-C/Python**: TTS용 WebM 컨테이너(Webm16Khz16BitMonoOpus 및 Webm24Khz16BitMonoOpus)가 포함된 두 가지 새로운 출력 오디오 형식이 추가되었습니다. 추가된 형식은 Opus 코덱으로 오디오를 스트리밍하는 데 보다 적합합니다. [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), [Objective-C](/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat)에 대한 세부 정보를 제공합니다.
- **C++/C#/Java**: 화자 인식 시나리오를 위한 음성 프로필 검색 지원이 추가되었습니다. [C++](/cpp/cognitive-services/speech/speaker-speakerrecognizer), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speaker.speakerrecognizer) 및 [Java](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer)에 대한 세부 정보를 제공합니다.
- **C++/C#/Java/Objective-C/Python**: 오디오 마이크 및 스피커 제어를 위한 별도의 공유 라이브러리 지원이 추가되었습니다. 이제 필수 오디오 라이브러리 종속성이 없는 환경에서 SDK를 사용할 수 있습니다.
- **Objective-C/Swift**: 엄브렐라 헤더가 포함된 모듈 프레임워크 지원이 추가되었습니다. 이제 iOS/Mac Objective-C/Swift 앱에서 음성 SDK를 모듈로 가져올 수 있습니다. 따라서 [GitHub #452 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)도 해결됩니다.
- **Python**: [Python 3.9](./quickstarts/setup-platform.md?pivots=programming-language-python)에 대한 지원이 추가되었으며 Python의 [3.5 수명 종료](https://devguide.python.org/devcycle/#end-of-life-branches)에 따라 Python 3.5 지원이 삭제되었습니다.

**알려진 문제**

- **C++/C#/Java**: `DialogServiceConnector`는 `CustomCommandsConfig`를 사용하여 사용자 지정 명령 애플리케이션에 액세스할 수 없으며 대신 연결 오류가 발생합니다. 이 문제는 `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)`를 사용하여 애플리케이션 ID를 요청에 수동으로 추가하여 해결할 수 있습니다. `CustomCommandsConfig`의 예상 동작은 다음 릴리스에서 복원됩니다.

#### <a name="improvements"></a>향상 된 기능

- Speech SDK의 메모리 사용량 및 디스크 공간을 줄이기 위한 다중 릴리스 노력의 일환으로 Android 바이너리가 3%~5% 작아졌습니다.
- [여기](/dotnet/api/microsoft.cognitiveservices.speech)서 C# 참조 설명서의 향상된 정확도, 가독성 및 참조 섹션을 확인하세요.

#### <a name="bug-fixes"></a>버그 수정

- **JavaScript**: 이제 큰 WAV 파일 헤더가 올바르게 구문 분석됩니다(헤더 조각을 512바이트로 확장). 따라서 [GitHub #962 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)도 해결됩니다.
- **JavaScript**: 인식 중지 전에 마이크 스트림이 종료되는 마이크 타이밍 문제가 수정되었으며, 음성 인식이 Firefox에서 작동하지 않는 문제가 해결되었습니다.
- **JavaScript**: 이제 turnOn이 완료되기 전에 브라우저가 마이크를 강제로 끄더라도 초기화 프라미스가 올바르게 처리됩니다.
- **JavaScript**: url 종속성을 url 구문 분석으로 대체했습니다. 따라서 [GitHub #264 문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)도 해결됩니다.
- **Android**: `minifyEnabled`를 true로 설정하면 콜백이 작동하지 않는 문제를 수정했습니다.
- **C++/C#/Java/Objective-C/Python**: 대기 시간을 줄이기 위해 `TCP_NODELAY`가 TTS의 기본 소켓 IO로 올바르게 설정됩니다.
- **C++/C#/Java/Python/Objective-C/Go**: 인식을 시작한 직후 인식기가 삭제되었을 때 발생하는 간헐적 충돌을 수정했습니다.
- **C++/C#/Java**: 화자 인식기가 삭제될 때 발생하는 간헐적 충돌을 수정했습니다.

#### <a name="samples"></a>샘플

- **JavaScript**: 이제 [브라우저 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)을 사용하기 위해 별도의 JavaScript 라이브러리 파일을 다운로드할 필요가 없습니다.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Speech CLI(SPX라고도 함): 2021년 3월 릴리스

> [!NOTE]
> [여기](spx-basics.md)서 Azure Speech Service CLI(명령줄 인터페이스)를 시작하세요. CLI를 사용하면 코드를 작성하지 않고도 Azure Speech Service를 사용할 수 있습니다.

#### <a name="new-features"></a>새로운 기능

- 의도 인식을 위한 `spx intent` 명령이 추가되었으며, 이 명령은 `spx recognize intent`를 대체합니다.
- 이제 인식 및 의도에서 Azure 함수를 사용하여 `spx recognize --wer url <URL>`을 통해 단어 오류율을 계산할 수 있습니다.
- 이제 인식에서 `spx recognize --output vtt file <FILENAME>`을 사용하여 결과를 VTT 파일로 출력할 수 있습니다.
- 이제 중요한 키 정보는 디버그/자세한 정보 출력에서 잘 보이지 않게 표시됩니다.
- 일괄 처리 대화 내용 기록 만들기의 콘텐츠 필드에 대한 URL 검사 및 오류 메시지가 추가되었습니다.

**코로나19로 테스트 축소**:

지속되는 전염병으로 인해 엔지니어들이 계속 집에서 근무하게 되면서, 전염병 발생 이전에 비해 수동 확인 단계가 크게 축소되었습니다. 테스트할 수 있는 디바이스와 구성의 수가 줄었기 때문에 환경별 버그를 놓칠 가능성이 높아질 수 있습니다. 저희는 수많은 자동화를 통해 여전히 엄격한 유효성 검사를 수행하고 있습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="text-to-speech-2021-february-release"></a>텍스트 음성 변환 2021년 2월 릴리스

**사용자 지정 신경망 음성 GA**

사용자 지정 신경망 음성은 2월부터 중국어(북경어, 간체), 영어(오스트레일리아), 영어(인도), 영어(영국), 영어(미국), 프랑스어(캐나다), 프랑스어(프랑스), 독일어(독일), 이탈리아어(이탈리아), 일본어(일본), 한국어(대한민국), 포르투갈어(브라질), 스페인어(멕시코), 스페인어(스페인) 총 13개 언어로 일반 공급됩니다. [사용자 지정 신경망 음성](custom-neural-voice.md)이란 무엇이며 [책임감 있게 사용하는 방법](concepts-guidelines-responsible-deployment-synthetic.md)은 무엇인지 자세히 알아보세요.
사용자 지정 신경망 음성 기능을 사용하려면 등록이 필요하며 Microsoft는 Microsoft의 자격 기준에 따라 액세스를 제한할 수 있습니다. [제한된 액세스](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)에 대해 자세히 알아보세요.

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: 2021년 1월 릴리스

> [!NOTE]
> Windows의 Speech SDK는 Visual Studio 2015, 2017 및 2019용 공유 Microsoft Visual C++ 재배포 가능 패키지를 사용합니다. [여기](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)에서 다운로드하세요.

**하이라이트 요약**
- 메모리 및 디스크 공간을 줄여서 SDK 효율을 높입니다.
- 사용자 지정 신경망 음성 프라이빗 미리 보기에 사용할 수 있는 충실도 높은 출력 형식이 제공됩니다.
- 이제 의도 인식기가 상위 의도보다 더 많은 반환값을 얻을 수 있으므로 고객의 의도에 대한 별도의 평가를 수행할 수 있습니다.
- 이제 음성 도우미 또는 봇을 더 쉽게 설정할 수 있으며, 즉시 수신 대기를 중지하고 오류에 대응하는 방법을 보다 효과적으로 제어할 수 있습니다.
- 압축을 선택 사항으로 만들어서 디바이스 성능을 향상했습니다.
- Windows ARM/ARM64에서 Speech SDK를 사용합니다.
- 낮은 수준의 디버깅이 향상되었습니다.
- 이제 발음 평가 기능을 보다 폭넓게 사용할 수 있습니다.
- 소중한 고객의 문제를 해결하기 위한 여러 버그 수정이 GitHub에 플래깅되었습니다. 감사합니다. 앞으로도 피드백을 계속 보내주세요.

**향상된 기능**
- 이제 Speech SDK는 더 효율적이고 가볍습니다. Speech SDK의 메모리 사용량 및 디스크 공간을 줄이기 위한 다중 릴리스 작업이 시작되었습니다. 첫 번째 단계로 대부분의 플랫폼에서 공유 라이브러리의 파일 크기를 대폭 줄였습니다. 1\.14 릴리스와 비교할 때 다음과 같은 차이가 있습니다.
  - 64비트 UWP 호환 Windows 라이브러리가 약 30% 작아졌습니다.
  - 32비트 Windows 라이브러리는 아직 크기가 개선되지 않았습니다.
  - Linux 라이브러리는 20-25% 작아졌습니다.
  - Android 라이브러리는 3-5% 작아졌습니다.

**새로운 기능**
- **모두**: TTS 음성 합성 API를 통해 사용자 지정 신경망 음성의 프라이빗 미리 보기에 사용할 수 있는 새로운 48KHz 출력 형식으로 Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm이 제공됩니다.
- **모두**: 사용자 지정 음성을 더 쉽게 사용할 수 있습니다. `EndpointId`([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id))를 통해 사용자 지정 음성을 설정하는 지원이 추가되었습니다. 이 변경 전에는 사용자 지정 음성 사용자가 `FromEndpoint` 메서드를 통해 엔드포인트 URL을 설정해야 했습니다. 이제 고객은 퍼블릭 음성과 마찬가지로 `FromSubscription` 메서드를 사용한 다음, `EndpointId`를 설정하여 배포 ID를 제공하면 됩니다. 사용자 지정 음성을 더 간단하게 설정할 수 있게 되었습니다.
- **C++/C#/Java/Objective-C/Python**: `IntentRecognizer`에서 상위 의도보다 더 많은 결과를 가져옵니다. 이제 `LanguageUnderstandingModel FromEndpoint` 메서드를 통해 `verbose=true` uri 매개 변수를 사용하여 상위 채점 의도뿐 아니라 모든 의도를 포함하는 JSON 결과 구성을 지원합니다. 따라서 [GitHub #880 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880)도 해결됩니다. [여기](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)서 업데이트된 설명서를 참조하세요.
- **C++/C#/Java**: 음성 도우미 또는 봇이 즉시 수신을 중지합니다. 이제 `DialogServiceConnector`([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector))에는 `ListenOnceAsync()`와 동반하는 `StopListeningAsync()` 메서드가 있습니다. 따라서 오디오 캡처를 즉시 중지하고 결과를 정상적으로 대기하므로 "지금 중지" 단추 누르기 시나리오에 사용하기에 적합합니다.
- **C++/C#/Java/JavaScript**: 음성 도우미 또는 봇이 기본 시스템 오류에 보다 효과적으로 대응합니다. 이제 `DialogServiceConnector`([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector))에는 새로운 `TurnStatusReceived` 이벤트 처리기가 있습니다. 이러한 선택적 이벤트는 봇의 모든 [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) 확인에 해당하며, 턴 실행 실패(예: Direct Line Speech와 봇 간의 처리되지 않은 예외, 시간 제한 또는 네트워크 드롭)를 보고합니다. `TurnStatusReceived`를 사용하면 실패 조건에 보다 쉽게 대응할 수 있습니다. 예를 들어 봇에서 백 엔드 데이터베이스 쿼리(예: 제품 조회)가 너무 오래 걸리는 경우 `TurnStatusReceived`를 통해 "죄송합니다만 이 제품은 잘 모르겠습니다. 다시 시도해 주세요" 또는 이와 비슷한 메시지로 사용자에게 다시 입력을 요청하도록 클라이언트에 알릴 수 있습니다.
- **C++/C#** : 더 많은 플랫폼에서 음성 SDK를 사용합니다. 이제 [Speech SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)는 Windows ARM/ARM64 데스크톱 네이티브 이진 파일을 지원하므로(UWP는 이미 지원됨) 더 많은 종류의 컴퓨터에서 Speech SDK를 보다 유용하게 사용할 수 있습니다.
- **Java**: 이제 [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)에는 이전에 의도치 않게 언어에서 제외된 `setSpeechActivityTemplate()` 메서드가 있습니다. 이 메서드는 `Conversation_Speech_Activity_Template` 속성을 설정하는 것과 동일하며, Direct Line Speech 서비스에서 발생하는 모든 향후 Bot Framework 활동을 제공된 콘텐츠를 해당 JSON 페이로드에 병합하도록 요청합니다.
- **Java**: 낮은 수준의 디버깅이 향상되었습니다. 이제 [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) 클래스에는 다른 프로그래밍 언어(C++, C#)와 비슷하게 `MessageReceived` 이벤트가 있습니다. 이 이벤트는 서비스에서 들어오는 데이터에 대한 하위 수준 액세스를 제공하며 진단 및 디버깅에 유용할 수 있습니다.
- **JavaScript**: [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig)를 통해 음성 도우미와 봇에 맞게 쉽게 설정할 수 있으며, 이제 속성을 수동으로 설정하지 않고 사용자 지정 서비스 위치를 편리하게 사용할 수 있는 `fromHost()` 및 `fromEndpoint()` 팩터리 메서드를 제공합니다. 또한 구성 팩터리에서 기본이 아닌 봇을 사용하도록 선택적 `botId` 사양을 표준화했습니다.
- **JavaScript**: websocket 압축을 위한 문자열 제어 속성을 추가하여 디바이스 성능을 개선했습니다. 성능상의 이유로 websocket 압축은 기본적으로 사용되지 않습니다. 낮은 대역폭 시나리오에서는 다시 사용하도록 설정할 수 있습니다. 자세한 내용은 [여기](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid)에 있습니다. 따라서 [GitHub #242 문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)도 해결됩니다.
- **JavaScript**: 음성 발음을 평가할 수 있도록 발음 평가에 대한 지원이 추가되었습니다. [여기](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)서 빠른 시작을 참조하세요.

**버그 수정**
- **모두**(JavaScript 제외): 인식기가 너무 많은 메모리를 할당하는 1.14 버전의 회귀를 수정했습니다.
- **C++** : `DialogServiceConnector`의 가비지 수집 문제를 해결했으며, 따라서 [GitHub #794 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794)도 해결됩니다.
- **C#** : 삭제 시 개체가 약 1초간 차단되는 스레드 종료 관련 문제를 해결했습니다.
- **C++/C#/Java**: 애플리케이션이 `DialogServiceConnector`에서 음성 권한 부여 토큰 또는 활동 템플릿을 두 번 이상 설정할 수 없게 만드는 예외를 수정했습니다.
- **C++/C#/Java**: 해제의 경합 상태로 인한 인식기 충돌을 수정했습니다.
- **JavaScript**: [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)가 이전에는 `BotFrameworkConfig`의 팩터리에 지정된 선택적 `botId` 매개 변수를 적용하지 않았습니다. 이로 인해 기본이 아닌 봇을 사용하도록 `botId` 쿼리 문자열 매개 변수를 수동으로 설정해야 했습니다. 이 버그가 수정되었으며 새로 추가된 `fromHost()` 및 `fromEndpoint()`를 포함하여 `BotFrameworkConfig`의 팩터리에 제공된 `botId` 값이 적용되고 사용됩니다. 이는 `CustomCommandsConfig`에 대한 `applicationId` 매개 변수에도 적용됩니다.
- **JavaScript**: [GitHub #881 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881)를 수정했습니다. 이제 인식기 개체를 다시 사용할 수 있습니다.
- **JavaScript**: SKD가 한 TTS 세션에서 `speech.config`를 여러 차례 전송하여 대역폭을 낭비하는 문제를 해결했습니다.
- **JavaScript**: 마이크 권한 부여에 대한 오류 처리를 간소화하여 사용자가 브라우저에서 마이크 입력을 허용하지 않을 때 더 많은 설명 메시지를 표시할 수 있게 했습니다.
- **JavaScript**: `ConversationTranslator` 및 `ConversationTranscriber`의 입력 오류가 TypeScript 사용자의 컴파일 오류로 이어지는 [GitHub #249 문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249)를 수정했습니다.
- **Objective-C**: GStreamer 빌드가 Xcode 11.4의 iOS에서 실패하는 문제를 수정했습니다. 따라서 [GitHub #911 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911)도 해결됩니다.
- **Python**: [GitHub #870 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)를 수정하고 "DeprecationWarning: imp 모듈이 사용되지 않는 대신 importlib가 사용됩니다" 메시지를 제거했습니다.

**샘플**
- [JavaScript 브라우저의 파일 기반 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html)은 이제 파일을 음성 인식에 사용합니다. 따라서 [GitHub #884 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884)도 해결됩니다.

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI(SPX라고도 함): 2021년 1월 릴리스

**새로운 기능**
- 이제 Speech CLI는 [NuGet 패키지](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/)로 제공되며 .NET CLI를 통해 셸/명령줄에서 호출할 수 있는 .NET 전역 도구로 설치할 수 있습니다.
- [Custom Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)는 Custom Speech 워크플로에 Speech CLI를 사용하도록 업데이트되었습니다.

**코로나19로 테스트 축소**: 지속되는 전염병으로 인해 엔지니어들이 계속 집에서 근무하게 되면서, 전염병 발생 이전에 비해 수동 확인 단계가 크게 축소되었습니다. 테스트할 수 있는 디바이스와 구성의 수가 줄었기 때문에 환경별 버그를 놓칠 가능성이 높아질 수 있습니다. 저희는 수많은 자동화를 통해 여전히 엄격한 유효성 검사를 수행하고 있습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="text-to-speech-2020-december-release"></a>텍스트 음성 변환 2020년 12월 릴리스

**GA 및 미리 보기로 제공되는 새 인공신경망 음성**

51가지 새 음성이 출시되면서 54개 언어/로캘로 총 129가지 인공신경망 음성이 제공됩니다.

- **GA 로캘의 46가지 새 음성**: `ar-EG` 아랍어(이집트)를 사용하는 Shakir, `ar-SA` 아랍어(사우디아라비아)를 사용하는 Hamed, `bg-BG` 불가리아어(불가리아)를 사용하는 Borislav, `ca-ES` 카탈로니아어(스페인)를 사용하는 Joana, `cs-CZ` 체코어(체코 공화국)를 사용하는 Antonin, `da-DK` 덴마크어(덴마크)를 사용하는 Jeppe, `de-AT` 독일어(오스트리아)를 사용하는 Jonas, `de-CH` 독일어(스위스)를 사용하는 Jan, `el-GR` 그리스어(그리스)를 사용하는 Nestoras, `en-CA` 영어(캐나다)를 사용하는 Liam, `en-IE` 영어(아일랜드)를 사용하는 Connor, `en-IN` 힌디어(인도)를 사용하는 Madhur, `en-IN` 텔구루어(인도)를 사용하는 Mohan, `en-IN` 영어(인도)를 사용하는 Prabhat, `en-IN` 타밀어(인도)를 사용하는 Valluvar, `es-ES` 카탈로니아어(스페인)를 사용하는 Enric, `et-EE` 에스토니아어(에스토니아)를 사용하는 Kert, `fi-FI` 핀란드어(핀란드)를 사용하는 Harri, `fi-FI` 핀란드어(핀란드)를 사용하는 Selma, `fr-CH` 프랑스어(스위스)를 사용하는 Fabrice, `ga-IE` 아일랜드어(아일랜드)를 사용하는 Colm, `he-IL` 히브리어(이스라엘)를 사용하는 Avri, `hr-HR` 크로아티아어(크로아티아어)를 사용하는 Srecko, `hu-HU` 헝가리어(헝가리)를 사용하는 Tamas, `id-ID` 인도네시아어(인도네시아)를 사용하는 Gadis, `lt-LT` 리투아니아어(리투아니아)를 사용하는 Leonas, `lv-LV` 라트비아어(라트비아)를 사용하는 Nils, `ms-MY` 말레이시아어(말레이시아)를 사용하는 Osman, `mt-MT` 몰타어(몰타)를 사용하는 Joseph, `nb-NO` 노르웨이어 복말(노르웨이)을 사용하는 Finn, `nb-NO` 노르웨이어 복말(노르웨이)을 사용하는 Pernille, `nl-NL` 네덜란드어(네덜란드)를 사용하는 Fenna, `nl-NL` 네덜란드어(네덜란드)를 사용하는 Maarten, `pl-PL` 폴란드어(폴란드)를 사용하는 Agnieszka, `pl-PL` 폴란드어(폴란드어)를 사용하는 Marek, `pt-BR` 포르투갈어(브라질)를 사용하는 Duarte, `pt-PT` 포르투갈어(포르투갈)를 사용하는 Raquel, `ro-RO` 루마니아어(루마니아)를 사용하는 Emil, `ru-RU` 러시아어(러시아)를 사용하는 Dmitry, `ru-RU` 러시아어(러시아)를 사용하는 Svetlana, `sk-SK` 슬로바키아어(슬로바키아)를 사용하는 Lukas, `sl-SI` 슬로베니아어(슬로베니아)를 사용하는 Rok, `sv-SE` 스웨덴어(스웨덴)를 사용하는 Mattias, `sv-SE` 스웨덴어(스웨덴)를 사용하는 Sofie, `th-TH` 태국어(태국)를 사용하는 Niwat, `tr-TR` 터키어(터키)를 사용하는 Ahmet, `vi-VN` 베트남어(베트남)를 사용하는 NamMinh, `zh-TW` 대만 북경어(대만)를 사용하는 HsiaoChen, `zh-TW` 대만 북경어(대만)를 사용하는 YunJhe, `zh-HK` 중국어 광둥어(홍콩)를 사용하는 HiuMaan, `zh-HK` 중국어 광둥어(홍콩)를 사용하는 WanLung.

- **미리 보기 로캘의 5가지 새 음성**: `et-EE` 에스토니아어(에스토니아)를 사용하는 Kert, `ga-IE` 아일랜드어(아일랜드)를 사용하는 Colm, `lv-LV` 라트비아어(라트비아)를 사용하는 Nils, `lt-LT` 리투아니아어(리투아니아)를 사용하는 Leonas, `mt-MT` 몰타어(몰타)를 사용하는 Joseph.

이번 릴리스에서는 54개 언어/로캘의 총 129개 인공신경망 음성을 지원합니다. 또한 70개가 넘는 표준 음성이 49개 언어/로캘로 제공됩니다. 전체 목록을 보려면 [언어 지원](language-support.md#text-to-speech)을 방문하세요.

**오디오 콘텐츠 만들기 업데이트**
- 음성 범주 및 자세한 음성 설명을 추가하여 음성 선택 UI를 개선했습니다.
- 여러 언어의 모든 인공신경망 음성에 대해 억양 튜닝을 사용하도록 설정했습니다.
- 브라우저의 언어에 따라 UI 지역화를 자동화했습니다.
- 모든 `zh-CN` 인공신경망 음성에 `StyleDegree` 컨트롤을 사용하도록 설정했습니다.
[오디오 콘텐츠 만들기 도구](https://speech.microsoft.com/audiocontentcreation)를 방문하여 새로운 기능을 확인하세요.

**zh-CN 음성 업데이트**
- 영어 말하기를 지원하도록 모든 `zh-CN` 인공신경망 음성을 업데이트했습니다.
- 억양 조정을 지원하기 위해 모든 `zh-CN` 인공신경망 음성을 사용하도록 설정했습니다. SSML 또는 오디오 콘텐츠 만들기 도구를 사용하여 최적의 억양으로 조정할 수 있습니다.
- `StyleDegree` 컨트롤을 지원하도록 모든 `zh-CN` 다중 스타일 인공신경망 음성을 업데이트했습니다. 감정 강도(부드럽게 또는 강하게)를 조정할 수 있습니다.
- 여러 감정을 수행할 수 있는 여러 스타일을 지원하도록 `zh-CN-YunyeNeural`을 업데이트했습니다.

## <a name="text-to-speech-2020-november-release"></a>텍스트 음성 변환 2020년 11월 릴리스

**미리 보기로 제공되는 새 로캘 및 음성**
- 인공신경망 TTS 포트폴리오에 다음과 같은 **5가지 새 음성 및 언어** 가 도입되었습니다. 몰타어(몰타)를 사용하는 Grace, 리투아니아어(리투아니아)를 사용하는 Ona, 에스토니아어(에스토니아)를 사용하는 Anu, 아일랜드어(아일랜드)를 사용하는 Orla, 라트비아어(라트비아)를 사용하는 Everita.
- **여러 스타일과 역할이 포함된 5가지 새 `zh-CN` 음성 지원**: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan 및 Yunxi를 지원합니다.

> 이러한 음성은 Azure 지역 EastUS, SouthEastAsia 및 WestEurope에서 공개 미리 보기로 제공됩니다.

**인공신경망 TTS 컨테이너 GA**
- 인공신경망 TTS 컨테이너를 사용하면 개발자는 보안 및 데이터 거버넌스 요구 사항에 맞게 자신의 환경에서 가장 자연스러운 디지털 음성이 포함된 음성 합성을 실행할 수 있습니다. [음성 컨테이너를 설치하는 방법](speech-container-howto.md)을 확인하세요.

**새로운 기능**
- **Custom Voice**: 사용자가 한 지역에서 다른 지역으로 음성 모델을 복사할 수 있으며, 엔드포인트 일시 중단 및 다시 시작을 지원합니다. 여기 [포털](https://speech.microsoft.com/customvoice)로 이동하세요.
- [SSML 묵음 태그](speech-synthesis-markup.md#add-silence)를 지원합니다.
- 일반 TTS 음성 품질 향상: nb-NO의 단어 수준 발음 정확도가 향상되었습니다. 발음 오류가 53% 감소했습니다.

> [이 기술 블로그](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)에서 자세히 알아보세요.

## <a name="text-to-speech-2020-october-release"></a>텍스트 음성 변환 2020년 10월 릴리스

**새로운 기능**
- Jenny가 새로운 `newscast` 스타일을 지원합니다. [SSML에서 말하기 스타일을 사용하는 방법](speech-synthesis-markup.md#adjust-speaking-styles)을 참조하세요.
- **인공신경망 음성을 더 높은 오디오 충실도 및 합성 속도를 제공하는 HiFiNet 보코더로 업그레이드**. 비디오 더빙, 오디오 서적 또는 온라인 교육 자료를 비롯한 하이파이 오디오 또는 긴 상호 작용을 사용하는 고객에게 도움이 됩니다. [기술 커뮤니티 블로그에서 자세한 스토리를 읽고 음성 샘플을 들어보세요](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).
- **17개 로캘로 지역화된 [Custom Voice](https://speech.microsoft.com/customvoice) & [오디오 콘텐츠 만들기 스튜디오](https://speech.microsoft.com/audiocontentcreation)** . 사용자는 UI를 현지 언어로 쉽게 전환하여 보다 친숙한 환경을 사용할 수 있습니다.
- **오디오 콘텐츠 만들기**: XiaoxiaoNeural에 대한 스타일 수준 제어를 추가했습니다. 50ms 단위 중단을 포함하도록 사용자 지정된 중단 기능을 구체화했습니다.

**일반 TTS 음성 품질 향상**
- `pl-PL`(오류 발생률 51% 감소) 및 `fi-FI`(오류 발생률 58% 감소)의 단어 수준 발음 정확도를 개선했습니다.
- 사전 시나리오에 대한 `ja-JP` 단일 단어 읽기 기능이 향상되었습니다. 발음 오류가 80% 감소했습니다.
- `zh-CN-XiaoxiaoNeural`: 감정/CustomerService/뉴스/쾌활한/화난 스타일의 음성 품질이 향상되었습니다.
- `zh-CN`: Erhua 발음 및 가벼운 톤을 개선하고 공백 운율을 구체화하여 명확성을 크게 향상했습니다.

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: 2020년 10월 릴리스

> [!NOTE]
> Windows의 Speech SDK는 Visual Studio 2015, 2017 및 2019용 공유 Microsoft Visual C++ 재배포 가능 패키지를 사용합니다. [여기](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)에서 다운로드하세요.

**새로운 기능**
- **Linux**: Debian 10 및 Ubuntu 20.04 LTS 지원이 추가되었습니다.
- **Python/Objective-C**: `KeywordRecognizer` API 지원이 추가되었습니다. 설명서는 [여기](./custom-keyword-basics.md)에 있습니다.
- **C++/Java/C#** : `ServicePropertyChannel::HttpHeader`를 통해 `HttpHeader` 키/값을 설정하는 지원이 추가되었습니다.
- **JavaScript**: `ConversationTranscriber` API 지원이 추가되었습니다. [여기](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 설명서를 참조하세요.
- **C++/C#** : .WAV 파일을 읽을 수 있도록 새 `AudioDataStream FromWavFileInput` 메서드를 추가했습니다. [여기(C++)](/cpp/cognitive-services/speech/audiodatastream) 및 [여기(C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream)를 참조하세요.
-  **C++/C#/Java/Python/Objective-C/Swift**: 텍스트 음성 변환 합성을 중지하는 `stopSpeakingAsync()` 메서드를 추가했습니다. [여기(C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [여기(C#)](/dotnet/api/microsoft.cognitiveservices.speech), [여기(Java)](/java/api/com.microsoft.cognitiveservices.speech), [여기(Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech) 및 [여기(Objective-C/Swift)](/objectivec/cognitive-services/speech/)서 참조 설명서를 확인하세요.
- **C#, C++, Java**: `DialogServiceConnector`에 대한 연결 및 연결 끊기 이벤트를 모니터링하는 데 사용할 수 있는 `FromDialogServiceConnector()` 함수를 `Connection` 클래스에 추가했습니다. [여기(C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [여기(C++)](/cpp/cognitive-services/speech/connection) 및 [여기(Java)](/java/api/com.microsoft.cognitiveservices.speech.connection)서 참조 설명서를 확인하세요.
- **C++/C#/Java/Python/Objective-C/Swift**: 음성 발음을 평가하고 음성 오디오의 정확도와 능숙도에 대한 화자 피드백을 제공하는 발음 평가 지원이 추가되었습니다. [여기](how-to-pronunciation-assessment.md) 설명서를 참조하세요.

**주요 변경 내용**
- **JavaScript**: PullAudioOutputStream.read()의 반환 형식이 내부 프라미스에서 네이티브 JavaScript 프라미스로 변경되었습니다.

**버그 수정**
- **모두**: `SetServiceProperty`에서 특정 특수 문자가 포함된 값을 무시하는 1.13 회귀를 수정했습니다.
- **C#** : Visual Studio 2019에서 네이티브 DLL을 찾지 못하는 Windows 콘솔 샘플을 수정했습니다.
- **C#** : 스트림을 `KeywordRecognizer` 입력으로 사용하는 경우 메모리 관리와 충돌하는 문제를 수정했습니다.
- **ObjectiveC/Swift**: 스트림이 인식기 입력으로 사용되는 경우 메모리 관리와 충돌하는 문제를 수정했습니다.
- **Windows**: UWP에서 BT HFP/A2DP가 동시에 존재하는 문제를 수정했습니다.
- **JavaScript**: 로깅을 개선하고 내부 디버그/서비스 상관 관계를 도와주도록 세션 ID 매핑을 수정했습니다.
- **JavaScript**: 첫 번째 호출을 수행한 후 `ListenOnce` 호출을 사용하지 않도록 설정하는 `DialogServiceConnector`에 대한 픽스를 추가했습니다.
- **JavaScript**: "단순" 결과만 출력되는 문제를 수정했습니다.
- **JavaScript**: macOS의 Safari에서 발생하는 지속적인 인식 문제를 수정했습니다.
- **JavaScript**: 요청 처리량이 높은 시나리오의 CPU 부하를 완화했습니다.
- **JavaScript**: 음성 프로필 등록 결과의 세부 정보에 대한 액세스를 허용합니다.
- **JavaScript**: `IntentRecognizer`에서 지속적인 인식을 위한 픽스가 추가되었습니다.
- **C++/C#/Java/Python/Swift/ObjectiveC**: `IntentRecognizer`에서 australiaeast 및 brazilsouth에 대한 잘못된 url을 수정했습니다.
- **C++/C#** : `VoiceProfile` 개체를 만들 때 `VoiceProfileType`을 인수로 추가했습니다.
- **C++/C#/Java/Python/Swift/ObjectiveC**: 지정된 위치에서 `AudioDataStream`을 읽으려고 할 때 발생할 수 있는 `SPX_INVALID_ARG`를 수정했습니다.
- **IOS**: Unity에서 음성 인식과 충돌하는 문제를 수정했습니다.

**샘플**
- **ObjectiveC**: [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)에 키워드 인식 샘플이 추가되었습니다.
- **C#/JavaScript**: [여기(C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) 및 [여기(JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)에 대화 기록에 대한 빠른 시작이 추가되었습니다.
- **C++/C#/Java/Python/Swift/ObjectiveC**: [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)에 발음 평가 샘플이 추가되었습니다.
- **Xamarin**: [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)에 최신 Visual Studio 템플릿에 대한 빠른 시작이 업데이트되었습니다.

**알려진 문제**
- DigiCert Global Root G2 인증서는 기본적으로 HoloLens 2 및 Android 4.4(KitKat)에서 지원되지 않으며, 시스템에 추가해야만 Speech SDK가 작동합니다. 이 인증서는 곧 HoloLens 2 OS 이미지에 추가됩니다. Android 4.4 고객은 업데이트된 인증서를 시스템에 추가해야 합니다.

**코로나19로 테스트 축소:** 지난 몇 주 동안 저희는 원격으로 작업해야 했기 때문에 평소처럼 수동 확인 테스트를 많이 수행할 수 없었습니다. 저희는 문제가 될 수 있는 어떤 것도 변경하지 않았으며, 자동화된 테스트는 모두 통과했습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI(SPX라고도 함): 2020년 10월 릴리스
SPX는 코드를 작성하지 않고 Azure Speech Service를 사용하는 명령줄 인터페이스입니다.
[여기](./spx-basics.md)서 최신 버전을 다운로드하세요. <br>

**새로운 기능**
- `spx csr dataset upload --kind audio|language|acoustic` – URL뿐만 아니라 로컬 데이터에서 데이터 세트를 만듭니다.
- `spx csr evaluation create|status|list|update|delete` – 새 모델을 기준 진위/기타 모델과 비교합니다.
- `spx * list` – 페이지가 아닌 환경을 지원합니다(--top X --skip X가 필요 없음).
- `spx * --http header A=B` – 사용자 지정 헤더(사용자 지정 인증을 위해 Office에 추가됨)를 지원합니다.
- `spx help` – 텍스트 및 역따옴표 텍스트 색상 구분(파란색)이 향상되었습니다.

## <a name="text-to-speech-2020-september-release"></a>텍스트 음성 변환 2020년 9월 릴리스

### <a name="new-features"></a>새로운 기능

* **인공신경망 TTS**
    * **18가지 새 언어/로캘을 지원하도록 확장되었습니다.** 18가지 언어는 불가리아어, 체코어, 독일어(오스트리아), 독일어(스위스), 그리스어, 영어(아일랜드), 프랑스어(스위스), 히브리어, 크로아티아어, 헝가리어, 인도네시아어, 말레이어, 루마니아어, 슬로바키아어, 슬로베니아어, 타밀어, 텔루구어 및 베트남어입니다.
    * **기존 언어를 더욱 풍성하게 만드는 14가지 새 음성이 출시되었습니다.** [전체 언어 및 음성 목록](language-support.md#neural-voices)을 참조하세요.
    * **`en-US` 및 `zh-CN` 음성에 대한 새로운 말하기 스타일이 추가되었습니다.** 영어(미국)를 사용하는 새 음성 Jenny는 챗봇, 고객 서비스 및 보조자 스타일을 지원합니다. zh-CN 음성 XiaoXiao는 10가지 새로운 말하기 스타일을 제공합니다. 또한 XiaoXiao 인공신경망은 `StyleDegree` 튜닝정을 지원합니다. [SSML에서 말하기 스타일을 사용하는 방법](speech-synthesis-markup.md#adjust-speaking-styles)을 참조하세요.

* **컨테이너: 인공신경망 TTS 컨테이너가 공개 미리 보기로 출시되었으며 16개 음성을 14개 언어로 제공합니다.** [인공신경망 TTS용 음성 컨테이너를 배포하는 방법](speech-container-howto.md)에 대해 자세히 알아보세요.

[Ignite 2020에 대한 TTS 업데이트 전체 공지](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544)를 읽어보세요.

## <a name="text-to-speech-2020-august-release"></a>텍스트 음성 변환 2020년 8월 릴리스

### <a name="new-features"></a>새로운 기능

* **인공신경망 TTS: `en-US` Aria 음성의 새로운 말하기 스타일이 출시되었습니다**. AriaNeural은 뉴스를 읽을 때 뉴스 캐스터처럼 들릴 수 있습니다. '뉴스캐스트-격식' 스타일은 진지한 말투이고, '뉴스캐스트-캐주얼' 스타일은 편하고 격식을 덜 차리는 말투입니다. [SSML에서 말하기 스타일을 사용하는 방법](speech-synthesis-markup.md)을 참조하세요.

* **Custom Voice: 학습 데이터 품질을 자동으로 확인하는 새 기능이 출시되었습니다**. 데이터를 업로드하면 시스템에서는 오디오 및 음성 텍스트 데이터의 다양한 측면을 검사하고, 자동으로 문제를 수정하거나 필터링하여 음성 모델의 품질을 향상합니다. 여기에는 오디오 및 스크립트 형식 외에도 오디오 볼륨, 노이즈 수준, 음성의 발음 정확도, 표준화된 텍스트와 음성 맞춤, 오디오의 무음 등이 포함됩니다.

* **오디오 콘텐츠 만들기: 보다 강력한 음성 튜닝 및 오디오 관리 기능을 제공하는 새 기능 세트입니다**.

    * 발음: 발음 튜닝 기능이 최신 음소 세트로 업데이트되었습니다. 라이브러리에서 올바른 음소 요소를 선택하고, 선택한 단어의 발음을 구체화할 수 있습니다.

    * 다운로드: 단락에서 오디오 생성을 지원하도록 오디오 "다운로드"/"내보내기" 기능이 향상되었습니다. 여러 오디오 출력을 생성하는 동안 동일한 파일/SSML에서 콘텐츠를 편집할 수 있습니다. "다운로드"의 파일 구조도 구체화되었습니다. 이제 한 폴더의 모든 오디오 파일을 쉽게 가져올 수 있습니다.

    * 작업 상태: 다중 파일 내보내기 환경이 개선되었습니다. 이전에는 여러 파일을 내보낼 때 파일 중 하나가 실패하면 전체 작업이 실패했습니다. 하지만 이제는 나머지 파일이 성공적으로 내보내집니다. 작업 보고서가 보다 상세하고 체계적인 정보로 보강되었습니다. 이제 실패한 모든 파일과 문장의 로그를 보고서에서 확인할 수 있습니다.

    * SSML 설명서: 모든 튜닝 기능을 사용하는 방법에 대한 규칙을 확인할 수 있는 SSML 문서에 연결되었습니다.

* **사용자에게 친숙한 표시 이름과 인공신경망 음성을 지원하는 말하는 스타일을 포함하도록 Voice List API가 업데이트되었습니다**.

### <a name="general-tts-voice-quality-improvements"></a>일반 TTS 음성 품질 향상

* `ru-RU`(오류 56% 감소) 및 `sv-SE`(오류 49% 감소)의 단어 수준 발음 오류가 감소했습니다.

* `en-US` 인공신경망 음성의 다성음악 단어 읽기가 40% 향상되었습니다. 다성음악 단어의 예로는 "read", "live", "content", "record", "object" 등이 있습니다.

* `fr-FR`의 질문 톤이 더 자연스럽게 향상되었습니다. MOS(Mean Opinion Score) +0.28을 획득했습니다.

* 다음 음성의 보코더가 업데이트되었습니다. 충실도가 향상되고 전반적인 성능 속도가 40% 빨라졌습니다.

    | Locale | 음성 |
    |---|---|
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | 선희 |

### <a name="bug-fixes"></a>버그 수정

* 오디오 콘텐츠 만들기 도구의 여러 버그 수정
    * 자동 새로 고침 문제를 수정했습니다.
    * 동남 아시아 지역 zh-CN의 음성 스타일 문제를 수정했습니다.
    * '중단' 태그가 있는 내보내기 오류와 구두점 오류를 포함하여 안정성 문제를 수정했습니다.

## <a name="new-speech-to-text-locales-2020-august-release"></a>새 음성 텍스트 변환 로캘: 2020년 8월 릴리스
8월에 새로운 음성 텍스트 변환 로캘 26개가 출시되었습니다. 2개는 유럽 언어 `cs-CZ` 및 `hu-HU`, 5개는 영어 로캘, 19개는 대부분의 남아메리카 국가를 포함하는 스페인어 로캘입니다. 아래는 새 로캘의 목록입니다. 전체 언어 목록은 [여기](./language-support.md)서 확인하세요.

| Locale  | 언어                          |
|---------|-----------------------------------|
| `cs-CZ` | 체코어(체코)            |
| `en-HK` | 영어(홍콩)               |
| `en-IE` | 영어(아일랜드)                 |
| `en-PH` | 영어(필리핀)             |
| `en-SG` | 영어(싱가포르)               |
| `en-ZA` | 영어(남아프리카 공화국)            |
| `es-AR` | 스페인어(아르헨티나)               |
| `es-BO` | 스페인어(볼리비아)                 |
| `es-CL` | 스페인어(칠레)                   |
| `es-CO` | 스페인어(콜롬비아)                |
| `es-CR` | 스페인어(코스타리카)              |
| `es-CU` | 스페인어(쿠바)                    |
| `es-DO` | 스페인어(도미니카 공화국)      |
| `es-EC` | 스페인어(에콰도르)                 |
| `es-GT` | 스페인어(과테말라)               |
| `es-HN` | 스페인어(온두라스)                |
| `es-NI` | 스페인어(니카라과)               |
| `es-PA` | 스페인어(파나마)                  |
| `es-PE` | 스페인어(페루)                    |
| `es-PR` | 스페인어(푸에르토리코)             |
| `es-PY` | 스페인어(파라과이)                |
| `es-SV` | 스페인어(엘살바도르)             |
| `es-US` | 스페인어(미국)                     |
| `es-UY` | 스페인어(우루과이)                 |
| `es-VE` | 스페인어(베네수엘라)               |
| `hu-HU` | 헝가리어(헝가리)               |


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: 2020년 7월 릴리스

> [!NOTE]
> Windows의 Speech SDK는 Visual Studio 2015, 2017 및 2019용 공유 Microsoft Visual C++ 재배포 가능 패키지를 사용합니다. [여기](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)에서 다운로드하여 설치합니다.

**새로운 기능**
- **C#** : 비동기 대화 기록을 위한 지원이 추가되었습니다. [여기](./how-to-async-conversation-transcription.md) 설명서를 참조하세요.
- **JavaScript**: [브라우저](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) 및 [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)에 대한 Speaker Recognition 지원이 추가되었습니다.
- **JavaScript**: 자동 언어 식별/언어 ID에 대한 지원이 추가되었습니다. [여기](./how-to-automatic-language-detection.md?pivots=programming-language-javascript) 설명서를 참조하세요.
- **Objective-C**: [다중 디바이스 대화](./multi-device-conversation.md) 및 [대화 기록](./conversation-transcription.md)에 대한 지원이 추가되었습니다.
- **Python**: Windows 및 Linux의 Python에 대한 압축 오디오 지원이 추가되었습니다. [여기](./how-to-use-codec-compressed-audio-input-streams.md) 설명서를 참조하세요.

**버그 수정**
- **모두**: 인식 후 KeywordRecognizer가 스트림을 앞으로 이동하지 않는 문제를 수정했습니다.
- **모두**: KeywordRecognitionResult에서 얻은 스트림에 키워드가 포함되지 않는 문제를 수정했습니다.
- **모두**: 사용자가 대기를 완료한 후 SendMessageAsync가 네트워크를 통해 메시지를 실제로 전송하지 않는 문제를 수정했습니다.
- **모두**: 사용자가 VoiceProfileClient::SpeakerRecEnrollProfileAsync 메서드를 여러 번 호출하고 호출이 완료될 때까지 기다리지 않을 때 Speaker Recognition API에서 발생하는 충돌을 수정했습니다.
- **모두**: VoiceProfileClient 및 SpeakerRecognizer 클래스의 파일 로깅 사용을 수정했습니다.
- **JavaScript**: 브라우저가 최소화되었을 발생하는 제한 [문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74)를 수정했습니다.
- **JavaScript**: 스트림에서 발생하는 메모리 누수 [문제](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78)를 수정했습니다.
- **JavaScript**: NodeJS의 OCSP 응답에 대한 캐싱을 추가했습니다.
- **Java**: BigInteger 필드에서 항상 0을 반환하는 문제를 수정했습니다.
- **iOS**: iOS App Store에서 Speech SDK 기반 앱을 게시할 때 발생하는 [문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702)를 수정했습니다.

**샘플**
- **C++** : [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)에 Speaker Recognition 샘플 코드를 추가했습니다.

**코로나19로 테스트 축소:** 지난 몇 주 동안 저희는 원격으로 작업해야 했기 때문에 평소처럼 수동 확인 테스트를 많이 수행할 수 없었습니다. 저희는 문제가 될 수 있는 어떤 것도 변경하지 않았으며, 자동화된 테스트는 모두 통과했습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="text-to-speech-2020-july-release"></a>텍스트 음성 변환 2020년 7월 릴리스

### <a name="new-features"></a>새로운 기능

* **인공신경망 TTS, 15가지 새 인공신경망 음성**: `ar-EG` 아랍어(이집트)를 사용하는 Salma, `ar-SA` 아랍어(사우디아라비아)를 사용하는 Zariyah, `ca-ES` 카탈로니아어(스페인)를 사용하는 Alba, `da-DK` 덴마크어(덴마크)를 사용하는 Christel, `es-IN` 영어(인도)를 사용하는 Neerja, `fi-FI` 핀란드어(핀란드)를 사용하는 Noora, `hi-IN` 힌디어(인도)를 사용하는, Swara, `nl-NL` 네덜란드어(네덜란드)를 사용하는 Colette, `pl-PL` 폴란드어(폴란드)를 사용하는 Zofia, `pt-PT` 포르투갈어(포르투갈)를 사용하는 Fernanda, `ru-RU` 러시아어(러시아)를 사용하는 Dariya, `sv-SE` 스웨덴어(스웨덴)를 사용하는 Hillevi, `th-TH` 태국어(태국)를 사용하는 Achara, `zh-HK` 중국어(광둥어, 번체)를 사용하는 HiuGaai, `zh-TW` 중국어(대만어)를 사용하는 HsiaoYu 음성이 인공신경망 TTS 포트폴리오에 추가되었습니다. [지원되는 모든 언어](./language-support.md#neural-voices)를 확인하세요.

* **Custom Voice, 사용자 환경을 단순화하기 위해 학습 흐름을 사용하여 음성 테스트 간소화**: 새로운 테스트 기능이 도입되면서, 일반 시나리오 및 음성 도우미 시나리오를 다루는 각 언어에 맞게 최적화된 미리 정의된 테스트 세트를 사용하여 각 음성을 자동으로 테스트합니다. 이러한 테스트 세트는 언어의 일반적인 사용 사례와 음소를 포함하도록 신중하게 선택되어 테스트를 거쳤습니다. 뿐만 아니라 사용자는 여전히 모델을 학습시킬 때 자체 테스트 스크립트를 업로드하도록 선택할 수 있습니다.

* **오디오 콘텐츠 만들기: 보다 강력한 음성 튜닝 및 오디오 관리 기능을 제공하는 새 기능 세트가 출시되었습니다**.

    * `Pitch`, `rate` 및 `volume`은 느리게, 보통, 빠르게 등과 같이 미리 정의된 값을 사용한 튜닝을 지원하도록 향상되었습니다. 이제 사용자는 오디오 편집을 위한 '상수' 값을 쉽게 선택할 수 있습니다.

    ![오디오 튜닝](media/release-notes/audio-tuning.png)

    * 이제 사용자는 작업 파일에 대한 `Audio history`를 검토할 수 있습니다. 이 기능을 사용하면 사용자는 작업 파일과 관련하여 생성된 모든 오디오를 쉽게 추적할 수 있습니다. 사용자는 기록 버전을 확인하고 품질을 비교하는 동시에 튜닝할 수 있습니다.

    ![오디오 기록](media/release-notes/audio-history.png)

    * `Clear` 기능이 더욱 유연해졌습니다. 사용자는 선택한 콘텐츠에 사용할 수 있는 다른 매개 변수를 유지하면서 특정 튜닝 매개 변수를 지울 수 있습니다.

    * 사용자가 TTS 음성 튜닝 및 오디오 관리를 신속하게 시작하는 데 도움이 되는 자습서 비디오가 [방문 페이지](https://speech.microsoft.com/audiocontentcreation)에 추가되었습니다.

### <a name="general-tts-voice-quality-improvements"></a>일반 TTS 음성 품질 향상

* 충실도를 높이고 대기 시간을 줄이도록 TTS 보코더가 향상되었습니다.

    * `it-IT`의 Elsa는 음성 품질에서 +0.464 CMOS(Comparative Mean Opinion Score)를 획득하고 합성 시간이 40% 빨라지고 첫 번째 바이트 대기 시간이 30% 감소한 새로운 보코더로 업데이트되었습니다.
    * `zh-CN`의 Xiaoxiao는 일반 도메인에서 +0148 CMOS, 뉴스캐스트 스타일에서 +0.348, 서정적 스타일에서 +0.195를 획득한 새로운 보코더로 업데이트되었습니다.

* TTS 출력이 보다 자연스럽게 보이도록 `de-DE` 및 `ja-JP` 음성 모델이 업데이트되었습니다.

    * `de-DE`의 Katja는 최신 운율 모델링 방법으로 업데이트되었으며 MOS(Mean Opinion Score)에서 +0.13을 획득했습니다.
    * `ja-JP`의 Nanami는 새로운 고저 악센트 운율 모델로 업데이트되었으며 MOS(Mean Opinion Score)에서 +0.19를 획득했습니다.

* 5개 언어의 단어 수준 발음 정확도가 향상되었습니다.

    | 언어 | 발음 오류 감소 |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>버그 수정

* 통화 읽기
    * `es-ES` 및 `es-MX`의 통화 읽기와 관련된 문제를 수정했습니다.

    | 언어 | 입력 | 개선 후 읽기 |
    |---|---|---|
    | `es-MX` | $1.58 | un peso cincuenta y ocho centavos |
    | `es-ES` | $1.58 | un dólar cincuenta y ocho centavos |

    * ,,,,, 로캘에서 음수 통화 (예: "-325 &euro; ")를 지원 `en-US` `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` 합니다.

* `pt-PT`의 주소 읽기가 개선되었습니다.
* Natasha(`en-AU`)와 Libby(`en-UK`)의 "for" 및 "four" 발음 문제를 수정했습니다.
* 오디오 콘텐츠 만들기 도구의 버그 수정
    * 두 번째 단락 뒤의 추가 일시 중지 및 예기치 않은 일시 중지가 수정되었습니다.
    * 회귀 버그의 '중단 없음' 기능이 다시 추가되었습니다.
    * Speech Studio의 무작위 새로 고침 문제가 수정되었습니다.

### <a name="samplessdk"></a>샘플/SDK

* JavaScript: Firefox와 macOS 및 iOS의 Safari에서 발생하는 재생 문제를 수정했습니다.

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: 2020년 6월 릴리스
**Speech CLI(SPX 라고도 함)**
-   CLI 내 도움말 검색 기능이 추가되었습니다.
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   새로 배포된 v3.0 Batch 및 Custom Speech API와 함께 작동하도록 업데이트되었습니다.
    -   `spx help batch examples`
    -   `spx help csr examples`

**새로운 기능**
-   **C\#, C++** : Speaker Recognition 미리 보기: 이 기능을 사용하면 화자 식별(누가 말하고 있나요?) 및 화자 검증(화자가 주장하는 사람이 맞나요?)이 가능합니다. [개요](./speaker-recognition-overview.md)부터 시작하고, [Speaker Recognition 기본 문서](./get-started-speaker-recognition.md) 또는 [API 참조 문서](/rest/api/speakerrecognition/)를 읽어보세요.

**버그 수정**
-   **C\#, C++** : 1.12 화자 인식에서 마이크 녹음이 작동하지 않는 문제를 수정했습니다.
-   **JavaScript**: Firefox와 macOS 및 iOS의 Safari에서 텍스트 음성 변환을 수정했습니다.
-   8채널 스트림을 사용할 때 대화 기록에서 발생하는 Windows 애플리케이션 검증 도구 액세스 위반 충돌을 수정했습니다.
-   다중 디바이스 대화 기록에서 발생하는 Windows 애플리케이션 검증 도구 액세스 위반 충돌을 수정했습니다.

**샘플**
-   **C#** : 화자 인식을 위한 [코드 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition)입니다.
-   **C++** : 화자 인식을 위한 [코드 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition)입니다.
-   **Java**: Android에서 의도 인식을 위한 [코드 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition)입니다. 

**코로나19로 테스트 축소:** 지난 몇 주 동안 저희는 원격으로 작업해야 했기 때문에 평소처럼 수동 확인 테스트를 많이 수행할 수 없었습니다. 저희는 문제가 될 수 있는 어떤 것도 변경하지 않았으며, 자동화된 테스트는 모두 통과했습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: 2020년 5월 릴리스
**Speech CLI(SPX 라고도 함)**
- **SPX** 는 명령줄에서 인식, 합성, 번역, 일괄 처리 대화 내용 기록 및 사용자 지정 음성 관리를 수행할 수 있는 새로운 명령줄 도구입니다. 이 도구를 사용하여 Speech Service를 테스트하거나 수행해야 하는 Speech Service 작업을 스크립팅할 수 있습니다. 이 도구를 다운로드하고 [여기](./spx-overview.md)서 설명서를 읽어보세요.

**새로운 기능**
- **Go**: [음성 인식](./get-started-speech-to-text.md?pivots=programming-language-go) 및 [사용자 지정 음성 도우미](./quickstarts/voice-assistants.md?pivots=programming-language-go)에 대한 새로운 go 언어 지원이 추가되었습니다. [여기](./quickstarts/setup-platform.md?pivots=programming-language-go)서 개발 환경을 설정하세요. 샘플 코드는 아래의 샘플 섹션을 참조하세요.
- **JavaScript**: 텍스트 음성 변환에 대한 브라우저 지원이 추가되었습니다. [여기](./get-started-text-to-speech.md?pivots=programming-language-JavaScript) 설명서를 참조하세요.
- **C++, C#, Java**: Windows, Android, Linux 및 iOS 플랫폼에서 새로운 `KeywordRecognizer` 개체와 API가 지원됩니다. [여기](./keyword-recognition-overview.md) 설명서를 참조하세요. 샘플 코드는 아래의 샘플 섹션을 참조하세요.
- **Java**: 번역을 지원하는 다중 디바이스 대화를 추가했습니다. [여기](/java/api/com.microsoft.cognitiveservices.speech.transcription)서 참조 문서를 확인하세요.

**개선 및 최적화**
- **JavaScript**: 브라우저 마이크 구현을 최적화하여 음성 인식 정확도를 높였습니다.
- **Java**: SWIG 없이 다이렉트 JNI 구현을 사용하여 바인딩을 리팩터링했습니다. 이번 변화로 Windows, Android, Linux 및 Mac에 사용되는 모든 Java 패키지의 바인딩 크기가 10분의 1로 축소되었으며 Speech SDK Java 구현을 더 쉽게 개발할 수 있게 되었습니다.
- **Linux**: 지원 [설명서](./speech-sdk.md?tabs=linux)에 최신 RHEL 7 관련 정보가 업데이트되었습니다.
- 서비스 및 네트워크 오류가 발생할 때 연결을 여러 번 시도하도록 연결 논리가 개선되었습니다.
- 개발자가 Azure Speech 여정의 다음 단계를 수행하는 데 도움이 되도록 [portal.azure.com](https://portal.azure.com) 음성 빠른 시작 페이지가 업데이트되었습니다.

**버그 수정**
- **C#, Java**: Linux ARM(32비트 및 64비트 모두)에 SDK 라이브러리를 로드할 때 발생하는 [문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587)를 수정했습니다.
- **C#** : TranslationRecognizer, IntentRecognizer 및 Connection 개체에 대한 네이티브 핸들의 명시적 삭제를 수정했습니다.
- **C#** : ConversationTranscriber 개체의 오디오 입력 수명 관리를 수정했습니다.
- 간단한 구의 의도를 인식할 때 `IntentRecognizer` 결과 이유가 올바르게 설정되지 않은 문제를 수정했습니다.
- `SpeechRecognitionEventArgs`결과 오프셋이 올바르게 설정되지 않은 문제를 수정했습니다.
- websocket 연결을 열기 전에 SDK가 네트워크 메시지를 보내려고 시도하는 경합 상태를 수정했습니다. 참가자를 추가하는 동안 `TranslationRecognizer`에 대해 재현할 수 있습니다.
- 키워드 인식기 엔진의 메모리 누수 문제를 수정했습니다.

**샘플**
- **Go**: [음성 인식](./get-started-speech-to-text.md?pivots=programming-language-go) 및 [사용자 지정 음성 도우미](./quickstarts/voice-assistants.md?pivots=programming-language-go)에 대한 빠른 시작을 추가했습니다. 샘플 코드는 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)서 찾을 수 있습니다.
- **JavaScript**: [텍스트 음성 변환](./get-started-text-to-speech.md?pivots=programming-language-javascript), [번역](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script) 및 [의도 인식](./get-started-intent-recognition.md?pivots=programming-language-javascript)에 대한 빠른 시작을 추가했습니다.
- [C\#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) 및 [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)(Android)에 대한 키워드 인식 샘플이 추가되었습니다.  

**코로나19로 테스트 축소:** 지난 몇 주 동안 저희는 원격으로 작업해야 했기 때문에 평소처럼 수동 확인 테스트를 많이 수행할 수 없었습니다. 저희는 문제가 될 수 있는 어떤 것도 변경하지 않았으며, 자동화된 테스트는 모두 통과했습니다. 저희가 놓친 부분이 있다면 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
건강에 유의하세요!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: 2020년 3월 릴리스
**새로운 기능**
- Linux: Speech SDK용 시스템을 구성하는 방법에 대한 [지침](./how-to-configure-rhel-centos-7.md)과 함께 RHEL(Red Hat Enterprise Linux)/CentOS 7 x64에 대한 지원이 추가되었습니다.
- Linux: Linux ARM32 및 ARM64의 .NET Core C#에 대한 지원이 추가되었습니다. 자세한 내용은 [여기](./speech-sdk.md?tabs=linux)를 읽어보세요.
- C#, C++: 모든 중간 및 최종 음성 인식 결과에서 일관적인 ID인 `UtteranceId`를 `ConversationTranscriptionResult`에 추가했습니다. [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult) 및 [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)에 대한 세부 정보를 제공합니다.
- Python: `Language ID`에 대한 지원이 추가되었습니다. [GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)의 speech_sample.py를 참조하세요.
- Windows: 모든 win32 콘솔 애플리케이션에 사용되는 Windows 플랫폼에서 압축된 오디오 입력 형식 지원을 추가했습니다. 자세한 내용은 [여기](./how-to-use-codec-compressed-audio-input-streams.md)를 참조하세요.
- JavaScript: NodeJS에서 음성 합성(텍스트 음성 변환)을 지원합니다. [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)를 참조하세요.
- JavaScript: 모든 송신 및 수신 메시지를 검사할 수 있도록 새 API를 추가합니다. [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)를 참조하세요.

**버그 수정**
- C#, C++: 이제 `SendMessageAsync`에서 이진 메시지를 이진 형식으로 보내도록 문제를 수정했습니다. [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) 및 [C++](/cpp/cognitive-services/speech/connection)에 대한 세부 정보를 제공합니다.
- C#, C++: `Connection` 개체보다 `Recognizer` 개체가 먼저 삭제된 경우 `Connection MessageReceived` 이벤트를 사용하면 충돌이 발생할 수 있는 문제를 수정했습니다. [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived) 및 [C++](/cpp/cognitive-services/speech/connection#messagereceived)에 대한 세부 정보를 제공합니다.
- Android: 대기 시간을 개선하기 위해 마이크의 오디오 버퍼 크기를 800ms에서 100ms로 줄였습니다.
- Android: Android Studio의 x86 Android 에뮬레이터 관련 [문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563)를 수정했습니다.
- JavaScript: `fromSubscription` API를 통해 중국 내 Azure 지역에 대한 지원을 추가했습니다. 자세한 내용은 [여기](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-)를 참조하세요.
- JavaScript: NodeJS의 연결 실패에 대한 오류 정보를 추가했습니다.

**샘플**
- Unity: LUIS json 가져오기가 실패하는 의도 인식 공개 샘플을 수정했습니다. 자세한 내용은 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)를 참조하세요.
- Python: `Language ID` 샘플이 추가되었습니다. 자세한 내용은 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)를 참조하세요.

**코로나19로 테스트 축소:** 지난 몇 주 동안 저희는 원격으로 작업해야 했기 때문에 평소처럼 수동 디바이스 확인 테스트를 많이 수행할 수 없었습니다. 예를 들어 Linux, iOS 및 macOS에서 마이크 입력과 스피커 출력을 테스트할 수 없었습니다. 저희는 문제가 될 수 있는 어떤 것도 변경하지 않았으며, 자동화된 테스트는 모두 통과했습니다. 저희가 놓친 부분이 있다면[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)에서 알려주세요.<br>
여러분의 지속적인 지원에 감사드립니다. 언제든지 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) 또는 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731)에 질문이나 피드백을 게시해 주세요.<br>
건강에 유의하세요!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: 2020년 2월 릴리스

**새로운 기능**

 - Python의 새로운 3.8 릴리스를 지원하는 Python 패키지를 추가했습니다.
 - RHEL(Red Hat Enterprise Linux)/CentOS 8 x64 지원(C++, C#, Java, Python)이 추가되었습니다.
   > [!NOTE]
   > 고객은 [이러한 지침](./how-to-configure-openssl-linux.md)에 따라 OpenSSL을 구성해야 합니다.
 - Debian 및 Ubuntu에 대해 Linux ARM32를 지원합니다.
 - DialogServiceConnector는 이제 BotFrameworkConfig에서 선택적 "bot ID" 매개 변수를 지원합니다. 이 매개 변수를 사용하면 Azure 음성 리소스 하나로 여러 Direct Line Speech 봇을 사용할 수 있습니다. 이 매개 변수를 지정하지 않으면 기본 봇(Direct Line Speech 채널 구성 페이지에서 결정)이 사용됩니다.
 - DialogServiceConnector에는 이제 SpeechActivityTemplate 속성이 있습니다. 이 JSON 문자열의 내용은 Direct Line Speech 봇에 도달하는 모든 활동에서 지원되는 다양한 지원 필드를 미리 채우기 위해 Direct Line Speech에서 사용하며, 여기에는 음성 인식과 같은 이벤트에 대응하여 자동으로 생성되는 활동이 포함됩니다.
 - 이제 TTS는 인증에 구독 키를 사용하여 신시사이저를 만든 후 첫 번째 합성 결과의 첫 번째 바이트 대기 시간을 줄입니다.
 - 19개 로캘(es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR)의 음성 인식 모델을 업데이트하여 단어 오류율을 평균 18.6% 줄였습니다. 새 모델은 받아쓰기, 콜센터 대화 내용 기록 및 비디오 인덱싱 시나리오를 비롯하여 여러 도메인을 대폭 개선합니다.

**버그 수정**

 - JAVA API에서 Conversation Transcriber가 올바르게 대기하지 않는 버그가 수정되었습니다.
 - Xamarin [GitHub 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)에 대한 Android x86 에뮬레이터 픽스가 추가되었습니다.
 - AudioConfig에 누락된 (Get|Set)Property 메서드를 추가했습니다.
 - 연결이 실패할 때 audioDataStream를 중지할 수 없는 TTS 버그를 수정했습니다.
 - 지역이 없는 엔드포인트를 사용하면 대화 번역기에 대한 USP 오류가 발생합니다.
 - 이제 유니버설 Windows 애플리케이션에서 ID를 생성할 때 적절한 고유 GUID 알고리즘이 사용됩니다. 이전에는 의도치 않게 대규모 상호 작용에서 자주 충돌을 일으키는 스텁 구현을 기본적으로 사용했습니다.

 **샘플**

 - [Unity 마이크 및 푸시 모드 스트리밍](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)에 Speech SDK를 사용하는 방법에 대한 Unity 샘플

**기타 변경 내용**

 - [Linux용 OpenSSL 구성 설명서 업데이트](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: 2020년 1월 릴리스

**새 기능**

- 다중 디바이스 대화: 동일한 음성 또는 텍스트 기반 대화에 여러 디바이스를 연결하고, 필요에 따라 디바이스 간에 전송되는 메시지를 번역합니다. [이 문서](multi-device-conversation.md)에서 자세히 알아보세요.
- Android .aar 패키지에 대한 키워드 인식 지원이 추가되었으며 x86 및 x64 기능에 대한 지원이 추가되었습니다.
- Objective-C: `SendMessage` 및 `SetMessageProperty` 메서드가 `Connection` 개체에 추가되었습니다. [여기](/objectivec/cognitive-services/speech/spxconnection) 설명서를 참조하세요.
- TTS C++ API는 이제 `std::wstring`을 합성 텍스트 입력으로 지원하므로 wstring을 SDK로 전달하기 전에 문자열로 변환할 필요가 없습니다. 자세한 내용은 [여기](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)를 참조하세요.
- C#: 이제 [언어 ID](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) 및 [소스 언어 구성](./how-to-specify-source-language.md?pivots=programming-language-csharp)을 사용할 수 있습니다.
- JavaScript: Speech Service의 사용자 지정 메시지를 `receivedServiceMessage` 콜백으로 전달하는 기능을 `Connection` 개체에 추가했습니다.
- JavaScript: 온-프레미스 컨테이너 및 소버린 클라우드에서 쉽게 사용할 수 있도록 `FromHost API`에 대한 지원이 추가되었습니다. [여기](speech-container-howto.md) 설명서를 참조하세요.
- JavaScript: [orgads](https://github.com/orgads)의 기여로 이제 `NODE_TLS_REJECT_UNAUTHORIZED`를 적용하게 되었습니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)를 참조하세요.

**주요 변경 내용**

- `OpenSSL`은 1.1.1b 버전으로 업데이트되었으며 Linux용 Speech SDK 핵심 라이브러리에 정적으로 연결됩니다. 이로 인해 시스템의 `/usr/lib/ssl` 디렉터리에 수신함 `OpenSSL`이 설치되지 않으면 중단이 발생할 수 있습니다. Speech SDK 문서의 [설명서](how-to-configure-openssl-linux.md)에서 문제 해결 방법을 확인하세요.
- C# `WordLevelTimingResult.Offset`에 대해 반환되는 데이터 형식을 `int`에서 `long`으로 변경했으므로 이제 음성 데이터가 2분보다 긴 경우 `WordLevelTimingResults`에 액세스할 수 있습니다.
- 이제 `PushAudioInputStream` 및 `PullAudioInputStream`은 `AudioStreamFormat`(만들 때 필요에 따라 설정)을 기반으로 wav 헤더 정보를 Speech Service에 보냅니다. 이제 고객은 [지원되는 오디오 입력 형식](how-to-use-audio-input-streams.md)을 사용해야 합니다. 그 외의 형식을 사용하면 최적이 아닌 인식 결과를 얻거나 다른 문제를 일으킬 수 있습니다.

**버그 수정**

- 위의 주요 변경 내용에서 `OpenSSL` 업데이트를 참조하세요. Linux 및 Java의 간헐적인 충돌 및 성능 문제(높은 부하 상태에서 잠금 경합)가 모두 수정되었습니다.
- Java: 높은 동시성 시나리오에서 개체 닫기를 개선했습니다.
- NuGet 패키지를 재구성했습니다. lib 폴더에서 `Microsoft.CognitiveServices.Speech.core.dll` 및 `Microsoft.CognitiveServices.Speech.extension.kws.dll`의 복사본 3개를 제거하여 NuGet 패키지를 더 작고 더 빠르게 다운로드할 수 있게 만들었으며, 일부 C++ 네이티브 앱을 컴파일하는 데 필요한 헤더를 추가했습니다.
- [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) 빠른 시작 샘플을 수정했습니다. 이전에는 Linux, macOS, Windows에서 "마이크를 찾을 수 없음" 예외를 표시하지 않고 종료되었습니다.
- [이 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)처럼 특정 코드 경로에서 긴 음성 인식 결과와 함께 SDK가 충돌하는 문제를 수정했습니다.
- [이 고객 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)를 해결하기 위해 Azure 웹앱 환경의 SDK 배포 오류를 수정했습니다.
- [이 고객 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)를 해결하기 위해 여러 `<voice>` 태그 또는 `<audio>` 태그를 사용할 때 발생하는 TTS 오류를 수정했습니다.
- SDK가 일시 중단에서 복구될 때 발생하는 TTS 401 오류를 수정했습니다.
- JavaScript: [euirim](https://github.com/euirim)의 기여 덕분에 오디오 데이터의 순환 가져오기를 수정했습니다.
- JavaScript: 1.7에 추가된 대로 서비스 속성을 설정하기 위한 지원이 추가되었습니다.
- JavaScript: 연결 오류가 발생하면 실패한 websocket 다시 연결 시도가 연속적으로 발생하는 문제를 수정했습니다.

**샘플**

- Android 키워드 인식 샘플이 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)에 추가되었습니다.
- 서버 시나리오에 대한 TTS 샘플이 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)에 추가되었습니다.
- C# 및 C++에 대한 다중 디바이스 대화 빠른 시작이 [여기](quickstarts/multi-device-conversation.md)에 추가되었습니다.

**기타 변경 내용**

- Android의 SDK 핵심 라이브러리 크기를 최적화했습니다.
- 1\.9.0 이상의 SDK는 Conversation Transcriber의 음성 서명 버전 필드에서 `int` 및 `string` 형식을 모두 지원합니다.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: 2019년 11월 릴리스

**새 기능**

- 온-프레미스 컨테이너 및 소버린 클라우드에서 편리하게 사용할 수 있도록 `FromHost()` API를 추가했습니다.
- Java 및 C++에서 음성 인식을 위한 소스 언어 식별을 추가했습니다.
- Java 및 C++에서 필요한 소스 언어를 지정하는 데 사용되는 음성 인식용 `SourceLanguageConfig` 개체를 추가했습니다.
- NuGet 및 Unity 패키지를 통해 Windows(UWP), Android 및 iOS에 대한 `KeywordRecognizer` 지원을 추가했습니다.
- 비동기 일괄 처리에서 대화 기록을 수행할 수 있도록 Remote Conversation Java API를 추가했습니다.

**주요 변경 내용**

- Conversation Transcriber 기능이 `Microsoft.CognitiveServices.Speech.Transcription` 네임스페이스에서 이동되었습니다.
- Conversation Transcriber 메서드의 일부는 새 `Conversation` 클래스로 이동되었습니다.
- 32비트(ARMv7 및 x86) iOS에 대한 지원 삭제

**버그 수정**

- 유효한 Speech Service 구독 키 없이 로컬 `KeywordRecognizer`를 사용하는 경우 발생하는 충돌 해결

**샘플**

- `KeywordRecognizer`에 대한 Xamarin 샘플
- `KeywordRecognizer`에 대한 Unity 샘플
- 자동 소스 언어 식별을 위한 C++ 및 Java 샘플.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: 2019년 9월 릴리스

**새 기능**

- UWP(유니버설 Windows 플랫폼), Android 및 iOS에서 Xamarin에 대한 베타 지원이 추가되었습니다.
- Unity에 대한 iOS 지원이 추가되었습니다.
- Android, iOS 및 Linux에서 ALaw, Mulaw, FLAC에 대한 `Compressed` 입력 지원이 추가되었습니다.
- 서비스에 메시지를 보낼 수 있도록 `SendMessageAsync` 클래스에 `Connection`이 추가되었습니다.
- 메시지의 속성을 설정할 수 있도록 `SetMessageProperty` 클래스에 `Connection`이 추가되었습니다.
- Java(JRE 및 Android), Python, Swift 및 Objective-C에 대한 바인딩이 TTS에 추가되었습니다.
- macOS, iOS 및 Android에 대한 재생 지원이 TTS에 추가되었습니다.
- TTS에 대한 "단어 경계" 정보가 추가되었습니다.

**버그 수정**

- Android용 Unity 2019에서 IL2CPP 빌드 문제가 수정되었습니다.
- wav 파일 입력의 잘못된 헤더가 올바르지 않게 처리되는 문제를 수정했습니다.
- UUID가 일부 연결 속성에서 고유하지 않은 문제를 수정했습니다.
- Swift 바인딩에서 Null 허용 여부 지정자에 대한 몇 가지 경고를 수정 했습니다(코드를 약간 변경해야 할 수 있음).
- 네트워크 부하 시 websocket 연결이 비정상적으로 종료되는 버그를 수정했습니다.
- Android에서 가끔 중복된 광고 노출 ID가 `DialogServiceConnector`에 사용되는 문제를 수정했습니다.
- 다중 턴 상호 작용의 연결 안정성 및 `DialogServiceConnector`에서 오류 발생 시 오류 보고(`Canceled` 이벤트를 통해) 기능이 향상되었습니다.
- 이제 `DialogServiceConnector` 세션 시작은 활성 `StartKeywordRecognitionAsync()` 상태에서 `ListenOnceAsync()`를 호출하는 경우를 포함하여 이벤트를 올바르게 제공합니다.
- `DialogServiceConnector` 활동 수신 중에 발생하는 충돌을 해결했습니다.

**샘플**

- Xamarin에 대한 빠른 시작
- CPP 빠른 시작의 Linux ARM64 정보 업데이트
- Unity 빠른 시작의 iOS 정보 업데이트

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019년 6월 릴리스

**샘플**

- UWP 및 Unity의 텍스트 음성 변환 빠른 시작 샘플
- iOS의 Swift에 대한 빠른 시작 샘플
- 음성 및 의도 인식과 번역에 대한 Unity 샘플
- `DialogServiceConnector`에 대한 빠른 시작 샘플 업데이트

**향상된 기능/변경 사항**

- 대화 상자 네임스페이스:
  - `SpeechBotConnector`는 `DialogServiceConnector`로 이름이 변경되었습니다.
  - `BotConfig`는 `DialogServiceConfig`로 이름이 변경되었습니다.
  - `BotConfig::FromChannelSecret()`이 `DialogServiceConfig::FromBotSecret()`으로 다시 매핑되었습니다.
  - 기존의 모든 Direct Line Speech 클라이언트는 이름 바꾸기 후에도 계속 지원됩니다.
- 프록시, 영구 연결을 지원하도록 TTS REST 어댑터 업데이트
- 잘못된 지역이 전달될 때 오류 메시지 개선
- Swift/Objective-C:
  - 오류 보고 개선: 오류를 발생시킬 수 있는 메서드는 이제 두 가지 버전으로 제공됩니다. 하나는 오류 처리를 위해 `NSError` 개체를 노출하는 버전이고, 다른 하나는 예외를 발생시키는 버전입니다. Swift에는 전자가 노출됩니다. 이렇게 변경하려면 기존 Swift 코드를 조정해야 합니다.
  - 이벤트 처리 개선

**버그 수정**

- 오디오가 렌더링을 완료할 때까지 기다리지 않고 `SpeakTextAsync`가 나중에 반환되는 TTS 문제 수정
- 전체 언어 지원을 가능하게 하는 C#의 문자열 마샬링 수정
- 샘플에서 net461 대상 프레임워크를 사용하여 핵심 라이브러리를 로드하는 .NET Core 앱 문제 수정
- 샘플에서 출력 폴더에 네이티브 라이브러리를 배포하는 간헐적 문제 수정
- 안정적으로 웹 소켓을 닫도록 수정
- Linux에서 부하가 높은 상태에서 연결을 여는 동안 발생할 수 있는 충돌 수정
- macOS용 프레임워크 번들에서 누락된 메타데이터 수정
- Windows에서 `pip install --user` 문제 수정

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

버그 수정 릴리스이며 네이티브/관리형 SDK에만 적용됩니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

**버그 수정**

- 대화 기록에 사용되는 FromSubscription을 수정했습니다.
- 음성 도우미의 키워드 검출 버그를 수정했습니다.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: 2019년 5월 릴리스

**새로운 기능**

- 이제 Windows 및 Linux에서 KWS(키워드 검출)를 사용할 수 있습니다. KWS 기능은 모든 마이크 종류에 사용할 수 있지만 공식 KWS 지원은 현재 Azure Kinect DK 하드웨어 또는 Speech Devices SDK에 있는 마이크 배열로 제한됩니다.
- 구문 힌트 기능은 SDK를 통해 사용할 수 있습니다. 자세한 내용은 [여기](./get-started-speech-to-text.md)를 참조하세요.
- 대화 기록 기능은 SDK를 통해 사용할 수 있습니다. [여기](./conversation-transcription.md)를 참조하세요.
- Direct Line Speech 채널을 사용하는 음성 도우미 지원이 추가되었습니다.

**샘플**

- SDK에서 지원하는 새 기능 또는 새로운 서비스에 대한 샘플이 추가되었습니다.

**향상된 기능/변경 사항**

- 서비스 동작 또는 서비스 결과(예: 욕설 및 기타 비속어 마스킹)를 조정하는 다양한 인식기 속성이 추가되었습니다.
- 이제 `FromEndpoint` 인식기를 만들었더라도 표준 구성 속성을 통해 인식기를 구성할 수 있습니다.
- Objective-C: `OutputFormat` 속성이 `SPXSpeechConfiguration`에 추가되었습니다.
- 이제 SDK는 Linux 배포판으로 Debian 9를 지원합니다.

**버그 수정**

- 화자 리소스가 텍스트 음성 변환에서 너무 일찍 소멸하는 문제를 수정했습니다.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

버그 수정 릴리스이며 네이티브/관리형 SDK에만 적용됩니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- 웹 팩이 https 프록시 에이전트를 로드할 수 없습니다.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019년 4월 릴리스

**새로운 기능**

- 이제 SDK는 텍스트 음성 변환 서비스를 베타 버전으로 지원합니다. C++ 및 C#의 Windows 및 Linux 데스크톱에서 지원됩니다. 자세한 내용은 [텍스트 음성 변환 개요](text-to-speech.md#get-started)를 참조하세요.
- 이제 SDK는 MP3 및 Opus/OGG 오디오 파일을 스트림 입력 파일로 지원합니다. 이 기능은 C++ 및 C#의 Linux에서만 사용할 수 있으며 현재 베타 버전입니다(자세한 내용은 [여기](how-to-use-codec-compressed-audio-input-streams.md) 참조).
- Java, .NET Core, C++ 및 Objective-C에 대한 Speech SDK에서 macOS를 지원합니다. Objective-C의 macOS 지원은 현제 베타 버전입니다.
- iOS: iOS용 Speech SDK(Objective-C) 역시 현재 CocoaPod로 게시됩니다.
- JavaScript: 기본이 아닌 마이크를 입력 디바이스로 지원합니다.
- JavaScript: Node.js에 대한 프록시를 지원합니다.

**샘플**

- macOS 기반의 C++ 및 Objective-C에서 Speech SDK를 사용하는 샘플이 추가되었습니다.
- 텍스트 음성 변환 서비스 사용법을 보여주는 샘플이 추가되었습니다.

**향상된 기능/변경 사항**

- Python: 이제 인식 결과의 추가 속성이 `properties` 속성을 통해 노출됩니다.
- 추가 개발 및 디버그 지원이 필요한 경우 SDK 로깅 및 진단 정보를 로그 파일로 리디렉션할 수 있습니다(자세한 내용은 [여기](how-to-use-logging.md) 참조).
- JavaScript: 오디오 처리 성능이 향상되었습니다.

**버그 수정**

- Mac/iOS: Speech Service에 대한 연결을 설정할 수 없을 때 오래 기다려야 하는 버그를 수정했습니다.
- Python: Python 콜백의 인수에 대한 오류 처리가 향상되었습니다.
- JavaScript: RequestSession에서 종료된 음성의 잘못된 상태 보고가 수정되었습니다.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: 2019년 2월 새로 고침

버그 수정 릴리스이며 네이티브/관리형 SDK에만 적용됩니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

**버그 수정**

- 마이크 입력을 사용할 때 메모리 누수가 수정되었습니다. 스트림 기반 또는 파일 입력에는 영향을 주지 않습니다.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019 2월 릴리스

**새 기능**

- Speech SDK는 `AudioConfig` 클래스를 통해 입력 마이크를 선택할 수 있도록 지원합니다. 따라서 기본이 아닌 마이크에서 Speech Service로 오디오 데이터를 스트리밍할 수 있습니다. 자세한 내용은 [오디오 입력 디바이스 선택](how-to-select-audio-input-devices.md)에 대한 설명서를 참조하세요. JavaScript에서는 아직 이 기능을 사용할 수 없습니다.
- 이제 Speech SDK는 베타 버전의 Unity를 지원합니다. [GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples)의 문제 섹션을 통해 피드백을 제공하세요. 이 릴리스에서는 Windows x86 및 x64(데스크톱 또는 유니버설 Windows 플랫폼 애플리케이션)와 Android(ARM32/64, x86)의 Unity를 지원합니다. 자세한 내용은 [Unity 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)을 참조하세요.
- 이전 릴리스에서 제공하던 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 파일은 더 이상 필요 없습니다. 이제 이 기능이 핵심 SDK에 통합되었습니다.

**샘플**

다음 새 콘텐츠는 [샘플 리포지토리](https://aka.ms/csspeech/samples)에서 사용할 수 있습니다.

- `AudioConfig.FromMicrophoneInput`에 대한 추가 샘플
- 의도 인식 및 변환에 대한 추가 Python 샘플
- iOS에서 `Connection` 개체를 사용하는 방법에 대한 추가 샘플
- 오디오 출력을 사용한 변환에 대한 추가 Java 예제
- [일괄 처리 기록 REST API](batch-transcription.md)

**향상된 기능/변경 사항**

- Python
  - `SpeechConfig`의 매개 변수 확인 및 오류 메시지를 개선했습니다.
  - `Connection` 개체 지원을 추가했습니다.
  - Windows에서 32비트 Python(x86)을 지원합니다.
  - Python용 Speech SDK가 베타를 벗어났습니다.
- iOS
  - SDK는 이제 iOS SDK 버전 12.1용으로 빌드되었습니다.
  - SDK는 이제 iOS 버전 9.2 이상을 지원합니다.
  - 참조 설명서를 개선하고 몇 가지 속성 이름을 수정합니다.
- JavaScript
  - `Connection` 개체 지원을 추가했습니다.
  - 번들로 묶은 JavaScript의 형식 정의 파일을 추가했습니다.
  - 구문 힌트의 초기 지원 및 구현이 제공됩니다.
  - 인식을 위해 서비스 JSON을 사용하는 속성 컬렉션을 반환합니다.
- 이제 Windows DLL이 버전 리소스를 포함합니다.
- `FromEndpoint` 인식기를 만드는 경우 엔드포인트 URL에 매개 변수를 직접 추가할 수 있습니다. `FromEndpoint`를 사용하면 표준 구성 속성을 통해 인식기를 구성할 수 없습니다.

**버그 수정**

- 빈 프록시 이름과 프록시 암호가 올바르게 처리되지 않았습니다. 이 릴리스에서는 프록시 이름과 프록시 암호를 빈 문자열로 설정하면 프록시에 연결될 때 제출되지 않습니다.
- SDK에서 만든 SessionId가 항상 일부 언어&nbsp;/ 환경에서 무작위인 것은 아니었습니다. 이 문제를 수정하기 위해 난수 생성기 초기화를 추가했습니다.
- 권한 부여 토큰 처리를 개선했습니다. 권한 부여 토큰을 사용하려는 경우 `SpeechConfig`에서 지정하고 구독 키를 비워 둡니다. 그런 다음, 평소처럼 인식기를 만듭니다.
- 경우에 따라 `Connection` 개체가 올바르게 해제되지 않았습니다. 이 문제가 해결되었습니다.
- Safari에서도 오디오 출력의 변환 합성을 지원하도록 JavaScript 샘플을 수정했습니다.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- speech.end가 아닌 turn.end에서 스트림 끝을 실행합니다.
- 현재 전송이 실패한 경우 다음 전송을 예약하지 않는 오디오 펌프의 버그를 수정했습니다.
- 인증 토큰을 사용한 연속 인식을 수정했습니다.
- 다른 인식기/엔드포인트의 버그를 수정했습니다.
- 설명서 향상된 기능입니다.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018년 12월 릴리스

**새 기능**

- Python
  - 이 릴리스에서는 베타 버전의 Python(3.5 이상)이 지원됩니다. 자세한 내용은 [여기](quickstart-python.md)를 참조하세요.
- JavaScript
  - JavaScript용 Speech SDK가 오픈 소스로 제공됩니다. 소스 코드는 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)에서 사용할 수 있습니다.
  - 이제 Node.js를 지원합니다. 자세한 정보는 [여기](./get-started-speech-to-text.md)에서 확인할 수 있습니다.
  - 오디오 세션에 대한 길이 제한이 제거되었으므로 백그라운드에서 자동으로 다시 연결됩니다.
- `Connection` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다.
  - `Recognizer`에서 `Connection` 개체에 액세스할 수 있습니다. 이 개체를 사용하면 명시적으로 서비스 연결을 시작하고 연결 및 연결 끊기 이벤트를 구독할 수 있습니다.
    (아직 JavaScript 및 Python에서는 이 기능을 사용할 수 없습니다.)
- Ubuntu 18.04 지원
- Android
  - APK 생성 중에 ProGuard 지원이 설정되었습니다.

**향상된 기능**

- 스레드, 잠금, 뮤텍스 수를 줄이면서 내부 스레드 사용이 개선되었습니다.
- 오류 보고/정보가 개선되었습니다. 여러 사례에서 오류 메시지가 완전히 전파되지 않았습니다.
- 최신 모듈을 사용하도록 JavaScript의 개발 종속성을 업데이트했습니다.

**버그 수정**

- `RecognizeAsync`의 형식 불일치로 인한 메모리 누수가 수정되었습니다.
- 어떤 경우에는 예외가 유출되었습니다.
- 번역 이벤트 인수에서 메모리 누수가 수정되었습니다.
- 장기 실행 세션에서 다시 연결 시 잠금 문제가 해결되었습니다.
- 번역 실패로 인해 최종 결과가 누락될 수 있는 문제가 해결되었습니다.
- C#: 주 스레드에서 `async` 작업을 기다리지 않는 경우 비동기 작업이 완료되기 전에 인식기가 삭제될 수 있었습니다.
- Java: Java VM 충돌 문제가 해결되었습니다.
- Objective-C: 고정 열거형 매핑: `RecognizingIntent` 대신 RecognizedIntent가 반환되었습니다.
- JavaScript: `SpeechConfig`에서 기본 출력 형식을 'simple'로 설정합니다.
- JavaScript: JavaScript 및 다른 언어에서 구성 개체 속성 간 불일치를 제거합니다.

**샘플**

- 여러 샘플을 업데이트하고 수정했습니다(예: 번역을 위한 출력 음성 등).
- Node.js 샘플을 [샘플 리포지토리](https://aka.ms/csspeech/samples)에 추가했습니다.

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**새 기능**

- Android x86/x64를 지원합니다.
- 프록시 지원: 이제 `SpeechConfig` 개체에서 함수를 호출하여 프록시 정보(호스트 이름, 포트, 사용자 이름 및 암호)를 설정할 수 있습니다. iOS에서는 아직 이 기능을 사용할 수 없습니다.
- 향상된 오류 코드 및 메시지입니다. 인식이 오류를 반환한 경우 이미 `Reason`(취소된 이벤트의) 또는 `CancellationDetails`(인식 결과의) `Error`로 설정되어 있습니다. 취소 이벤트에는 이제 두 개의 추가 멤버 `ErrorCode` 및 `ErrorDetails`가 포함됩니다. 서버에서 보고된 오류를 사용하여 추가 오류 정보를 반환하는 경우 해당 서버를 새 멤버에서 사용할 수 있습니다.

**향상된 기능**

- 인식기 구성에서 추가 확인을 추가하고, 추가 오류 메시지를 추가했습니다.
- 오디오 파일의 중간에 긴 무음 시간 처리가 향상되었습니다.
- NuGet 패키지: .NET Framework의 경우 프로젝트는 AnyCPU 구성을 통한 빌드를 방지합니다.

**버그 수정**

- 인식기에서 발견된 몇 가지 예외를 수정했습니다. 또한 예외가 catch되고 `Canceled` 이벤트로 변환됩니다.
- 속성 관리에서 메모리 누수를 해결합니다.
- 오디오 입력 파일이 인식기 작동을 중지할 수 있는 버그가 수정되었습니다.
- 세션 중지 이벤트 후 이벤트를 수신할 수 있는 버그가 수정되었습니다.
- 스레딩에서 일부 경합 상태가 수정되었습니다.
- 결국 충돌을 일으킬 수 있는 iOS 호환성 문제가 해결되었습니다.
- Android 마이크 지원의 안정성 향상.
- JavaScript의 인식기가 인식 언어를 무시하는 버그가 수정되었습니다.
- JavaScript에서 `EndpointId`(일부 경우) 설정을 방해하는 버그가 수정되었습니다.
- JavaScript의 AddIntent에서 매개 변수 순서를 변경하고, 누락된 `AddIntent` JavaScript 서명을 추가했습니다.

**샘플**

- [샘플 리포지토리](https://aka.ms/csspeech/samples)에 끌어오기 및 밀어넣기 스트리밍 사용에 대한 C++ 및 C# 샘플을 추가했습니다.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

안정성 향상 및 버그 수정:

- 삭제 중인 인식기의 경합으로 인한 잠재적인 심각한 오류가 수정되었습니다.
- 속성이 설정되지 않은 경우 발생할 수 있는 심각한 오류가 수정되었습니다.
- 추가 오류 및 매개 변수 검사가 추가되었습니다.
- Objective-C: NSString에서 이름 재정의로 인한 잠재적인 심각한 오류가 수정되었습니다.
- Objectice-C: API의 표시 유형이 조정되었습니다.
- JavaScript: 관련 이벤트 및 해당 페이로드가 수정되었습니다.
- 설명서 향상된 기능입니다.

[샘플 리포지토리](https://aka.ms/csspeech/samples)에서 JavaScript에 대한 새로운 샘플이 추가되었습니다.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018년 9월 release

**새로운 기능**

- iOS에서 Objective-C를 지원합니다. [iOS용 Objective-C 빠른 시작](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)을 확인하세요.
- 브라우저에서 JavaScript를 지원합니다. [JavaScript 빠른 시작](./get-started-speech-to-text.md)을 확인하세요.

**주요 변경 내용**

- 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다.
  자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018년 8월 릴리스

**새로운 기능**

- Speech SDK를 사용하여 빌드된 UWP 앱은 이제 WACK(Windows 앱 인증 키트)를 전달할 수 있습니다.
  [UWP 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)을 확인하세요.
- Linux(Ubuntu 16.04 x64)에서 .NET 표준 2.0을 지원합니다.
- 실험: Windows(64비트) 및 Linux(Ubuntu 16.04 x64)에서 Java 8을 지원합니다.
  [Java Runtime Environment 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre)을 확인하세요.

**기능 변경**

- 연결 오류에 대한 추가 오류 세부 정보를 표시합니다.

**주요 변경 내용**

- Java(Android)에서, `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 함수는 더 이상 경로 매개 변수를 요구하지 않습니다. 이제 지원되는 모든 플랫폼에서 경로가 자동으로 검색됩니다.
- Java 및 C#에서 `EndpointUrl` 속성의 get-accessor가 제거되었습니다.

**버그 수정**

- Java에서, 번역 인식기의 오디오 합성 결과가 구현되었습니다.
- 비활성 스레드 및 사용되지 않는 열린 소켓 수가 증가하는 버그가 수정되었습니다.
- 오래 실행되는 인식이 전송 중에 종료될 수 있는 문제가 해결되었습니다.
- 인식기 종료에서 발생하는 경합 조건을 수정했습니다.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018년 7월 릴리스

**새로운 기능**

- Android 플랫폼(API 23: Android 6.0 Marshmallow 이상)을 지원합니다. [Android 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)을 확인하세요.
- Windows에서 .NET Standard 2.0을 지원합니다. [.NET Core 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)을 확인하세요.
- 실험: Windows(버전 1709 이상)에서 UWP를 지원합니다.
  - [UWP 빠른 시작](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)을 확인하세요.
  - Speech SDK를 사용하여 빌드된 UWP 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다.
- 자동 다시 연결을 통해 장기 실행 인식을 지원합니다.

**기능 변경 내용**

- `StartContinuousRecognitionAsync()`에서 장기 실행 인식을 지원합니다.
- 인식 결과에 더 많은 필드가 포함됩니다. 인식된 텍스트의 오디오 시작 및 지속 시간의 오프셋(두 가지 모두 틱 단위) 및 인식 상태를 나타내는 추가 값(예: `InitialSilenceTimeout`, `InitialBabbleTimeout`)입니다.
- 팩터리 인스턴스를 만들기 위한 AuthorizationToken을 지원합니다.

**주요 변경 내용**

- 인식 이벤트: `NoMatch` 이벤트 유형이 `Error` 이벤트에 병합되었습니다.
- C#의 SpeechOutputFormat이 C++에 맞게 `OutputFormat`으로 이름이 바뀌었습니다.
- `AudioInputStream` 인터페이스의 일부 메서드 반환 형식이 다음과 같이 약간 변경되었습니다.
  - Java에서 `read` 메서드는 이제 `int` 대신 `long`을 반환합니다.
  - C#에서 `Read` 메서드는 이제 `int` 대신 `uint`을 반환합니다.
  - C++에서 `Read` 및 `GetFormat` 메서드는 이제 `int` 대신 `size_t`를 반환합니다.
- C++: 오디오 입력 스트림의 인스턴스가 이제 `shared_ptr`로만 전달될 수 있습니다.

**버그 수정**

- `RecognizeAsync()` 시간이 초과될 때 결과의 잘못된 반환 값이 수정되었습니다.
- Windows에서 미디어 파운데이션 라이브러리에 대한 종속성이 제거되었습니다. SDK에 이제 Core Audio API가 사용됩니다.
- 설명서 수정 사항: 지원되는 지역을 설명하는 [지역](regions.md) 페이지가 추가되었습니다.

**알려진 문제**

- Android용 Speech SDK에서는 번역에 대한 음성 합성 결과를 보고하지 않습니다. 이 문제는 다음 릴리스에서 해결될 예정입니다.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018년 6월 릴리스

**기능 변경 내용**

- AudioInputStream

  이제 인식기가 스트림을 오디오 원본으로 사용할 수 있습니다. 자세한 내용은 관련 [방법 가이드](how-to-use-audio-input-streams.md)를 참조하세요.

- 자세한 출력 형식

  `SpeechRecognizer`를 만들 때 `Detailed` 또는 `Simple` 출력 형식을 요청할 수 있습니다. `DetailedSpeechRecognitionResult`에는 신뢰도 점수, 인식된 텍스트, 원시 어휘 형식, 정규화된 형식 및 마스킹된 욕설이 포함된 정규화된 형식이 포함됩니다.

**주요 변경 내용**

- C#의 `SpeechRecognitionResult.RecognizedText`가 `SpeechRecognitionResult.Text`로 변경되었습니다.

**버그 수정**

- 종료하는 동안 USP 계층에서 발생할 수 있는 콜백 문제가 해결되었습니다.
- 인식기가 오디오 입력 파일을 사용한 경우, 필요한 것보다 더 긴 파일 핸들을 사용하고 있었습니다.
- 메시지 펌프와 인식기 간에 여러 가지 교착 상태가 제거되었습니다.
- 서비스 응답 시간이 초과되면 `NoMatch` 결과를 실행합니다.
- Windows의 미디어 파운데이션 라이브러리는 지연 로드됩니다. 이 라이브러리는 마이크 입력에만 필요합니다.
- 오디오 데이터의 업로드 속도는 원본 오디오 속도의 두 배 정도로 제한됩니다.
- Windows에서 C# .NET 어셈블리에 이제 강력한 이름이 지정됩니다.
- 설명서 수정: `Region`은 인식기를 만드는 데 필요한 정보입니다.

추가 샘플이 추가되었고 지속적으로 업데이트되고 있습니다. 최신 샘플 집합은 [Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018년 5월 릴리스

이 릴리스는 Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
