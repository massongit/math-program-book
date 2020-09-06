
公式サイトから取得（2017年秋場所）
http://www.sumo.or.jp/ResultRikishiData/search


# 取得コード
import requests, time, os, pickle, pandas as pd
from bs4 import BeautifulSoup
links = set()
urls = ['http://www.sumo.or.jp/ResultRikishiData/search', 'http://www.sumo.or.jp/ResultRikishiData/search?p=2']
for url in urls:
    r = requests.get(url)
    soup = BeautifulSoup(r.content, 'html.parser')
    for x in soup.find_all('a'):
        if x.get('href').startswith('/ResultRikishiData/profile/?id='):
            links.add(x.get('href'))
    time.sleep(3)
links = list(links)

if not os.path.exists('tmpdata'):
    os.mkdir('tmpdata')
df = []
for link in links:
    id = link[-4:]
    if os.path.exists('tmpdata/' + id):
        with open('tmpdata/' + id, 'rb') as r:
            content = r.read()
    else:
        r = requests.get('http://www.sumo.or.jp/' + link)
        content = r.content
        with open('tmpdata/' + id, 'wb') as w:
            w.write(content)
        time.sleep(10)
    page = BeautifulSoup(content, 'html.parser')
    table = page.find_all("table", { "class" : "mdTable2" })[0]
    result = {}
    for idx, row in enumerate(table.find_all('tr')):
        if idx == 0:
            texts = row.text.strip().split('\n')
            result['名前'] = texts[0].strip()
            result['名前読み'] = texts[1].strip()
            print(link, result['名前'])
        else:
            name = row.find('th').text.strip()
            value = row.find('td').text.strip()
            result[name] = value
    df.append(result)
df = pd.DataFrame(df)
df.to_csv('力士一覧.csv', index=False)

