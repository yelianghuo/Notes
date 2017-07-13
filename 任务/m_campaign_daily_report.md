2017/06/26
==========

* aim_cti :click之后install的一个设定值，01表示点击之后1小时内安装的计数，24表示24小时内
* type:rta,rba,b2t...不同团队的针对campaign_id的投放策略



1. 回复邮件完成情况
2. fvta是数据获取方式
3. 性能分析

1 回复邮件要注意格式，回复邮件的目的，结论：明确具体。



    在前面2个月的实习时间里，我负责了我们部门的一个线下的小项目：m_campaign_daily_report，该表以date、campaign_id、type为主键，包含了每一天不同的campaign_id在不同type情况下的相关数据。、
    目前该项目的架构为: 
        pull  ->  join  ->  push ：
          pull : 从不同的数据源中获得当前该项目需求下的相关数据；
          join : 以 date、campaign_id、type为主键，将这些数据进行整合；
          push : 将整合得到的数据update、insert到mysql、es、3s中，在es上面现在已经有相关的可视化图标；
       目前该项目已经在34.225.156.97服务器上面上线，每天都会更新前一天的数据，然后还有log邮件报警功能，所有该项目当前的需求已经完成，并测试通过。