没有显卡？显存不足？内存不足？环境不会配置？现在都不用怕了，直接白嫖Google Colab吧！

## [点击这里快速到达](https://colab.research.google.com/drive/19cKRLE6WBVoVK1cHPNuc3KvmeQ9TO19G#scrollTo=t4daxu3L1Rbi)

如果需要给角色加头像的话，可以把头像命名为"角色名.png"放在char目录下，至于头像去哪儿找嘛……用sd生成一个就好了。

该项目虽然是个webui，但是不适合多人同时使用，如果需要多用户的话，可以参考这个项目

https://github.com/shengxia/RWKV_Role_Playing_API

## 一个基于RWKV的角色扮演玩具

![图片1](./pic/1.png)

就是这么一个玩意儿，连抄带编的弄出来了一个玩具，所以代码质量吗……请各位不要吐槽太多，但是也算是能玩吧。

另外，如果不知道人物性格怎么设定的话，我建议你可以下载一个Glow，这是[官网地址](https://glowapp.vip/)，然后到上面去找智能体，直接把它们的性格粘过来用是完全没问题的。

这段时间我到[Chub](https://www.chub.ai)上面翻了翻角色卡，找了一些高质量的角色卡测试了一下，虽然上面的内容英文的比较多，但是稍微整理一下格式，放进去用还是没啥问题的，虽然我知道模型有语言迁移的能力，但是亲自尝试一下还是觉得挺神奇的，就是我可以用中文来回复，然后模型说话的时候用英文，而且上下文还搭能得上，我也不知道是因为RWKV英文能力更强还是这角色卡写得好，回复的还挺带感的，至少比我自己写的中文角色卡好上不少，感兴趣的话，大家也可以试试这种玩儿法，反正对我来说，看英文比写英文要简单多了。

目前我不再支持Raven系列的模型，而是改用World系列的模型了，这个模型在角色扮演上好太多了，另外我在FAQ中也推荐了一些其他的模型，他们同样也非常适合角色扮演，如果你使用的是老版本的话，可能还需要更新一下RWKV库

```
pip install rwkv --upgrade
```

如果更新拉取代码后，运行起来发现会报错，请更新一下gradio

```
pip install gradio --upgrade
```

### 安装方法：

先安装依赖
```
pip install torch==1.13.1 --extra-index-url https://download.pytorch.org/whl/cu117 --upgrade

pip install -r requirements.txt
```

启动：
```
python webui.py --listen --model model/path
```

以下是一个例子: 
```
python webui.py --listen --model model/RWKV-x060-World-3B-v2-20240228-ctx4096
```
各种启动参数解释如下：

| 参数 | 解释 |
| --- | --- |
| --port | webui的端口 |
| --model | 要加载的模型路径 |
| --state | 要加载的state路径 |
| --strategy | 模型加载的策略 |
| --listen | 加上这个参数就允许其他机器访问 |
| --cuda_on | 控制RWKV_CUDA_ON这个环境变量的，0-禁用，1-启用 |
| --jit_on | 控制RWKV_JIT_ON这个环境变量的，0-禁用，1-启用 |
| --share | 生成gradio链接 |
| --lang | 语言，zh-中文，en-英文 |
| --chat_length | 聊天长度，以前当聊天文字超过模型的上下文长度之后，生成的效果会下降，于是需要对聊天进行截断，但是我发现到了RWKV 6之后，在聊天记录超过模型的上下文长度两倍的情况下，依然能够正常生成文字，所以这个参数现在意义不大了，建议可以给设置的稍微大一些，比如32000，避免频繁截断聊天记录。 |
| --autosave | 是否自动保存对话进度，目前默认是不自动保存了，因为RWKV5之后的state变大了好多，如果需要的话记得把这个参数加上 |

模型的加载方式（--strategy）我默认使用的是"cuda fp16i8"，如果想使用其他的加载方式可以自行调整该参数，具体有哪些值可以参考[这个文章](https://zhuanlan.zhihu.com/p/609154637)或者这张图![图片](./pic/4.jpg)

关于显卡的选择方面，我个人挺推荐2080ti 22G显存版（这显卡只有二手的，大概2300元左右吧，改装的卡，而且有矿，这卡不支持bf16，所以适合推理，不适合微调RWKV，总之见仁见智吧），主要是这个显卡可以玩RWKV系列的所有尺寸的模型（14B需要int8量化30层），当然，也可以根据自己的需要来选择别的显卡，有能力上3090或者4090这种卡当然更好，会折腾散热啥的P40之类的也能玩。

## FAQ

### 1. 能让AI生成文字的速度再快一点吗？

当然可以，在启动命令中加入--cuda_on 1，例子：
```
python webui.py --listen --model model/RWKV-x060-World-3B-v2-20240228-ctx4096 --cuda_on 1
```
但是你的机器必须安装Visual C++生成工具，以及Nvidia的CUDA以及CUDNN，CUDA和CUDNN比较好解决，去官网下载就行了，建议安装11.7版本，这个Visual C++生成工具可以参考[这个链接](https://learn.microsoft.com/zh-cn/training/modules/rust-set-up-environment/3-install-build-tools)装好之后还需要配置一下环境变量，如下图：
![图片3](./pic/3.png)
我这里配置的值是C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\VC\Tools\MSVC\14.16.27023\bin\Hostx64\x64，你们根据实际情况进行配置，主要是找到cl.exe这个文件所在的文件夹，当然也要注意架构，不过一般来说，大家都是64位的系统了吧。这样就算是完成了，然后再运行脚本，你会发现文字的生成速度提高了很多。

### 2. 我在哪里可以下载的到模型呢？

当然是在[这里](https://huggingface.co/BlinkDL)或者[使用镜像](https://hf-mirror.com/BlinkDL)，这里推荐rwkv6的模型。

另外也推荐Xiaol发布的模型，地址在[这里](https://huggingface.co/xiaol)，[镜像地址](https://hf-mirror.com/xiaol)，这里有12B的模型，很适合有大显存（24G）的玩家尝试。

不太推荐a686d380的rwkv-5-h-world系列模型，倒不是这个模型不好（它使用的数据集算是一座宝库），主要是他的那个模型是主打小说续写，不适合角色扮演。

### 3. 参数上有什么设置技巧吗？

- 目标熵：此为Mirostat采样的参数，这个值类似于Top k采样中的k值，只不过这个k值是基于生成token的交叉熵来动态调整的，设置高了容易说胡话。
- 学习率：这个值我找了半天，也没看到有啥推荐的方案，它的大小决定了最大惊奇值的变化速度（最大惊奇值一般都是越来越大），可以保持默认值，设为0.1。
- 学习率衰减系数：为了防止最大惊奇值过大，所以引入了一个衰减系数，当最大惊奇值大于10倍目标熵的值时，每生成一个token，就会让当前学习率除以1+该系数的值，从而减缓最大惊奇值的增长速度。
- Min P：候选token中最高的那个token的概率乘以Min P的值，丢弃掉概率小于该结果的token，这个值越大，输出的结果就越准确，但生成的内容也更容易重复。
- 温度值：对候选词的概率进行一个乘方运算，指数的值就是该值的倒数，当该值大于1时，运算后候选词之间概率的值相差会减小，如果小于1，则候选词之间概率的值相差就会增大，如果等于1，则不变。
- 重复惩罚：值不建议太高，一般在0.1到0.3之间就行了，太高容易出问题（我看论文中说0.2就不错）。

RWKV目前的迭代速度非常快，而且每次更新提升都很大，所以模型尽量用新的。

这里建议使用如下配置：
```json
{
  "tau": 5,
  "lr": 0.1,
  "lr_decay": 0.01,
  "min_p": 0.05,
  "temp": 1.5,
  "presence": 0.2
}
```

### 4. 模型会在输出回答后，又输出一大堆乱七八糟的内容。

我增加了一个调试页面，可以查看当前的token内容，一般输出乱七八糟的内容都是对话格式有误，正确的对话格式是这样的：
```
用户名: xxxxxxxxx

角色名: xxxxxxxx

用户名: xxxxxxxxxxxxxxx

```
其中冒号是英文冒号且冒号后面有一个空格，每句对话后面都有两个换行（\n\n）。如果可以，尽量不要在角色名中加空格。

### 5. 为什么清除上一条对话然后再生成对话时，需要这么长的时间？

因为我不会用gradio清除多条对话，所以只能让用户一条一条的清除，但是以前的方法，每清除一条就要重新计算一次状态的确有点儿浪费，于是我改了一下逻辑，你可以随意清除上一条，我这边只记录下来你清除了多少，当你再次对话的时候，我就一次性的把状态回退到你清除的那个地方，这样在清除多条的时候就比较省时（当然了，这个重新计算状态所耗的时间实在是没办法，除非你勤存档）。

### 6. 还有其他想说的吗？

我在角色选项卡中增加了一个选项，可以使用User和Assistant来代替你和角色的名称，使用该选项生成的格式如下：
```
User: xxxxxxxxx

Assistant: xxxxxxxx

User: xxxxxxxxxxxxxxx

```
不使用该选项生成的格式如下：
```
User: 用户名: xxxxxxxxx

Assistant: 角色名: xxxxxxxx

User: 用户名: xxxxxxxxxxxxxxx

```
随着模型的更新，目前就算使用User和Assistant的感觉已经好了很多，而且对话生成的效果比使用“用户名和角色名”要好（目前7B的模型还是规模较小，生成过长的文字很容易跑偏，所以我把控制输出长度的功能都给删了），但是也会有一些思想钢印的存在（比如AI会很拒绝做坏事，就算做了也是不情不愿的，但是使用“用户名和角色名”时就不会这样）。总之可以根据各位的喜好来修改。

[这里还有一些个人关于角色扮演上的心得](doc.md)

**不过需要注意的是，在修改完角色选项卡中的任何项目并点击保存后，程序会清空当前的对话（不清空没办法让修改后的设置生效），所以进行此操作务必谨慎。**
# Rwkv-Role-Playing-Quantization-and-Original-Version
