# 分词算法
## Byte-Pair-Encoding (BPE)
<strong>核心思想</strong>：将词元进行配对，统计频次最高的配对组合，将其作为新的词元添入词表<br>
<strong>应用模型</strong>：GPT-2、GPT-3、LLaMA、RoBERTa、BART<br>
### :v:算法相关

<strong>初始词表</strong>：词元均为单个字符<br>
<strong>步骤</strong>：<br>
<ol>
<li>将语料库划分为单个单词，统计单词出现频次</li>
<li>将单词根据现词表拆分为多个词元，统计词元对频次，将最高频次的组合添入词表。并用新的词元替换部分原划分。如下</li>
<img width="865" height="351" alt="image" src="https://github.com/user-attachments/assets/60032e38-2d99-49fb-9ee1-35aebb65353b" />
<li>重复以上步骤，直到达到预设词表大小/合并次数或合并不再有意义</li>
</ol>

```python
# 初始词表
0:'a'
1:'b'
2:'+'
...
10:'r'
# 更新一次后
11:'a+'
```

<strong>其他</strong>：<br>
<ul>
<li>集外词（OOV）字符会被标记为[UNK]</li>
<li>用&lt;/w&gt;表示词边界。（1）可以引入部分语境信息：比如"t&lt;/w&gt;"和"t"分别表示单词末尾和非末尾的字符t。同理，这样就可以将有意义的单词后缀与中间位置的同组合区别开来。（2）提供了边界信息</li>
</ul>
<br>

## Byte-level BPE (BBPE)
<strong>核心思想</strong>：词元最小单位是字节，而不是字符<br>
### UTF-8
一种可变长度的字符编码方案，专门用于编码**Unicode**（一种字符集，为每一个字符分配了一个唯一的标识，也叫代码点）字符集，将每一个字符的唯一标识编码为1-4个字节长度的字节序列<br><br>
<strong>编码规则</strong>：
| 代码点范围               | 编码后序列长度                               | 编码序列格式 | 其他 |
|------------------       |----|--------                                  |------|
| **U+0000到U+007F**     |1字节| `0xxxxxxx`                             | 完全兼容ASCII |
| **U+0080到U+07FF**     |2字节| `110xxxxx 10xxxxxx`                    | |
| **U+0800到U+FFFF**     |3字节| `1110xxxx 10xxxxxx 10xxxxxx`           | |
| **U+10000到U+10FFFF**  |4字节| `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx`  |  |

**例如**：
字符'你'的Unicode码是U+4F60，二进制形式为0100 1111 0110 0000，对照3字节格式是：1110`0100` 10`111101` 10`100000`，转换为16进制为：0xe4bda0

### :v:算法相关
<strong>初始词表</strong>：均为单个字节，一个字节由8位二进制数表示，故BBPE初始词表大小为256<br>
<strong>步骤</strong>：与BPE基本相同，只是词元表示形式从字符变为了字节，以及初始词表大小固定为了256<br>

```python
# 初始词表
0: b'\x00'
1: b'\x01'
2: b'\x02'
...
97: b'\x61' 
98: b'\x62'      
99: b'\x63'    
...
255: b'\xff'
# 更新几次后
257: b'\xe4\xbd\xa0'
259: b'\xe5\xa5\xbd'
260: b'\xe4\xbd\xa0\xe5\xa5\xbd'
```

<br>

## WordPiece
<strong>核心思想</strong>：类似于BPE，最大区别在于合并规则，根据合并分数来合并<br>
<strong>应用模型</strong>：BERT、ALBERT<br>


### :v:算法相关
<strong>初始词表</strong>：与BPE类似<br>
<strong>步骤</strong>：<br>
<ol>
<li>统计现词元和合并对的频次</li>
<li>计算合并分数： $$\frac{合并对的频次}{词元1的频次\times词元2的频次}$$ </li>
<li>将分数最高的组合合并，添入词表。重复以上步骤</li>
</ol>
<strong>其他</strong>：<br>
<ul>
<li>##前缀：初始词表中词元均无此前缀，在训练过程中如果发现合并对位于中间，则加上前缀，如变成"##ing"</li>
</ul>
