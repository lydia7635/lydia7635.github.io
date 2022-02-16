---
title: "[Telegram] 建立聊天機器人"
slug: create-telegram-bot
image: /img/post/telegram-pixabay.jpg
date: 2021-02-18
description: telegram 建立聊天機器人的簡單記錄

tags: 
  - telegram
  - chat bot
categories: 
  - tech
---

雖然有網站將教學寫得很清楚了，不過還是來記錄一下。

## 操作

首先，先在搜尋框找到 BotFather 並點選 start ，之後會出現各指令的簡單介紹。

{{< figure src= "https://i.imgur.com/KToHeUP.png" width=70% >}}

如果未來想再看一次此資訊，可以輸入 `/start`

接下來輸入 `/newbot` 新增一個聊天機器人，並依序輸入對外顯示名稱及機器人 ID。

{{< figure src="https://i.imgur.com/MsbqrLv.png" width=70% >}}

其中，對外顯示名稱 (name for bot) 是未來公布聊天機器人的名稱，可以是中文，在此設為 `lydia7635_test`。而機器人 ID (username for bot) 僅能為英文，不可和他人重複，並且結尾須為 bot ，在此設為 `lydia7635_test_bot`。

接下來，拿到的 HTTP API 請勿公開，否則別人可以控制你的聊天機器人。若忘記也可以在和 BotFather 的對話中輸入 `/mybots` 查詢或修改。

## 參考資料
* [[教學] 第一步: 建立 Telegram聊天機器人 (Chatbot) - LINE官方帳號 & Telegram 行銷專家，幫助你快速上手 | 天長互動創意](https://tcsky.cc/tips-01-telegram-chatbot/)
