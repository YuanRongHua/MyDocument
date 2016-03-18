### 5. 服务确认接口

| 接口名称 |
| -------- |
|/confirmService.do |

| 接口访问路径 |
| -------- |
|/UserPurchase/confirmService.do |

* 入参 

| 参数名 | 描述 | 类型 | 是否必填 | 例如 |
| -------- | -------- | -------- | -------- | -------- |  
| id|  | user_purchase_id | Long | Y | 102548 |
| status| 服务状态 | Int | Y | 4 |
| feedback| 意见反馈 | String | Y | Good |


* 返回结果 

```
{
    "restCode": 200,
    "appCode": "_successful_",
    "msg": "",
    "result": 1 //updateRow
}
```