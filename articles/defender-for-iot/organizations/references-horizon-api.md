---
title: Horizon API
description: 이 가이드에서는 일반적으로 사용되는 Horizon 메서드를 설명합니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: db3fd241593f2d5c3e43485bcca4790a73f975fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331322"
---
# <a name="horizon-api"></a>Horizon API

이 가이드에서는 일반적으로 사용되는 Horizon 메서드를 설명합니다.

## <a name="getting-more-information"></a>자세한 정보 보기

IoT 용 Defender Api는 [MICROSOFT Api 라이선스 및 사용 약관](/legal/microsoft-apis/terms-of-use)의 적용을 받습니다.

Horizon 및 Defender for IoT 플랫폼 사용에 대한 자세한 내용은 다음 정보를 참조하세요.

- Horizon ODE(Open Development Environment) SDK의 경우 IoT용 Defender 담당자에게 문의하세요.

- 지원 및 문제 해결 정보는 <support@cyberx-labs.com>에 문의하세요.

- IoT용 Defender 콘솔에서 IoT용 Defender 사용자 가이드에 액세스하려면 :::image type="icon" source="media/references-horizon-api/profile.png":::을 선택한 다음 **사용자 가이드 다운로드** 를 선택합니다.

## `horizon::protocol::BaseParser`

모든 플러그인에 대한 요약입니다. 이는 다음의 두 가지 메서드로 구성됩니다.

- 위에 정의된 플러그인 필터 처리용. 이 방법을 통해 Horizon이 파서와 통신하는 방법을 알 수 있습니다.
- 실제 데이터 처리용.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

플러그인에 호출되는 첫 번째 함수는 Horizon용 파서의 인스턴스를 만들어 이를 인식하고 등록합니다.

### <a name="parameters"></a>매개 변수

없음

### <a name="return-value"></a>반환 값

파서 인스턴스에 대한 shared_ptr입니다.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

이 함수는 위에 등록된 각 플러그인에 대해 호출됩니다.

대부분의 경우 비어 있습니다. Horizon에 대한 예외를 throw하여 오류가 발생했음을 알 수 있습니다.

### <a name="parameters"></a>매개 변수

- 등록하려는 다른 플러그인의 config.js에 정의된 dissect_as 구조를 포함하는 맵입니다.

### <a name="return-value"></a>반환 값

일종의 uint64_t로 처리되는 등록인 uint64_t 배열입니다. 즉, 맵에서 값이 uin64_t인 포트 목록이 표시됩니다.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

main 함수입니다. 특히 새 패킷이 파서에 도달할 때마다 플러그인의 논리입니다. 이 함수는 호출되며, 여기에서 패킷 처리와 관련된 모든 작업을 수행해야 합니다.

### <a name="considerations"></a>고려 사항

다른 스레드에서 이 함수를 호출할 수 있으므로, 플러그인이 스레드로부터 안전해야 합니다. 바람직한 방법은 스택에서 모든 항목을 정의하는 것입니다.

### <a name="parameters"></a>매개 변수

- 데이터를 저장하고 ILayer 및 필드 같은 SDK 관련 개체를 만드는 작업을 담당하는 SDK 조절 장치입니다.
- 원시 패킷의 데이터를 읽기 위한 도우미입니다. config.json에서 정의한 바이트 순서를 사용하여 이미 설정되어 있습니다.

### <a name="return-value"></a>반환 값

처리 결과입니다. *Success*, *Malformed* 또는 *Sanity* 중 하나일 수 있습니다.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

처리를 정리 실패로 표시합니다. 이는 패킷이 현재 프로토콜에서 인식되지 않음을 의미하며, Horizon은 동일한 필터에 등록된 경우 다른 파서로 전달해야 합니다.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

생성자

### <a name="parameters"></a>매개 변수

- config.json에 정의된 대로, Horizon에서 로깅하기 위해 사용하는 오류 코드를 정의합니다.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

잘못된 형식 결과입니다. 이미 패킷을 프로토콜로 인식했지만, 일부 유효성 검사가 잘못되었음을 나타냅니다(예약된 비트가 켜져 있거나 일부 필드가 누락됨).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

생성자

### <a name="parameters"></a>매개 변수

- config.json에 정의된 오류 코드입니다.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

처리 성공 시 Horizon에 알림이 전송됩니다. 성공하면 패킷이 수락되고, 데이터가 속하게 되며, 모든 데이터가 추출됩니다.

## `horizon::protocol::SuccessResult()`

생성자입니다. 기본적으로 성공적인 결과를 만듭니다. 즉, 패킷에 대한 방향 또는 다른 메타데이터를 알 수 없습니다.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

