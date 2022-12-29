# laravel 命名原則

- 對於類、介面/契約、特性：使用 大駝峰式「PascalCase」
- 對於常量：使用「TITLE_CASE」
- 對於函式/方法、類屬性和變數：使用 小駝峰式「camelCase」
- 對於陣列索引/資料庫欄位名/模型可填充項/模型關係：使用 蛇形命名法「lower_snake_case」
- 對於路由：使用 短橫線「lower-kebab-case」

|                         |            命名方式             |                 範例                  |
| :---------------------: | :-----------------------------: | :-----------------------------------: |
|       Controller        |          單數、大駝峰           |            UserController             |
|          Route          |              複數               |              articles/1               |
| Named Route - 路由命名  |       使用點標記的蛇底式        |           users.show_active           |
|          Model          |              單數               |                 User                  |
| hasOne, belongTo 的關聯 |           單數/小駝峰           |            articleComment             |
|        其他關連         |           複數/小駝峰           |            articleComments            |
|         資料表          |           複數/蛇底式           |           article_comments            |
|   Pivot Table 中介表    | 以字母順序排列的單數 Model 名稱 |             article_user              |
|       資料表欄位        |    蛇底式/不包含 model 名稱     |              meta_title               |
|       model 屬性        |             蛇底式              |          $model->created_at           |
|   Foreign Key - 外鍵    | 以單數 Model 名稱後方加上 \_id  |              article_id               |
|          方法           |             小駝峰              |                getAll                 |
|    測試類別中的方法     |             小駝峰              |       testGuestCannotSeeArticle       |
|          變數           |             小駝峰              |          $articlesWithAuthor          |
|       Collection        |         描述性名稱/複數         | $activeUsers = User::active()->get()  |
|          物件           |         描述性名稱/單數         | $activeUser = User::active()->first() |
| 設定檔及語系檔的索引鍵  |             蛇底式              |           articles_enabled            |
|          View           |           kebab-case            |        show-filtered.blade.php        |
|         設定檔          |             蛇底式              |          google_calendar.php          |
|     Contract (界面)     |          形容詞或名詞           |        AuthenticationInterface        |
|          trait          |             形容詞              |              Notifiable               |
