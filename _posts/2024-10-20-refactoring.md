---
layout: post
title: The example of Refactoring
categories:
  - Program
description: Refactoring
keywords: Program
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
Refactoring (《重构》)这本书中有一个例子，挺有代表性，记录如下：

## 场景

设想有一个戏剧演出团，演员们经常要去各种场合表演戏剧。
通常客户（customer）会指定几出剧目，而剧团则根据观众（audience）人数及剧目类型来向客户收费。
该团目前出演两种戏剧：悲剧（tragedy）和喜剧（comedy）。
给客户发出账单时，剧团还会根据到场观众的数量给出“观众量积分”（volume credit）优惠，
下次客户再请剧团表演时可以使用积分获得折扣。

摘录来自--> 重构: 改善既有代码的设计

## 代码表示

剧团的演出剧目、种类等信息放在下面的json文件：
```json
// play.json
{
  "hamlet": { "name": "Hamlet", "type": "tragedy" },
  "as-like": { "name": "As You Like It", "type": "comedy" },
  "othello": { "name": "Othello", "type": "tragedy" }
}
```

账单信息也存储在一个 JSON 文件里,
```json
//invoices.json
[
  {
    "customer": "BigCo",
    "performances": [
      {
        "playID": "hamlet",
        "audience": 55
      },
      {
        "playID": "as-like",
        "audience": 35
      },
      {
        "playID": "othello",
        "audience": 40
      }
    ]
  }
]
```

打印账单的函数
```js
function statement (invoice, plays) {
  let totalAmount = 0;
  let volumeCredits = 0;
  let result = `Statement for ${invoice.customer}\n`;
  const format = new Intl.NumberFormat("en-US",
                        { style: "currency", currency: "USD",
                          minimumFractionDigits: 2 }).format;
  for (let perf of invoice.performances) {
    const play = plays[perf.playID];
    let thisAmount = 0;

    switch (play.type) {
	    case "tragedy":
	      thisAmount = 40000;
	      if (perf.audience > 30) {
	        thisAmount += 1000 * (perf.audience - 30);
	      }
	      break;
	    case "comedy":
	      thisAmount = 30000;
	      if (perf.audience > 20) {
	        thisAmount += 10000 + 500 * (perf.audience - 20);
	      }
	      thisAmount += 300 * perf.audience;
	      break;
	    default:
	        throw new Error(`unknown type: ${play.type}`);
    }

    // add volume credits
    volumeCredits += Math.max(perf.audience - 30, 0);
    // add extra credit for every ten comedy attendees
    if ("comedy" === play.type) volumeCredits += Math.floor(perf.audience / 5);

    // print line for this order
    result += ` ${play.name}: ${format(thisAmount/100)} (${perf.audience} seats)\n`;
    totalAmount += thisAmount;
  }
  result += `Amount owed is ${format(totalAmount/100)}\n`;
  result += `You earned ${volumeCredits} credits\n`;
  return result;
}
```

用上面的数据文件（invoices.json 和 plays.json）作为测试输入，运行这段代码，会得到如下输出：

```text
Statement for BigCo
  Hamlet: $650.00 (55 seats)
  As You Like It: $580.00 (35 seats)
  Othello: $500.00 (40 seats)
Amount owed is $1,730.00
You earned 47 credits
```

