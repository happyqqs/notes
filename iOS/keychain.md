# Keychain简介

Keychain Services 是 macOS 和 iOS 都提供一种安全地存储敏感信息的工具，keychain保存在iOS系统内部的数据库中，而不是应用沙盒中，keychain中的数据不会随着应用的卸载而消失。

![img](https://cdn.yuque.com/lark/0/2018/png/111990/1527050288978-1b9940a0-9cb6-40be-9e95-df8ec172d4a6.png)

在keychain中保护用户敏感信息

# Keychain结构

Keychain 可以包含任意数量的 keychain item。keychain item一般为一个字典，每条keychain item包含一条data和很多attributes。举个例子，一个用户账户就是一条item，用户名可以作为一个attribute , 密码就是data。对于一个需要保护的 keychain item，比如密码或者私钥（用于加密或者解密的string字节）数据是加密的，会被 keychain 保护起来的；对于无需保护的 keychain item，例如，证书，数据未被加密。

![img](https://cdn.yuque.com/lark/0/2018/png/111990/1527056122663-c9f4fc02-2d47-4b99-95e5-f7637ba63c16.png)

# Keychain使用

例如：通过keychain存储一个网络密码

首次使用app需要证书，但是keychain中还没有存储密码，查找失败。此时app提示用户认证，认证成功后新增一个keychain item。之后服务器需要重新认证时，应用程序可以从keychain中检索证书认证，而不需要打扰用户。若从keychain中检索得到的证书认证失败，才需要提示用户再次认证，认证成功后更新keychain item。

![img](https://cdn.yuque.com/lark/0/2018/png/111990/1527058424463-32732728-1578-43ef-ba3d-239eced1a551.png)

# Keychain services API

keychain item 增删改查

```
SecItemAdd(_ attributes: CFDictionary, _ result: UnsafeMutablePointer<CoreFoundation.CFTypeRef?>?) -> OSStatus
SecItemDelete(_ query: CFDictionary) -> OSStatus
SecItemUpdate(_ query: CFDictionary, _ attributesToUpdate: CFDictionary) -> OSStatus
SecItemCopyMatching(_ query: CFDictionary, _ result: UnsafeMutablePointer<CoreFoundation.CFTypeRef?>?) -> OSStatus
```

# Keychain Access Group

Keychain通过provisioning profile来区分不同的应用，provisioning文件内含有应用的bundleID和添加的access groups。不同的应用是完全无法访问其他应用保存在Keychain的信息，除非指定了同样的access group。指定了同样的group名称后，不同的应用间就可以分享保存在Keychain内的信息。

指定keychain access group的步骤：

1. 先开启Keychain share,选中项目的Target -> Capabilities -> Keychain Sharing。打开这个选项。需要分享Keychain的不同应用添加相同的Group名称(例: com.example.apple-samplecode.GenericKeychainShared)，这里的名称不包括AppIdentifierPrefix
2. 同时项目会生成一个entitlements文件。里面会有Access group，文件内自动添加所有Group名称，然后在每一个Group前自动加上$(AppIdentifierPrefix)前缀，例如：$(AppIdentifierPrefix)com.example.apple-samplecode.GenericKeychainShared。
3. entitlements文件的路径要配置在 Project->build setting->Code Signing Entitlements 里，否则公共区无效。
4. 需要支持跨设备分享的Keychain item添加一条AccessGroup属性,不过代码里Group名称一定要加上AppIdentifierPrefix前缀。

注意：

- 必须是相同的AppIdentifierPrefix和相同的group id才能共享keychain数据
- AppIdentifierPrefix表示发布者的一个身份，与开发者账号相关，一个开发者账号可以有一个或多个App ID如果一个开发者帐号有多个AppIdentifierPrefix（team帐号），需要确认一下mobileprovision文件里面的keychain-access-groups是否和keychain.entitlements文件定义的一致，否则xcode会报错。
- AppIdentifierPrefix可以在开发者账号中找到，也可以在info.plist中添加一对键值对key = AppIdentifierPrefix ，value = $(AppIdentifierPrefix)，再通过NSBundle取出。

# 规范

当我们没有打开keychain sharing，并且没有entitlement文件时，keychain默认以bundleID为group(带AppIdentifierPrefix)。如果我们在版本更新的时候改变了bundleID，或者后续打开了Keychain Sharing并设置了新的keychain Access Group，那么新版本就访问不了旧版本的keychain信息了。解决办法是从一开始我们就打开Keychain Sharing，同时要注意以下几点。

1. 打开Keychain Sharing，添加Keychain Access Group，并且指定每条keychain Item的group，私有的信息就指定app的bundleID为它的group。
2. 打开keychain sharing时，若代码中group传nil，则keychain默认以entitlement文件中的第一个group为group，所以为了保持与之前不打开keychain sharing时的统一，entitlement文件中的第一条Group最好以自己的bundleID命名。
3. 代码内Access group名称一定要有AppIdentifierPrefix前缀。
4. 如果如果传入的group是没有包含在entitlements里面的时候，会报错。
5. keychain基于数据库存储，不允许添加重复条目。所以每条item都必须指定对应的唯一标识符也就是那些主要的key（kSecClass,kSecAttrService,kSecAttrAccount,kSecAttrAccessGroup等），如果Key指定不正确，可能会出现添加后查找不到的问题。kSecAttrSynchronizable也是主要的key之一。它的value值默认为No，如果之前添加的item此条属性为YES，在搜索，更新，删除的时候必须添加此条属性才能查找到之前添加的item。
6. Kechain item字典内添加自定义key时会出现参数不合法的错误。