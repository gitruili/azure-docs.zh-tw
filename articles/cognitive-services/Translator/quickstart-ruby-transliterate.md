---
title: 翻譯工具文字搭配 Ruby 轉換文字腳本 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您可以使用認知服務的翻譯工具文字 API 搭配 Ruby，將文字轉換成另一種語言的腳本。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 56872ed6b6d7168fb1bf2c45de8104a592af34e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "43750236"
---
# <a name="quickstart-transliterate-text-with-ruby"></a>快速入門：使用 Ruby 來音譯文字

在本快速入門中，您可以使用翻譯工具文字 API，將文字轉換成另一種語言的腳本。

## <a name="prerequisites"></a>必要條件

您將需要有 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) (英文) 或更新版本，才能執行此程式碼。

若要使用翻譯工具文字 API，您也需要有訂用帳戶金鑰；請參閱[如何註冊翻譯工具文字 API](translator-text-how-to-signup.md)。

## <a name="transliterate-request"></a>Transliterate 要求

下列程式碼會使用 [Transliterate](./reference/v3-0-transliterate.md) 方法，將文字轉換成另一種語言的腳本。

1. 在您慣用的程式碼編輯器中，建立新的 Ruby 專案。
2. 新增下方提供的程式碼。
3. 以訂用帳戶有效的存取金鑰來取代 `key` 值。
4. 執行程式。

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = "&language=ja&fromScript=jpan&toScript=latn";

uri = URI (host + path + params)

# Transliterate "good afternoon".
text = 'こんにちは'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="transliterate-response"></a>Transliterate 回應

成功的回應會以 JSON 格式傳回，如下列範例所示：

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括翻譯和語言識別，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Ruby 範例](https://aka.ms/TranslatorGitHub?type=&language=ruby) (英文)
