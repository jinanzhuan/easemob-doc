# 翻译

<Toc />

为方便用户在聊天过程中对文字消息进行翻译，环信即时通讯 im_flutter_sdk 集成了 Microsoft Azure Translation API，支持在发送或接收消息时对文本消息进行按需翻译或自动翻译：

- 按需翻译：接收方在收到文本消息后，将消息内容翻译为目标语言。
- 自动翻译：发送方发送消息时，SDK 根据发送方设置的目标语言自动翻译文本内容，然后将消息原文和译文一起发送给接收方。

## 前提条件

开始前，请确保满足以下条件：

1. 完成 `1.0.5 或以上版本` SDK 初始化，详见 [快速开始](quickstart.html)。
2. 了解环信即时通讯 IM API 的 [使用限制](/product/limitation.html)。
3. 已在 [环信即时通讯云控制台](https://console.easemob.com/user/login) 开通翻译功能。
4. 该功能由 Microsoft Azure Translation API 提供，因此开始前请确保你了解该功能支持的目标语言。详见 [翻译语言支持](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support)。

## 技术原理

SDK 支持你通过调用 API 在项目中实现如下功能：

- `fetchSupportedLanguages` 获取支持的翻译语言；
- 按需翻译：接收方在收到文本消息后调用 `translateMessage` 进行翻译；
- 自动翻译：发送方发送消息之前设置 `EMTextMessageBody` 中的 `targetLanguages` 字段为目标语言，然后发送消息，接收方会收到消息原文和译文。

如下为按需翻译示例：

![img](@static/images/ios/translation.png)

## 实现方法

### 获取翻译服务支持的语言

无论是按需翻译还是自动翻译，都需先调用 `fetchSupportedLanguages` 获取支持的翻译语言。获取支持的翻译语言的示例代码如下：

```dart
// 获取支持的翻译语言
try {
  List<EMTranslateLanguage> list =
      await EMClient.getInstance.chatManager.fetchSupportedLanguages();
} on EMError catch (e) {
}
```

### 按需翻译

接收方调用 `translateMessage` 对收到的文本消息进行翻译。翻译调用过程如下：

```dart
// 指定需要翻译的目标语言
List<String> languages = ["en"];
try {
  // 执行消息内容的翻译，`textMessage`：收到的文本消息
  EMMessage translatedMessage = await EMClient.getInstance.chatManager
      .translateMessage(msg: textMessage, languages: languages);
} on EMError catch (e) {
}
```

翻译成功之后，译文信息会保存到消息中。调用 `translations` 获取译文内容。示例代码如下：

```dart
EMTextMessageBody body = translatedMessage.body as EMTextMessageBody;
debugPrint("translation: ${body.translations}");
```

### 自动翻译

创建消息时，发送方设置 `EMTextMessageBody` 中的 `targetLanguages` 字段为译文语言，设置过程如下：

```dart
// 指定翻译的目标语言
EMMessage textMessage = EMMessage.createTxtSendMessage(
  username: targetUser,
  content: content,
  targetLanguages: ["en"],
);
```

发送时消息原文和译文一起发送。

接收方收到消息后，调用 `translations` 获取消息的译文列表，示例代码如下：

```dart
EMTextMessageBody body = receiveMessage.body as EMTextMessageBody;
debugPrint("translation: ${body.translations}");
```

## 参考

### 设置和获取推送的目标语言

设置推送的目标语言，设置之后收到的离线推送就会是目标语言，如果目标语言在消息里不存在，就以原文推送，详见 [设置推送翻译](push.html#_4-3-设置推送翻译)。