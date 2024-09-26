## 码流实时监控前端对接展示

以下是一个简单的示例，展示如何使用WebRTC在前端接收和播放实时视频流。

### 1. HTML 部分

```
html复制代码<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebRTC Video Stream</title>
</head>
<body>
    <video id="video" autoplay playsinline></video>
    <script src="script.js"></script>
</body>
</html>
```

### 2. JavaScript 部分 (script.js)

```
javascript复制代码const videoElement = document.getElementById('video');
const peerConnection = new RTCPeerConnection({
    iceServers: [
        { urls: 'stun:stun.l.google.com:19302' } // Google STUN服务器
    ]
});

// 接收视频流
peerConnection.ontrack = (event) => {
    videoElement.srcObject = event.streams[0];
};

// 处理ICE候选
peerConnection.onicecandidate = (event) => {
    if (event.candidate) {
        // 将候选发送到后端
        sendCandidateToServer(event.candidate);
    }
};

// 创建和发送Offer
async function startConnection() {
    const offer = await peerConnection.createOffer();
    await peerConnection.setLocalDescription(offer);
    
    // 将offer发送到后端
    sendOfferToServer(offer);
}

// 接收Answer
function handleAnswer(answer) {
    peerConnection.setRemoteDescription(new RTCSessionDescription(answer));
}

// 接收ICE候选
function handleCandidate(candidate) {
    peerConnection.addIceCandidate(new RTCIceCandidate(candidate));
}

// 这里可以用来模拟发送数据到服务器的函数
function sendOfferToServer(offer) {
    // 通过WebSocket或其他方式发送offer到后端
}

function sendCandidateToServer(candidate) {
    // 通过WebSocket或其他方式发送candidate到后端
}

// 启动连接
startConnection();
```

### 3. 后端代码示例

这里是一个使用Node.js和`ws`库的WebRTC后端示例：

```
javascript复制代码const WebSocket = require('ws');
const { v4: uuidv4 } = require('uuid');

const wss = new WebSocket.Server({ port: 8080 });

const clients = {};

wss.on('connection', (ws) => {
    const id = uuidv4();
    clients[id] = ws;

    ws.on('message', (message) => {
        const parsedMessage = JSON.parse(message);

        switch (parsedMessage.type) {
            case 'offer':
                // 将offer转发给目标客户端
                Object.values(clients).forEach(client => {
                    if (client !== ws) {
                        client.send(JSON.stringify({
                            type: 'offer',
                            offer: parsedMessage.offer
                        }));
                    }
                });
                break;

            case 'answer':
                // 将answer转发给发送offer的客户端
                clients[parsedMessage.to].send(JSON.stringify({
                    type: 'answer',
                    answer: parsedMessage.answer
                }));
                break;

            case 'candidate':
                // 将ICE候选转发给目标客户端
                clients[parsedMessage.to].send(JSON.stringify({
                    type: 'candidate',
                    candidate: parsedMessage.candidate
                }));
                break;
        }
    });

    ws.on('close', () => {
        delete clients[id];
    });
});
```

### 工作流程

1. **建立连接**：前端创建`RTCPeerConnection`，并配置STUN服务器用于NAT穿越。
2. **发送Offer**：前端生成`offer`并发送给后端，后端将其转发给目标客户端。
3. **接收Answer**：目标客户端收到`offer`后，生成`answer`并发送回发起客户端。
4. **ICE候选**：每个客户端在连接过程中会生成ICE候选，并通过后端进行交换。

### 注意事项

- 确保在HTTPS环境下使用WebRTC，或者使用localhost进行开发。
- STUN/TURN服务器配置对于跨网络的连接是必要的。



## WebSocket实时报警数据

每秒向后端请求是否有报警数据，是不是更适合webSocket？

是的，使用WebSocket在这种情况下通常是更合适的选择。以下是一些原因：

### 1. **实时性**：

- WebSocket允许服务器主动向客户端发送数据，这样一旦有报警数据产生，服务器可以立即通知客户端，而不需要客户端每秒去询问。

### 2. **减少带宽消耗**：

- 如果每秒向服务器发送请求，尤其是当没有新的报警数据时，会产生很多不必要的请求和流量。WebSocket可以在连接建立后，保持低开销的通信。

### 3. **降低延迟**：

- WebSocket的持久连接特性使得数据传输延迟更低，适合需要快速响应的实时应用。

### 4. **避免频繁建立连接**：

- 传统的HTTP请求每次都要建立连接，频繁的请求会增加服务器的负担。WebSocket则在连接建立后保持开放。

### 适用场景

如果你的应用需要实时监控报警数据，使用WebSocket会更高效。如果使用WebSocket，你可以在建立连接后等待服务器发送数据，或者使用事件机制来处理收到的数据。