初识Python并行编程
=====================

    ::

        咸鱼上接了一单，任务就是统计某个文件夹里的所有txt文件，获取某些信息然后统一放到excel里。
        这并不难。
        但是，我不满意。为什么呢？太慢了，由于需要大规模读取文件，io压力比较大，同时遍历文件内容也是很cost的，我们认为这玩意既是io密集型也是cpu密集型。
        看了看文件数量`45988`真的蛮多的，这些玩意加起来都1GB了。
        实际上对于各个文件处理直接是没有联系的，那么我们为什么不采取并行编程呢？
        说干就干！
    
    ::

        在这之前先展示一下我们的暴力代码。

    ::

        使用`pandas`写入`excel`就不用说了，常规操作罢了。
        看下面几个函数。

    ::

        `remove_punctuation`
        这个函数是为了移除字符串中的标点符号和特殊字符，只保留字母、数字以及中日韩文字。
        为了使用正则表达式这里我们要`import re`，'[^a-zA-Z0-9\u4e00-\u9fff]'这个正则表达式模式匹配所有不是字母、数字或者中日韩统一表意文字的字符。
        我们通过`sub`方法把这个正则表达式模式匹配到的内容替换为空就行了。

    ::

        `count_characters_in_file`我不解释了，很简单。

    ::

        `process_files`函数就是处理文件的整体逻辑，在这里为了可视化进度，我们使用了`tqdm`。

    .. code:: python

        import os
        import re
        from tqdm import tqdm
        import pandas as pd

        def remove_punctuation(text):
            pattern = r'[^a-zA-Z0-9\u4e00-\u9fff]'
            cleaned_text = re.sub(pattern, '', text)
            return cleaned_text

        def count_characters_in_file(file_path):
            with open(file_path, 'r', encoding='utf-8') as file:
                content = file.read()
            return len(remove_punctuation(content))

        def process_files(directory):
            dict = {
                '股票代码' : [],
                '年份' : [],
                '字数' : [],
            }

            progress_bar = tqdm([file for file in os.listdir(directory) if file.endswith('.txt')])
            for file in progress_bar:
                progress_bar.set_description(f"Processing : {file}")
                tokens = file.split('_')
                dict['股票代码'].append(tokens[0])
                dict['年份'].append(tokens[1])
                dict['字数'].append(count_characters_in_file(os.path.join(directory, file)))

            return dict

        directory = r'./年报（08-22）'

        df = pd.DataFrame(process_files(directory))

        output_file = './output.xlsx'
        df.to_excel(output_file, index=False)

        print(f'DataFrame 已成功写入 {output_file}')

    ::

        接下来，我要开始大展拳脚了。
        我会更改上方代码，在业务逻辑不变的情况下使用Python的`concurrent.futures`模块。
    
    ::

        这里就不进行代码讲解了，毕竟我也是比葫芦画瓢弄的，不过代码的思路很清晰哦。

    ::

        性能提升很显著，在最大线程数为10的情况下，性能峰值提升了15倍，之后会降低到5倍。
        我跑代码的机子很烂，内存也很少，cpu和内存跑满了。
        性能损失是可以预见的，但如果cpu性能足够，内存也够多，咱再开大点最大线程数，这提升的性能真的会很恐怖。
    
    ::

        最后评价一下我的工作，完美符号预期！

    ::

        哦对了，因为处理数据很大，而且还要照顾人家的隐私，这里就不提供了。
        不过作为代码思路参考，还是很合适的^_^。

    .. code:: python

        import os
        import re
        from tqdm import tqdm
        import pandas as pd
        import concurrent.futures

        def remove_punctuation(text):
            pattern = r'[^a-zA-Z0-9\u4e00-\u9fff]'
            cleaned_text = re.sub(pattern, '', text)
            return cleaned_text

        def count_characters_in_file(file_path):
            with open(file_path, 'r', encoding='utf-8') as file:
                content = file.read()
            return len(remove_punctuation(content))

        def process_file(file):
            file_path = os.path.join(directory, file)
            tokens = file.split('_')
            stock_code = tokens[0]
            year = tokens[1]
            char_count = count_characters_in_file(file_path)
            return {'股票代码': stock_code, '年份': year, '字数': char_count}

        def process_files(directory):
            dict_list = []
            files = [file for file in os.listdir(directory) if file.endswith('.txt')]

            with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
                futures = [executor.submit(process_file, file) for file in files]
                progress_bar = tqdm(concurrent.futures.as_completed(futures), total=len(files), colour='red')
                for future in progress_bar:
                    dict_list.append(future.result())

            dict_list = sorted(dict_list, key=lambda x: (x['股票代码'], x['年份']))

            return dict_list

        directory = r'./年报（08-22）'
        df = pd.DataFrame(process_files(directory))

        output_file = './output.xlsx'
        df.to_excel(output_file, index=False)

        print(f'DataFrame 已成功写入 {output_file}')