# 对象变更处理说明 #
1. ArbNode事件
	1. EN_EVENT_ARBOBJ_NEW
		1. ELECT:参与分布式选举投票(已实现，默认广播发送，计算时，动态计算数量）
	2. EN_EVENT_ARBOBJ_DELETE
		1. ELECT:退出分布式选举投票(已实现，默认广播发送，计算时，动态计算数量）
	3. EN_EVENT_ARBOBJ_UPDATE(预留）
	4. EN_EVENT_ARBOBJ_UPDATE_STATE
		1. 本地对象变化
			1. 对象unMaster->Master
				1. ArbNode：重新平衡所有仲裁者。(已实现)
				2. SYNC：主动发起同步。(已实现)
			2. 对象unFollower->Follower
				1. ArbNode：清空服务组。(已实现)
				2. SYNC：主动请求同步(已实现)
			3. 对象unFault->Fault
				1. ArbNode：清空服务组。(已实现)
				2. SYNC：停止同步(已实现)
				
		2. 其他对象变化
			1. 对象unMaster->Master
				1. ArbNode：清空服务组。(已实现)
				2. SYNC：主动请求同步(已实现)
			2. 对象unFollower->Follower
				1. 本地是仲裁者
					1.  ArbNode：重新平衡所有仲裁者。(已实现)
					2.  SYNC：主动发起同步。(平衡后，根据变化主动通知同步，已实现)
				2.	本地不是仲裁者
					1.	SYNC：主动请求同步(平衡后，由主用根据变化主动通知同步，已实现)
			3. 对象unFault->Fault
				1. 本地是仲裁者
					1. ArbNode：重新平衡所有仲裁者。(已实现)
					2. SYNC：主动发起同步。(平衡后，根据变化主动通知同步，已实现)
				2. 本地不是仲裁者
					1. SYNC：主动请求同步(平衡后，由主用根据变化主动通知同步，已实现)
				
	5. EN_EVENT_ARBOBJ_UPDATE_CLUSTER_STATE	
		1. 集群不可用(无主）
			1. HA: 
				1. 业务层：除Elect以外，停止所有服务组、服务实例、仲裁者定时器。(已实现)
		2. 集群可用(有主）
			1. HA:
				1. 业务层：启动所有服务组、服务实例、仲裁者定时器。(已实现)


2. ServiceGroup事件
	1. EN_EVENT_SGOBJ_NEW
		1. 本地仲裁者管理范围内：
			1. 启动状态收集定时器 (已实现)
			2. 启动服务组所有实例HB监控。(xxxxx)
		2. 本地仲裁者管理范围外：
			1. 不处理。(已实现)
	2. EN_EVENT_SGOBJ_DELETE(xxxxx)
		1. 本地仲裁者管理范围内：
			1. 停止状态收集定时器 (已实现)
			2. 停止服务组所有实例HB监控。 (xxxxx)
		2. 本地仲裁者管理范围外：
			1. 不处理。(已实现)
	3. EN_EVENT_SGOBJ_UPDATE_MID (xxxxx,补充同步后事件发送处理)
		1. 迁入本地仲裁者管理范围：
			1. 启动状态收集定时器 (已实现)
			2. 启动服务组所有实例HB监控。 (已实现)
		2. 迁出本地仲裁者管理范围：
			1. 停止状态收集定时器(已实现)
			2. 停止服务组所有实例HB监控。(已实现)
	4. EN_EVENT_SGOBJ_UPDATE_STATE(xxxxx,补充其他状态事件发送)
		1. 本地仲裁者管理范围内：
			1. 状态->收集状态：
				1. 启动状态收集定时器(已实现)
			2. 状态->正常状态：
				1. 停止状态收集定时器(已实现)
				2. 进入状态机处理。(已实现)
			3. 状态->锁定状态：(xxxx禁止倒换）
				1. 不进入状态机处理。(xxxxxxxxxxxxx）

3. ServiceInstance事件
	1. EN_EVENT_SIOBJ_NEW
		1. 本地仲裁者管理范围内：
			1. 启动该实例HB监控。(已实现)
		2. 本地仲裁者管理范围外：
			1. 不处理。(已实现)
	2. EN_EVENT_SIOBJ_DELETE
		1. 本地仲裁者管理范围内：
			1. 停止该实例HB监控。(已实现)
		2. 本地仲裁者管理范围外：
			1. 不处理。(已实现)
