## StreamingHttpResponse

1. Criar view que recebe requisições por streaming
2. Crira função que vai responder ao streaming

`` A comunicação ocorre pelo envio de dados através de arquivos JSON ``

```python
import json
import time

from django.core.serializers.json import DjangoJSONEncoder
from django.http import StreamingHttpResponse
from django.shortcuts import render
from django.views import View

from instagram_clone.models import Like, Post

def event_stream():

    initial_data = ""
    while True:
        data = json.dumps(
            list(
                Post.objects.all().order_by('-id').values(
                    "caption",
                    "created_at",
                    "photo",
                    "user__username"
                )),
            cls=DjangoJSONEncoder
        )

        if not initial_data == data:
            yield "\ndata: {}\n\n".format(data)
            initial_data = data
        time.sleep(1)


class PostStreamView(View):

    def get(self, request):
        response = StreamingHttpResponse(event_stream())
        response['Content-Type'] = 'text/event-stream'
        return response
```

3. Criar url para fazer o caminho da comunicação

```python

path('stream/', PostStreamView.as_view(), name='stream'),

```

4. Javascript para ligar a comunicação e receber os dados

```javascript
<script>
  
  var eventSource  = new EventSource("{% url 'insta:stream' %}");

  eventSource.onopen = function() {
    console.log('Yay! its open?');
  }

  eventSource.onmessage = function(e) {
    console.log(e)
    final_data = JSON.parse(e.data)
    content = "";

    final_data.forEach(function (item){
      content += buildPostContent(item['caption'], item['created_at'], item['photo'], item['user__username'])
    })
      document.getElementById('feed-box').innerHTML = content;
      console.log(content)
  }

  eventSource.onerror = function(e) {
    console.log(`error ${e}`);
  }

  function buildPostContent(caption, created_at, photo, username) {
    content = `Adicione o html aqui`;
	return content

  }

</script
```