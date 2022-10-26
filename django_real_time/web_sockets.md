## Websockets

WebSockets é uma tecnologia avançada que torna possível abrir uma sessão de comunicação interativa entre o navegador do usuário e um servidor. Com esta API, você pode enviar mensagens para um servidor e receber respostas orientadas a eventos sem ter que consultar o servidor para obter uma resposta.

* Protocolo bi-direcional
* Comunicação full-duplex
* Secure WebSockets (WSS)

### Channels

https://channels.readthedocs.io/en/stable/

### Criando uma conexão simples com websockets

1. Dentro do template: Javascript para fazer estabelecer a conexão com o socket

```javascript
<script type="text/javascript">
    let url = `ws://${window.location.host}/ws/socket-server/`

    const chatSocket = new WebSocket(url)

    chatSocket.onmessage = function(e){
        let data = JSON.parse(e.data)
        console.log('Data:', data)

        if(data.type === 'chat'){
            let messages = document.getElementById('messages')

            messages.insertAdjacentHTML('beforeend', `<div>
                                    <p>${data.message}</p>
                                </div>`)
        }
    }

    let form = document.getElementById('form')
    form.addEventListener('submit', (e)=> {
        e.preventDefault()
        let message = e.target.message.value 
        let message2 = e.target.message2.value 
        chatSocket.send(JSON.stringify({
            'message':message,
            'message2':message2,
        }))
        form.reset()
    })

</script>
```

2. Dentro do app, configurar arquivo consumers.py
```python
import json
from channels.generic.websocket import WebsocketConsumer
from asgiref.sync import async_to_sync

class ChatConsumer(WebsocketConsumer):
    def connect(self):
        self.room_group_name = 'test'

        async_to_sync(self.channel_layer.group_add)(
            self.room_group_name,
            self.channel_name
        )

        self.accept()
   

    def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']
        message2 = text_data_json['message2']

        async_to_sync(self.channel_layer.group_send)(
            self.room_group_name,
            {
                'type':'chat_message',
                'message':message,
                'message2':message2,
            }
        )

    def chat_message(self, event):
        message = event['message']
        message2 = event['message2']

        self.send(text_data=json.dumps({
            'type':'chat',
            'message':message,
            'message2':message2,
        }))
```

3. Ainda dentro do app, configurar arquivo routing.py

```python
from django.urls import re_path 
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/socket-server/', consumers.ChatConsumer.as_asgi())
]
```

4. No arquivo settings.py 

* Colocar channels nos apps instalados
```python
INSTALLED_APPS = [
    'channels',
    ...
```

* Definir variável de ambiente do ASGI
```python
ASGI_APPLICATION = 'mywebsite.asgi.application'
```

* Em seguida definir as camadas do channels
```python
CHANNEL_LAYERS = {
    'default':{
        'BACKEND':'channels.layers.InMemoryChannelLayer'
    }
}
```

5. No arquivo asgi dentro do núcleo do projeto configurar o channel
```python
import os

from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import chat.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mywebsite.settings')

application = ProtocolTypeRouter({
    'http':get_asgi_application(),
    'websocket':AuthMiddlewareStack(
        URLRouter(
            chat.routing.websocket_urlpatterns
        )
    )
})
```