# Burp Suite Intruder 的四种攻击模式解析
在 Burp Suite 的 Intruder 模块中，有四种攻击模式：

- Sniper（狙击手）
- Battering Ram（攻城锤）
- Pitchfork（草叉）
- Cluster Bomb（集束炸弹）

它们的主要区别在于如何将 Payload 插入到请求中的不同位置。

---

## 1. Sniper（狙击手）

**特点**：每次只替换一个参数位置，使用一个 payload 列表。  
**适用场景**：测试单个参数是否存在漏洞，如 SQL 注入、XSS 等。

**示例**：

- 请求参数：`username=§admin§&password=§123456§`
- Payload 列表：`test1`, `test2`
- 生成请求：
  - `username=test1&password=123456`
  - `username=test2&password=123456`
  - `username=admin&password=test1`
  - `username=admin&password=test2`

---

## 2. Battering Ram（攻城锤）

**特点**：同时替换所有参数位置，使用一个 payload 列表。  
**适用场景**：需要在多个位置插入相同的 payload，如测试会话固定漏洞。

**示例**：

- 请求参数：`username=§admin§&password=§123456§`
- Payload 列表：`test1`, `test2`
- 生成请求：
  - `username=test1&password=test1`
  - `username=test2&password=test2`

---

## 3. Pitchfork（草叉）

**特点**：每个参数位置使用不同的 payload 列表，按顺序一一对应替换。  
**适用场景**：测试多个参数的组合，如用户名和密码的配对。

**示例**：

- 请求参数：`username=§admin§&password=§123456§`
- Payload 列表 1（username）：`user1`, `user2`
- Payload 列表 2（password）：`pass1`, `pass2`
- 生成请求：
  - `username=user1&password=pass1`
  - `username=user2&password=pass2`

---

## 4. Cluster Bomb（集束炸弹）

**特点**：每个参数位置使用不同的 payload 列表，生成所有可能的组合（笛卡尔积）。  
**适用场景**：测试所有参数组合的情况，如暴力破解用户名和密码的所有可能组合。

**示例**：

- 请求参数：`username=§admin§&password=§123456§`
- Payload 列表 1（username）：`user1`, `user2`
- Payload 列表 2（password）：`pass1`, `pass2`
- 生成请求：
  - `username=user1&password=pass1`
  - `username=user1&password=pass2`
  - `username=user2&password=pass1`
  - `username=user2&password=pass2`

---

## 四种模式对比总结表

| 模式           | Payload 列表数 | 替换方式             | 请求数量计算方式             | 适用场景                   |
|----------------|----------------|----------------------|------------------------------|----------------------------|
| Sniper         | 1              | 每次替换一个位置     | payload 数 × 参数位置数      | 单参数漏洞测试             |
| Battering Ram  | 1              | 同时替换所有位置     | payload 数                   | 相同值多位置测试           |
| Pitchfork      | 多             | 不同列表一一对应替换 | 最短 payload 列表的长度      | 参数配对测试               |
| Cluster Bomb   | 多             | 所有组合（笛卡尔积） | 所有 payload 数的乘积        | 所有参数组合测试           |