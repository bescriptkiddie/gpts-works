# GPTs Works

GPTs Works是一个第三方GPT存储中心。

## 介绍

这个项目包含以下三个部分👇

1. 网站

代码位于 `web` 目录中。

你可以在这里看到一个线上demo: [https://gpts.works](https://gpts.works)

![网站](https://ph-files.imgix.net/45d5eb41-849f-415b-91da-079b6e32946e.png?auto=compress&codec=mozjpeg&cs=strip&auto=format&fit=max&dpr=2)

2. 索引系统

代码位于 `index` 目录中。

索引系统用于通过向量搜索GPT。

这里是一个使用了索引系统的GPT: [https://chat.openai.com/g/g-EBKM6RsBl-gpts-works](https://chat.openai.com/g/g-EBKM6RsBl-gpts-works)

![GPT](https://ph-files.imgix.net/f12174a9-085c-42d0-8f0d-06013cd17c7b.png?auto=compress&codec=mozjpeg&cs=strip&auto=format&fit=max&dpr=2)

3. 浏览器扩展程序

代码位于 `extension` 目录中。

浏览器扩展程序用于在ChatGPT探索页面显示第三方GPT。

![扩展程序](https://ph-files.imgix.net/e43df90d-f55f-4dee-b121-5051710397bd.png?auto=compress&codec=mozjpeg&cs=strip&auto=format&fit=max&dpr=2)

## 依赖

- [Vercel](https://vercel.com/): 用于部署网站
- [Vercel Storage Postgres](https://vercel.com/docs/storage/vercel-postgres): 用于存储数据
- [Zilliz Cloud](https://cloud.zilliz.com/): 用于向量存储和搜索

## 快速开始

### 克隆项目

```shell
git clone https://github.com/all-in-aigc/gpts-works.git 项目路径
```

### 准备数据

1. 在你的postgres数据库中创建表,SQL如下:

```sql
CREATE TABLE gpts (
    id SERIAL PRIMARY KEY, 
    uuid VARCHAR(255) UNIQUE NOT NULL,
    org_id VARCHAR(255),
    name VARCHAR(255),
    description TEXT,
    avatar_url TEXT,
    short_url VARCHAR(255),
    author_id VARCHAR(255),
    author_name VARCHAR(255),
    created_at timestamptz,
    updated_at timestamptz,
    detail JSON,
    index_updated_at INT NOT NULL DEFAULT 0
);
```

2. 将你自己的GPT数据插入postgres数据库

### 启动网站

1. 在 `项目路径/web` 中创建.env文件,内容如下:

```
POSTGRES_URL="postgres://default:xxx@xxx.postgres.vercel-storage.com/verceldb"

INDEX_API_BASE_URI="http://127.0.0.1:8068"
INDEX_API_KEY="gsk-xxx"
```

2. 安装依赖

```shell
pnpm install
```

3. 启动web服务器

```
make dev
```

4. 访问网站

打开 `http://localhost:8067`

### 启动索引系统

1. 在 `项目路径/index` 中创建.env文件,内容如下:

```
DATABASE_URL=postgres://default:xxx@xxx.postgres.vercel-storage.com:5432/verceldb

AZURE_API_KEY=xxx
AZURE_API_BASE=https://xxx.openai.azure.com/  
AZURE_API_VERSION=2023-07-01-preview
AZURE_LLM_MODEL=gpt-35-turbo-16k
AZURE_EMBED_MODEL=text-embedding-ada-002

STORE_TYPE=zilliz 
STORE_URI=https://xxx.zillizcloud.com
STORE_TOKEN=xxx
STORE_DIM=1536
STORE_COLLECTION=gpts

INDEX_API_KEY=gsk-xxx
```

2. 安装依赖

```shell
pip install -r requirements.txt
```

3. 启动 API 服务器

```
make dev
```

4. 为GPT数据构建索引

```  
curl -X POST -H "Authorization: Bearer gsk-xxx" http://127.0.0.1:8068/gpts/index
```

5. 从索引中搜索GPT

```
curl -X POST -H "Authorization: Bearer gsk-xxx" -H "Content-Type: application/json" -d '{"question": "什么GPT可用于编程?"}' http://127.0.0.1:8068/gpts/index
```

### 启动扩展程序

进入 `项目路径/extension`

1. 安装依赖

```
pnpm install
```

2. 启动服务器

```
make dev
```

3. 调试扩展程序

打开 `chrome://extensions/`, 点击 `加载未打包扩展程序`,选择项目目录

## 感谢

- [GPTs Hunter](https://www.gptshunter.com) 分享了gpts数据
- [next.js](https://github.com/vercel/next.js) 用于网站部署
- [fastapi](https://github.com/tiangolo/fastapi) 用于构建索引系统
- [plasmo](https://github.com/PlasmoHQ/plasmo) 用于浏览器扩展开发
