---
layout: post
title: BertModel使用
date: 2021-03-20 17:00:00 +0800
Author: 杨舒文	
categories: [NLP, transformers] 
tags: [NLP, bert]
comments: true
---

## BertModel解读

Hugging Face的[transformers](https://huggingface.co/transformers/)模块中BertModel主要组成：

- 基本模型和配置

  - BertModel
  - BertConfig

- Tokenizer

  - BertTokenizer
  - BasicTokenizer
  - WordpieceTokenizer
  - BertTokenizerFast

- 预训练模型

  - BertForPreTraining
  - BertForMaskedLM
  - BertForNextSentencePrediction

- 针对特定任务进行 fine-tune 的模型，在BertModel基础上添加了针对特定任务的网络层

  - BertForQuestionAnswering：问答系统
  - BertForTokenClassification：对每个 token 做分类
  - BertForMultipleChoice：对本文做多选任务
  - BertForSequenceClassification：文本分类

  ```
  而关于训练好的模型的选择，常用的有如下这些（下面这些模型，没有在特定任务上进行 fine-tune）：
  
  bert-base-chinese：基于中文
  bert-base-uncased：基于英语，不区分大小写
  bert-base-cased：基于英语，区分大小写
  bert-base-german-cased：基于德语，区分大小写
  bert-base-multilingual-uncased：多语言，不区分大小写
  bert-base-multilingual-cased：多语言，区分大小写
  bert-large-cased：基于英语，更加大型的 Bert，区分大小写
  bert-large-uncased：基于英语，更加大型的 Bert，不区分大小写
  其中用的较多的是：bert-base-uncased。
  
  更多模型的选择，你可以查看 https://huggingface.co/。
  ```

### 使用示例

#### BertModel

> 1. 先经过`tokenizer`，返回`input_ids`， `token_type_ids`， `attention_mask`
>2. 将1的结果传入`model`，返回`last_hidden_state`,`pooler_output`,`(hidden_states)`,`(attentions)`

  ```python
from transformers import BertTokenizer,BertModel
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained("bert-base-uncased")
text="Hello, my dog is cute"
encoded_input = tokenizer(text, max_length=100,
                          add_special_tokens=True, truncation=True,
                          padding=True, return_tensors="pt")
output = model(**encoded_input)
last_hidden_state, pooler_output = output[0], output[1]
  ```

  也可以加载自己预训练的模型

  ```python
# 文件路径包含三个文件config.json、vocab.txt、model.bin
bert_path = 'bert/bert-mini'
  
from transformers import BertTokenizer,BertConfig,BertForSequenceClassification
# 定义 tokenizer，传入词汇表
tokenizer = BertTokenizer(bert_path)
# 定义配置，加载模型
config = BertConfig.from_pretrained(bert_path,num_labels=num_labels, hidden_dropout_prob=hidden_dropout_prob)
model = BertForSequenceClassification.from_pretrained(bert_path, config=config)
  ```

#### BertForSequenceClassification

  ```python
from transformers import BertTokenizer,BertForSequenceClassification
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertForSequenceClassification.from_pretrained("bert-base-uncased")
text="Hello, my dog is cute"
encoded_input = tokenizer(text, max_length=100,
                          add_special_tokens=True, truncation=True,
                          padding=True, return_tensors="pt")
labels = torch.tensor([1]).unsqueeze(0)
output = model(**encoded_input, labels=labels)
loss, logits = outputs[:2]
  ```

## 参数细节

### 1. BertModel forward

输入：
  > input_ids：输入序列
  > attention_mask：输入序列对应mask
  > token_type_ids：类型序列
  > position_ids：位置序列
  > head_mask：head mask
  > inputs_embeds：输入序列embeddings，与input_ids二选一
  > encoder_hidden_states：当BertConfig的is_decoder=True时需输入的参数，表示编码器的输出
  > encoder_attention_mask：解码器输入序列对应的mask
  > output_attentions：是否返回attention，默认None
  > output_hidden_states：是否返回hidden_states，默认None

返回：
  > last_hidden_state
  > pooler_output
  > (hidden_states)
  > (attentions)

### 2. BertConfig初始化

> vocab_size：词表大小，默认30522
> hidden_size：隐层维度，默认768
> num_hidden_layers：隐层个数，默认12
> num_attention_heads：encoder中attention层head数，默认12

### 3. BertTokenizer初始化

> vocab_file：包含词汇表的文件路径
> do_lower_case：是否转换为小写，默认为 True
> do_basic_tokenize：在使用 `WordpieceTokenizer`之前，是否先使用 `BasicTokenizer`，默认为 True
> never_split：当 `do_basic_tokenize=True` 时才生效。一个列表，列表里面出现的词不会进行 tokenization
> tokenize_chinese_chars：是否对中文的字前后添加空格，默认为 True。当对日文进行 tokenization 时，这个值要设置为 False

- **BasicTokenizer**

  `BasicTokenizer` 的作用是：根据空格分割词汇和符号。

   `tokenize()` 方法，流程如下：

  - 当`tokenize_chinese_chars=True`，会首先在中文的每个字前后增加空格。
  - 调用`whitespace_tokenize()`，通过空格分割单词、符号，以及中文的字
  - 然后调用`_run_strip_accents()`，从单词中去除重音字母
  - 最后再调用`whitespace_tokenize()` 分割单词、符号，以及中文的字，返回结果

- **WordpieceTokenizer**

  `WordpieceTokenizer` 的作用是：对每个单词进行 WordPiece tokenization。

  `tokenize()` 方法流程如下：

  - 首先判断每个单词的长度，如果单词的字母数量超过 max_input_chars_per_word，就不进行 WordPiece tokenization，而是用 unk_token 表示这个单词
  - 然后使用贪婪最长匹配优先算法，找出每一个 piece
  - 最后把 piece 添加到输出的列表中
  
  
  
  
  
  