# Channel Order: Ready Ack Mode

By default, when you subscribe to the `order` channel via WebSocket, you automatically enter the **New Ack** Mode. In this mode, when the order is accepted by the matching engine, you will 
receive an `order` message with status (st) `New`. This is true even when the order will immediately change status. For instance, when you place a Post-only order that crosses the book,
it will immediately be rejected. However, in the **New Ack** Mode, you will first receive an order message with `st=New`, then immediately receive another message with `st=Canceled`. 
However, if your order successfully enters the orderbook without crossing with order on the opposite side, you will only receive one message with `st=New`. This makes it hard for trading
bots to decide when to take actions. 

In this document, we introduce an alternative **Ready Ack** mode, making it easier to implement event handling functions for order status change events.


## Activate the Ack Mode

You shall specify the websocket version by adding an ack field in the auth message when authenticating the websocket, (set `ack=ready` to turn on the ready ack mode):

```json
{
    "op": "auth",
    "id": "abc123",
    "t": 1585438955783,
    "key": "<api-key>",
    "sig": "<signature>",
    "ack": "ready"
}
```

If your request is successful, the server will echo back the ack mode in the response:

```json
{
    "m": "auth",
    "id": "BSpl5qob",
    "code": 0,
    "ack": "ready"
}
```

Once the ack mode is set, it cannot be changed within the same WebSocket session. You must start a new session if you want to change the ack mode.


## New Features when `ack=ready`

### OrderCancelReject

When you set ack mode to ready and your cancel request isn’t successful, you will receive an OrderCancelReject message.

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5447283902,
        "ot": "NULL_VAL",
        "s": "BTC/USDT",
        "t": 1585438957899,
        "orderId": "s17123e1eff1U9698887124T0dYM2YHd",
        "origOrderId": "FAKE-ID",
        "st": "Rejected",
        "err": "OrderCancelReject",
        "text": "TooLateToCancel"
    }
}
```

Please note that in this message, `orderId` is the ID of the cancel request; `origOrderId` is the ID of the order to be canceled.


### Order Status: `Ready` vs `New`

Under the default setting (`ack=New`), the order life cycle consists of five different stages:

* New – the order has reached the Matching engine
* PartiallyFilled
* Filled
* Canceled
* Rejected

You will receive order updates messages whenever the order gets updated throughtout the order's life cycle. For instance, when your order gets
crossed with other orders, you will receive `order` message with status `PartiallyFilled` or `Filled` ; when you successfully canceled your order, you will receive `order` message with status `Canceled`, etc.  

![order-life-cycle-new-ack-mode](/misc/images/order-life-cycle-new-ack-mode.png "Order Life Cycle in the New Ack mode")

In the **Ready Ack** mode (`ack=Ready`), We introduced a new state Ready, which means the order has been accepted by the matching engine as a maker order and is ready to cross with other orders. 
Also, in the Ready Ack mode, the server will no longer send message to client when the order enters the New state. Thus, in the ready ack mode, clients will receive the following messages for an order:

* Ready – the order has been accepted by the matching engine as a maker order.
* PartiallyFilled
* Filled
* Canceled
* Rejected

Please note: the first message following an successfully placed order could be any of the five messages above. For instance, a large buy order with price = `ask1` will first cross with maker orders on `ask1` 
and then becomes a maker order. In this case, the first order message will be `PartiallyFilled`.

![order-life-cycle-ready-ack-mode](/misc/images/order-life-cycle-ready-ack-mode.png "Order Life Cycle in the Ready Ack mode")

Here is a sample Ready message:

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 4724910263,
        "orderId": "s17123e1eff1U9698887124T0dYM2YHd",
        "ot": "Limit",
        "s": "BTC/USDT",
        "t": 1585444943527,
        "p": "9900",
        "q": "0.1",
        "sd": "Buy",
        "st": "Ready",
        "ap": "0",
        "cfq": "0",
        "sp": "",
        "err": "",
        "cf": "0",
        "fa": "USDT",
        "ei": "Post"
    }
}
```

## Sample Order Messages in Ready Ack Mode

### New 

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5744236244,
        "orderId": "s1723274f109U9698887124UYZcCovZR",
        "s": "BTC/USDT",
        "ot": "Limit",
        "t": 1589984424581,
        "p": "0.06",
        "q": "150",
        "sd": "Sell",
        "st": "Ready",
        "ap": "0",
        "cfq": "0",
        "sp": "",
        "err": "",
        "btb": "650",
        "bab": "500",
        "qtb": "100",
        "qab": "100",
        "cf": "0",
        "fa": "USDT",
        "ei": "NULL_VAL"
    }
}
```

### PartiallyFilled

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5744212681,
        "orderId": "s1723271a990U9698887124oF1ZWI2E0",
        "s": "BTC/USDT",
        "ot": "Limit",
        "t": 1589984209677,
        "p": "0.04054",
        "q": "400",
        "sd": "Sell",
        "st": "PartiallyFilled",
        "ap": "0.04054",
        "cfq": "296",
        "sp": "",
        "err": "",
        "btb": "604",
        "bab": "500",
        "qtb": "100",
        "qab": "100",
        "cf": "0.01199984",
        "fa": "USDT",
        "ei": "NULL_VAL"
    }
}
```

### Filled

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5744228002,
        "orderId": "s1723273cc4dU9698887124KmD7JCk9K",
        "s": "BTC/USDT",
        "ot": "Limit",
        "t": 1589984349641,
        "p": "0.04",
        "q": "150",
        "sd": "Sell",
        "st": "Filled",
        "ap": "0.04054",
        "cfq": "150",
        "sp": "",
        "err": "",
        "btb": "500",
        "bab": "500",
        "qtb": "100",
        "qab": "100",
        "cf": "0.006081",
        "fa": "USDT",
        "ei": "NULL_VAL"
    }
}
```

### Canceled 

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5744236665,
        "orderId": "s1723274f109U9698887124UYZcCovZR",
        "s": "BTC/USDT",
        "ot": "Limit",
        "t": 1589984426698,
        "p": "0.06",
        "q": "150",
        "sd": "Sell",
        "st": "Canceled",
        "ap": "0",
        "cfq": "0",
        "sp": "",
        "err": "",
        "btb": "500",
        "bab": "500",
        "qtb": "100",
        "qab": "100",
        "cf": "0",
        "fa": "USDT",
        "ei": "NULL_VAL"
    }
}
```

### Rejected

```json
{
    "m": "order",
    "accountId": "cshQtyfq8XLAA9kcf19h8bXHbAwwoqDo",
    "ac": "CASH",
    "data": {
        "sn": 5744182462,
        "orderId": "s172326e1f85U9698887124qTSAvg1uJ",
        "s": "BTC/USDT",
        "ot": "Limit",
        "t": 1589983977725,
        "p": "0.05",
        "q": "400",
        "sd": "Sell",
        "st": "Rejected",
        "ap": "0",
        "cfq": "0",
        "sp": "",
        "err": "PostOnlyWouldTake",
        "btb": "500",
        "bab": "500",
        "qtb": "100",
        "qab": "100",
        "cf": "0",
        "fa": "USDT",
        "ei": "Post"
    }
}
```
