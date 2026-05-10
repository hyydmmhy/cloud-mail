# Cloudflare D1 数据库操作指南

## 一、安装 Wrangler

```bash
npm install -g wrangler
```

安装完成后验证版本：

```bash
wrangler --version
```

---

## 二、登录 Cloudflare 账号

```bash
wrangler login
```

执行后会自动打开浏览器，授权完成即可。

---

## 三、查看 D1 数据库列表

```bash
wrangler d1 list
```

---

## 四、常用查询命令

以下命令中 `your-database` 替换为你的数据库名，`--remote` 表示操作线上数据库，去掉则操作本地开发库。

### 查看所有表

```bash
wrangler d1 execute your-database --command "SELECT name FROM sqlite_master WHERE type='table'" --remote
```

### 查看表结构（字段信息）

```bash
wrangler d1 execute your-database --command "PRAGMA table_info(your_table)" --remote
```

### 查询数据

```bash
# 查前 20 条
wrangler d1 execute your-database --command "SELECT * FROM your_table LIMIT 20" --remote

# 带条件查询
wrangler d1 execute your-database --command "SELECT * FROM your_table WHERE status=1" --remote

# 统计行数
wrangler d1 execute your-database --command "SELECT COUNT(*) FROM your_table" --remote
```

### 输出 JSON 格式（推荐，显示更完整）

```bash
wrangler d1 execute your-database --command "SELECT * FROM your_table LIMIT 20" --remote --json
```

---

## 五、常用修改命令

**修改前务必先用 SELECT 确认条件正确，D1 没有撤销功能。**

### 更新数据

```bash
wrangler d1 execute your-database --command "UPDATE your_table SET field='new_value' WHERE id=1" --remote
```

### 更新多个字段

```bash
wrangler d1 execute your-database --command "UPDATE your_table SET field1='value1', field2=0 WHERE id=1" --remote
```

### 插入数据

```bash
wrangler d1 execute your-database --command "INSERT INTO your_table (field1, field2) VALUES ('value1', 'value2')" --remote
```

### 删除数据

```bash
wrangler d1 execute your-database --command "DELETE FROM your_table WHERE id=1" --remote
```

---

## 六、执行 SQL 文件

适合批量操作，把 SQL 写进文件再执行：

```bash
wrangler d1 execute your-database --file ./your_script.sql --remote
```

---

## 七、注意事项

- `--remote` 操作的是线上生产数据库，务必谨慎
- 修改数据前先 `SELECT` 确认目标行
- D1 不支持传统 TCP 连接，只能通过 Wrangler 命令行或 Worker 代码访问
- 数据库名可在 `wrangler.toml` 的 `database_name` 字段找到，或通过 `wrangler d1 list` 查看
