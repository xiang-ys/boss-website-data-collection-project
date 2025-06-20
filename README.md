# 高级职位信息采集器 (Advanced Job Scraper for Zhipin.com)

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/) [![Status](https://img.shields.io/badge/Status-Active-green.svg)](https://github.com/xiang-ys/boss-website-data-collection-project) [![License](https://img.shields.io/badge/License-MIT-brightgreen.svg)](./LICENSE)

这是一个针对BOSS直聘（zhipin.com）设计的Python网络爬虫项目。它通过分析并模拟其后端API请求，能够高效、稳定地采集指定城市和关键词的职位信息，并将包含完整职位描述的详细数据保存为Excel文件。

该项目旨在解决实际爬取中常见的**反爬虫**、**动态数据加载**和**Cookie失效**等核心痛点，集成了一套高拟真度的请求策略和强大的交互式错误处理机制。

---

## 核心亮点 (Key Features)

-   **🎯 API优先策略**：放弃了传统低效且脆弱的HTML页面解析，通过浏览器抓包分析，直接调用后端数据API，获取结构化的JSON数据，极大地提升了采集效率和数据准确性。

-   **🛡️ 强大的反-反爬虫机制**：
    -   **智能交互式Cookie管理**：当API返回特定错误码（表明Cookie失效或需要验证）时，脚本会自动暂停并提示用户在浏览器中更新Cookie文件，**无需重启程序**即可继续任务，极大地提升了长时间采集的成功率。
    -   **高度拟真请求头**：精心构造了包含`User-Agent`, `Referer`, `sec-ch-ua`等在内的完整请求头，最大程度模拟真实用户的浏览器行为。
    -   **动态安全参数处理**：能够自动从列表页API的响应中提取`lid`和`securityId`等动态参数，并用于后续的翻页和详情页请求。
    -   **多层级随机延时**：在请求列表页、详情页以及切换关键词之间，都加入了人性化的随机延时，有效规避基于请求频率的IP封锁策略。

-   **💪 高健壮性与错误处理**：
    -   全面的`try...except`异常捕获，覆盖网络请求错误（如HTTP 4xx/5xx）和数据解析错误。
    -   基于API响应码的精准逻辑判断，只在必要时触发重试或Cookie更新，避免无效操作。
    -   提供备用输出方案，当Excel写入失败时，自动将数据保存为JSON文件，确保数据万无一失。

-   **📄 结构化数据输出**：使用`pandas`库将采集到的数据整理成结构化表格，并输出为通用的`.xlsx`格式，方便后续进行数据分析或导入其他系统。

## 技术栈 (Technology Stack)

-   **核心语言**: Python 3
-   **HTTP请求**: `requests` (利用其`Session`对象持久化Cookie)
-   **数据解析**: `BeautifulSoup4` & `lxml` (用于处理HTML格式的职位描述)
-   **数据处理与导出**: `pandas` & `openpyxl`

## 安装与配置 (Installation & Setup)

1.  **克隆仓库到本地**
    ```bash
    git clone https://github.com/xiang-ys/boss-website-data-collection-project.git
    cd boss-website-data-collection-project
    ```

2.  **创建并激活Python虚拟环境 (推荐)**
    ```bash
    # Windows
    python -m venv venv
    .\venv\Scripts\activate

    # macOS / Linux
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  **安装项目依赖**
    ```bash
    pip install -r requirements.txt
    ```

4.  **配置Cookie (关键步骤!)**
    -   在你的浏览器中（推荐使用Chrome），安装一个Cookie管理插件，如 **Cookie-Editor**。
    -   打开并登录 [https://www.zhipin.com/](https://www.zhipin.com/)，**并成功进行一次职位搜索**。
    -   点击Cookie-Editor插件图标，选择 "Export" -> "Export as Netscape" (或 "Export as Text")。
    -   将导出的内容粘贴到项目根目录下的 `cookies.txt` 文件中，覆盖原有内容。

## 如何使用 (Usage)

1.  **修改`boss.py`脚本配置**: 打开`boss.py`文件，根据你的需求修改顶部的配置项：
    ```python
    # --- 配置 ---
    SEARCH_KEYWORDS_LIST = ['数据分析', 'Python开发'] # 修改为你想要的关键词
    TARGET_CITIES = {
        "成都": "101270100",
        "北京": "101010100"  # 修改或添加目标城市和对应的代码
    }
    EXPERIENCE_CODE = '108'  # 经验要求代码，'108'代表实习，留空''代表不限
    MAX_PAGES_TO_SCRAPE_PER_CITY_KEYWORD = 10 # 每个关键词组合要爬取的最大页数
    ```

2.  **运行脚本**
    ```bash
    python boss.py
    ```

3.  **查看结果**: 脚本运行结束后，将在同目录下生成一个以时间戳命名的Excel文件，例如 `BOSS直聘_岗位_成都_北京_20240520_153000.xlsx`。

---

## 未来可优化方向 (Potential Improvements)

-   [ ] **全自动登录**: 引入`Selenium`或`Playwright`等浏览器自动化工具，模拟登录过程以自动获取和更新Cookie，实现无人值守的7x24小时运行。
-   [ ] **异步化改造**: 使用`asyncio`配合`aiohttp`库，将串行的网络请求改造为并行异步请求，可以成倍提升大规模数据采集的效率。
-   [ ] **数据库集成**: 将数据直接存入MySQL或MongoDB等数据库，便于进行更复杂的数据管理、查询和增量更新。
-   [ ] **代理IP池**: 集成代理IP服务，通过轮换IP地址来进一步降低被目标网站限制的风险。
-   [ ] **Docker容器化**: 将整个应用打包成Docker镜像，实现一键部署和环境隔离。

## 免责声明 (Disclaimer)

本项目仅用于个人学习和技术研究，请勿用于任何商业用途或非法行为。使用者应自觉遵守目标网站的`robots.txt`协议和用户协议。对于因使用不当而造成的任何后果，本人概不负责。
