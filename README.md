import requests
from bs4 import BeautifulSoup
import os

headers = {'User-Agent':"Mozilla/5.0(Windows NT 6.1;WOW64) AppleWebKit/537.1(KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1"}
all_url = 'http://www.mzitu.com/all'
start_html = requests.get(all_url, headers=headers)
Soup = BeautifulSoup(start_html.text,'html.parser')
all_a = Soup.find('div',class_='all').find_all('a')
all_a.pop(0)
for a in all_a:
    href = a['href']
    title = a.get_text()
    html = requests.get(href,headers = headers)
    html_Soup = BeautifulSoup(html.text,'html.parser')
    max_span = html_Soup.find('div',class_='pagenavi').find_all('span')[-2].get_text()
    for page in range(1,int(max_span)+1):
        page_url = href + '/' + str(page)
        img_html = requests.get(page_url,headers = headers)
        img_Soup = BeautifulSoup(img_html.text,'html.parser')
        img_url = img_Soup.find('div',class_='main-image').find('img')['src']
        name = img_url[-9:-4]
        img = requests.get(img_url,headers=headers)
        f = open(name+'.jpg','ab')
        f.write(img.content)
        f.close()
