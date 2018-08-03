<template>
  <div id="app">
  	<div class="begin" v-show="!beginBtn">
  		<h2 class="begin-label">广发银行专用打票据练习</h2>
  		<div class="begin-btn" @click="begin">点击开始</div>
  	</div>
  	<div class="body" v-show="beginBtn">
  		<div class="top" v-show="!gameover">
  			<div class="head">
  				<div class="counter">第 {{count}} 个</div>
	  			<div class="timer">{{time}}</div>
  			</div>
	  		<div class="nums">{{num}}</div>
	  		<input type="text" v-model="textNum" @keydown="keyPress($event)" class="inputText">
	  	</div>
	  	<div class="result" v-show="gameover">
	  		<div class="re-body">
	  			共完成{{count - 1}}个
		  		<br>
		  		正确{{correctCount}}个
		  		<br>
		  		错误{{count - 1 - correctCount}}个
		  		<br>
		  		正确率{{Math.floor(correctCount/(count - 1)*100)}}%
		  		<br>
		  		得分
		  		<div class="score">
		  			{{score}}
		  		</div>
	  		</div>
	  		<div class="re-btn" @click="restart">重新开始</div>
	  	</div>
	  	<div class="bottom">
	  		<div class="block" v-for="(item, key) in resultArr" :key="key">
	  			<div class="block-top">{{item.num}}</div>
	  			<div class="block-bottom" :class="{wrong: item.isWrong}">{{item.inputNum}}</div>
	  		</div>
	  	</div>
  	</div>
  </div>
</template>
<script>
export default {
  name: 'app',
  data () {
    return {
      beginBtn: false,
      textNum: '',
      time: '00 : 00',
      count: 1,
      num: '',
      gameover: false,
      correctCount: 0,
      resultArr: [],
      // 首次键盘点击开始计时
      firstPress: false,
      score: 0,
      limitMin: 5
    }
  },
  methods: {
  	restart () {
  		this.time = '00 : 00';
  		this.count = 1;
  		this.correctCount = 0;
  		this.textNum = '';
  		this.resultArr = [];
  		this.firstPress = false;
  		this.gameover = false;
  		this.score = 0;
  	},
  	_gameover () {
  		let cut = 0;
  		let percent = Math.floor(this.correctCount / (this.count-1)*100);
  		if (percent < 100 && percent > 95) {
  			cut = -5;
  		}else if (percent <= 95 && percent > 9){
  			cut = -10;
  		}else if(percent <= 9 && percent > 8){
  			cut = -20;
  		}
  		console.log(percent);
  		console.log(this.correctCount);
  		console.log(cut);
  		this.score = (this.correctCount * 0.5) + cut;
  		this.gameover = true;
  	},
  	keyPress (ev) {
  	  if (!this.firstPress) {
  	  	this.firstPress = true;
  			this._getTime();
  		}
  		if (ev.keyCode === 13 || ev.keyCode === 108) {
  			this._nextNum();
  		}
  	},
  	_nextNum () {
  		if (this.textNum == this.num) {
  			this.correctCount++;
  		}
  		let newItem = {
  			num: this.num,
  			inputNum: this.textNum,
  			isWrong: this.textNum !== this.num
  		};
  		this.resultArr.push(newItem);
  		this.count++;
  		this.textNum = '';
  		this.num = this._getNum();
  	},
  	begin () {
  		this.beginBtn = true;
  		this.num = this._getNum();
		},
  	_getTime () {
			let second = 0;
  		let min = 0;
  		let clock = window.setInterval(() => {
  			second++;
  			if (second >= 60) {
  				second = 0;
  				min++;
			    if (min >= this.limitMin){
		  			window.clearInterval(clock);
		  			this._gameover();
		  			alert('time up');
		  		}
  			}
  			this.time = (min > 9 ? min : ('0' + min)) + ' : ' + (second > 9 ? second : ('0' + second));
  		}, 1000);
  	},
  	_getNum () {
  		let intPart  = Math.floor(Math.random() * 10000000);
  		let floatPart = Math.floor(Math.random() * 100);
  		return intPart + (floatPart > 0 ? '.' : '') + floatPart;
  	}
  }
}
</script>

<style>
.begin{
	text-align: center;
	margin-top: 60px;
}
.begin .begin-label{
	font-size: 60px;
}
.begin .begin-btn{
	cursor: pointer;
	height: 60px;
	width: 240px;
	margin: 60px auto 0 auto;
	line-height: 60px;
	border: 1px solid #000;
	transition: all 1s ;
	border-radius: 10px;
}
.begin .begin-btn :hover{
	background-color: #aaa;
	width: 400px;
}
.top{
	text-align: center;
}
.top .head .counter{
	display: inline-block;
	margin-right: 30%;
	font-size: 26px;
}
.top .head .timer{
	display: inline-block;
	font-size: 26px;
}
.nums{
	margin: 20px 0;
	font-size: 60px;
	color: #F15A24;
}
.inputText{
	text-align: right;
	line-height: 56px;
	height: 60px;
	width: 400px;
	font-size: 52px;
	border-radius: 10px;
	border: 1px solid #AAAAAA;
	outline: none;
}
.score{
	margin-top: 20px;
	line-height: 60px;
	font-size: 60px;
	font-weight: 700;
}
.result{
	text-align: center;
}
.result .re-body{
	font-size: 18px;
	line-height: 26px;
}
.result .re-btn{
	cursor: pointer;
	text-align: center;
	height: 60px;
	line-height: 60px;
	width: 180px;
	margin: 30px auto 0 auto;
	border: 1px solid #000;
	border-radius: 10px;
}
.bottom{
	margin: 20px 50px 0 50px;
}
.bottom .block{
	text-align: center;
	vertical-align: top;
	display: inline-block;
	width: 100px;
	height: 60px;
	line-height: 30px;
	border: solid 1px #000000;
}
.bottom .block .block-top{
	font-weight: 500;
	height: 30px;
	line-height: 30px;
	border-bottom: solid 1px #000000;
}
.wrong{
	color: #F15A24;
}
</style>
