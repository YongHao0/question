#### 撤回消息

```js
1. 收到 RecallCommandMessage
2. 通过 RecallCommandMessage content 获取 messageUid
3. 获取 messageUid 对应的 message(建议收发消息、获取历史消息后缓存 message, 有助于匹配 messageUid 对应 msg, 也提高效率)
4. 调用方法:
var objectName = RongIMClient.MessageParams[message.messageType].objectName; // message 为匹配后的消息, 而不是 RecallCommandMessage
var messageId = message.messageId; // 注意此处为 messageId, 而不是 messageUid
RongIMClient.getInstance().setMessageContent(messageId, message.content, objectName);
5. 重新获取历史消息, 则被撤回的消息已被修改为 RecallCommandMessage (获取历史消息可通过 RecallMsg.content.sentTime 获取)
```

#### 已读未读

```
1. 收到 ReadReceiptMessage
2. 执行:
var type = message.conversationType;
var id = message.targetId;
RongIMClient.getInstance().setMessageStatus(conversationType, targetId, +new Date(), '1', {
	onSuccess: function(){
		console.log('success')
	},
	onError: function(){}
})
3. 重新获取此会话的消息并展示
```

#### 群聊已读人员列表

请求回执消息类型：`ReadReceiptRequestMessage`

示例：

```js
var msg = new RongIMLib.ReadReceiptRequestMessage({
  // messageUId 为消息的唯一标识，通过 message.messageUId 可取到
  messageUId: messageUId
});
// 将 msg 通过 sendMessage 接口发送即可
```

响应回执消息类型：`ReadReceiptResponseMessage`

示例：

```js
var msg = new RongIMLib.ReadReceiptResponseMessage({
  receiptMessageDic: {
    // userId01 为具体的用户 Id， messageUId 为 ReadReceiptRequestMessage 消息中的 messageUId
    userId01: [messageUId]
  }
});
```

备注：

1、请求回执成功后可在 message.receiptResponse 属性查看回执情况

2、群回执存储在 localStorage，若需持久化存储可将群响应回执存储在应用服务器

  存储需可用 `用户 Id`、`messageUId` 做唯一标识，结果为数组

```js
// 示例
// 例如，用户 A (userA) 请求 messageId 为 JDKS-DKSL-FHDK-FSGH 的消息回执
// 用户 B (userB) 和用户 C （userC）阅读了消息
var responses = {
  'userA_JDKS-DKSL-FHDK-FSGH': ["userB", "userC"]
};
```

#### 判断是否为离线消息

```
1. connect 成功后使用一个变量记录连接成功的时间, 比如: connectedTime
2. 收消息, isOfflineMessage = connectedTime > message.sentTime
```

#### 搜索会话

> 方法:

searchConversationByContent

> 结论

无法再修改, 需保持现状
