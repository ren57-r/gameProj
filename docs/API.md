# API 文档

## 目录
1. [认证服务](#认证服务)
2. [用户服务](#用户服务)
3. [支付服务](#支付服务)
4. [游戏服务](#游戏服务)
5. [分析服务](#分析服务)
6. [通用规范](#通用规范)

---

## 认证服务

### 1. 用户注册

```http
POST /api/auth/register
Content-Type: application/json
```

**请求体：**
```json
{
  "username": "player123",
  "email": "player@example.com",
  "password": "SecurePassword123!",
  "deviceId": "device_uuid_here"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "userId": 12345,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 86400,
  "user": {
    "id": 12345,
    "username": "player123",
    "email": "player@example.com"
  }
}
```

**错误响应 (400)：**
```json
{
  "success": false,
  "error": "Username already exists",
  "code": "USERNAME_DUPLICATE"
}
```

---

### 2. 用户登录

```http
POST /api/auth/login
Content-Type: application/json
```

**请求体：**
```json
{
  "username": "player123",
  "password": "SecurePassword123!",
  "deviceId": "device_uuid_here"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "userId": 12345,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 86400,
  "lastLoginAt": "2024-06-15T10:30:00Z",
  "user": {
    "id": 12345,
    "username": "player123",
    "nickname": "Player123",
    "level": 5,
    "totalPlayTime": 3600
  }
}
```

---

### 3. Token 刷新

```http
POST /api/auth/refresh-token
Content-Type: application/json
Authorization: Bearer {old_token}
```

**请求体：**
```json
{
  "refreshToken": "refresh_token_here"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "token": "new_jwt_token",
  "expiresIn": 86400
}
```

---

## 用户服务

### 1. 获取用户档案

```http
GET /api/user/profile
Authorization: Bearer {token}
```

**响应 (200)：**
```json
{
  "success": true,
  "profile": {
    "userId": 12345,
    "username": "player123",
    "nickname": "Player123",
    "level": 5,
    "experience": 2500,
    "totalPlayTime": 3600,
    "avatar": "https://cdn.example.com/avatars/12345.png",
    "joinDate": "2024-01-15T08:00:00Z",
    "lastPlayedAt": "2024-06-15T10:30:00Z",
    "statistics": {
      "totalGames": 150,
      "wins": 85,
      "winRate": 0.567,
      "highestScore": 25000
    }
  }
}
```

---

### 2. 更新用户档案

```http
PUT /api/user/profile
Content-Type: application/json
Authorization: Bearer {token}
```

**请求体：**
```json
{
  "nickname": "NewNickname",
  "avatar": "avatar_url_or_base64"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "message": "Profile updated successfully",
  "profile": {
    "userId": 12345,
    "nickname": "NewNickname",
    "avatar": "https://cdn.example.com/avatars/12345.png"
  }
}
```

---

### 3. 获取虚拟货币余额

```http
GET /api/user/currency
Authorization: Bearer {token}
```

**响应 (200)：**
```json
{
  "success": true,
  "currency": {
    "coins": 5000,
    "gems": 150,
    "lastUpdated": "2024-06-15T10:30:00Z"
  }
}
```

---

## 支付服务

### 1. 获取商品列表

```http
GET /api/payment/products
Authorization: Bearer {token}
```

**响应 (200)：**
```json
{
  "success": true,
  "products": [
    {
      "productId": "com.game.gems.100",
      "name": "100 Gems",
      "description": "100 Premium Gems",
      "price": 0.99,
      "currency": "USD",
      "gems": 100,
      "bonus": 0,
      "category": "gems",
      "icon": "https://cdn.example.com/icons/gems.png"
    },
    {
      "productId": "com.game.gems.500",
      "name": "500 Gems",
      "description": "500 Premium Gems + 50 Bonus",
      "price": 4.99,
      "currency": "USD",
      "gems": 500,
      "bonus": 50,
      "category": "gems",
      "icon": "https://cdn.example.com/icons/gems.png"
    }
  ]
}
```

---

### 2. 验证支付收据

```http
POST /api/payment/verify
Content-Type: application/json
Authorization: Bearer {token}
```

**请求体：**
```json
{
  "productId": "com.game.gems.100",
  "platform": "ios",
  "receiptData": "MIIGbAYJKoZIhvcNAQcCoIIGXTCCBlkCAQExCzAJBgUrDgMCGgUAMIIFXAYJKo...",
  "bundleId": "com.mycompany.mygame",
  "packageName": "com.mycompany.mygame",
  "transactionId": "1234567890"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "message": "Payment verified successfully",
  "transactionId": "txn_12345678",
  "productId": "com.game.gems.100",
  "gems": 100,
  "bonus": 0,
  "currency": {
    "coins": 5000,
    "gems": 250
  },
  "timestamp": "2024-06-15T10:30:00Z"
}
```

**错误响应 (400)：**
```json
{
  "success": false,
  "error": "Receipt already verified",
  "code": "DUPLICATE_RECEIPT"
}
```

---

### 3. 获取交易历史

```http
GET /api/payment/transactions?limit=20&offset=0
Authorization: Bearer {token}
```

**响应 (200)：**
```json
{
  "success": true,
  "transactions": [
    {
      "transactionId": "txn_12345678",
      "productId": "com.game.gems.100",
      "productName": "100 Gems",
      "amount": 0.99,
      "currency": "USD",
      "status": "completed",
      "platform": "ios",
      "timestamp": "2024-06-15T10:30:00Z"
    }
  ],
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

---

## 游戏服务

### 1. 获取关卡信息

```http
GET /api/game/levels/{gameType}
Authorization: Bearer {token}
```

**参数：**
- `gameType`: "casual" 或 "fps"

**响应 (200)：**
```json
{
  "success": true,
  "levels": [
    {
      "levelId": 1,
      "name": "Level 1 - Tutorial",
      "difficulty": 1,
      "description": "Learn the basics",
      "rewards": {
        "coins": 100,
        "experience": 50
      },
      "isUnlocked": true,
      "bestScore": 5000,
      "isCompleted": true,
      "stars": 3
    },
    {
      "levelId": 2,
      "name": "Level 2 - Challenge",
      "difficulty": 2,
      "description": "Increase difficulty",
      "rewards": {
        "coins": 200,
        "experience": 100
      },
      "isUnlocked": true,
      "bestScore": 0,
      "isCompleted": false,
      "stars": 0
    }
  ]
}
```

---

### 2. 上传游戏结果

```http
POST /api/game/submit-result
Content-Type: application/json
Authorization: Bearer {token}
```

**请求体：**
```json
{
  "gameType": "casual",
  "levelId": 1,
  "score": 12500,
  "duration": 180,
  "isWin": true,
  "difficulty": 1,
  "playedAt": "2024-06-15T10:30:00Z",
  "deviceId": "device_uuid",
  "clientVersion": "1.0.0"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "message": "Game result submitted successfully",
  "rewards": {
    "coins": 500,
    "experience": 200
  },
  "newLevel": 6,
  "newExperience": 2700,
  "leaderboardRank": 42,
  "achievements": [
    {
      "achievementId": "first_win",
      "name": "First Victory",
      "description": "Win your first game",
      "reward": 100
    }
  ]
}
```

---

### 3. 获取排行榜

```http
GET /api/game/leaderboard/{gameType}?limit=100&period=weekly
Authorization: Bearer {token}
```

**参数：**
- `gameType`: "casual" 或 "fps"
- `period`: "daily", "weekly", "monthly", "all-time"
- `limit`: 返回数量 (最多100)

**响应 (200)：**
```json
{
  "success": true,
  "leaderboard": [
    {
      "rank": 1,
      "userId": 99999,
      "nickname": "TopPlayer",
      "score": 500000,
      "level": 50,
      "wins": 1000,
      "avatar": "https://cdn.example.com/avatars/99999.png",
      "country": "US"
    },
    {
      "rank": 2,
      "userId": 12345,
      "nickname": "Player123",
      "score": 450000,
      "level": 48,
      "wins": 950,
      "avatar": "https://cdn.example.com/avatars/12345.png",
      "country": "CN"
    }
  ],
  "period": "weekly",
  "updatedAt": "2024-06-15T00:00:00Z",
  "yourRank": 2
}
```

---

### 4. 获取成就系统

```http
GET /api/game/achievements
Authorization: Bearer {token}
```

**响应 (200)：**
```json
{
  "success": true,
  "achievements": [
    {
      "achievementId": "first_win",
      "name": "First Victory",
      "description": "Win your first game",
      "icon": "https://cdn.example.com/achievements/first_win.png",
      "reward": 100,
      "isUnlocked": true,
      "unlockedAt": "2024-06-15T10:30:00Z"
    },
    {
      "achievementId": "ten_wins",
      "name": "Getting Started",
      "description": "Win 10 games",
      "icon": "https://cdn.example.com/achievements/ten_wins.png",
      "reward": 500,
      "isUnlocked": false,
      "progress": 8,
      "total": 10
    }
  ]
}
```

---

## 分析服务

### 1. 上报游戏事件

```http
POST /api/analytics/events
Content-Type: application/json
Authorization: Bearer {token}
```

**请求体：**
```json
{
  "events": [
    {
      "eventName": "game_start",
      "eventData": {
        "gameType": "casual",
        "levelId": 1,
        "deviceType": "iOS"
      },
      "timestamp": "2024-06-15T10:30:00Z"
    },
    {
      "eventName": "shop_opened",
      "eventData": {
        "pageLocation": "main_menu"
      },
      "timestamp": "2024-06-15T10:35:00Z"
    }
  ]
}
```

**响应 (200)：**
```json
{
  "success": true,
  "message": "Events recorded successfully",
  "recordedCount": 2
}
```

---

### 2. 获取用户统计

```http
GET /api/analytics/user-stats?period=7d
Authorization: Bearer {token}
```

**参数：**
- `period`: "1d", "7d", "30d", "all"

**响应 (200)：**
```json
{
  "success": true,
  "stats": {
    "totalSessionCount": 150,
    "totalPlayTime": 36000,
    "averageSessionLength": 240,
    "dailyActiveCheckins": 20,
    "lastActiveAt": "2024-06-15T10:30:00Z",
    "retentionDays": 45,
    "spendingTotal": 49.99,
    "spendingCount": 5
  },
  "period": "7d"
}
```

---

## 通用规范

### 请求头

所有需要认证的请求都需要提供：

```http
Authorization: Bearer {jwt_token}
Content-Type: application/json
User-Agent: GameProj/1.0.0
X-Client-Version: 1.0.0
X-Device-Id: device_uuid_here
```

### 响应格式

**成功响应 (2xx)：**
```json
{
  "success": true,
  "data": {},
  "message": "Operation successful"
}
```

**错误响应 (4xx/5xx)：**
```json
{
  "success": false,
  "error": "Human readable error message",
  "code": "ERROR_CODE",
  "details": {}
}
```

### 通用错误码

| 错误码 | HTTP状态 | 说明 |
|--------|---------|------|
| `UNAUTHORIZED` | 401 | 未授权，需要登录 |
| `FORBIDDEN` | 403 | 禁止访问 |
| `NOT_FOUND` | 404 | 资源不存在 |
| `VALIDATION_ERROR` | 400 | 请求参数验证失败 |
| `SERVER_ERROR` | 500 | 服务器内部错误 |
| `RATE_LIMIT_EXCEEDED` | 429 | 请求频率过高 |

### 分页规范

```http
GET /api/resource?limit=20&offset=0&sort=created_at&order=desc
```

**响应：**
```json
{
  "success": true,
  "data": [],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 150,
    "pages": 8
  }
}
```

---

**最后更新**: 2024-06-15

