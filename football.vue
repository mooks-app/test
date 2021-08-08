<template>
  <!-- youtube - api -->
  <!-- https://qiita.com/g-k/items/7c98efe21257afac70e9 -->
  <div class="container">
    <div style="display: flex;">
        <v-switch
        v-model="switch1"
        :label="`Switch 1: ${switch1.toString()}`"
        class="mr-5"
        ></v-switch>
        <v-switch
        v-model="switch2"
        :label="`Switch 2: ${switch2.toString()}`"
        ></v-switch>
    </div>
    <h1>FootBall Tactical board</h1>
    <div id="canvas-parent">
        <select id="color">
            <option value="white" style="background-color:white;color:black" selected>white</option>
            <option value="red" style="background-color:red;color:black">red</option>
            <option value="blue" style="background-color:blue;color:black">blue</option>
            <option value="yellow" style="background-color:yellow;color:black">yellow</option>
        </select>
        <canvas id="tactical-board" width="640" height="480"></canvas>
        <div id="players">

        </div>
        <!-- <canvas id="tactical-board2" width="640" height="480"></canvas> -->
    </div>
  </div>
</template>

<script>
// <script lang="ts">
import aa from 'static/football-tactical'


// キャンバス内の拡大/縮小
// https://note.affi-sapo-sv.com/js-zoom-canvas.php

// 基本
// https://qiita.com/tfrcm/items/c875e96453159eae44d7


