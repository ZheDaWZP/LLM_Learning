# 删除停用词
停用词：过于常见，主要起语法作用的词。比如冠词，连词，介词，代词，助动词等
为什么要删除？
1、语料库中大量存在。删除可以节省资源
2、功能意义大于语义意义。删除不会带来过多负面影响，让模型更多关注文本的核心信息

什么时候不能删除：由具体任务而定，比如情绪分析任务


如何删除停用词，方法介绍
1、自然语言工具包 NLTK（主要支持英文）

```python
import nltk
from nltk.corpus import stopwords

# 获取停用词列表，这些词是全小写的，列表
sw_nltk = stopwords.words('english')
print(sw_nltk)

# new_text = " ".join(words)


# 这个停用词列表更流行
import spacy
en = spacy.load('en_core_web_sm')
sw_spacy = en.Defaults.stop_words # 集合
print(sw_spacy)


# 优势：内存友好不需一次全部加载
import gensim
from gensim.parsing.preprocessing import remove_stopwords, STOPWORDS # 集合

new_text = remove_stopwords(text)

#
from sklearn.feature_extraction.text import ENGLISH_STOP_WORDS # 冻结集合
```

# 词干提取和词形还原
<img width="628" height="72" alt="7b43664d-6752-480d-9b9f-cbfaeebe5348" src="https://github.com/user-attachments/assets/ecd3af38-dc9c-4618-8506-7b5fa42a2792" />

词干提取（Stemming）：去除单词的前后缀得到词根的过程，负责单纯的去除操作，不保证结果的有意义性。主要被用于信息检索领域，粒度较粗
词形还原：处理的是单词的变形形式，不全是简单的在原基础上加了前后缀。负责替换操作。被用于更细粒度的任务
<img width="263" height="214" alt="efa4a8ca-e733-4c94-8434-38705fb0dd27" src="https://github.com/user-attachments/assets/37b9f312-6ed7-4bc0-bf94-eceda6c01c0b" />
<img width="275" height="205" alt="7f6f8aa1-faeb-4251-9719-52b015870997" src="https://github.com/user-attachments/assets/3d8c3d88-2113-46eb-a54f-545b40e141cb" />

为什么要这么做：
当我搜索「play basketball」时，Bob is playing basketball 也符合我的要求，，但是 play 和 playing 对于计算机来说是 2 种完全不同的东西

结果部分交叉。在结果上，词干提取的结果可能不是完整的，具有意义的单词。词形还原的单词是有意义且完整的


词形还原的局限性
1、词表中的未见过的词，比如希望："blockchains" → "blockchain"，就只能用词干提取处理
2、匹配局限性，"runner's shoes"会被替换为"runner"而不是"run"，如果遇上搜索的是"running shoes"会替换为"run shoes"导致匹配失败，这时候词干提取就会使匹配正确。但我认为损失了一部分信息

## 词干提取算法
Snowball
也被称为Porter2算法，
```python
import nltk
from nltk.stem import SnowballStemmer

stemmer = SnowballStemmer("english")

print(stemmer.stem("running"))     # run
print(stemmer.stem("happily"))     # happili
print(stemmer.stem("studies"))     # studi
print(stemmer.stem("better"))      # better
print(stemmer.stem("worst"))       # worst
```

## 词形还原方法
基于词库和词性
基于词性："runner's shoes"会被替换为"runner"而不是"run"

```python
import nltk
from nltk.stem import WordNetLemmatizer

lemmatizer = WordNetLemmatizer()

# 处理单个单词（默认当作名词处理）
# 一般使用前要指定词性
print(lemmatizer.lemmatize("running", pos='v'))  # run 
print(lemmatizer.lemmatize("better", pos='a'))   # good 
print(lemmatizer.lemmatize("studies", pos='n'))  # study 
```

标点符号的去除

将标点符号替换为空格，合并连续的空格为一个，最后去除首尾空格
```python
from string import punctuation as en_punctuation
from zhon.hanzi import punctuation as zh_punctuation
```











