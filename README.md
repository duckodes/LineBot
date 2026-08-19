# LINE Bot 簡易 API

本專案提供 LINE Developers 平台上設定 Webhook 與 LIFF EndPoint URL，並透過設定的 URL API 來輸出文字訊息。

---

## 步驟一、API 說明及 LINE Developers設置

1. 請使用 [設置底部選單及取得兩組 URL](https://noteest.com/api/linebotsetup/) 進行設定。
2. 底部選單可至以上進行上傳及更新，選單的設置 data: "action=get_message&index=0" 及 data: "action=delete_message&index=0" 這兩個事件一個是獲取訊息及刪除訊息，index 為第幾筆存在 messages 的資料，為以下第二步驟設置的 messages

## 步驟二、前端程式碼範例

網頁程式碼中加入以下片段：

```html
<button id="loginBtn">使用 LINE 登入</button>
<div id="status"></div>

<script>
  // 一定要有 liffId 及 messages
  const liffId = 填入自己的 LIFF ID;

  // 加過 BOT 傳送的訊息
  const messages = [
    {
      type: "text",
      text: `🐻預約成功!!~

公司：${data.company}
聯絡人：${data.name}
電話：${data.phone}

如需修改或取消預約，請使用預約系統查詢功能。`,
    },
  ];

  // 第一次加入 BOT 傳送的訊息
  const followmsgs = [
    {
      type: "text",
      text: `🤔感謝追蹤!!`,
    },
    {
      type: "text",
      text: `🐻預約成功!!~

公司：${data.company}
聯絡人：${data.name}
電話：${data.phone}

如需修改或取消預約，請使用預約系統查詢功能。`,
    },

    // 總共有兩個 data action 可以用
    // 1. delete_message&index=0
    // 2. get_message&index=0
    // 後面 index 是在 messages 的順序，可以進行用戶資料移除
    // 這是傳送小選單
    // {
    //   type: "template",
    //   altText: "預約功能",
    //   template: {
    //     type: "buttons",
    //     text: "請選擇功能",
    //     actions: [
    //       {
    //         type: "postback",
    //         label: "查詢預約",
    //         data: "action=get_message&index=0",
    //         displayText: "查詢預約",
    //       },
    //       {
    //         type: "postback",
    //         label: "取消預約",
    //         data: "action=delete_message&index=0",
    //         displayText: "取消預約",
    //       },
    //     ],
    //   },
    // },
  ];
  // messages無資料 查詢失敗
  const failgetmsg = [
    {
      type: "text",
      text: `{LINE_ID}尚未預約! 快去預約!!!`,
    },
  ];
  // messages無資料 刪除失敗
  const faildeletemsg = [
    {
      type: "text",
      text: `{LINE_NAME}尚未預約喔! 想預約快快預約!`,
    },
  ];
  // messages有資料 刪除成功
  const successdeletemsg = [
    {
      type: "text",
      text: `{LINE_NAME}成功取消預約!~`,
    },
  ];
  // 存資料
  const responseLineData = await fetch(
    "https://apis.line.data.noteest.com",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        liffId,
        messages: JSON.stringify(messages),
        followmsgs: JSON.stringify(followmsgs),
        faildeletemsg: JSON.stringify(faildeletemsg),
        successdeletemsg: JSON.stringify(successdeletemsg),
        failgetmsg: JSON.stringify(failgetmsg)
      })
    }
  );

  const lineData = await responseLineData.json();

  if (lineData.success) {
    const pushKey = lineData.pushKey;

    const loginWindow = window.open(
      "https://liff.line.me/" + liffId + "/?pushKey=" + pushKey,
      "_blank"
    );

    window.addEventListener("message", (event) => {
      if (event.data.type === "LINE_LOGIN_SUCCESS") {
        const profile = event.data.profile;
        document.getElementById("status").innerText =
          "登入成功，使用者：" + profile.displayName;
      }
    });
  }
</script>
```
