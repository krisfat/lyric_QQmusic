<<<<<<<<<<<<<<head

# 爬取QQ音乐

1. 爬取QQ音乐排行榜的歌词，歌曲名，歌手，专辑等

```
from time import sleep

from selenium import webdriver
from selenium.webdriver.support import expected_conditions as ec
from selenium.webdriver.common.by import By
from selenium.webdriver.support.wait import WebDriverWait
from lxml import etree

browser = webdriver.Chrome()
wait = WebDriverWait(browser, 5)


def get_lyric():
	# 获取歌词
    try:
        wait.until(ec.presence_of_all_elements_located((By.CSS_SELECTOR, '#lrc_content p')))
    except:
        return
    page_source = browser.page_source
    html = etree.HTML(page_source)
    # 获取歌名
    song_name = html.xpath('/html/body/div[2]/div[1]/div/div[1]/h1/@title')[0]
    # 歌曲演唱者
    actor = html.xpath('/html/body/div[2]/div[1]/div/div[2]/a/text()')[0]
    # 歌词
    lyric = html.xpath('//div[@id="lrc_content"]/p/text()')
    # 专辑
    album = html.xpath('/html/body/div[2]/div[1]/div/ul/li[1]/a/text()')[0]
    str = '\n' + song_name + '\n' + actor + '\n' + album
    for p in lyric:
        str += '\n' + p.strip()
    filename = './歌词/' + song_name + '.txt'
    with open(filename, 'w', encoding='utf-8') as f:
        f.write(str)
        print('%s Done!' % song_name)


def get_html():
	# 获取页面的HTML代码 

    browser.get('https://y.qq.com/n/yqq/toplist/55.html#stat=y_new.toplist.menu.55')
    for x in range(30):
    	# 获取首页的三十首歌对应的点击按钮
        buttons = wait.until(ec.presence_of_all_elements_located((By.CSS_SELECTOR, '.songlist__songname_txt')))
        sleep(1)
        # 点击进入歌曲的详情洁面
        buttons[x].click()
        get_lyric()
        sleep(1)
        # 从详情页面返回排行榜页面，toplist是榜单的的编号修改这个可以修改榜单
        browser.execute_script('window.location.href="https://y.qq.com/n/yqq/toplist/55.html#stat=y_new.toplist.menu.55"')
    browser.close()


def main():
    get_html()


if __name__ == '__main__':
    main()

```

