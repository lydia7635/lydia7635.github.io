---
title: "[Git] 使用 PPA 將 git 升級成較新版 (Ubuntu)"
slug: upgrade-git-to-newer-version-with-PPA
date: 2021-02-21
description: 利用 PPA 升級 git

tags: 
  - git
  - Ubuntu
  - apt
  - PPA
categories: 
  - dev
---

## 前言

因為 GitHub 政策改變，會將預設的 branch 設為 main ，發現本地端使用 git init 時還是以 master 當成預設。在工作站使用 `git config --global init.defaultBranch main` 是有效的，但在個人筆電上的 WSL 卻仍是沒有反應。

查了一下資料[^1]後發現和 git 版本有關，工作站上目前是 2.30.1 ，WSL 目前是 2.25.1 ，而要讓 `defaultBranch` 生效，需要讓版本升級為 2.28 以上。（22-02-18 更：工作站目前是 2.35.1 ，而 Ubuntu 未做此調整前仍是 2.28 以下）

如果輸入 `sudo apt install git` 讓它重新檢查 git 的版本不會升級，因此需要加入 PPA 個人套件庫。

## 新增 PPA 個人套件庫

依照此篇指示[^2]，依序輸入下列指令
{{< highlight fish "linenos=false" >}}
sudo add-apt-repository ppa:git-core/ppa
sudo apt-get update
sudo apt-get install git
{{< / highlight >}}

安裝完後，使用 `git --version` 確認版本。

雖然版本是 2.30.0，比工作站上舊，但還是可以使用 `defaultBranch` 了。（22-02-18 更：目前也可以升級成和工作站同為 2.35.1 版）

## 結語

平常自己的使用習慣，不會使用到額外的套件庫來裝東西，藉此機會記錄一下使用方式。

[^1]: [Git init not working as usual - Reddit](https://www.reddit.com/r/git/comments/jwgn8p/git_init_not_working_as_usual/)
[^2]: [ubuntu - How can I update to a newer version of Git using apt-get? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/33617/how-can-i-update-to-a-newer-version-of-git-using-apt-get)
