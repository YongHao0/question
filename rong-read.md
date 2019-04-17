## 群消息已读列表

`注:` 已文本消息为例, 发送方为 A, 接收方为 B, A、B 所在群组为 G

1、A 向群组 G 发送文本消息, 发送成功后接着向群组 G 发送 ReadReceiptRequestMessage 消息

```js
// 此处 message 为发送文本消息后, onSuccess 返回的 message 对象
var msg = new RongIMLib.ReadReceiptRequestMessage({
  // messageUId 为消息的唯一标识，通过 message.messageUId 可取到
  messageUId: messageUId
});
```

2、使用 localStorage 存储 Key 为 messageUid 的值, 用来记录该消息的已读人 id, 格式如:

```js
// 例如 messageUid 为 BA4J-8928-MJQ1-3417, 存储格式为:
{
  'BA4J-8928-MJQ1-3417': []
}
```

3、接收到的消息都记录一个是否已读的状态. 当 UI 层读该消息时, 将该状态置为 true, 否则为 false

4、接收方 B 收到 ReadReceiptRequestMessage, 如果此时该消息已读, 则向 G 发送 ReadReceiptResponseMessage. 如果此时该消息未读, 当读此消息时, 发送 ReadReceiptResponseMessage 消息

```js
// ReadReceiptResponseMessage 格式:
// 例如 A id 为 'a', 已读的文本消息 id 为 BA4J-8928-MJQ1-3417
var msg = new RongIMLib.ReadReceiptResponseMessage({ 
  receiptMessageDic: {
    'a': [ 'BA4J-8928-MJQ1-3417' ]
  } 
});
```

5、发送方 A 收到 ReadReceiptResponseMessage 后

```js
(1) 先判断 receiptMessageDic 的 key 是否为自己的 id
(2) 如果 key 为自己的 id, 取出对应的 消息 Uid
(3) 通过 message.senderUserId 获取是谁读的这条消息
(4) 存储 localStorage, 根据 messageUid, 存储 senderUserId

// 例如 收到的 ReadReceiptResponseMessage 为步骤 4 中的字段, B 的 id 为 'b', 则存储结果为
{
  BA4J-8928-MJQ1-3417: [ 'b' ]
}
```

6、当又一接收者 C 发送了 ReadReceiptResponseMessage 消息, 则 ReadReceiptResponseMessage 消息格式和最终存储 localStorage 为:

```js
// ReadReceiptResponseMessage 字段:
var msg = new RongIMLib.ReadReceiptResponseMessage({ 
  receiptMessageDic: {
    'c': [ 'BA4J-8928-MJQ1-3417' ]
  } 
});

// 最终 localStroage 相关存储:
{
  BA4J-8928-MJQ1-3417: [ 'b', 'c' ]
}
```

7、获取消息时, 先取对应 message.messageUId 下 localStorage 的已读人员 id 列表, 再展示