생성자입니다.

### <a name="parameters"></a>매개 변수

- 패킷의 방향(확인된 경우)입니다. 값은 *REQUEST* 또는 *RESPONSE* 가 될 수 있습니다.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

생성자입니다.

### <a name="parameters"></a>매개 변수

- 패킷 방향(확인된 경우)은 *REQUEST*, *RESPONSE* 일 수 있습니다.
- 경고. 이러한 이벤트는 실패하지 않지만, Horizon에 알림이 전송됩니다.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

생성자입니다.

### <a name="parameters"></a>매개 변수

- 경고. 이러한 이벤트는 실패하지 않지만, Horizon에 알림이 전송됩니다.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

필드 이름(예: function_code)에 대한 문자열 기반 참조를 HorizonID로 변환합니다.

### <a name="parameters"></a>매개 변수

- 변환할 문자열입니다.

### <a name="return-value"></a>반환 값

- 문자열에서 만든 HorizonID입니다.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

플러그인에서 일부 데이터를 저장하려고 한다는 것을 Horizon이 알 수 있도록 새 레이어를 만듭니다. 사용해야 하는 기본 스토리지 단위입니다.

### <a name="return-value"></a>반환 값

만든 레이어에 대한 참조로, 여기에 데이터를 추가할 수 있습니다.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

다른 개체(예: ILayer)에서 필드를 만드는 일을 담당하는 필드 관리 개체를 가져옵니다.

### <a name="return-value"></a>반환 값

관리자에 대한 참조입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

레이어에서 요청된 ID를 사용하여 64비트의 새 숫자 필드를 만듭니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 레이어입니다.
- **HORIZON_FIELD** 매크로에 의해 만들어진 HorizonID입니다.
- 저장하려는 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

요청된 ID를 사용하여 레이어에 새 문자열 필드를 만듭니다. 메모리가 이동되므로 주의해야 합니다. 이 값은 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 레이어입니다.
- **HORIZON_FIELD** 매크로에 의해 만들어진 HorizonID입니다.
- 저장하려는 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

요청된 ID를 사용하여 레이어에 새 원시 값(바이트 배열) 필드를 만듭니다. 메모리가 이동되므로 주의해야 합니다. 이 값은 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 레이어입니다.
- **HORIZON_FIELD** 매크로에 의해 만들어진 HorizonID입니다.
- 저장하려는 원시 값입니다.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

요청된 ID를 사용하여 지정된 형식의 레이어에 배열 값(배열) 필드를 만듭니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 레이어입니다.
- **HORIZON_FIELD** 매크로에 의해 만들어진 HorizonID입니다.
- 배열 내에 저장될 값의 형식입니다.

### <a name="return-value"></a>반환 값

값을 추가해야 하는 배열에 대한 참조입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

앞에서 만든 배열에 새 정수 값을 추가합니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

이전에 만든 배열에 새 문자열 값을 추가합니다. 메모리가 이동되므로 주의해야 합니다. 이 값은 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

이전에 만든 배열에 새 원시 값을 추가합니다. 메모리가 이동되므로 주의해야 합니다. 이 값은 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

버퍼에 X바이트 이상 포함되어 있는지 확인합니다.

### <a name="parameters"></a>매개 변수

존재해야 하는 바이트 수입니다.

### <a name="return-value"></a>반환 값

버퍼에 X바이트 이상 포함되어 있으면 True입니다. 그렇지 않으면 `False`입니다.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

바이트 순서에 따라 버퍼에서 uint8 값(1바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

바이트 순서에 따라 버퍼에서 uint16 값(2바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

바이트 순서에 따라 버퍼에서 uint32 값(4바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

바이트 순서에 따라 버퍼에서 uint64 값(8바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

지정된 크기의 미리 할당된 메모리로 읽기는 실제로 데이터를 메모리 영역에 복사합니다.

### <a name="parameters"></a>매개 변수 

- 데이터를 복사할 메모리 영역입니다.
- 메모리 영역의 크기입니다. 이 매개 변수는 복사될 바이트 수도 정의합니다.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

버퍼에서 문자열로 읽습니다.

### <a name="parameters"></a>매개 변수 

- 읽어야 하는 바이트 수입니다.

### <a name="return-value"></a>반환 값

문자열의 메모리 영역에 대한 참조입니다.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

버퍼에 남아 있는 바이트 수를 알려 줍니다.

### <a name="return-value"></a>반환 값

남은 버퍼의 크기입니다.

## `void horizon::general::IDataBuffer::skip(size_t)`

버퍼에서 X바이트를 건너뜁니다.

### <a name="parameters"></a>매개 변수

- 건너뛸 바이트 수입니다.
