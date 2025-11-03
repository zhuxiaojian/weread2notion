# 微信读书 Notion 同步工具

这是一个将微信读书数据同步到 Notion 数据库的 Python 工具。它可以自动获取你的微信读书书籍列表、划线、笔记、阅读进度等信息，并将这些信息整理成结构化的数据存储在 Notion 中。

## 功能特点

- 📚 **自动同步书籍信息**：获取书名、作者、封面、ISBN、评分等基本信息
- 📝 **同步划线内容**：将所有划线内容按章节整理，支持不同样式的划线（直线、背景色、波浪线）
- ✍️ **同步笔记和点评**：获取书籍笔记和用户点评
- 📊 **阅读进度跟踪**：记录阅读状态、阅读时间、阅读进度等信息
- 🔄 **增量同步**：只同步新添加的书籍，避免重复同步
- 🎨 **美观的排版**：在 Notion 中自动生成目录、章节标题、不同颜色的划线块
- ☁️ **支持 Cookie Cloud**：可选的 Cookie 云存储支持

## 安装要求

- Python 3.7+
- 所需的 Python 包：
  - `requests==2.32.5`
  - `notion-client==2.5.0`
  - `github-heatmap==1.2.7`
  - `python-dotenv==1.2.1`
  - `retrying==1.4.2`

## 安装步骤

1. 克隆或下载本项目
2. 安装依赖包：
   ```bash
   pip install -r requirements.txt
   ```
   或手动安装指定版本：
   ```bash
   pip install requests==2.32.5 notion-client==2.5.0 github-heatmap==1.2.7 python-dotenv==1.2.1 retrying==1.4.2
   ```

## 配置说明

在使用前，需要配置以下环境变量：

### 必需配置

1. **WEREAD_COOKIE**：微信读书的 Cookie
   - 在浏览器中登录微信读书网页版
   - 打开开发者工具（F12）
   - 在 Network 面板中找到任意一个请求，查看 Cookie 中的 `wr_vid` 和 `wr_skey` 等值
   - ⚠️注意，cookie 一定不能换行，wr_skey 需要从手机获取

2. **NOTION_TOKEN**：Notion API Token
   - 访问 [Notion Integration](https://www.notion.so/my-integrations)
   - 创建一个新的 Integration
   - 复制生成的 Internal Integration Token

3. **NOTION_PAGE** 或 **NOTION_DATABASE_ID**：Notion 数据库 ID
   - 在 Notion 中创建一个数据库，至少包含以下属性：
     - `BookName`（标题）
     - `BookId`（文本）
     - `ISBN`（文本）
     - `URL`（URL）
     - `Author`（文本）
     - `Sort`（数字）
     - `Rating`（数字）
     - `Cover`（文件）
     - `Status`（选择：在读/读完）
     - `ReadingTime`（文本）
     - `Progress`（数字）
     - `Date`（日期）
     - `Categories`（多选）
   - 获取数据库的 ID（URL 中的长字符串）


## 环境变量配置文件

创建 `.env` 文件，内容如下：

```env
# 微信读书 Cookie
WEREAD_COOKIE="your_weread_cookie_here"

# Notion 配置
NOTION_TOKEN="your_notion_token_here"
NOTION_DATABASE_ID="your_notion_database_id_here"

# 可选：Cookie Cloud 配置
CC_URL="https://cookiecloud.example.com/"
CC_ID="your_cookiecloud_id"
CC_PASSWORD="your_cookiecloud_password"
```

## 使用方法

1. 配置好环境变量
2. 运行脚本：
   ```bash
   python weread.py
   ```

脚本会自动：
- 获取你的微信读书书籍列表
- 检查 Notion 数据库中已同步的书籍
- 同步新添加的书籍信息
- 将划线、笔记等内容整理后添加到 Notion

## 数据库结构建议

在 Notion 中创建数据库时，建议设置以下属性：

### 基础属性
- **BookName**：标题类型，存储书名
- **BookId**：文本类型，存储微信读书书籍 ID
- **ISBN**：文本类型，存储书籍 ISBN
- **URL**：URL 类型，存储微信读书阅读链接
- **Author**：文本类型，存储作者
- **Cover**：文件类型，存储书籍封面
- **Categories**：多选类型，存储书籍分类

### 阅读信息
- **Status**：选择类型，选项："在读"、"读完"
- **ReadingTime**：文本类型，存储阅读时间（如："2时30分"）
- **Progress**：数字类型，存储阅读进度百分比
- **Date**：日期类型，存储完成阅读的日期

### 评分信息
- **Rating**：数字类型，存储书籍评分（0-5分）
- **Sort**：数字类型，用于排序，按添加时间排序

## 注意事项

1. **Cookie 有效性**：微信读书 Cookie 可能会过期，需要定期更新
2. **API 限制**：Notion API 有调用频率限制，大量同步时可能需要添加延迟
3. **数据备份**：建议定期备份 Notion 数据库
4. **隐私安全**：妥善保管你的 Cookie 和 Notion Token，不要泄露给他人

## 故障排除

### 常见问题

1. **Cookie 错误**
   - 确保 Cookie 完整且有效
   - 检查是否需要登录验证

2. **Notion API 错误**
   - 检查 Token 是否正确
   - 确认 Integration 是否有访问数据库的权限
   - 检查数据库 ID 是否正确

3. **同步中断**
   - 检查网络连接
   - 查看 Notion API 调用频率是否超限
   - 检查是否有书籍数据格式异常

### 调试方法

1. 启用详细日志：
   ```python
   import logging
   logging.basicConfig(level=logging.DEBUG)
   ```

2. 检查中间数据：
   - 脚本会在控制台输出同步进度
   - 可以添加临时打印语句查看具体数据

## 更新日志

### v1.0.0
- 初始版本，支持基本的书籍信息同步
- 支持划线、笔记内容同步
- 支持阅读进度跟踪
- 支持 Cookie Cloud 配置

## 贡献

欢迎提交 Issue 和 Pull Request 来改进这个项目！

## 许可证

本项目采用 MIT 许可证。
