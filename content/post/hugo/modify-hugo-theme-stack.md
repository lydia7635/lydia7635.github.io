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
    - dev
---
## 前言
雖然 [hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack) 這個主題，剛開始看的時候覺得簡單乾淨，也很符合個人審美觀，但看久了還是覺得一些地方可以稍加改進。

## Theme 掛載
其實這部分和修改並沒有直接的關係，不過目前打算是要同時保有可能會自己大改的空間，加上偶爾會想將原作者的更新加入，因此採用 fork + submodule 的方式，並且**直接修改**相關的程式碼。不過因為是大改，就算想發個 PR 幫幫忙也不行了：(

雖然 Stack 這個主題很貼心的加入 `custom` 檔案，讓使用者自行調整[^1]，但等筆者改完後才發現這個東西……（再不看 document 啊 Q_Q）所以筆者並不採用這個方式修改。

## 修改內容
筆者有在網路上找到一些針對 Stack 這個主題的修改[^2]，大部分也挺符合個人品味，所以也會拿部分來套用。

### 將最後編輯時間移至上方
文章上方的 detail 原本就有放像是建立日期與預計閱讀時長[^3]等時間相關資訊，那為何不將最後編輯時間也放在一起呢？

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

另外，可以注意到這邊使用其它 icon 來表示最後編輯日期，可以從 [Tabler Icons](https://tablericons.com/) 搜尋想要的 icon 。複製完貼到 `hugo-theme-stack/assets/icons/edit.svg` 中，需做以下修改：
- `width="24"`
- `height="24"`
- `stroke-width="2"`
- `stroke="currentColor"`

### 修改文章卡片圓角與字型字體
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

字體大小的部分，修改所有的 `--article-font-size` 為 1.6rem 即可。

### 調整最後編輯日期出現條件
因為有在 `config.yaml` 上設定 `enableGitInfo: true` ，讓文章的最後編輯時間自動設定成 commit 時間，但又因只想顯示日期，變成會顯示文章建立日期等於編輯日期的狀況。

筆者想要做的是，若在建立日期同一天編輯文章，也不會出現最後編輯日期的特殊設定。

參考這篇[^4]的設定，需要在 `hugo-theme-stack/layouts/partials/article/components/details.html` 修改如下（對，就是上面調整編輯時間位置的部分）：
{{< highlight html "hl_lines=9-11" >}}
{{ if not .Date.IsZero }}
    <div>
        {{ partial "helper/icon" "date" }}
        <time class="article-time--published">
            {{- .Date.Format (or .Site.Params.dateFormat.published "Jan 02, 2006") -}}
        </time>
    </div>

    {{ $date := .Date.Format "2006-01-02" }}
    {{ $lastmod := .Lastmod.Format "2006-01-02" }}
    {{- if and (ne $lastmod $date) (gt .Lastmod .Date) -}}
    <div>
        {{ partial "helper/icon" "edit" }}
        <time class="article-time--lastmod">
            {{- .Lastmod.Format ( or .Site.Params.dateFormat.lastUpdated "Jan 02, 2006 15:04 MST" ) -}}
        </time>
    </div>
    {{- end -}}
{{ end }}
{{< / highlight >}}
真是個有點拐彎抹腳的設定😅

### 程式碼區塊調整
由於預設的程式碼區塊顏色主題為 monokai ，之後想換成 dracula，但發現套用後看起來有奇怪的效果：
{{< figure src= "/img/post/old-code-block-style.JPG" width=600px >}}
程式碼區塊又圍了一圈顏色不同的 padding ，而且區塊並不是和文字切齊，而是對齊文章卡片邊緣。

經過一番查詢與修改，最後會設定成這樣：
{{< figure src= "/img/post/new-code-block-style.JPG" width=600px >}}

首先將 `config.yaml` 的 `markup.highlight.style` 設定為 `dracula`。
{{< highlight yaml >}}
markup:
    highlight:
        style: dracula
{{< / highlight >}}

若要將貼齊文章卡片邊緣的設定取消，需修改 `hugo-theme-stack/assets/scss/partials/layout/article.scss`，在檔案最下方的段落中，將 `.highlight` 與 `pre`（非 shortcode 產生的程式碼區塊）那行刪除。
{{< highlight scss >}}
    /// Negative margins
    blockquote,
    figure,
    .gallery,
    .video-wrapper,
    .table-wrapper,
    .s_video_simple {
        margin-left: calc((var(--card-padding)) * -1);
        margin-right: calc((var(--card-padding)) * -1);
        width: calc(100% + var(--card-padding) * 2);
    }
{{< / highlight >}}

以上是同樣會貼齊文章卡片邊緣的區塊。

而若欲產生區塊圓角，也在同樣檔案中設定：
{{< highlight scss "hl_lines=6" >}}
.article-content {
    // ...
    .highlight {
        background-color: var(--pre-background-color);
        padding: var(--card-padding);
        border-radius: 5px;
        position: relative;
        // ...
    }
    // ...
}
{{< / highlight >}}

`pre` 的部分也做類似設定。

但此時仍會因為 padding 顏色不同而顯得突兀。修改 `hugo-theme-stack/assets/scss/variables.scss` 最下方為：

{{< highlight scss "hl_lines=3" >}}
[data-scheme="dark"] {
    --pre-text-color: #f8f8f2;
    --pre-background-color: #282a36;
    @import "partials/highlight/dark.scss";
}
{{< / highlight >}}

可惜的是，目前不能隨著主題自動設定相對應顏色。

### scrollbar 調整
覺得頁面與程式碼區塊的 scrollbar 太粗，並且程式碼區塊的 scrollbar 持續顯示有點醜，所以也設定一下。

欲設定頁面的 scroll bar ，在 `hugo-theme-stack/assets/scss/partials/base.scss` 修改：
{{< highlight scss "hl_lines=2 9-10" >}}
* {
    scrollbar-width: thin;
    scrollbar-color: var(--scrollbar-thumb) transparent;
}
/**/

/* scrollbar styles for Chromium */
::-webkit-scrollbar {
    width: 1rem;
    height: 1rem;
}
{{< / highlight >}}

`::-webkit-scrollbar` 中的 `height` 也需一併設定，才會套用到程式碼區塊。

欲設定 scrollbar 圓角，也編輯相同檔案：

{{< highlight scss "hl_lines=3" >}}
/* scrollbar styles for Chromium */
::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background-color: var(--scrollbar-thumb);
}
{{< / highlight >}}

不過目前 Firefox 並不支援設定 scrollbar 圓角，希望之後會盡快推出。

接下來編輯程式碼區塊中，將滑鼠移至該區塊才顯現 scrollbar 的設定。在 `hugo-theme-stack/assets/scss/partials/layout/article.scss` 加上以下兩個段落：
{{< highlight scss "hl_lines=5-12 18-25" >}}
.article-content {
    // ...
    pre {
        // ...
        ::-webkit-scrollbar-thumb {
            visibility: hidden;
        }
        &:hover {
            ::-webkit-scrollbar-thumb {
                visibility: visible;
            }
        }
        // ...
    }
    
    .highlight {
        // ...
        ::-webkit-scrollbar-thumb {
            visibility: hidden;
        }
        &:hover {
            ::-webkit-scrollbar-thumb {
                visibility: visible;
            }
        }
        // ...
    }
}
{{< / highlight >}}

然後不知為何，使用 `scrollbar-width` 不能對 Firefox 做類似設定：(

經測試，在 Edge 與 Chrome 上應該是能正確顯示的，然而筆者沒有 Safari 的測試環境，所以不知道會不會炸掉😥

---

未完待續。

[^1]: [Modify theme | Hugo theme Stack](https://docs.stack.jimmycai.com/modify-theme/)
[^2]: [Bigs3cir](https://www.bigs3.com/)
[^3]: 目前覺得這個功能在本站有點雞肋，就先關掉了
[^4]: [How to Add a Last Modified Date in Hugo Articles - Simplernerd](https://simplernerd.com/hugo-last-modified-date/)