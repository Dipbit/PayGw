# Dipbit Pay Gateway API 
## 商户接入
### 概述
商户接入Dipbit支付系统能够完成的功能包括充币、提币、充币查询、提币查询、余额查询。客服人员在完成商户的注册之后，会将一次性激活密钥颁发给商户，
一次性激活密钥有效期为24小时，用户在获取激活密钥后，需要尽快调用激活授权API，完成激活并获取调用支付API接口所需要的ACCESS_KEY和ACCESS_SECRET。

环境 | HTTPS请求地址
------------ | -------------
正式环境 | https://paygw.dipbit.com/

API定义详见 https://app.swaggerhub.com/apis/HollyAtGwave/dipbit-pay_gateway_api/1.0.0

## 激活授权API
```http request
/api/v1/key/init
```
该API仅在后台人员进行初始化后的24小时进行调用有效，并且参数token只可调用一次，若超过时效或者token已使用，请联系客服人员进行重置。
## 请求签名
除了激活授权API的调用，支付API所有API请求需要用到激活授权API返回的accessKey和accessSecret来进行数字签名，请妥善保存防止泄露。
数字签名采用MD5withRSA算法。
API请求的签名和调用步骤如下：
* 将API请求的所有参数名称按照字典顺序进行排序，如访问API /api/v1/deposit/apply, 请求参数为merchantID=2018071202345， 
merchantUserID=userA, currency=ETH, 按照字典顺序，参数重新排序为currency，merchantID，merchantUserID，则需要签名的数据为：
currency=ETH&merchantID=2018071202345&merchantUserID=userA，将签名数据通过MD5withRSA并使用accessSecret进行签名。

* 进行数字签名(Java) 
    ```Java
      //取得私钥  
      PKCS8EncodedKeySpec pkcs8KeySpec=new PKCS8EncodedKeySpec(accessSecret);  
      KeyFactory keyFactory=KeyFactory.getInstance("RSA");  
      //生成私钥  
      PrivateKey priKey=keyFactory.generatePrivate(pkcs8KeySpec);  
      //实例化Signature  
      Signature signature = Signature.getInstance("MD5withRSA");  
      //初始化Signature  
      signature.initSign(priKey);  
      //更新  
      signature.update(data);  
      YOUR_SIGNATURE = signature.sign()
    ```
 * 将签名得到的签名YOUR_SIGNATURE和accessKey作为HTTP请求的header参数X-AccessKeyId=accessKey， signature=YOUR_SIGNATURE

## 支付API
1. **充币申请**
```http request
/api/v1/deposit/apply
```

2. **充币查询**
```http request
/api/v1/deposit/list
```

3. **提币申请**
```http request
/api/v1/withdraw/apply
```

4. **提币查询**
```http request
/api/v1/withdraw/list
```

5. **余额查询**
```http request
/api/v1/balance
```

6. **币种列表**
```http request
/api/v1/currencies
```

## 客户端
使用者可根据API定义（https://github.com/Dipbit/PayGw/blob/master/yaml-resolved/swagger.yaml），在 https://editor.swagger.io 自主生成客户端代码
