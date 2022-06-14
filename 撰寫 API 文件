# 撰寫 API 文件

> 文件內容包括：用途，路由、參數、回傳值
> 更詳細的會包括：參數放置的位置(route, queryString, body)、參數是否必填，回傳的 JSON 範例

範例

```markdown
## GET /card/{id}

**查詢指定編號的卡片**

### Parameter

- Route
  - `id (int, required)` 卡片編號

example: https://exampleProjN.com/api/card/1

### Response

200: 回傳對應的卡片
{
"id": 0,
"name": "string",
"description": "string",
"attack": 0,
"health": 0,
"cost": 0
}

404: 找不到
```

### postman 的描述區域

- API 用途
- 參數說明，除了 request body 說明之外，也能說明 uri 參數從哪來的。
- 成功或失敗案例的說明：因為目前我還不知道怎麼在 Response 加上註解。
- 別名，類似中文名稱。

## 編輯房間

### 用途

- 編輯房間
- 同時處理增、刪、修動作
- 可以陣列傳入 room_id

### header

|      key      |        值        | 備註  |
| :-----------: | :--------------: | :---: |
| Authorization | Bearer {{token}} | token |

### 參數

|   key   |   值   |             驗證規則              |
| :-----: | :----: | :-------------------------------: |
| account | string |             required              |
|  role   | string | required, string, in:member,guest |
| room_id | array  |     required, exists:rooms,id     |

./postman_doc_gen [C:\Users\RD\Desktop\V5-Cloud.postman_collection.json] -o [C:\Users\RD\Desktop] 