export default {
    data () {
        return {
            switch1: false,
            switch2: false,
            dx: 0,
            dy: 0,

            x: 0,
            y: 0,
            relX: 0, // Objectの描画位置の補正 - X
            relY: 0, // Objectの描画位置の補正 - Y
            objX: 0, // Objectの描画位置 - X
            objY: 0, // Objectの描画位置 - Y
            objWidth: 50,  // object - width
            objHeight: 50, // object - height
            dragging: false,
            ballRedis: 15,
            draggTarget: 0,
            teamColor: ['#0095DD', '#ff5b5b'],
            players : [
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 0},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
                {dragging: false, 'objX': 0, 'objY': 0, 'relX': 0, 'relY': 0, t: 1},
            ],
            flagDraw: false,

            // football-coart
            fpadding: 5,
            fWidth: 0,
            fHeight: 0,
        }
    },
    methods: {
        changeFootballPlayers() {
            this.$store.commit("changeFootballPlayers", this.players);
        },
        footBallPitch: function () {
            let canvas = document.getElementById('tactical-board');
            let ctx = canvas.getContext('2d');
            let fpadding = this.fpadding;
            let lineWidth = 1;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // フットボールコートを描画
            // 1ヤード = 1.8288m
            ctx.strokeStyle = "#fff";
            ctx.lineWidth = lineWidth;
            ctx.fillStyle = '#060';

            // pitch background
            ctx.beginPath()
            ctx.rect(0,0,canvas.width,canvas.height);
            ctx.fill();
            ctx.closePath()


            // center mark
            ctx.beginPath();
            ctx.arc(canvas.width/2, canvas.height/2, 3, 0, Math.PI*2);
            ctx.fillStyle = '#fff';
            ctx.fill();
            ctx.fillStyle = '#060';
            ctx.closePath();

            // center cercle
            ctx.beginPath();
            ctx.arc(canvas.width/2, canvas.height/2, this.halfLine, 0, Math.PI*2);
            ctx.stroke();
            ctx.closePath();

            // Home penalty arc
            ctx.beginPath();
            ctx.arc(this.penaX, this.penaY, this.penaArcRedis, 0, Math.PI*2);
            ctx.stroke();
            ctx.closePath();

            // Awayh penalty arc
            ctx.beginPath();
            ctx.arc(canvas.width-fpadding-this.penaX, this.penaY, this.penaArcRedis, 0, Math.PI*2);
            ctx.stroke();
            ctx.closePath();

            // Home penalty box
            ctx.beginPath();
            ctx.rect(fpadding , this.penaStart, this.penaHeight, this.penaWidth);
            ctx.fill();
            ctx.stroke();
            ctx.closePath();

            // Away penalty box
            ctx.beginPath();
            ctx.rect(canvas.width-fpadding-this.penaHeight, this.penaStart, this.penaHeight, this.penaWidth);
            ctx.fill();
            ctx.stroke();
            ctx.closePath();

            // Home goal box
            ctx.beginPath();
            ctx.rect(fpadding , this.goalAreaStart, this.goalHeight, this.goalWidth);
            ctx.stroke();
            ctx.closePath();

            // Home goal box
            ctx.beginPath();
            ctx.rect(canvas.width-fpadding-this.goalHeight, this.goalAreaStart, this.goalHeight, this.goalWidth);
            ctx.stroke();
            ctx.closePath();

            // Home penalty mark
            ctx.beginPath();
            ctx.arc(this.penaX, this.penaY, 3, 0, Math.PI*2);
            ctx.fillStyle = '#fff';
            ctx.fill();
            ctx.fillStyle = '#060';
            ctx.closePath();

            // Away penalty mark
            ctx.beginPath();
            ctx.arc(canvas.width-fpadding-this.penaX, this.penaY, 3, 0, Math.PI*2);
            ctx.fillStyle = '#fff';
            ctx.fill();
            ctx.fillStyle = '#060';
            ctx.closePath();

            // home corner L
            ctx.beginPath();
            ctx.arc(this.fpadding, this.fpadding, this.cornerArc, 0, Math.PI*0.5);
            ctx.stroke();
            ctx.closePath();
            
            // home corner R
            ctx.beginPath();
            ctx.arc(this.fpadding, this.cornerBottom, this.cornerArc, Math.PI*1.5, Math.PI*2);
            ctx.stroke();
            ctx.closePath();

            // Away corner L
            ctx.beginPath();
            ctx.arc(this.cornerAway, this.fpadding, this.cornerArc, Math.PI*0.5, Math.PI*1);
            ctx.stroke();
            ctx.closePath();

            // Away corner R
            ctx.beginPath();
            ctx.arc(this.cornerAway, this.cornerBottom, this.cornerArc, Math.PI*1, Math.PI*1.5);
            ctx.stroke();
            ctx.closePath();

            // Side Line & Half Line
            ctx.beginPath();
            ctx.rect(fpadding , fpadding, this.fWidth, this.fHeight);   // サイドライン
            ctx.rect(fpadding , fpadding, this.fWidth/2, this.fHeight); // ハーフウェライン
            ctx.stroke();
            ctx.closePath();
        },
        objectOnDown: function (e) {
            const canvas = document.getElementById('tactical-board');
            var offsetX = canvas.getBoundingClientRect().left; // canvasのX座標
            var offsetY = canvas.getBoundingClientRect().top;  // canvasのY座標
            var x = e.clientX - offsetX; // 要素がクリックされたX座標からcanvasのX座標を引いたもの
            var y = e.clientY - offsetY; // 要素がクリックされたY座標からcanvasのY座標を引いたもの

            for (var i in this.players) {
                // https://kuroeveryday.blogspot.com/2019/04/how-to-detect-click-on-shape-on-canvas.html
                // ピラゴラスの定理をつかってユークリッド距離と半径で判定する
                var hit =
                    Math.pow(this.players[i].objX - x, 2) + Math.pow(this.players[i].objY - y, 2) <= Math.pow(this.ballRedis, 2);
                if (hit) {
                    this.draggTarget = i;
                    this.players[i].dragging = true;
                    this.players[i].relX = this.objX - x; // もとのX座標からクリックされたX座標
                    this.players[i].relY = this.objY - y; // もとのY座標からクリックされたY座標
                    break;
                }
            }
        },
        objectOnMove: function (e) {
            const canvas = document.getElementById('tactical-board');
            var offsetX = canvas.getBoundingClientRect().left; // canvasのX座標
            var offsetY = canvas.getBoundingClientRect().top;  // canvasのY座標
            var x = e.clientX - offsetX; // 要素がクリックされたX座標からcanvasのX座標を引いたもの
            var y = e.clientY - offsetY; // 要素がクリックされたY座標からcanvasのY座標を引いたもの

            var tarPlayer;
            for (var i in this.players) {
                if (this.players[i].dragging) {
                    if (this.players[i].dragging) {
                        this.players[i].objX = x + this.relX;
                        this.players[i].objY = y + this.relY;
                    }
                    this.drawRect();
                    break;
                }
            }
            // canvasから外れた場合
            // onUpする
        },
        objectOnUp: function () {
            for (var i in this.players) {
                this.players[i].dragging = false;
            }
            this.drawRect();
            // this.changeFootballPlayers(); // localstorageの値を格納
        },
        drawRect: function () {
            let t = '';
            const canvas = document.getElementById('tactical-board');
            var ctx_main = canvas.getContext('2d');
            ctx_main.clearRect(0, 0, canvas.width, canvas.height);

            this.footBallPitch();

            // 描画するごとに分けないと
            // arcは一筆書きなのでオブジェクトをずらすと
            // 塗りつぶしが発生する
            for (var i in this.players) {
                t += `<p>Player: ${Number(i)+1}  - x: ${this.players[i].objX} - y: ${this.players[i].objY}</p>`;
                var ctx = canvas.getContext('2d');
                ctx.beginPath();
                ctx.shadowColor = "gray";  //赤色の影を付ける
                if (this.players[i].dragging) {
                //     ctx.shadowBlur  = 10;      //ぼかしを０にする
                //     ctx.shadowOffsetX = 6;    //横に3pxずらす
                //     ctx.shadowOffsetY = 4;    //縦に1pxずらす

                    ctx.font = '16px Arial';
                    ctx.fillStyle = this.teamColor[this.players[i].t];
                    ctx.fillText(`Player: ${Number(this.draggTarget)+1}`, 20, 20);
                } else {
                    // ctx.shadowBlur  = 0;      //ぼかしを０にする
                    // ctx.shadowOffsetX = 0;    //横に3pxずらす
                    // ctx.shadowOffsetY = 0;    //縦に1pxずらす
                }
                ctx.arc(this.players[i].objX, this.players[i].objY, this.ballRedis, 0, Math.PI*2);
                ctx.fillStyle = this.teamColor[this.players[i].t];
                ctx.fill();
                ctx.lineWidth = 0.2;
                ctx.strokeStyle = "black";
                ctx.stroke();
                ctx.closePath();
            }

            let playersDom = document.getElementById('players');
            playersDom.innerHTML = t;
        },
        removeEvent: function () {
            // event削除
            const canvas = document.getElementById('tactical-board');
            canvas.removeEventListener('mousedown', this.objectOnDown);
            canvas.removeEventListener('mousemove', this.objectOnMove);
            canvas.removeEventListener('mouseup', this.objectOnUp);
        },
        addEvent: function () {
            // event追加
            const canvas = document.getElementById('tactical-board');
            canvas.addEventListener('mousedown', this.objectOnDown, false);
            canvas.addEventListener('mousemove', this.objectOnMove, false);
            canvas.addEventListener('mouseup', this.objectOnUp, false);
        }
    },
    mounted() {
        // コートの比率
        // コートサイズ
        // https://footballbox.club/field-size.html
        // サッカーを数式的に見る
        // http://metalogue.jugem.jp/?eid=1102
        // サイドライン: 105
        // ゴールライン:  68
        // センターサークル: 9.15
        // ゴール: 7.23
        // ゴールエリアの開始: 24.84 = (68-(5.5+5.5+7.32))/2
        // ゴールエリア-縦: 5.5
        // ゴールエリア-横: 18.32 <- ゴールの端から16.5
        // ペナルティエリアの開始: 13.84
        // ペナルティエリア-縦: 16.5
        // ペナルティエリア-横: 40.32 <- ゴールの端から16.5
        // ペナルティマーク: 11
        // ペナルティアーク: ペナルティマークから半径9.15
        // コーナーアーク: 1
        // センターサークル: センターマークから半径9.15

        // >>> 105 / 68
        // 1.5441176470588236
        // const canvas = document.getElementById('tactical-board');
        // const v_main = document.getElementById('v-main');
        // const v_main_pTop = Number(v_main.style.paddingTop.slice(0, -2));

        // 四角形の移動
        // 68 / 105 = 0.6476190476190476
        // 9.15 / 105 = 0.08714285714285715
        const canvas = document.getElementById('tactical-board');
        var container = document.querySelector("#v-main > div > div");
        var canvasW = container.offsetWidth - 24 - 20; // canvas要素の横幅(px)
        canvas.width = canvasW;
        canvas.height = canvasW * 0.6476190476190476;

        // フットボールコート描画用
        this.fWidth  = canvas.width - 10;
        this.fHeight = canvas.height - 10;
        this.halfLine = canvas.width * 0.08714285714285715;
        this.penaStart = canvas.width * (13.84/105);
        this.penaWidth = canvas.width * (40.32/105);
        this.penaHeight = canvas.width * (16.5/105);
        this.goalAreaStart = canvas.width * (24.84/105);
        this.goalWidth = canvas.width * (18.32/105);
        this.goalHeight = canvas.width * (5.5/105);
        this.penaX = canvas.width * (11/105);
        this.penaY = canvas.height / 2;
        this.penaArcRedis = canvas.width * (9.15/105);
        this.cornerArc = canvas.width * (1/105);
        this.cornerBottom = canvas.height-this.fpadding;
        this.cornerAway = canvas.width-this.fpadding;
        

        // localstorageからデータを取得する
        // console.log('aaaa', this.$store.state.footballPlayers)
        // if (this.$store.state.footballPlayers.length) {
        //     this.players = this.$store.state.footballPlayers;
        // }


        // playerの初期位置を設定する
        var xx = 0;
        for (var i in this.players) {
            if (i <= 10) {
                this.players[i].objX = this.fpadding + 50 + xx;
                this.players[i].objY = 30;
            } else {
                this.players[i].objX = canvas.width/2 - this.objWidth/2 + xx;
                this.players[i].objY = 30;
            }
            if (i == 10) {
                xx = 50
            }
            // this.players[i].objY = canvas.height/2 - this.objHeight/2 + xx;
            xx = xx + 32;
        }

        this.drawRect();
        this.switch1 = true;
    },
    watch: {
        switch1: function(val) {
            if (val) {
                this.addEvent();
            } else {
                this.removeEvent();
            }
        },
        switch2: function(val) {
            if (val) {
                this.addEvent();
            } else {
                this.removeEvent();
            }
        }
    },
}
</script>

<style>
#canvas-parent {
    background: white;
    padding: 10px;
    display: inline-block;
    /* position: relative; */
    /* width : 100%; */
    /* height: 100vh; */
}
#tactical-board {
    /* width: 100%; */
    vertical-align: bottom;
    /* background: #005d07; */
    background: white; 
    background: rgb(255, 255, 255);
    border: 1px solid;
}
</style>


