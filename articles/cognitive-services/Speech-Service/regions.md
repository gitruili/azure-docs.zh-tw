---
title: 語音服務區域
description: 語音服務區域的參考。
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379565"
---
# <a name="regions-of-the-speech-service"></a>語音服務的區域

語音服務可在不同的區域使用。
當您建立訂用帳戶時，可以根據您的需求，挑選一個可用的區域。

當您使用訂用帳戶時，必須考慮您所挑選的區域。

## <a name="rest-api"></a>REST API

使用 REST API，挑選正確的區域專屬端點。
如需詳細資訊，請參閱 [REST API](rest-apis.md)。

## <a name="speech-sdk"></a>語音 SDK

在[語音 SDK](speech-sdk.md) 中，區域會指定為字串 (例如，在適用於 C# 的語音 SDK 中，作為 [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) \(英文\) 的參數)。

### <a name="regions-for-speech-recognition-and-translation"></a>語音辨識和轉譯的區域

下表列出**語音辨識**和**翻譯**的可用區域：

區域| 語音 SDK 中區域參數的值| 入口網站
-|-
美國西部| `westus`| https://westus.cris.ai
美國西部 2| `westus2`| https://westus2.cris.ai
美國東部| `eastus`| https://eastus.cris.ai
美國東部 2| `eastus2`| https://eastus2.cris.ai
東亞| `eastasia`| https://eastasia.cris.ai
東南亞| `southeastasia`| https://southeastasia.cris.ai
北歐| `northeurope`| https://northeurope.cris.ai
西歐|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>意圖辨識的區域

可透過語音 SDK 用於**意圖辨識**的區域列在 [Language Understanding 服務區域頁面](/azure/cognitive-services/luis/luis-reference-regions)中。
列出的每個發佈區域會將對應的語音 SDK 區域參數判定為端點網域名稱的第一個部分。
例如，使用 `westus` 指定美國西部發佈區域。
