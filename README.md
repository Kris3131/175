### 一、需求

主要功能：

1. 用戶管理(後台管理)
2. 寵物管理
3. 公園資訊管理
4. 狗狗聚會
5. 走失通報
6. 通知系統
7. Google Maps 整合
8. 協尋功能

### Function Flow

#### 使用者管理流程

- **註冊**

  1. 使用者填寫註冊表單。
  2. 後端驗證內容。
  3. 創建使用者並儲存到資料庫。
  4. 返回成功訊息或錯誤訊息。

- **登入**

  1. 使用者填寫登入表單。
  2. 後端驗證使用者名和密碼。
  3. 驗證成功則生成 JWT，返回給使用者。
  4. 使用者使用 JWT 。

#### 2. 寵物管理流程

- **新增寵物**

  1. 使用者提交寵物表單。
  2. 後端驗證數據。
  3. 儲存寵物資訊到資料庫。
  4. 返回成功訊息或錯誤訊息。

- **更新寵物訊息**

  1. 使用者更新的寵物資訊。
  2. 後端驗證數據。
  3. 更新資料庫中的寵物資訊。
  4. 返回成功訊息或錯誤訊息。

#### 3. 公園資訊管理流程

- **新增公園**

  1. 使用者新增公園。
  2. 後端驗證數據。
  3. 儲存公園到資料庫。
  4. 返回成功訊息或錯誤訊息。

- **公園評價**
  1. 使用者新增公園評價。
  2. 後端驗證數據。
  3. 儲存評價到資料庫。
  4. 返回成功訊息或錯誤訊息。

#### 4. 活動管理流程

- **新增活動**

  1. 使用者新增活動。
  2. 後端驗證數據。
  3. 儲存活動到資料庫。
  4. 返回成功訊息或錯誤訊息。

- **報名活動**
  1. 使用者提交報名申請。
  2. 後端驗證請求。
  3. 更新資料庫中的報名訊息。
  4. 返回成功訊息或錯誤訊息。

#### 5. 走失通報流程

- **發布走失通報**
  1. 使用者新增走失通報。
  2. 後端驗證數據。
  3. 儲存走失通報到資料庫。
  4. 通知系統發送通知。
  5. 返回成功訊息或錯誤訊息。

#### 6. Google Maps 整合

- **顯示公園位置**

  1. 使用者請求查看公園位置。
  2. 後端調用 Google Maps API，獲取公園的地理位置。
  3. 返回地理位置數據，前端顯示在地圖上。

- **顯示走失地點**
  1. 使用者發布或查看走失通報。
  2. 後端儲存或查詢走失地點的地理位置。
  3. 調用 Google Maps API 獲取地圖數據。
  4. 返回地圖數據，前端顯示在地圖上。

#### 7. 協尋功能（連接第三方資訊）

- **獲取協尋資訊**
  1. 使用者請求協尋資訊。
  2. 後端調用第三方 API（例如 data.gov.tw 的協尋資料集）。
  3. 解析第三方 API 返回的數據。
  4. 返回協尋資訊給前端。

### 三、系統架構圖

```
                     +--------------------+
                     |    API Gateway     |
                     +---------+----------+
                               |
            +------------------+------------------+
            |        |         |                  |
    +-------+-------+--+-------+-------+  +-------+-------+
    |  User Service |  |  Pet Service  |  | Event Service |
    +-------+-------+  +-------+-------+  +-------+-------+
            |                  |                  |
   +--------+--------+ +-------+-------+  +-------+-------+
   |   MySQL Database  | |   MySQL Database  |  |   MySQL Database  |
   +-------------------+ +-------------------+  +-------------------+
            |
    +-------+-------+
    |  Google Maps  |
    +-------+-------+
            |
    +-------+-------+
    | Third-Party API|
    +----------------+
```

### 四、API 設計

#### 1. User Service

- **API**:
  - POST /users/register
  - POST /users/login
  - GET /users/profile
  - PUT /users/profile
- **Table**:
  - User（id, username, password, email, created_at, updated_at）
  - UserProfile（id, user_id, name, age, gender, bio, created_at, updated_at）

#### 2. Pet Service

- **API**:
  - POST /pets
  - GET /pets
  - PUT /pets/:id
  - DELETE /pets/:id
- **Table**:
  - Pet（id, user_id, name, type, breed, age, created_at, updated_at）

#### 3. Event Service

- **API**:
  - POST /events
  - GET /events
  - PUT /events/:id
  - DELETE /events/:id
  - POST /events/:id/participants
- **Table**:
  - Event（id, title, description, date, location, host_user_id, created_at, updated_at）
  - EventParticipant（id, event_id, user_id, status, created_at, updated_at）

#### 4. Park Service

- **API**:
  - POST /parks
  - GET /parks
  - PUT /parks/:id
  - DELETE /parks/:id
  - POST /parks/:id/reviews
- **Table**:
  - Park（id, name, location, description, created_at, updated_at）
  - ParkReview（id, park_id, user_id, rating, comment, created_at, updated_at）

#### 5. LostPet Service

- **API**:
  - POST /lost-pets
  - GET /lost-pets
  - PUT /lost-pets/:id
  - DELETE /lost-pets/:id
- **Table**:
  - LostPet（id, pet_id, user_id, lost_date, location, description, contact_info, created_at, updated_at）

#### 6. Notification Service

- **功能**：處理通知發送（如走失通報）。
- **消息**：
  - LostPet 通報事件
  - Event 通知事件

#### 7. Google Maps 整合

- **API**:
  - GET /parks/:id/map
  - GET /lost-pets/:id/map
- **數據模型**:
  - Park（增加 location 儲存地理位置）
  - LostPet（增加 location 儲存走失地點）

#### 8. 協尋功能

- **API**:
  - GET /search-pets
- **連接第三方 API**:
  - 使用 `axios` 或 `fetch` 調用第三方 API，解析返回的 JSON 數據，並將其轉換為應用需要的格式。
