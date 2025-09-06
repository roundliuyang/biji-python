# 流式传输（Streaming）

>[流式输出](https://www.langchain.asia/modules/agents/how_to/streaming)

**优化后的版本（技术文档风格）：**
 流式输出是 LLM 应用中提升用户体验的关键因素，Agent 场景也不例外。相比直接返回最终结果，在 Agent 中实现流式输出更为复杂，因为除了逐步传输最终答案的 token，通常还需要实时输出 Agent 执行过程中的中间步骤（如调用工具、解析结果等）。

在这个笔记本中，我们将介绍用于流式传输的`stream/astream`和`astream_events`。

我们的代理将使用工具API进行工具调用：

1. `where_cat_is_hiding`: 返回猫藏在哪里的位置
2. `get_items`: 列出可以在特定地方找到的物品

这些工具将允许我们在一个更有趣的情况下探索流式传输，代理将不得不使用两者来回答一些问题（例如，回答问题`猫藏在哪里的物品？`）。

准备好了吗？

```python
from langchain import hub
from langchain.agents import AgentExecutor, create_openai_tools_agent
from langchain.prompts import ChatPromptTemplate
from langchain.tools import tool
from langchain_core.callbacks import Callbacks
from langchain_openai import ChatOpenAI
```



## 创建模型

**注意** 我们在LLM上设置了`streaming=True`。这将允许我们使用`astream_events` API从代理中流式传输令牌。这对于LangChain的旧版本是必需的。

```python
model = ChatOpenAI(temperature=0, streaming=True)
```



## 工具

我们定义了两个依赖于聊天模型生成输出的工具！

```python
import random
 
@tool
async def where_cat_is_hiding() -> str:
    """猫现在躲在哪里？"""
    return random.choice(["床下", "架子上"])
 
 
@tool
async def get_items(place: str) -> str:
    """使用此工具查找给定地方有哪些物品。"""
    if "床" in place:  # 对于床下
        return "袜子、鞋子和灰尘兔"
    if "架子" in place:  # 对于'架子'
        return "书、铅笔和图片"
    else:  # 如果代理决定询问不同的地方
        return "猫零食"
```

```python
await where_cat_is_hiding.ainvoke({})
```

```
'架子上'
```

```python
await get_items.ainvoke({"place": "架子"})
```

```
'书、铅笔和图片'
```



## 初始化 Agent

这里，我们将初始化一个OpenAI工具 agent。

**注意** 请注意，我们使用`"run_name"="Agent"`将名称`Agent`与我们的 agent 关联起来。我们稍后将在`astream_events` API中使用这一事实。

```python
# 获取要使用的提示 - 您可以修改这个！
prompt = hub.pull("hwchase17/openai-tools-agent")
# 打印(prompt.messages) -- 查看提示
tools = [get_items, where_cat_is_hiding]
agent = create_openai_tools_agent(
    model.with_config({"tags": ["agent_llm"]}), tools, prompt
)
agent_executor = AgentExecutor(agent=agent, tools=tools).with_config(
    {"run_name": "Agent"}
)
```



## 流传中间步骤

我们将使用AgentExecutor的`.stream`方法来流式传输代理的中间步骤。

`.stream`的输出在操作和观察对之间交替，最终以代理是否达到目标为结束。

它看起来像这样：

1. 操作输出
2. 观察输出
3. 操作输出
4. 观察输出

**...（继续直到达到目标）...**

然后，如果达到最终目标，代理将输出**最终答案**。

这些输出的内容摘要如下：

| 输出         | 内容                                                         |
| ------------ | ------------------------------------------------------------ |
| **操作**     | `actions` `AgentAction`或子类，与操作调用对应的聊天消息      |
| **观察**     | `steps`到目前为止代理的历史记录，包括当前操作及其观察，`messages`具有函数调用结果的聊天消息（即观察） |
| **最终答案** | `output` `AgentFinish`，具有最终输出的聊天消息               |

```python
# 注意：我们使用`pprint`仅打印到深度1，这样可以更容易地看到高层次的输出，然后再进行挖掘。
import pprint
 
chunks = []
 
async for chunk in agent_executor.astream(
    {"input": "猫躲在哪里有哪些物品？"}
):
    chunks.append(chunk)
    print("------")
    pprint.pprint(chunk, depth=1)
```

```sh
------
{'actions': [...], 'messages': [...]}
------
{'messages': [...], 'steps': [...]}
------
{'actions': [...], 'messages': [...]}
------
{'messages': [...], 'steps': [...]}
------
{'messages': [...], 'output': '猫躲在床下，床下有袜子、鞋子和灰尘兔。'}
```

### 使用消息

您可以访问输出中的基础`消息`。在处理聊天应用程序时，使用消息可能会很好，因为一切都是消息！

```python
chunks[0]["actions"]
```

```sh
[OpenAIToolAgentAction(tool='where_cat_is_hiding', tool_input={}, log='\n正在调用：使用`{}`查找猫的藏身之处\n\n\n', message_log=[AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_pKy4OLcBx6pR6k3GHBOlH68r', 'function': {'arguments': '{}', 'name': 'where_cat_is_hiding'}, 'type': 'function'}]})], tool_call_id='call_pKy4OLcBx6pR6k3GHBOlH68r')]
```

```python
for chunk in chunks:
    print(chunk["messages"])
```

```sh
[AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_pKy4OLcBx6pR6k3GHBOlH68r', 'function': {'arguments': '{}', 'name': 'where_cat_is_hiding'}, 'type': 'function'}]})]
[FunctionMessage(content='架子上', name='where_cat_is_hiding')]
[AIMessageChunk(content='', additional_kwargs={'tool_calls': [{'index': 0, 'id': 'call_qZTz1mRfCCXT18SUy0E07eS4', 'function': {'arguments': '{\n  "place": "架子"\n}', 'name': 'get_items'}, 'type': 'function'}]})]
[FunctionMessage(content='书、铅笔和图片', name='get_items')]
[AIMessage(content='猫藏在架子上的物品是书、铅笔和图片。')]
```

此外，它们包含完整的日志信息（`actions`和`steps`），可能更容易在渲染目的中处理。

### 使用AgentAction/Observation

输出还包含更丰富结构化信息，位于`actions`和`steps`内部，这在某些情况下可能很有用，但也可能更难解析。

**注意** `AgentFinish`不作为`streaming`方法的一部分提供。如果您希望添加这个功能，请在github上发起讨论并解释为什么需要它。

```python
async for chunk in agent_executor.astream(
    {"input": "猫躲在哪里有哪些物品？"}
):
# 代理执行
 
如果`chunk`中包含"actions"字段:
对于`chunk["actions"]`中的每一个action:
输出`Calling Tool: `{action.tool}` with input `{action.tool_input}``
 
如果`chunk`中包含"steps"字段:
对于`chunk["steps"]`中的每一个step:
输出`Tool Result: `{step.observation}``
 
如果`chunk`中包含"output"字段:
输出`Final Output: {chunk["output"]}`
否则:
抛出数值错误
 
输出"---"分割线
```

```sh
Calling Tool: `where_cat_is_hiding` with input `{}`
---
Tool Result: `on the shelf`
---
Calling Tool: `get_items` with input `{'place': 'shelf'}`
---
Tool Result: `books, pencils and pictures`
---
Final Output: The items located where the cat is hiding on the shelf are books, pencils, and pictures.
---
```

## 使用自定义事件进行流式处理

如果默认的*stream*行为不适用于你的应用程序(例如，如果你需要从代理或工具内部发生的步骤中流式处理每个标记)，请使用`astream_events` API。

⚠️ 这是一个**beta**API，这意味着一些细节可能会根据使用情况稍微改变。 ⚠️ 为了确保所有回调正确工作，请始终使用`async`代码。尽量避免混合同步版本的代码(例如，同步版本的工具)。

我们使用这个API来流式处理以下事件:

1. 带输入的代理开始
2. 带输入的工具开始
3. 带输出的工具结束
4. 逐个标记流式处理代理最终答案
5. 带输出的代理结束

```python
async for event in agent_executor.astream_events(
    {"input": "where is the cat hiding? what items are in that location?"},
    version="v1",
):
    kind = event["event"]
    if kind == "on_chain_start":
        if (
            event["name"] == "Agent"
        ):
            print(
                f"Starting agent: {event['name']} with input: {event['data'].get('input')}"
            )
    elif kind == "on_chain_end":
        if (
            event["name"] == "Agent"
        ):
            print()
            print("--")
            print(
                f"Done agent: {event['name']} with output: {event['data'].get('output')['output']}"
            )
    if kind == "on_chat_model_stream":
        content = event["data"]["chunk"].content
        if content:
            # 在OpenAI上下文中“空内容”表示模型正在请求调用工具。
            # 所以我们只打印非空内容
            print(content, end="|")
    elif kind == "on_tool_start":
        print("--")
        print(
            f"Starting tool: {event['name']} with inputs: {event['data'].get('input')}"
        )
    elif kind == "on_tool_end":
        print(f"Done tool: {event['name']}")
        print(f"Tool output was: {event['data'].get('output')}")
        print("--")
```

```sh
Starting agent: Agent with input: {'input': 'where is the cat hiding? what items are in that location?'}
--
Starting tool: where_cat_is_hiding with inputs: {}
Done tool: where_cat_is_hiding
Tool output was: 床下
--
--
Starting tool: get_items with inputs: {'place': '床下'}
Done tool: get_items
Tool output was: 袜子、鞋子和灰尘兔
--
猫|现|在|躲|在|床|下|。|在|床|下|的|物|品|有|袜|子|、|鞋|子|和|灰|尘|兔|。|
--
Done agent: Agent with output: 猫现在躲在床下。在床下的物品有袜子、鞋子和灰尘兔。

```



### 从工具内部流式处理事件

如果你的工具利用LangChain可运行对象(例如LCEL链、LLMs、检索器等)， 并且你想要从这些对象中流式处理事件，你需要确保回调正确传播。

查看如何传递回调，让我们重新实现`get_items`工具，使其使用LLM并将回调传递给LLM。随时根据自己的情况进行调整。

```python
@tool
async def get_items(place: str, callbacks: Callbacks) -> str:
    """使用此工具查找给定地点中的物品。"""
    template = ChatPromptTemplate.from_messages(
        [
            (
                "human",
                "Can you tell me what kind of items i might find in the following place: '{place}'. "
                "List at least 3 such items separating them by a comma. And include a brief description of each item..",
            )
        ]
    )
    chain = template | model.with_config(
        {
            "run_name": "Get Items LLM",
            "tags": ["tool_llm"],
            "callbacks": callbacks,
        }
    )
    chunks = [chunk async for chunk in chain.astream({"place": place})]
    return "".join(chunk.content for chunk in chunks)
```

```sh
1.创建聊天提示模板：使用ChatPromptTemplate.from_messages()创建一个包含单条人类消息的聊天模板，该消息是一个询问某个地点可能有什么物品的提示词，其中{place}是一个占位符。
2.构建处理链：使用管道操作符|将模板和配置好的模型组合成一个处理链(chain)。
  -model.with_config()为模型设置了特定的配置：
    -"run_name": "Get Items LLM" - 设置运行名称，便于跟踪和调试
    -"tags": ["tool_llm"] - 添加标签，用于标识这是工具使用的LLM
    -"callbacks": callbacks - 设置回调函数，用于处理异步流式输出
这个处理链使得当调用get_items2工具时，会使用这个特定配置的模型来处理输入，生成对指定地点物品的描述。这种设计允许在复杂的AI代理系统中对不同部分使用不同的模型配置，并能够跟踪每个组件的执行情况。


chunks = [chunk async for chunk in chain.astream({"place": place})]
各部分解释：
1.chain.astream({"place": place})：
  -这是调用chain对象的astream方法，它是一个异步流式处理方法
  -传入参数{"place": place}，将place变量的值填入前面定义的模板中的{place}占位符
  -该方法返回一个异步生成器，会逐步产生响应内容的片段(chunk)
2.async for chunk in ...：
  -这是Python的异步for循环语法
  -用于迭代异步生成器产生的每个数据块(chunk)
  -因为是异步操作，所以需要用async for而不是普通的for
3.[chunk ...]：
  -这是列表推导式的语法
  -将异步for循环中获取的每个chunk收集到一个列表中
4.整体含义：
  -异步执行chain的流式处理，将流式返回的所有数据块收集到chunks列表中
  -每个chunk都是语言模型逐步生成的一个响应片段
```



看看工具如何传播回调。

接下来，让我们初始化我们的代理，并查看新的输出。

```python
prompt = hub.pull("hwchase17/openai-tools-agent")
tools = [get_items, where_cat_is_hiding]
agent = create_openai_tools_agent(
    model.with_config({"tags": ["agent_llm"]}), tools, prompt
)
agent_executor = AgentExecutor(agent=agent, tools=tools).with_config(
    {"run_name": "Agent"}
)
 
async for event in agent_executor.astream_events(
    {"input": "where is the cat hiding? what items are in that location?"},
    version="v1",
):
    kind = event["event"]
    if kind == "on_chain_start":
        if (
            event["name"] == "Agent"
        ):
            print(
                f"Starting agent: {event['name']} with input: {event['data'].get('input')}"
            )
    elif kind == "on_chain_end":
        if (
            event["name"] == "Agent"
        ):
            print()
            print("--")
            print(
                f"Done agent: {event['name']} with output: {event['data'].get('output')['output']}"
            )
    if kind == "on_chat_model_stream":
        content = event["data"]["chunk"].content
        if content:
            # 在OpenAI上下文中“空内容”表示模型正在请求调用工具。
            # 所以我们只打印非空内容
            print(content, end="|")
    elif kind == "on_tool_start":
        print("--")
        print(
            f"Starting tool: {event['name']} with inputs: {event['data'].get('input')}"
        )
    elif kind == "on_tool_end":
        print(f"Done tool: {event['name']}")
        print(f"Tool output was: {event['data'].get('output')}")
        print("--")
```



```sh
Starting agent: Agent with input: {'input': 'where is the cat hiding? what items are in that location?'}
--
Starting tool: where_cat_is_hiding with inputs: {}
Done tool: where_cat_is_hiding
Tool output was: 架子上
--
--
Starting tool: get_items2 with inputs: {'place': '架子上'}
书|籍| -| 在|架|子|上|常|见|的|物|品|之|一|是|书|籍|。|书|籍|可以|是|小|说|、|教|科|书|或|非|虛|構|类|书|籍|，|提|供|知|识|和|娱|乐|。

|照|片|框| -| 架|子|上|也|常|放|置|照|片|框|，|里|面|可以|放|家|人|、|朋|友|的|照|片|，|用|于|装|饰|和|回|忆|美|好|时|光|。

|盆|栽| -| 另|一|种|常|见|的|架|子|物|品|是|盆|栽|，|小|型|的|室|内|植|物|，|不|仅|美|化|环|境|，|还|可以|净|化|空|气|。|Done tool: get_items2
Tool output was: 书籍 - 在架子上常见的物品之一是书籍。书籍可以是小说、教科书或非虛構类书籍，提供知识和娱乐。

照片框 - 架子上也常放置照片框，里面可以放家人、朋友的照片，用于装饰和回忆美好时光。

盆栽 - 另一种常见的架子物品是盆栽，小型的室内植物，不仅美化环境，还可以净化空气。
--
猫|现|在|躲|在|架|子|上|。|在|这|个|位置|，|你|可以|找|到|以下|物|品|：

|-| **|书|籍|**|：|架|子|上|放|有|各|种|类型|的|书|籍|，|如|小|说|、|教|科|书|或|非|虚|构|类|。
|-| **|照|片|框|**|：|也|有|放|置|有|家|人|或|朋|友|照|片|的|照|片|框|，|供|装|饰|和|怀|旧|之|用|。
|-| **|盆|栽|**|：|还|有|小|型|室|内|植|物|用|来|美|化|环|境|和|净|化|空|气|。|
--
Done agent: Agent with output: 猫现在躲在架子上。在这个位置，你可以找到以下物品：

- **书籍**：架子上放有各种类型的书籍，如小说、教科书或非虚构类。
- **照片框**：也有放置有家人或朋友照片的照片框，供装饰和怀旧之用。
- **盆栽**：还有小型室内植物用来美化环境和净化空气。
```



### 其他方法

#### 使用 astream_log

略

