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

# мы получим следующий словарь : 

{'current_user_url': '[https://api.github.com/user](https://api.github.com/user)',
 'current_user_authorizations_html_url': '[https://github.com/settings/connections/applications{/client_id](https://github.com/settings/connections/applications%7B/client_id)}',
 'authorizations_url': '[https://api.github.com/authorizations](https://api.github.com/authorizations)',
 'code_search_url': '[https://api.github.com/search/code?q={query}{&page,per_page,sort,order](https://api.github.com/search/code?q={query}{&page,per_page,sort,order)}',
 'commit_search_url': '[https://api.github.com/search/commits?q={query}{&page,per_page,sort,order](https://api.github.com/search/commits?q={query}{&page,per_page,sort,order)}',
 'emails_url': '[https://api.github.com/user/emails](https://api.github.com/user/emails)',
 'emojis_url': '[https://api.github.com/emojis](https://api.github.com/emojis)',
 'events_url': '[https://api.github.com/events](https://api.github.com/events)',
 'feeds_url': '[https://api.github.com/feeds](https://api.github.com/feeds)',
 'followers_url': '[https://api.github.com/user/followers](https://api.github.com/user/followers)',
 'following_url': '[https://api.github.com/user/following{/target](https://api.github.com/user/following%7B/target)}',
 'gists_url': '[https://api.github.com/gists{/gist_id](https://api.github.com/gists%7B/gist_id)}',
 'hub_url': '[https://api.github.com/hub](https://api.github.com/hub)',
 'issue_search_url': '[https://api.github.com/search/issues?q={query}{&page,per_page,sort,order](https://api.github.com/search/issues?q={query}{&page,per_page,sort,order)}',
 'issues_url': '[https://api.github.com/issues](https://api.github.com/issues)',
 'keys_url': '[https://api.github.com/user/keys](https://api.github.com/user/keys)',
 'label_search_url': '[https://api.github.com/search/labels?q={query}&repository_id={repository_id}{&page,per_page](https://api.github.com/search/labels?q={query}&repository_id={repository_id}{&page,per_page)}',
 'notifications_url': '[https://api.github.com/notifications](https://api.github.com/notifications)',
 'organization_url': '[https://api.github.com/orgs/{org](https://api.github.com/orgs/%7Borg)}',
 'organization_repositories_url': '[https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort](https://api.github.com/orgs/%7Borg%7D/repos%7B?type,page,per_page,sort)}',
 'organization_teams_url': '[https://api.github.com/orgs/{org}/teams](https://api.github.com/orgs/%7Borg%7D/teams)',
 'public_gists_url': '[https://api.github.com/gists/public](https://api.github.com/gists/public)',
 'rate_limit_url': '[https://api.github.com/rate_limit](https://api.github.com/rate_limit)',
 'repository_url': '[https://api.github.com/repos/{owner}/{repo](https://api.github.com/repos/%7Bowner%7D/%7Brepo)}',
 'repository_search_url': '[https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order](https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order)}',
 'current_user_repositories_url': '[https://api.github.com/user/repos{?type,page,per_page,sort](https://api.github.com/user/repos%7B?type,page,per_page,sort)}',
 'starred_url': '[https://api.github.com/user/starred{/owner}{/repo](https://api.github.com/user/starred%7B/owner%7D%7B/repo)}',
 'starred_gists_url': '[https://api.github.com/gists/starred](https://api.github.com/gists/starred)',
 'topic_search_url': '[https://api.github.com/search/topics?q={query}{&page,per_page](https://api.github.com/search/topics?q={query}{&page,per_page)}',
 'user_url': '[https://api.github.com/users/{user](https://api.github.com/users/%7Buser)}',
 'user_organizations_url': '[https://api.github.com/user/orgs](https://api.github.com/user/orgs)',
 'user_repositories_url': '[https://api.github.com/users/{user}/repos{?type,page,per_page,sort](https://api.github.com/users/%7Buser%7D/repos%7B?type,page,per_page,sort)}',
 'user_search_url': '[https://api.github.com/search/users?q={query}{&page,per_page,sort,order](https://api.github.com/search/users?q={query}{&page,per_page,sort,order)}'}

```

Отсюда мы можем взять любую апишку и играть с ней как хотим. Посмотрим например страницу поисковика гитхаба. Любой запрос будет выглядеть так : 
`https://github.com/search?q=c++&type=repositories` 

Соответственно мы можем по этой апишке : 

```python
requests.get('https://api.github.com/search/repositories' , params = params)
```

и со следующими параметрами : 

```python 
params = {
    'q' : 'java',
    'per_page': 100,  
    'page': 1
}
```

мы сможем по факту делать следующий запрос : 
`https://github.com/search?q=java&type=repositories` 

Также выше мы используем параметры `page` , `per_page`. Для чего это делается? Если мы бы не использовали это , то нам бы просто бы выдалось 30-40 юнитов (блоков) , так как  результаты пагинируются. А так : 
- `per_page`: 100 — задает количество результатов, которое нужно вернуть на одной странице. В данном случае API вернет до 100 результатов на каждой странице.
- ``

