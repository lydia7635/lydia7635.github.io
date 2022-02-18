---
title: "[Hugo] 修改 Hugo Stack 主題"
slug: "modify-hugo-theme-stack"
description:
date: 2022-02-18
image: 
math: 
license: 
hidden: false
comments: true

tags: 
    - Hugo
categories:
    - tech
---
## 前言
雖然 [hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack) 這個主題，剛開始看的時候覺得簡單乾淨，也很符合個人審美觀，但看久了還是覺得一些地方可以稍加改進。

## Theme 掛載
其實這部分和修改並沒有直接的關係，不過目前打算是要同時保有可能會自己大改的空間，加上偶爾會想將原作者的更新加入，因此採用 fork + submodule 的方式，並且**直接修改**相關的程式碼。不過因為是大改，就算想發個 PR 幫幫忙也不行了：(

雖然 Stack 這個主題很貼心的加入 `custom` 檔案，讓使用者自行調整[^1]，但等筆者改完後才發現這個東西……（再不看 document 啊 Q_Q）所以筆者並不採用這個方式修改。

## 修改內容
筆者有在網路上找到一些針對 Stack 這個主題的修改[^2]，大部分也挺符合個人品味，所以也會拿部分來套用。

### 將修改時間移至上方
文章上方的 detail 原本就有放像是建立日期與預計閱讀時長[^3]等時間相關資訊，那為何不將修改時間也放在一起呢？

從 `hugo-theme-stack/layouts/partials/article/components/details.html` 先開始編輯。這裡是設定文章詳細資訊的版面配置，可以在文章上方或文章清單中的預覽看到。
{{< highlight html "hl_lines=8-15" >}}
    <div>
        {{ partial "helper/icon" "date" }}
        <time class="article-time--published">
            {{- .Date.Format (or .Site.Params.dateFormat.published "Jan 02, 2006") -}}
        </time>
    </div>

    {{- if ne .Lastmod .Date -}}
    <div>
        {{ partial "helper/icon" "edit" }}
        <time class="article-time--lastmod">
            {{- .Lastmod.Format ( or .Site.Params.dateFormat.lastUpdated "Jan 02, 2006 15:04 MST" ) -}}
        </time>
    </div>
    {{- end -}}
{{< / highlight >}}

加上標示的部分，是從 `hugo-theme-stack/layouts/partials/article/components/footer.html` 的相關部分修改而來。
{{< highlight html >}}
{{- if ne .Lastmod .Date -}}
<section class="article-lastmod">
    {{ partial "helper/icon" "clock" }}
    <span>
        {{ T "article.lastUpdatedOn" }} {{ .Lastmod.Format ( or .Site.Params.dateFormat.lastUpdated "Jan 02, 2006 15:04 MST" ) }}
    </span>
</section>
{{- end -}}
{{< / highlight >}}
參考完之後把這段刪掉。

另外，可以注意到這邊使用其它 icon 來表示修改日期，可以從 [Tabler Icons](https://tablericons.com/) 搜尋想要的 icon 。複製完貼到 `hugo-theme-stack/assets/icons/edit.svg` 中，需做以下修改：
- `width="24"`
- `height="24"`
- `stroke-width="2"`
- `stroke="currentColor"`

### 修改文章卡片圓角
參見這篇[^2] 的修改方式。

在同一個檔案上，也順便修改一下網站字型。

原本的中文字型如果為粗體，有種筆畫黏在一起的感覺，筆者改成 [Noto Sans Traditional Chinese Light 300](https://fonts.google.com/noto/specimen/Noto+Sans+TC?subset=chinese-traditional)。

{{< figure src= "/img/post/font-difference.JPG" width=400px >}}

來個對照。上半部是修改前，下半部是修改後，看起來應該比較清爽些。

先在 `hugo-theme-stack/assets/scss/variables.scss` 最上方加上

{{< highlight scss "linenos=false" >}}
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300&display=swap');
{{< / highlight >}}

然後在 `--zh-font-family` 加上 `"Noto Sans TC"` ，記得要放在所有字型的前面。

未完待續。

[^1]: [Modify theme | Hugo theme Stack](https://docs.stack.jimmycai.com/modify-theme/)
[^2]: [Bigs3cir](https://www.bigs3.com/)
[^3]: 目前覺得這個功能在本站有點雞肋，就先關掉了