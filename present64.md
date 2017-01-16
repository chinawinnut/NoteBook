# usage
用法: presentautobatch -t type [-r region] [-m month] [-b begin_account_id] [-e end_account_id] [-p presentid] [-B batch_size] [-P parallel] [-o] [-s] [-h]

type             1-日转兑 2-月转兑(销账前) 3-月累计(销账后) 4-临时帐本转正式帐本(2-月转兑(销账前)做) 7-不转兑处理

month            转兑月份

begin_account_id 起始帐号

end_account_id   结束帐号

region           代表本进程将完成指定业务区的赠送转兑处理,缺省为ALL(所有业务区)

presentid        指定活动ID 解冻 如 -p 200001,20002,20003

parallel         并行处理线程数(1-30),缺省为10

batch_size       每批次处理的用户数,缺省为1000

-o               OCS用户转兑处理,缺省为BSS用户

-s               预发短信处理模式,不进行转兑

# make
该程序是64位程序，编译前先运行“. setenv.ksh”

可能会报lua库找不到之类的，需要把setenv.ksh中的LUA64_HOME变量修改下。

# acc_ocs_para

FLAG字段：0打开ocs处理（会回写）
1，关闭

# ucs_subs_freeze表
转兑定购表
### *ORGINAL_AMOUNT*
划拨原始总额

### *ALLOT_STATUS*
划拨状态
0-未解冻
1－正解冻
3－已清理

### *TAKE_TYPE*
1-营业受理 2-缴费受理

### *DELAY_FLAG*
1-顺延 0-非顺延

### *PAY_RULE_ID*
缴费规则id
缴费受理专用


### *ORG_ALLOT_MONTH*
剩余转兑次数

# ACC_PRESENT_PACKAGE
赠送/转兑包定义表

### *ALLOT_RUN_MODE*
划拨运行模式 划拨运行模式。描述划拨动作什么时候开始。
0：实时划拨
1：日划拨
2：月划拨
3：月累计，划拨总额不能提前确定的时候
4：全局活动
9: 实时划拨+日划拨+月划拨

### *MAX_ALLOT_RULE*
最高配送金额规则ID
最高配送金额规则id。引用计算规则中的规则id。
实际配送金额=min(Amount_cal_rule计算值，Max_amount_rule计算值)

### *PAY_STYLE*
配送缴费方式
配送金额缴费方式。

### *PAY_CHANNEL*
配送缴费渠道
pay_channel 配送金额缴费渠道。

### *ACTIVE_TYPE*
相对生效时间类型
相对生效时间类型：
0-当前划拨时间
1-入网时间
2-第一次划拨时间

### *EFFECT_TYPE*
生效变更类型
相对有效期类型：
0：无变化
1：相对年有效期
2：相对月有效期
3：相对日有效期

### *EFFECT_VALUE*
生效变更值
相对有效期值：
如：effect_type为2，effect_value为3，用户2006-8-15订购此活动，那么用户订购活动的生效时间是在三个月后，即订购生效时间为：2006-11-1；
Effect_type为1，2，3时，effect_value对应不同的年数/月数/天数；


# ACC_PRESENT_DEST
划拨目的配置表

### *ALLOT_MONTH*
划拨月数，描述多少个月划拨完。
如，缴99，返100，分12月划拨。
此处描述12月


### *ALLOT_RULE*
划拨金额计算id

### *BOOK_ACTIVE_TYPE*
### *BOOK_EFFECT_TYPE*
### *BOOK_EFFECT_VALUE*
### *BOOK_EXPIRE_TYPE*
### *MAX_AMOUNT_RULE*
最大解兑金额计算公式，很可能和ALLOT_RULE是一样的


# acc_payment_rule
缴费规则表

### *PAY_CHANNEL*
缴费渠道
客户实际缴费的渠道，包括：
0：帐务中心补记；
1：营业缴费；
2：客服缴费；
3：银行托收；
4：银行代扣；
5：银行代缴（联网代收）；
6：银行代收；
在实际处理时有些可能无法细致区分。

### *PAY_STYLE*
缴费方式
缴纳资金的方式，包括：
10：现金；
20：现金支票；
21：转帐支票；
30：信用卡；
31：储蓄卡；
32：普通银行帐户；
40：充值卡；
41：活卡资金；
50：转帐；
51：奖励；

### *NEED_LATEFEE*
收取滞纳金
是否收取滞纳金，包括：
0：不收滞纳金；
1：收取滞纳金。

### *NEED_CHECK*
资金审核标志
是否需要进行资金审核，包括：
0：不需要资金审核；
1：需要资金审核。

# acc_account_info
关注字段
### *BOOK_COUNT*
资金帐本计数
### *CASH_BALANCE*
通用帐本汇总
### *TOTAL_BALANCE*
帐户总余额
### *CASH_FLAG*
通用标识。
通用帐本标识，为1情况下监控直接进行cash_balance和unsettled_bill_balance的对比，为0情况下说明有专款专用，需要调用帐管实时结余接口


# 代码解析
```
if ( bookbalance < maxfee ){//赠款账本上剩余的钱少于解冻金额，那解冻金额要调整（可能由于其他原因账本余额少了，这里只是增加这么个校验，防止联通多送钱。）
			theLog.warn("余额不足(account_id=%d,book_id=%d,balance=%lld,maxfee=%lld).",freeze.theAccountId,bookid,bookbalance,maxfee);
			maxfee = bookbalance;
}
```
