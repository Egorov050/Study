Метод GET в библиотеке requests используется для отправки HTTP-запроса на сервер и получения данных.

Для базового запроса : 

```python
import requests
import json 

requests.get('https://api.github.com')

<Response [200]>

```

Про статусы ( [[HTTP-статусы]] )

<h3>Headers</h3>
 HTTP заголовки ответов на запрос могут предоставить определенную полезную информацию. Это может быть тип содержимого ответного пейлоада, а также ограничение по времени для кеширования ответа. Для этого мы обращаемся к headers. 

Там мы можем посмотреть какой формат возвращает нам сервер : 

```python
{'Server': 'GitHub.com', 'Date': 'Mon, 10 Dec 2018 17:49:54 GMT', 'Content-Type': 'application/json; charset=utf-8', 'Transfer-Encoding': 'chunked', 'Status': '200 OK', 'X-RateLimit-Limit': '60', 'X-RateLimit-Remaining': '59', 'X-RateLimit-Reset': '1544467794', 'Cache-Control': 'public, max-age=60, s-maxage=60', 'Vary': 'Accept', 'ETag': 'W/"7dc470913f1fe9bb6c7355b50a0737bc"', 'X-GitHub-Media-Type': 'github.v3; format=json', 'Access-Control-Expose-Headers': 'ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type', 'Access-Control-Allow-Origin': '*', 'Strict-Transport-Security': 'max-age=31536000; includeSubdomains; preload', 'X-Frame-Options': 'deny', 'X-Content-Type-Options': 'nosniff', 'X-XSS-Protection': '1; mode=block', 'Referrer-Policy': 'origin-when-cross-origin, strict-origin-when-cross-origin', 'Content-Security-Policy': "default-src 'none'", 'Content-Encoding': 'gzip', 'X-GitHub-Request-Id': 'E439:4581:CF2351:1CA3E06:5C0EA741'}|
```

Видим `'Content-Type': 'application/json;`



<h3>Params</h3>
Параметр `params` в библиотеке requests используется для добавления query-параметров (параметров URL) к HTTP-запросу. Эти параметры помогают уточнять запрос, например, фильтровать данные или выбирать конкретные результаты, не изменяя основной URL.

Когда params указываются в запросе, они автоматически добавляются в конец URL в виде `key=value пар`, и библиотека requests сама позаботится о правильной кодировке этих параметров.

Например , посмотрим на ссылку : 

```python
https://www.google.com/search?q=корги+&newwindow=1&sca_esv=fc2e42ae252a1d83&ei=2y0uZ9vtKPGJi-gPvOjGkQo&ved=0ahUKEwibx_SZhs2JAxXxxAIHHTy0MaIQ4dUDCA8&uact=5&oq=корги+&gs_lp=Egxnd3Mtd2l6LXNlcnAiC9C60L7RgNCz0LggMgUQABiABDIFEAAYgAQyCBAuGIAEGNQCMgUQABiABDIFEAAYgAQyBRAAGIAEMgUQABiABDIKEAAYgAQYQxiKBTIFEAAYgAQyBRAAGIAESLkkUMsFWJEhcAN4AZABAJgBtQKgAdkSqgEHMC4zLjYuMbgBA8gBAPgBAZgCDaACrhOoAgrCAgoQABiwAxjWBBhHwgINEAAYgAQYsAMYQxiKBcICDhAAGLADGOQCGNYE2AEBwgITEC4YgAQYsAMYQxjIAxiKBdgBAcICExAuGIAEGEMYtAIYigUY6gLYAQLCAhMQABiABBhDGLQCGIoFGOoC2AECwgIKEC4YgAQYQxiKBcICCxAuGIAEGNEDGMcBwgIFEC4YgATCAhkQLhiABBhDGIoFGJcFGNwEGN4EGOAE2AEBwgIOEAAYgAQYARhDGIoFGArCAgkQLhiABBgBGArCAgkQABiABBgBGArCAgwQLhiABBgBGNQCGArCAgcQABiABBgKwgIZEC4YgAQYQxiKBRiXBRjcBBjeBBjfBNgBAcICCxAuGIAEGAEYChgqwgIaEC4YgAQYARgKGCoYlwUY3AQY3gQY3wTYAQHCAhQQLhiABBiXBRjcBBjeBBjfBNgBAZgDBogGAZAGE7oGBggBEAEYCboGBAgCGAeSBwczLjIuNy4xoAf31wE&sclient=gws-wiz-serp
```

После `?` идут параметры. Здесь мы можем видеть , q это query : 

```python
{
 q : 'корги'
 ...... #и т.д 
}
```

По той же самой логике мы можем прописать другие параметры , например : 

```python 

api_key = "3ac.............................."
params = {
    'q' : 'Москва',
    'appid' : api_key,
    'units' : 'metric'
}

response = requests.get('https://api.openweathermap.org/data/2.5/weather', params = params)
```

Узнать о параметрах мы можем в документации того API , который мы хотим использовать. 

И мы можем очень гибко играться с этим. Например , посмотрим на api гитхаба : 

```python 

data = requests.get('https://api.github.com/')
data.json()

# мы получим следующее : 



```