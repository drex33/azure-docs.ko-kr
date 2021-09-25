---
title: Python을 사용하여 클라이언트 쪽 암호화
titleSuffix: Azure Storage
description: Python용 Azure Storage 클라이언트 라이브러리는 Azure Storage 애플리케이션의 보안을 최대화하기 위해 클라이언트 쪽 암호화를 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 82a5dadb813a15cf278811ab80e358444bc1d5e6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626706"
---
# <a name="client-side-encryption-with-python"></a>Python을 사용하여 클라이언트 쪽 암호화

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>개요

[Python용 Azure 스토리지 클라이언트 라이브러리](https://pypi.python.org/pypi/azure-storage) 는 Azure Storage에 업로드하기 전에 클라이언트 애플리케이션 내부에서 데이터를 암호화하고 클라이언트로 다운로드하는 동안 데이터 암호를 해독하는 기능을 지원합니다.

> [!NOTE]
> Azure Storage Python 라이브러리는 미리 보기 상태입니다.
>
>

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호화 및 암호 해독

암호화 및 암호 해독 프로세스는봉투(Envelope) 기법을 따릅니다.

### <a name="encryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호화

암호화는 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1. Azure Storage 클라이언트 라이브러리는 1회용 대칭 키인 콘텐츠 암호화 키(CEK)를 생성합니다.
2. 사용자 데이터는 이 CEK를 사용하여 암호화됩니다.
3. 그런 다음 키 암호화 KEK를 사용하여 CEK를 래핑(암호화)합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리됩니다.
   스토리지 클라이언트 라이브러리 자체는 KEK에 액세스할 수 없습니다. 라이브러리는 KEK에서 제공하는 키 래핑 알고리즘을 호출합니다. 사용자는 원하는 경우 키 래핑/래핑 해제를 위해 사용자 지정 공급자를 사용하도록 선택할 수 있습니다.
4. 그런 다음 암호화된 데이터를 Azure Storage 서비스에 업로드합니다. 일부 추가 암호화 메타데이터와 함께 래핑된 키에 메타 데이터로(Blob) 저장 되거나 암호화 된 데이터 (메시지 큐 및 테이블 엔터티)와 보관 합니다.

### <a name="decryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호해독

암호해독은 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1. 클라이언트 라이브러리는 사용자가 KEK(키 암호화 키)를 로컬로 관리한다고 가정합니다. 사용자는 암호화에 사용된 특정 키를 알 필요가 없습니다. 대신 다른 키 식별자를 키로 확인하는 키 확인자를 설정하고 사용할 수 있습니다.
2. 클라이언트 라이브러리는 서비스에 저장된 모든 암호화 자료와 함께 암호화된 데이터를 다운로드 합니다.
3. 래핑된 콘텐츠 암호화 키 (CEK)는 키 암호화 키를(KEK) 사용하여 래핑해제(암호 해독)합니다. 여기서 다시, 클라이언트 라이브러리는 KEK에 대한 액세스권한이 없습니다. 단순히 사용자 지정 공급자의 래핑 해제 알고리즘을 호출합니다.
4. 그리고 콘텐츠 암호화 키 (CEK)는  암호화 된 사용자 데이터의 암호를 해독 하는데 사용 됩니다.

## <a name="encryption-mechanism"></a>암호화 메커니즘

스토리지 클라이언트 라이브러리는 사용자 데이터를 암호화하기 위해 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 를 사용합니다. 특히, AES를 이용한 [CBC(암호화 블록 체인)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 모드입니다. 각 서비스는 하는 일이 각각 다르므로 여기서 이것들을 살펴볼 것입니다.

### <a name="blobs"></a>Blob

클라이언트 라이브러리는 현재 전체 blob 암호화만 지원합니다. 특히 사용자가 **create*** 메서드를 사용할 때 암호화가 지원됩니다. 다운로드로는 전체 및 범위 다운로드가 지원되며, 업로드 및 다운로드의 병렬화를 사용할 수 있습니다.

암호화 하는 동안 클라이언트 라이브러리는 임의 IV (Initialization Vector) 32 바이트의 임의의 콘텐츠 암호화 키 (CEK)와 함께 16 바이트를 생성 하고 이 정보를 사용 여 blob 데이터의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터 서비스에서 암호화 된 blob과 함께 메타 데이터를 blob으로 저장합니다.

> [!WARNING]
> blob에 대해 고유 메타데이터를 편집하거나 업로드 할 경우, 메타데이타가 유지되는지 확인하세요. 이 메타 데이터 없이 새 메타 데이터를 업로드 하는 경우에는 래핑된 CEK, IV 및 기타 메타 데이터가 손실 되고 blob 콘텐츠를 절대로 다시 검색할 수 없습니다.
>
>

암호화 Blob 다운로드는 **get*** 편리한 메서드를 사용한 전체 Blob의 콘텐츠 검색을 포함합니다. 래핑된 CEK는 IV (blob 메타 데이터로 저장된 경우)와 함께 암호해독되고 사용되어 지며 해독된 데이터가 사용자에게 돌아갑니다.

암호화된 Blob 내에서 임의의 범위를 다운로드할 경우(범위 매개 변수가 제공된 **get*** 메서드) 요청된 범위를 성공적으로 암호를 해독하는 데 사용되는 소량의 추가 데이터를 얻기 위해 사용자가 제공하는 범위가 조정됩니다.

이 스키마를 사용하여 블록 Blob 및 페이지 Blob만 암호화/암호 해독할 수 있습니다. 추가 Blob에 대한 암호화 지원은 현재 없습니다.

### <a name="queues"></a>큐

큐 메시지의 모든 형식이 될 수, 있으므로 클라이언트 라이브러리는 IV (Initialization Vector) 및 암호화 된 콘텐츠 암호화 키 (CEK) 메시지 텍스트에 포함 된 사용자 지정 형식을 정의 합니다.

암호화 하는 동안 클라이언트 라이브러리는 32 바이트의 임의 CEK 함께 16 바이트의 임의 IV를 생성하고 이 정보를 사용하여 큐 메시지 텍스트의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터를 암호화 된 큐 메시지에 추가합니다. (아래 참조)이 수정 된 메시지는 서비스에 저장 됩니다.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

암호를 해독 하는 동안, 래핑된 키는 큐 메시지에서 추출되고 래핑이 해제됩니다. IV 또한 큐메시지에서 추출되고 큐 메시지 데이터를 암호해독하기 위해 래핑해제된 키와 함께 사용 됩니다. 참고로 암호화 메타데이터는 작아야하므로(500바이트 이하),큐 메시지는 64KB의 제한이 있어야만 영향을 관리 할 수 있습니다.

### <a name="tables"></a>테이블

클라이언트 라이브러리는 작업 삽입 및 삭제의 엔터티 속성 암호화를 지원합니다.

> [!NOTE]
> 병합은 현재 지원 되지 않습니다. 속성의 하위 집합은 이전에 다른 키를 사용하여 암호화됐을 가능성이 있기 때문에 단순히 새로운 속성을 병합하는 것과 메타데이터를 업데이트 하는 것은 데이터 손실을 불러 올 수 있습니다. 서비스에서 기존 엔터티를 읽을 수 있는 추가 서비스 호출을 수행 하거나 속성 당 새 키를 사용하는 것 모두에 성능상의 이유로 적합하지 않습니다.
>
>

테이블 데이터 암호화는 다음과 같이 작동합니다.

1. 사용자는 암호화할 속성을 지정합니다.
2. 클라이언트 라이브러리는 모든 엔터티에 대해 16바이트의 임의 IV(Initialization Vector)와 함께 32바이트의 임의 CEK(콘텐츠 암호화 키)를 생성하고 속성당 새 IV를 파생하여 암호화해야 하는 개별적인 속성에 대해 봉투(envelope) 암호화를 수행합니다. 암호화된 속성은 이진 데이터로 저장됩니다.
3. 래핑된 CEK 및 일부 추가 암호화 메타 데이터는 다음  추가 예약 된 두 가지 속성으로 저장 됩니다. 첫 번째 예약 된 속성 (\_ClientEncryptionMetadata1)은 IV, 버전, 래핑된 키의 정보를 담고 있는 문자열 속성입니다. 또 다른 예약된 속성(\_ClientEncryptionMetadata2)은 암호화된 속성에 대한 정보를 담고 있는 이진 속성입니다. 이 두 번째 속성(\_ClientEncryptionMetadata2)의 정보는 자체적으로 암호화됩니다.
4. 이 추가적인 예약 속성이 암호화에 필요하기 때문에 사용자들은 252가지 사용자 지정 속성 대신 250가지를 갖게 됩니다. 엔터티의 총 크기는 1MB 미만 이어야 합니다.

   문자열 속성만 암호화 할 수 있다는 것을 참고하세요. 다른 유형의 속성이 암호화 된 경우, 문자열로 변환합니다. 암호화된 문자열은 서비스에 이진 속성으로 저장되고 암호 해독 후에는 다시 문자열(원시 문자열, EdmType.STRING 형식을 갖는 EntityProperties 아님)로 변환됩니다.

   테이블의 경우, 암호화 정책 외에도 사용자가 암호화할 속성을 지정해야 합니다. 형식을 EdmType.STRING으로 설정하고 암호화를 true로 설정하여 TableEntity 개체에 이러한 속성을 저장하거나 tableservice 개체에 대해 encryption_resolver_function을 설정하여 이 작업을 수행할 수 있습니다. 암호화 해결 프로그램은 파티션 키, 행 키, 그리고 속성 이름 및 암호화 여부 속성을 나타내는 부울을 반환하는 함수입니다. 암호화 하는 동안 클라이언트 라이브러리는 네트워크에 쓰는 동안 속성을 암호화 해야 하는지 여부를 결정하는데 이 정보를 사용합니다. 대리자 속성은 암호화 하는 방법 논리의 가능성도 제공 합니다. (예를 들어 X인 경우 A 속성을 암호화하고, 그렇지 않은 경우 A와 B 속성을 암호화합니다.) 엔터티를 읽거나 쿼리하는 동안 해당 정보를 제공할 필요가 없습니다.

### <a name="batch-operations"></a>Batch 작업

배치의 모든 행에는 단일 암호화 정책이 적용됩니다. 클라이언트 라이브러리는 내부적으로 배치의 새로운 임의 IV와 행 기준 임의 CEK를 만듭니다. 사용자가 암호화 해결 프로그램에 이동작을 정의하여 배치의 모든 작업에 대해 암호화 할 다른 속성들을 선택할 수 있습니다.
tableservice batch() 메서드를 통해 배치가 컨텍스트 관리자로 생성되면 tableservice의 암호화 정책이 해당 배치에 자동으로 적용됩니다. 생성자를 호출하여 배치를 명시적으로 만들 경우 암호화 정책이 매개 변수로 전달되고 배치의 수명 동안 수정되지 않은 상태로 유지됩니다.
엔터티는 배치의 암호화 정책을 사용하여 배치에 삽입될 때 암호화됩니다(tableservice의 암호화 정책을 사용하여 배치를 커밋할 때 암호화되지 않음).

### <a name="queries"></a>쿼리

> [!NOTE]
> 엔터티는 암호화되므로 암호화된 속성을 필터링하는 쿼리를 실행할 수 없습니다.  시도하면 암호화되지 않은 데이터와 암호화된 데이터를 비교하려고 하기 때문에 결과가 잘못됩니다.
>
>
> 쿼리 작업을 수행 하려면 결과 집합에 있는 모든 키를 확인할 수 있는 키 확인자를 지정 해야 합니다. 공급자에는 쿼리 결과에 포함 된 엔터티를 확인할 수 없으면, 클라이언트 라이브러리는 오류를 throw 합니다. 서버 쪽 프로젝션을 수행하는 모든 쿼리에 대해 클라이언트 라이브러리는 선택한 열에 기본적으로 특별한 암호 메타데이터 속성(\_ClientEncryptionMetadata1 및 \_ClientEncryptionMetadata2)을 추가합니다.
>
> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용할 때는 이러한 중요점을 유의하세요.
>
> - 암호화된 blob에서 읽거나 여기에 쓸 때는 전체 blob 업로드 명령 및 범위/전체 blob 다운로드 명령을 사용하세요. 블록 배치, 블록 목록 배치, 페이지 쓰기나 페이지 지우기와 같은 프로토콜 작업을 사용하여 암호화된 blob에 쓰기를 피하십시오. 그렇지 않으면 암호화된 blob이 손상되어 읽지 못하게 될 수 있습니다.
> - 테이블의 경우에는 유사한 제약 조건이 있습니다. 암호화 메타데이터를 업데이트하지 않고 암호화된 속성을 업데이트하지 않도록 주의해야 합니다.
> - 암호화된 blob에서 메타데이터를 설정하는 경우 메타데이터의 설정은 가산적이 아니므로 암호 해독에 필요한 암호화 관련 메타데이터를 덮어쓸 수도 있습니다. 이것은 스냅샷에 대해서 마찬가지입니다. 암호화된 blob의 스냅샷을 생성하는 동안 메타데이터를 지정하지 않도록 하세요. 메타데이터가 설정되어야 하는 경우 먼저 **get_blob_metadata** 메서드를 호출하여 현재 암호화 메타데이터를 가져오고, 메타데이터가 설정되는 동안에는 동시 쓰기를 피합니다.
> - 암호화된 데이터에만 작동해야 하는 사용자의 서비스 개체에 **require_encryption** 플래그를 사용하도록 설정합니다. 자세한 내용은 다음을 참조하세요.

스토리지 클라이언트 라이브러리는 제공된 KEK 및 키 확인자가 다음 인터페이스를 구현할 것으로 예상합니다. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 지원이 보류 중이며, 완료 시 이 라이브러리에 통합될 예정입니다.

## <a name="client-api--interface"></a>클라이언트 API / 인터페이스

스토리지 서비스 개체(예: blockblobservice)가 생성된 후에 사용자는 암호화 정책 key_encryption_key, key_resolver_function 및 require_encryption을 구성하는 필드에 값을 할당할 수 있습니다. 사용자는 KEK만, 확인자만 또는 둘 다를 제공할 수 있습니다. key_encryption_key는 key 래핑/래핑 해제에 대한 논리를 제공하고 키 식별자를 사용하여 식별 되는 기본 키 유형입니다. key_resolver_function은 암호 해독 프로세스에서 키를 해독하기 위해 사용됩니다. 지정된 키 식별자에 대해 유효한 KEK를 반환합니다. 이것은 사용자에게 여러 위치에서 관리되는 여러 키 중 하나를 선택할 수 있게 합니다.

KEK는 데이터를 성공적으로 암호화하기 위해 다음 메서드를 구현해야 합니다.

- wrap_key(cek): 사용자가 선택한 알고리즘을 사용하여 지정된 CEK(바이트)를 래핑합니다. 래핑된 키를 반환합니다.
- get_key_wrap_algorithm(): 키를 래핑하는 데 사용되는 알고리즘을 반환합니다.
- get_kid(): 이 KEK에 대한 문자열 키 ID를 반환합니다.
  KEK는 데이터를 성공적으로 암호 해독하기 위해 다음 메서드를 구현해야 합니다.
- unwrap_key(cek, algorithm): 문자열 지정 알고리즘을 사용하여 지정된 CEK의 래핑 해제된 형식을 반환합니다.
- get_kid(): 이 KEK에 대한 문자열 키 ID를 반환합니다.

키 확인자는 적어도 지정된 키 ID에 대해 위의 인터페이스를 구현하는 해당 KEK를 반환하는 메서드를 구현해야 합니다. 이 메서드만 서비스 개체의 key_resolver_function 속성에 할당되어야 합니다.

- 암호화는 키가 항상 사용되고, 키가 없으면 오류가 발생합니다.
- 암호를 해독하려면

  - 키 확인자는 키를 가져오기 위해 지정된 경우 호출됩니다. 확인자를 지정 하 고 키 식별자에 대한 매핑이 없는 경우, 오류가 전달됩니다.
  - 확인자는 지정하지 않고 키는 지정한 경우 해당 식별자가 필요한 키 식별자와 일치하는 경우 키가 사용됩니다. 식별자가 일치하지 않으면 오류가 throw됩니다.

    azure.storage.samples의 암호화 샘플은 Blob, 큐 및 테이블에 대한 보다 자세한 엔드투엔드 시나리오를 보여 줍니다.
      KEK 및 키 확인자의 샘플 구현은 예제 파일에 각각 KeyWrapper 및 KeyResolver로 제공됩니다.

### <a name="requireencryption-mode"></a>RequireEncryption 모드

사용자는 모든 업로드 및 다운로드를 암호화해야 할 경우 작업 모드를 선택적으로 사용하도록 설정할 수 있습니다. 이 모드에서는 클라이언트에서 암호화 정책 없이 데이터를 업로드하거나 서비스에서 암호화되지 않은 데이터를 다운로드하려고 하면 실패합니다. 서비스 개체에 대한 **require_encryption** 플래그는 이 동작을 제어합니다.

### <a name="blob-service-encryption"></a>Blob service 암호화

blockblobservice 개체에 대해 암호화 정책 필드를 설정합니다. 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

현재 Azure Storage 클라이언트 라이브러리의 버전 12.x를 반영하는 코드 조각을 만드는 작업을 하고 있습니다. 자세한 내용은 [Azure Storage v12 클라이언트 라이브러리 발표](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)를 참조하세요.

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

---

### <a name="queue-service-encryption"></a>큐 서비스 암호화

queueservice 개체에 대해 암호화 정책 필드를 설정합니다. 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

현재 Azure Storage 클라이언트 라이브러리의 버전 12.x를 반영하는 코드 조각을 만드는 작업을 하고 있습니다. 자세한 내용은 [Azure Storage v12 클라이언트 라이브러리 발표](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)를 참조하세요.

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

---

### <a name="table-service-encryption"></a>Table service 암호화

암호화 정책을 생성하고 요청 옵션에 설정하는 것 외에도 **tableservice** 에서 **encryption_resolver_function** 을 지정하거나 EntityProperty에 대해 encrypt 특성을 설정해야 합니다.

### <a name="using-the-resolver"></a>확인자를 사용하여

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

현재 Azure Storage 클라이언트 라이브러리의 버전 12.x를 반영하는 코드 조각을 만드는 작업을 하고 있습니다. 자세한 내용은 [Azure Storage v12 클라이언트 라이브러리 발표](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)를 참조하세요.

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.

def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

---

### <a name="using-attributes"></a>특성 사용

위에서 설명한 것처럼 EntityProperty 개체에 저장하고 암호화 필드를 설정하여 속성을 암호화용으로 표시할 수 있습니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

현재 Azure Storage 클라이언트 라이브러리의 버전 12.x를 반영하는 코드 조각을 만드는 작업을 하고 있습니다. 자세한 내용은 [Azure Storage v12 클라이언트 라이브러리 발표](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)를 참조하세요.

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

---

## <a name="encryption-and-performance"></a>암호화 및 성능

스토리지 데이터를 암호화하면 추가 성능 오버헤드가 발생합니다. 콘텐츠 키 및 IV를 생성해야 하고, 콘텐츠 자체를 암호화해야 하고, 추가 메타데이터의 형식을 지정한 후 업로드해야 합니다. 이 오버헤드는 암호화되는 데이터의 양에 따라 달라집니다. 고객은 항상 개발 중에 애플리케이션 성능을 테스트하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Java PyPi 패키지에 대한 Azure Storage 클라이언트 라이브러리](https://pypi.python.org/pypi/azure-storage)
- [Python 소스 코드용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-python)
