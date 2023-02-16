# Contrastive Search Is What You Need For Neural Text Generation
**Authors**: Yixuan Su and Nigel Collier

**[Contact]** If you have any questions, feel free to contact me via (ys484 at cam.ac.uk).

This repository contains code other related resources of our paper ["Contrastive Search Is What You Need For Neural Text Generation"](https://arxiv.org/abs/2210.14140).

:star2: Check out this awesome [[demo]](https://huggingface.co/spaces/joaogante/contrastive_search_generation) generously supported by Huggingface ([@huggingface](https://github.com/huggingface) :hugs:) which compares contrastive search with other popular decoding methods. Many thanks to Huggingface :hugs:! 

In <a href='#install_transformers'>Section 2.1.</a>, we demonstrate how to use contrastive search in Huggingface `transformers`.

****
If you find our paper and resources useful, please kindly leave a star and cite our papers. Thanks!

```bibtex
@article{su2022contrastiveiswhatyouneed,
title={Contrastive Search Is What You Need For Neural Text Generation},
author={Yixuan Su and Nigel Collier},
journal={In Transactions on Machine Learning Research},
year={2023},
url={https://openreview.net/forum?id=GbkWw3jwL9}
}

@inproceedings{su2022a,
  title={A Contrastive Framework for Neural Text Generation},
  author={Yixuan Su and Tian Lan and Yan Wang and Dani Yogatama and Lingpeng Kong and Nigel Collier},
  booktitle={Advances in Neural Information Processing Systems},
  editor={Alice H. Oh and Alekh Agarwal and Danielle Belgrave and Kyunghyun Cho},
  year={2022},
  url={https://openreview.net/forum?id=V88BafmH9Pj}
}
```

****

### News:
* [2023/02/14] Our manuscript is accepted to TMLR'23! [[OpenReview Link]](https://openreview.net/forum?id=GbkWw3jwL9)
* [2022/11/22] Released a technical report that compares Contrastive Search with another recently proposed method, i.e. Contrastive Decoding. Check out our [[paper]](https://arxiv.org/abs/2211.10797) and [[code]](https://github.com/yxuansu/Contrastive_Search_versus_Contrastive_Decoding).
* [2022/10/26] The paper "Contrastive Search Is What You Need For Neural Text Generation" is publicly released!

****

<span id='all_catelogue'/>

### Catalogue:
* <a href='#introduction'>1. Introduction</a>
* <a href='#reproduce_examples'>2. Reproducing Examples Provided in the Paper</a>
    * <a href='#use_transformers'>2.1. Using Huggingface Transformers</a>
        * <a href='#install_transformers'>2.1.1. Install Transformers Package</a>
        * <a href='#transformers_table_1'>2.1.2. Example in Table 1</a>
        * <a href='#transformers_table_8'>2.1.3. Example in Table 8 at Appendix A</a>
        * <a href='#transformers_table_9'>2.1.4. Example in Table 9 at Appendix A</a>
    * <a href='#use_simctg'>2.2. Using SimCTG Package</a>
        * <a href='#install_simctg'>2.2.1. Install SimCTG Package</a>
        * <a href='#simctg_table_1'>2.2.2. Example in Table 1</a>
        * <a href='#simctg_table_8'>2.2.3. Example in Table 8 at Appendix A</a>
        * <a href='#simctg_table_9'>2.2.4. Example in Table 9 at Appendix A</a>
* <a href='#LMs_isotropy'>3. Measuring Isotropy of LMs</a>
* <a href='#open_ended_text_generation'>4. Open-ended Text Generation</a>
* <a href='#code_generation'>5. Code Generation</a>
* <a href='#machine_translation'>6. Machine Translation</a>
    
****

<span id='introduction'/>

#### 1. Introduction: <a href='#all_catelogue'>[Back to Top]</a>

Generating text with autoregressive language models (LMs) is of great importance to many natural language processing (NLP) applications. Previous solutions for this task often produce text that contains degenerative expressions or lacks semantic consistency. Recently, Su et al. introduced a new decoding method, contrastive search, based on the isotropic representation space of the language model and obtained new state of the art on various benchmarks. Additionally, Su et al. argued that the representations of autoregressive LMs (e.g. GPT-2) are intrinsically anisotropic which is also shared by previous study. Therefore, to ensure the language model follows an isotropic distribution, Su et al. proposed a contrastive learning scheme, SimCTG, which calibrates the language model's representations through additional training.

In this study, we first answer the question: _"Are autoregressive LMs really anisotropic?"_. To this end, we extensively evaluate the isotropy of LMs across 16 major languages. Surprisingly, we find that the anisotropic problem only exists in the two specific English GPT-2-small/medium models. On the other hand, all other evaluated LMs are naturally isotropic which is in contrast to the conclusion drawn by previous studies. Based on our finding, we further assess the contrastive search decoding method using off-the-shelf LMs on four generation tasks across 16 languages. Our experimental results demonstrate that contrastive search significantly outperforms previous decoding methods without any additional training. More notably, on 12 out of 16 evaluated languages, contrastive search performs comparably with human-level performances as judged by human evaluations.

****


<span id='reproduce_examples'/>


#### 2. Reproducing Examples Provided in the Paper: <a href='#all_catelogue'>[Back to Top]</a>
In this section, we demonstrate two ways of reproducing the examples generated by contrastive search provided in our paper.

<span id='use_transformers'/>

##### 2.1. Using Huggingface Transformers: <a href='#all_catelogue'>[Back to Top]</a>

<span id='install_transformers'/>

###### 2.1.1. Install Transformers Package: 

To install `transformers` from the source, please run the following command:
```yaml
pip install "transformers>=4.24.0"
```

<span id='transformers_table_1'/>

###### 2.1.2. Example in Table 1: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 1, please run the following command:
```python
# load the LMs
import torch
from transformers import GPT2Tokenizer, GPT2LMHeadModel
model_name = 'gpt2-large'
tokenizer = GPT2Tokenizer.from_pretrained(model_name)
model = GPT2LMHeadModel.from_pretrained(model_name, pad_token_id=tokenizer.eos_token_id)
model.eval()

# prepare the prefix
prefix_text = r"Kobe Bryant is"
inputs = tokenizer(prefix_text, return_tensors='pt')

# generate the result with contrastive search
output = model.generate(**inputs, penalty_alpha=0.6, top_k=4, max_length=256)
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output[0], skip_special_tokens=True))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------
Kobe Bryant is the best player in the world.

I know this is a bold statement to make, but it's true. He may have won a lot of games, but his
impact on the game is immeasurable. Kobe is one of the most popular players in the world, and
that's no small feat when you consider how many people play video games, watch sports, listen
to music, and have social media accounts.

Kobe has made a career out of being a role model for young people, and he does an amazing job
of that every time he steps on the court. His career stats are impressive, but what makes him
stand out is the way he goes about his business. When he's on the court, he's the center of
attention, and it's easy to see why.

The Lakers' All-Star point guard wears a smile on his face like no other. He's always in the
right place at the right time, making the right play at the right time, and doing the right
thing in the right way. He's not afraid to get his hands dirty, and he never shies away from
a physical confrontation.

Kobe's personality is something that can't be taught, and it's a big reason why...
----------------------------------------------------------------------------------------------------
```
</details>


<span id='transformers_table_8'/>

###### 2.1.3. Example in Table 8 at Appendix A: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 8 at Appendix A, please run the following command:
```python
# load the LMs
import torch
from transformers import GPT2Tokenizer, GPT2LMHeadModel
model_name = 'gpt2-large'
tokenizer = GPT2Tokenizer.from_pretrained(model_name)
model = GPT2LMHeadModel.from_pretrained(model_name, pad_token_id=tokenizer.eos_token_id)
model.eval()

# prepare the prefix
prefix_text = r'DeepMind Company is'
inputs = tokenizer(prefix_text, return_tensors='pt')

# generate the result with contrastive search
output = model.generate(**inputs, penalty_alpha=0.6, top_k=4, max_length=512)
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output[0], skip_special_tokens=True))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------  
DeepMind Company is a leader in artificial intelligence (AI). We have a long history of working
with companies such as Google, Facebook, Amazon, and Microsoft to build products that improve
people's lives, and today we are excited to announce that DeepMind's AlphaGo program has won the
game of Go, becoming the first program to defeat a professional Go player.

The victory is a testament to the power of deep learning, and to the incredible work of our
research team, which has been at the forefront of AI research for the past five years. AlphaGo
is one of the most advanced Go programs ever created, and its performance is an important step
towards the goal of human-level AI.

"This is the culmination of a decade of hard work," said Andy Ng, co-founder and CTO of DeepMind.
"We are thrilled to have achieved this milestone and look forward to continuing to develop AI that
can be used in a wide range of applications and to help people live better lives."

DeepMind's work on Go began in 2010, when it began to train a neural network to play Go using
millions of games played by top Go players around the world. Since then, the team has refined the
algorithm, adding more and more layers of reinforcement learning to make it better at recognizing
patterns and making decisions based on those patterns. In the past year and a half, the team has
made significant progress in the game, winning a record-tying 13 games in a row to move into the
top four of the world rankings.

"The game of Go is a complex game in which players have to be very careful not to overextend their
territory, and this is something that we have been able to improve over and over again," said
Dr. Demis Hassabis, co-founder and Chief Scientific Officer of DeepMind. "We are very proud of our
team's work, and we hope that it will inspire others to take the next step in their research and
apply the same techniques to other problems."

In addition to the win in Go, DeepMind has also developed an AI system that can learn to play a
number of different games, including poker, Go, and chess. This AI system, called Tarsier, was
developed in partnership with Carnegie Mellon University and the University of California, 
Berkeley, and is being used to teach computer vision and machine learning to identify objects in
images and recognize speech in natural language. Tarsier has been trained to play the game of Go
and other games on a number of different platforms...
----------------------------------------------------------------------------------------------------
```
</details>


<span id='transformers_table_9'/>

###### 2.1.4. Example in Table 9 at Appendix A: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 9 at Appendix A, please run the following command:
```python
# load the LMs
import torch
from transformers import GPT2Tokenizer, GPT2LMHeadModel
model_name = 'gpt2-large'
tokenizer = GPT2Tokenizer.from_pretrained(model_name)
model = GPT2LMHeadModel.from_pretrained(model_name, pad_token_id=tokenizer.eos_token_id)
model.eval()

# prepare the prefix
prefix_text = r"In a shocking finding, scientist discovered a herd of unicorns living in a remote, previously unexplored valley, in the Andes Mountains. Even more surprising to the researchers was the fact that the unicorns spoke perfect English."
inputs = tokenizer(prefix_text, return_tensors='pt')

# generate the result with contrastive search
output = model.generate(**inputs, penalty_alpha=0.6, top_k=4, max_length=512)
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output[0], skip_special_tokens=True))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------  
In a shocking finding, scientist discovered a herd of unicorns living in a remote, previously
unexplored valley, in the Andes Mountains. Even more surprising to the researchers was the fact
that the unicorns spoke perfect English.

According to the BBC, a team of scientists led by Dr David MacKay, from the University of
Bristol, spent two years searching for the unicorn herd, which they discovered during a survey
of the area.

"It's a very rare find," MacKay told the BBC. "There are a few in the Himalayas, but this is
the first time we've been able to find one in such a remote area."

The team was surprised to find a herd of unicorns living in a region that has been known to be
a hotbed of poaching, with many of the animals poached for their horns, which are used in
traditional Chinese medicine to treat everything from rheumatism to cancer.

"We knew that the area was rich in rhino horn, but we had no idea how many there were, or what
they were doing there," MacKay said. "This is an area of high poaching pressure, and we wanted
to find out what was going on."

In order to do so, the team used GPS collars to track the animals as they moved around the
mountain and the surrounding area. The GPS data was then compared with information gathered
from local villagers, who had a wealth of information about the animals' movements, including
where they were eating, what they were doing at night, and how much time they spent in the
mountains each day.

After analyzing the data, the team determined that the herd consisted of at least three species
of unicorns, including a male and two females. One of the females was the mother of the male,
and the other two were her daughters. All three had the same horn color, which is believed to
be a sign of purity in the animal kingdom.

While the discovery is exciting, it's not the first time scientists have discovered an animal
that speaks English. Last year, scientists discovered a species of porcupine that can be heard
by humans, and has been dubbed "Porcupine Man" for his ability to converse with the human race.
----------------------------------------------------------------------------------------------------
```
</details>


<span id='use_simctg'/>

##### 2.2. Using SimCTG Package: <a href='#all_catelogue'>[Back to Top]</a>

<span id='install_simctg'/>

###### 2.2.1. Install SimCTG Package: 

To install the package, please run the following command:

```yaml
pip install simctg --upgrade
```

<span id='simctg_table_1'/>

###### 2.2.2. Example in Table 1: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 1, please run the following command:
```python
# load the LMs
import torch
from simctg.simctggpt import SimCTGGPT
model_name = r'gpt2-large'
model = SimCTGGPT(model_name)
model.eval()
tokenizer = model.tokenizer
eos_token_id = tokenizer.eos_token_id

# prepare the prefix
prefix_text = r"Kobe Bryant is"
tokens = tokenizer.tokenize(prefix_text)
input_ids = tokenizer.convert_tokens_to_ids(tokens)
input_ids = torch.LongTensor(input_ids).view(1,-1)

# generate the result with contrastive search
beam_width, alpha, decoding_len = 4, 0.6, 256
output = model.fast_contrastive_search(input_ids=input_ids, beam_width=beam_width, 
                                       alpha=alpha, decoding_len=decoding_len,
                                      end_of_sequence_token_id = eos_token_id, early_stop = True) 
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------
Kobe Bryant is the best player in the world.

I know this is a bold statement to make, but it's true. He may have won a lot of games, but his
impact on the game is immeasurable. Kobe is one of the most popular players in the world, and
that's no small feat when you consider how many people play video games, watch sports, listen
to music, and have social media accounts.

Kobe has made a career out of being a role model for young people, and he does an amazing job
of that every time he steps on the court. His career stats are impressive, but what makes him
stand out is the way he goes about his business. When he's on the court, he's the center of
attention, and it's easy to see why.

The Lakers' All-Star point guard wears a smile on his face like no other. He's always in the
right place at the right time, making the right play at the right time, and doing the right
thing in the right way. He's not afraid to get his hands dirty, and he never shies away from
a physical confrontation.

Kobe's personality is something that can't be taught, and it's a big reason why...
----------------------------------------------------------------------------------------------------
```
</details>




<span id='simctg_table_8'/>

###### 2.2.3. Example in Table 8 at Appendix A: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 8 at Appendix A, please run the following command:
```python
# load the LMs
import torch
from simctg.simctggpt import SimCTGGPT
model_name = r'gpt2-large'
model = SimCTGGPT(model_name)
model.eval()
tokenizer = model.tokenizer
eos_token_id = tokenizer.eos_token_id

# prepare the prefix
prefix_text = r"DeepMind Company is"
tokens = tokenizer.tokenize(prefix_text)
input_ids = tokenizer.convert_tokens_to_ids(tokens)
input_ids = torch.LongTensor(input_ids).view(1,-1)

# generate the result with contrastive search
beam_width, alpha, decoding_len = 4, 0.6, 512
output = model.fast_contrastive_search(input_ids=input_ids, beam_width=beam_width, 
                                       alpha=alpha, decoding_len=decoding_len,
                                      end_of_sequence_token_id = eos_token_id, early_stop = True) 
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------  
DeepMind Company is a leader in artificial intelligence (AI). We have a long history of working
with companies such as Google, Facebook, Amazon, and Microsoft to build products that improve
people's lives, and today we are excited to announce that DeepMind's AlphaGo program has won the
game of Go, becoming the first program to defeat a professional Go player.

The victory is a testament to the power of deep learning, and to the incredible work of our
research team, which has been at the forefront of AI research for the past five years. AlphaGo
is one of the most advanced Go programs ever created, and its performance is an important step
towards the goal of human-level AI.

"This is the culmination of a decade of hard work," said Andy Ng, co-founder and CTO of DeepMind.
"We are thrilled to have achieved this milestone and look forward to continuing to develop AI that
can be used in a wide range of applications and to help people live better lives."

DeepMind's work on Go began in 2010, when it began to train a neural network to play Go using
millions of games played by top Go players around the world. Since then, the team has refined the
algorithm, adding more and more layers of reinforcement learning to make it better at recognizing
patterns and making decisions based on those patterns. In the past year and a half, the team has
made significant progress in the game, winning a record-tying 13 games in a row to move into the
top four of the world rankings.

"The game of Go is a complex game in which players have to be very careful not to overextend their
territory, and this is something that we have been able to improve over and over again," said
Dr. Demis Hassabis, co-founder and Chief Scientific Officer of DeepMind. "We are very proud of our
team's work, and we hope that it will inspire others to take the next step in their research and
apply the same techniques to other problems."

In addition to the win in Go, DeepMind has also developed an AI system that can learn to play a
number of different games, including poker, Go, and chess. This AI system, called Tarsier, was
developed in partnership with Carnegie Mellon University and the University of California, 
Berkeley, and is being used to teach computer vision and machine learning to identify objects in
images and recognize speech in natural language. Tarsier has been trained to play the game of Go
and other games on a number of different platforms...
----------------------------------------------------------------------------------------------------
```
</details>

<span id='simctg_table_9'/>

###### 2.2.4. Example in Table 9 at Appendix A: <a href='#all_catelogue'>[Back to Top]</a>

To reproduce our example provided in Table 9 at Appendix A, please run the following command:
```python
# load the LMs
import torch
from simctg.simctggpt import SimCTGGPT
model_name = r'gpt2-large'
model = SimCTGGPT(model_name)
model.eval()
tokenizer = model.tokenizer
eos_token_id = tokenizer.eos_token_id

# prepare the prefix
prefix_text = r"In a shocking finding, scientist discovered a herd of unicorns living in a remote, previously unexplored valley, in the Andes Mountains. Even more surprising to the researchers was the fact that the unicorns spoke perfect English."
tokens = tokenizer.tokenize(prefix_text)
input_ids = tokenizer.convert_tokens_to_ids(tokens)
input_ids = torch.LongTensor(input_ids).view(1,-1)

# generate the result with contrastive search
beam_width, alpha, decoding_len = 4, 0.6, 512
output = model.fast_contrastive_search(input_ids=input_ids, beam_width=beam_width, 
                                       alpha=alpha, decoding_len=decoding_len,
                                      end_of_sequence_token_id = eos_token_id, early_stop = True) 
print("Output:\n" + 100 * '-')
print(tokenizer.decode(output))
print("" + 100 * '-')
```

<details>
<summary><b>Model Output: [click to expand]</b></summary>
  
```
Output:
----------------------------------------------------------------------------------------------------  
In a shocking finding, scientist discovered a herd of unicorns living in a remote, previously
unexplored valley, in the Andes Mountains. Even more surprising to the researchers was the fact
that the unicorns spoke perfect English.

According to the BBC, a team of scientists led by Dr David MacKay, from the University of
Bristol, spent two years searching for the unicorn herd, which they discovered during a survey
of the area.

"It's a very rare find," MacKay told the BBC. "There are a few in the Himalayas, but this is
the first time we've been able to find one in such a remote area."

The team was surprised to find a herd of unicorns living in a region that has been known to be
a hotbed of poaching, with many of the animals poached for their horns, which are used in
traditional Chinese medicine to treat everything from rheumatism to cancer.

"We knew that the area was rich in rhino horn, but we had no idea how many there were, or what
they were doing there," MacKay said. "This is an area of high poaching pressure, and we wanted
to find out what was going on."

In order to do so, the team used GPS collars to track the animals as they moved around the
mountain and the surrounding area. The GPS data was then compared with information gathered
from local villagers, who had a wealth of information about the animals' movements, including
where they were eating, what they were doing at night, and how much time they spent in the
mountains each day.

After analyzing the data, the team determined that the herd consisted of at least three species
of unicorns, including a male and two females. One of the females was the mother of the male,
and the other two were her daughters. All three had the same horn color, which is believed to
be a sign of purity in the animal kingdom.

While the discovery is exciting, it's not the first time scientists have discovered an animal
that speaks English. Last year, scientists discovered a species of porcupine that can be heard
by humans, and has been dubbed "Porcupine Man" for his ability to converse with the human race.
----------------------------------------------------------------------------------------------------
```
</details>



****

<span id='LMs_isotropy'/>

##### 3. Measuring Isotropy of LMs: <a href='#all_catelogue'>[Back to Top]</a>

The detailed tutorial on measuring the isotropy of LMs is provided [[here]](./isotropy_analysis/).

**[Note]** To replicate our experimental results, please make sure you have installed the environment as
```yaml
pip install simctg --upgrade
```


****

<span id='open_ended_text_generation'/>

##### 4. Open-ended Text Generation: <a href='#all_catelogue'>[Back to Top]</a>

The detailed tutorial on the experiments of open-ended text generation is provided [[here]](./open_ended_generation/).

**[Note]** To replicate our experimental results, please make sure you have installed the environment as
```yaml
pip install simctg --upgrade
```

****

<span id='code_generation'/>

##### 5. Code Generation: <a href='#all_catelogue'>[Back to Top]</a>

The detailed tutorial on the experiments of code generation is provided [[here]](./code_generation/).

**[Note]** To replicate our experimental results, please make sure you have installed the environment as
```yaml
pip install simctg --upgrade
```

****

<span id='machine_translation'/>

##### 6. Machine Translation: <a href='#all_catelogue'>[Back to Top]</a>

The detailed tutorial on the experiments of machine translation is provided [[here]](./translation/).

**[Note]** To replicate our experimental results, please make sure you have installed the environment as
```yaml
pip install simctg --upgrade
```


