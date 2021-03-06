# 高级应用场景

### 每条消息需要确认收到

#### 业务场景

在某些场景中，比如企业办公类、订单状态通知，推送的消息需要确保用户一定收到；如果未能及时收到，则需要采取进一步的其他措施。

#### 问题解析

通过网络推送通知/消息，存在通知不能及时地到达用户手里的可能（因为手机原因、网络原因）。所以，需要能够很及时的检查确认客户端是否收到。

#### 解决思路

* JPush Report API 提供一条消息的送达统计。这个统计是实时的，可以在调用推送后立即调用这个 Report API 来检查，并且允许连续间隔一定时长多次检查；
* 开发者App 向自己的应用服务器反馈推送消息收到确认。

### 基于 JPush 实现聊天

	为了满足开发者的 IM 功能需求，极光提供了 JMessage。JMessage 基于 JPush 的长连接技术，增加了 IM 功能特性，满足典型的 IM App 使用场景。
	集成 JMessage SDK 的 App 与 JPush 是共享同一个连接的并且具备完整的 JPush 功能。
详细参考[极光IM指南](../guideline/jmessage_guide/)
	
	


### 多帐户登录的离线消息

#### 业务场景

客户端App 是类QQ（聊天）客户端应用，支持多个帐号登录。

聊天帐号UID 设置为 JPush 别名，从而聊天用户之间可以通过别名互相推送消息。

A 帐号登录后（绑定 A uid 到别名），可以通过 JPush 向 A 推送消息。

A 退出后，登录 B（更新绑定 B uid 到别名）。这时候可以通过 JPush 向 B 推送消息。 但是：继续向 A 推送消息则会报错，通过别名 A uid 找不到目标用户。

#### 问题解析

JPush 别名是针对设备设置的。一台设备上，只能绑定一个别名。新的别名会覆盖，被覆盖掉的别名不再对应设备。

这是 JPush 的机制。

这个业务场景，想要在 A 用户没有注销的情况下，也可以向其推送消息，让 JPush 来保存离线消息，从而 A 再次登录时可以继续收到。

这个业务场景需求对 JPush 是过度依赖了。

JPush 只是做 “推送” 这个唯一的功能。JPush 提供有限时长的离线消息，它是推送机制的一部分，而不是为了保存消息历史记录。

如果开发者应用想要做历史消息记录功能，则应自己的应用服务器去做。

#### 解决思路

A 用户注销后，当有其他用户要向 A 用户发送消息时，开发者应用服务器应保存消息历史记录，而不要向 A 用户推送。只是 A 用户再次登录上线时，才可以向其推送消息。

### 多帐户登录的别名设置

#### 业务场景

客户端App支持多帐户登录。

每个用户登录时，都把该帐号UID设置为别名，从而接收到该用户的推送消息。

但有个问题：A 退出登录后，B 还未更新设置别名之前的发给 A 的推送消息，会被 B 收到。

#### 问题解析

别名是与本设备（本应用）绑定的。在 B 未设置别名成功之前，推送给 A 的信息都一直会被收到。

#### 解决思路

A 用户注销前，先做一个别名设置：把别名设置为空。

这样发给 A 的推送，将不会再给到本设置。

