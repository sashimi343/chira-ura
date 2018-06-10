---
title: JavaScript シングル/ダブルクリックイベントを両方取得する
category: programming
tags: javascript
date: 2018-06-10 16:29:57
---

とあるプログラムを書いているときに、ある要素をシングルクリックしたときの処理と、ダブルクリックしたときの処理を別々にしたいと思いました。
それをスマートに実現する方法がなく、自前で実現したのでその方法をメモ。

# onclickとondblclickを両方登録すればいいのでは？
それがそうもいかないのです。
イベントの発火順序がシングルクリック→ダブルクリックとなっているようです。
そのため、両方登録してもシングルクリックの処理しか行われないみたいです。

# ならどうする？
そこで、クリックされた状態を保持するフラグを用意し、クリック後一定時間以内に再度クリックした場合、ダブルクリックと判定します。
ごちゃごちゃ言うよりコードを示すほうが早いと思います。
こんな感じ。

~~~~javascript
// グローバル変数
var clicked = false;	// クリック状態を保持するフラグ
// id="hoge"の要素をシングル/ダブルクリックしたときのイベント
document.getElementById("hoge").onclick = function (evt) {
    // クリックフラグが立っている状態でのクリック
    //     -> ダブルクリック
    if (clicked) {
        alert("double click!!");
        
        clicked = false;
        return;
    }
    
    // シングルクリックを受理、300ms間だけダブルクリック判定を残す
    clicked = true;
    setTimeout(function () {
        // ダブルクリックによりclickedフラグがリセットされていない
        //     -> シングルクリックだった
        if (clicked) {
            alert("single click!");
        }
        
        clicked = false;
    }, 300);
};
~~~~

これで実現できます。

# 動くサンプル

[http://jsfiddle.net/1q1wLggj/1/](http://jsfiddle.net/1q1wLggj/1/)

