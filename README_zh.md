# AutoRestTest

### 简介
***AutoRestTest*** 是一款用于自动化 API 测试的完整测试软件，它结合了图论、大型语言模型 (LLM) 和多智能体强化学习 (MARL) 的功能，能够解析 OpenAPI 规范并创建增强的综合测试用例。

观看这个 AutoRestTest 的[演示视频](https://www.youtube.com/watch?v=VVus2W8rap8)，了解它如何解决自动化 REST API 测试中的复杂挑战，以及其配置、执行步骤和输出。

<p align="center">
  <a href="https://www.youtube.com/watch?v=VVus2W8rap8">
    <img src="https://img.youtube.com/vi/VVus2W8rap8/0.jpg" alt="观看视频">
  </a>
p>

该程序在创建强化学习表和图边的过程中，使用 OpenAI 的尖端 LLM 进行自然语言处理。

> [!重要]
> 使用该软件需要一个 OpenAI API 密钥。该密钥可以从 [OpenAI 的 API 网站](https://openai.com/index/openai-api/)获取。软件每次执行的成本与 API 的可用操作数量成线性关系。作为参考，在测试一个平均拥有约 15 个操作的 API 时，成本约为 0.1 美元。

## 安装

以下是安装 *AutoRestTest* 软件的步骤：
1. 克隆 Github 仓库。
2. 安装所需的依赖项：
   - 可以使用 `requirements.txt` 来安装依赖项，或者使用 Conda 环境文件 `auto-rest-test.yml`。
3. 在根目录中创建一个 `.env` 文件，并添加以下环境变量：
   - `OPENAI_API_KEY = '<YOUR_API_KEY>'`

> [!提示]
> 为了在不同操作系统间保持兼容性，建议使用 Conda 环境文件。

可选地，如果用户想要测试特定的 API，他们可以将自己的 OpenAPI 规范文件安装在根目录下的一个文件夹中。为方便起见，我们提供了大量用于流行和广泛使用的 API 的 OpenAPI 规范。这些规范可以在 `aratrl-openapi` 和 `specs` 目录中找到。

至此，安装步骤已完成，软件可以执行。但是，为了有目的地执行，完成以下配置步骤非常重要。

## 配置

代码库中提供了广泛的配置选项。为方便起见，这些配置选项可以在根目录下的 `configurations.py` 文件中轻松访问。该文件包含了关于每个可修改参数的信息。以下是每个配置选项的附加说明。
请注意，以下步骤中引用的参数均可在 `configurations.py` 文件中找到。

#### 1. 指定 API 目录

如果用户打算使用他们在*安装*部分描述的自己的 OpenAPI 规范文件，他们可以更改 `configurations.py` 文件中的 **SPECIFICATION_DIRECTORY** 变量，将其设置为包含所添加规范的目录名称。

#### 2. 配置强化学习参数

用户可以配置强化学习参数，例如学习率 (**LEARNING_RATE**)、折扣因子 (**DISCOUNT_FACTOR**) 和 Q-learning 中使用的 epsilon-greedy 探索值 (**EXPLORATION_RATE**)。默认情况下，参数设置为以下值：
- 学习率：0.1
- 折扣因子：0.9
- Epsilon: 0.3

程序不限制训练的回合数（episodes），而是采用基于时间的方法来限制强化学习的迭代次数。默认值设置为 10 分钟。可以通过更改配置文件中的 **TIME_DURATION** 变量来修改此值。单位为秒。

> [!提示]
> 以上步骤会更改软件中所有四个智能体的变量。如果用户希望更改单个智能体的参数，可以导航到 `src/reinforcement/agents.py` 文件并进行修改。

#### 3. 配置 OpenAI 模型

如果用户希望降低软件执行的成本，他们可以更改整个程序中使用的 OpenAI 模型。用户可以使用 **OPENAI_LLM_ENGINE** 变量来指定一个合适的模型。默认情况下，选择的是 **gpt-4o-mini** 模型，因为它具有高性能、成本效益和高 token 限制。

> [!警告]
> 该软件大量使用了来自最新 OpenAI API 引擎的 **JSON 模式**。所有 GPT 3.5-Turbo、4-Turbo、4o 和 o1 引擎都支持 JSON 模式。
> 此外，执行成本仅针对最新版本的 4o、4o-mini、o1 和 o1-mini 引擎提供。

#### 4. 使用缓存的图和强化学习表

该软件使用缓存机制，在生成后为每个 OpenAPI 规范存储图边和强化学习表。这样做是为了降低重复试验的执行成本并加快处理速度。用户可以通过更改 **USE_CACHED_GRAPH** 和 **USE_CACHED_TABLE** 变量来决定是否使用缓存的图和表。默认情况下，这两个变量都设置为 **False**。

### 5. 可选的 Header 智能体

AutoRestTest 包含一个可选的 Header 智能体，负责使用不同的身份验证头来测试 API。由于不同身份验证流程的复杂性，Header 智能体仅能使用基本身份验证（Basic Authentication）。默认情况下，该智能体是禁用的。

> [!注意]
> 当执行的服务使用会刷新的本地数据库时，应重新运行 Header 智能体的 Q-table，因为用户凭据可能会失效。

## 执行

可以通过从根目录运行 `AutoRestTest.py` 文件来执行该软件，使用以下命令：

```angular2html
python3 AutoRestTest.py
```

要指定执行的规范，请更改 `configurations.py` 文件中的 **SPECIFICATION_LOCATION** 变量。

### Docker 执行

为方便使用，该软件可以使用 Docker 执行。用户可以从根目录依次应用以下命令，以使用预构建的 Docker 镜像执行 AutoRestTest：

```angular2html
docker build -t autoresttest .
docker run -it autoresttest
```

在执行软件之前，请确保 `configurations.py` 文件已正确配置。

## 结果

在 AutoRestTest 执行过程中，其命令行界面 (CLI) 将为用户提供关于当前流程步骤的持续更新。此外，在请求生成期间，软件将输出有关已用时间、成功处理 (2xx) 的操作数量、唯一的服务器错误 (5xx)、剩余时间以及所有状态码分布的信息。在请求生成和强化学习完成后，软件将输出程序的成本（以美元计，与使用 LLM 的成本相关）。

#### 报告生成和数据文件

AutoRestTest 将在根文件夹内的 `data` 目录中生成一系列文件，以便轻松访问执行结果。
以下是生成的文件：
- `report.json`: 包含 API 测试过程的编译报告，包括关键摘要统计信息。
- `operation_status_codes.json`: 包含每个操作的状态码分布。
- `q_tables.json`: 包含每个智能体完成的 Q-table。
- `server_errors.json`: 包含测试过程中遇到的唯一服务器错误。只存储可 JSON 序列化的错误。
- `successful_parameters.json`: 包含每个操作的成功参数。
- `successful_responses.json`: 包含每个操作的成功响应。
- `successful_bodies.json`: 包含每个操作的成功对象请求体。
- `successful_primitives.json`: 包含每个操作的成功原始类型请求体。

这些文件包含了分析 AutoRestTest 成功与否所需的信息。

> [!说明]
> 输出文件的大小会根据操作数量和执行持续时间而增长。
> 例如，当长时间执行时，包含成功响应的文件可能会增长到几 **GB**。
> 当不再需要这些文件时，建议清理 `data` 目录。