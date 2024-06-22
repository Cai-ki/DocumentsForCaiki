PyCarbon
=========

`Github 链接 <https://github.com/MiliLong/PyCarbon>`_
*********************************************************

源代码
********

.. code-block:: python

    from selenium import webdriver
    from selenium.webdriver.edge.service import Service
    from selenium.webdriver.common.by import By

    import os
    import time
    import glob
    from urllib import parse

    def get_browser(download_dir):
        download_dir = os.path.abspath(download_dir)
        edge_options = webdriver.EdgeOptions()
        edge_options.add_experimental_option('prefs', {
            "download.default_directory": download_dir, # 设置下载文件的默认目录
            "download.prompt_for_download": False, # 禁用下载提示
            "download.directory_upgrade": True, # 允许下载目录升级，即使指定的下载目录不存在，浏览器也会创建它。
            "safebrowsing.enabled": True # 启用安全浏览功能
        })
        edge_options.add_argument("--log-level=3") # 过滤多余信息   
        driver_path = r'./msedgedriver.exe'
        service = Service(driver_path)
        browser = webdriver.Edge(service=service, options=edge_options)
        browser.minimize_window()
        return browser

    def read_file(file):
        with open(file, 'r') as f:
            text = f.read()
        return text

    def get_files(path):
        files = glob.glob(f'{path}/*')
        for file in files:
            if os.path.isfile:
                yield file

    def encode_text(file):
        text = read_file(file)
        uri_encoded_text = parse.quote_plus(text)
        return uri_encoded_text

    def main():
        with (get_browser(OUTPUT_DIR))as browser:
            for file in get_files(CODE_DIR):
                print(f"{'\033[32m'}Processing '{file}'{'\033[0m'}")
                encoded_text = encode_text(file)
                url = "https://carbon.now.sh?code=" + encoded_text
                browser.get(url)

                time.sleep(2.5)
                browser.find_element(By.XPATH, "//button[@id='export-menu']").click()
                browser.find_element(By.XPATH, "//input[@title='filename']").send_keys(file.split('.')[0].split('\\')[-1])
                browser.find_element(By.XPATH, "//button[contains(text(), '4x')]").click()
                browser.find_element(By.XPATH, f"//button[@id='export-{OUTPUT_DIR}']").click()
                time.sleep(2.5)
            
            time.sleep(2.5)
            print(f"{'\033[32m'}Finished.{'\033[0m'}")   

    if __name__ == '__main__':
        CODE_DIR = 'code'
        OUTPUT_DIR = 'svg' # 'png' or 'svg'
        if os.path.isdir(CODE_DIR):
            main()
        else:
            os.mkdir(CODE_DIR)
            msg = f"{'\033[33m'}'{CODE_DIR}' is created please put files in '{os.path.abspath(CODE_DIR)}'{'\033[0m'}"
            print(msg)

评价
*****

相较于 `requests` 库，`selenium` 库可以动态的爬取网页内容。

其本质上是模拟人的操作行为。

在对动态渲染，或者需要模拟人类操作行为的场景下 `selenium` 库非常好用。