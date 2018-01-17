---
layout: post
title:  " TMC-emberjs下添加关注订单"
date:   2015-12-17 18:13:05
categories: ember.js JavaScript
excerpt: ember.js
---

* content
{:toc}

##2015-12-07
新增关注订单：所有订单信息 【订单号 PNR 预定人 预定时间 】
              点击订单号->判断订单类型（后台传参数）->国内&国际航空（写两表）
              详情 [不可更改 大致不变]

scripts-controllers-orders
       -routes-oders_route
       -router 路由概总
       -admin-orders
templates-partial-order
templates-orders
                ->focus-index
                       -flightDetail
                       -internationalDetail
##2015-12-08
？？
1-进入不同路由时，列表的生成<table fixed-id="flightList" class="">
                  遍历获取数据时{{#each order in model itemController="ordersFlightIndexItem"}}
    指定模板进行数据获取
    在scripts-controllers-orders 下，也是每种订单一个模板。
    怎么能同时获取所有订单并按时间排序？？
   so 再写个模板？？

2-将所有订单汇总打印后，点击订单号进入详情，如何判断是什么类型模板？（传参数?但每个订单模板不一样啊）
3-成功判断类型后，如何跳转
  原方法是{{#link-to 'orders.flight.multi' order.orderId}}{{order.orderCode}}{{/link-to}}点击后直接获取分类好订单的id
  但现在需要在点击后先判断类型，然后再判断跳转至order的哪个类下，再获取订单id
  或者说在订单总表排列的时候就提前分类好order类，点击时依然直接获取id

##2015-12-09
改router，然后templates，然后改对应controller,将servers的business_server（已经改了）.
有detail controller 的几个还有item controller
在router里面有findInternationalOrder，这种需要在business_server里面写对应的获取详情

？国际机票没有开放接口
把国内机票的详情加上了。

##2015-12-10
focusOrder的index   
Data.json
{
  "code": "0",
  "count": 260807, /*订单数量*/
  "data": [{
    "orderId": 28614,
    "orderCode": "SBF20151210000001",
    "orderExtraType": "NORMAL",/*国内机票-区分公务员机票*/
    "type": "flight",/*区分订单类型 flight international train hotel*/
    "statusDesc": "出票成功",
    "orderTime": "2015-12-10 10:20:05",
    "ordererName": "曾世强",
    "ordererTel": "15100000000",
    "tmcShortName": "移花互动",
    "companyName": "紫色",
    "passengers": [{
      "name": "李三",
      "passengerType": "ADULT"
    }]
  }]
}

原型如下：

【订单号 预定时间 预定信息（公司 预定人姓名 预定人电话）所属tmc 人员信息 状态】

Router 添加 然后order_route添加index页面路由并且返回后端传入数据，再将数据model给controller，下面写controller和template
App.OrdersFocusController = Ember.ArrayController.extend()获取查询参数
还需写itemsController来获取多条

把关注订单的index写了，但是没有接口数据。用了国内机票的数据。

##2015-12-11
	       <!-- {{#if order.type=="flight"}}
	  					  {{#link-to 'orders.flight.detail' order.orderId}}{{order.orderCode}}{{/link-to}}<br>{{to-csPrice order.orderExtraType}}
              {{/if}}
							{{#if order.type=="international"}}
							  {{#link-to 'orders.international.detail' order.orderId}}{{order.orderCode}}{{/link-to}}<br>{{to-csPrice order.orderExtraType}}
              {{/if}}
							{{#if order.type=="train"}}
							  {{#link-to 'orders.train.detail' order.orderId}}{{order.orderCode}}{{/link-to}}<br>{{to-csPrice order.orderExtraType}}
              {{/if}}
							{{#if order.type=="hotel"}}
							  {{#link-to 'orders.hotel.detail' order.orderId}}{{order.orderCode}}{{/link-to}}<br>{{to-csPrice order.orderExtraType}}
              {{/if}} -->

加itemController,一个计算属性，判断type，return ’oders.flight.detail’,再link指向不同路由
给focus最后加一个详情按钮。

2015-12-14
学习了JSON(javascript object notation)javascript对象表示法
      AJAX

>GET 还是 POST？
与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
然而，在以下情况中，请使用 POST 请求：
无法使用缓存文件（更新服务器上的文件或数据库）
向服务器发送大量数据（POST 没有数据量限制）
发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠


##2015-12-14~2013-12-16
A题，学习JS犀牛书。利用github搭建了一个自己的网站。selcho.githu.io 但是现在用的别人的模板。大多数好像用bootstrap搭的。写的文章需要在post中，用markdown语法。
2015-12-17
并没有为关注订单添加发送请求
于是在serves中添加了
	findFocusOrders: function(query) {
		query.tmcId = 0;
		query.operatorId = App.CurrentUser.id;
		var data = {queryParam: query};
		return this.post('rest/queryFocusOnOrder', data);
	}
tmcId有两层，一层是写在data请求之前，一层是之后。这里我们写的是之前。先获取权限，再请求数据。
这个js头部为
var mock = App.Mock;

App.BusinessServer = DS.Server.extend({

	 // [获取机票订单列表]
	 // @param  {[object]} query [查询参数]
	 // @return {[array]}        [机票订单列表]
   }
 ){...}


并不明白mock是什么。
注意：在.xhs中，当使用controller时，一定一定记得加
{{#each order in model itemController="ordersFocusItem"}}
 {{#each passenger in order.passengersExample}}
    {{order.xxx}}
也有不用指明controller的情况 比如数据直接在默认controller中时，可以不用指明。
因为定义类型，数组里面套数组，所以用两个controller来each遍历。
[完了，我现在已经明白mvvc结构了。
对数据交互部分模糊。]
没有接收到部分数据以及未设置条件下的数据筛选。
