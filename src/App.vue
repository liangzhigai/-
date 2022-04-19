<template>
  <div id="app">
    <div class="content">
      <div class="sectionWrap" ref="boxWrapper">
        <div class="box" >
          <div class="boxHead">
            <div class="textContainer">
              <img class="userIcon" src="static/userIcon.png">
              <div class="text">{{message}}</div>
					</div>
          </div>
          <div class="boxCard">
            <div class="container">
              <div class="card" v-for="(item,index) in cardBox" @click="clickRobot(item.id,item.name)">
                <div class="image">
                  <img :src="item.url">
                </div>
                <span class="name">{{item.name}}</span>
						</div>
					</div>
          </div>
          <div class="boxMessage">
            <div class="wrapper">
              <div class="left">
                <div class="guess">
                  <div class="a">猜</div>
                  <div>你</div>
                  <div>想</div>
                  <div>问</div>
                </div>
                <div class="icon">
                  <img src="static/icon.png">
                  <span>换一批</span>
                </div>
              </div>
              <div class="right">
                <div class="option" v-for="item in option">
                  <span @click="appendRobotMsg(item.questionId,item.questionName)">{{item.questionName}}</span>
                  <svg class="rightIcon"></svg>
                </div>
              </div>
            </div>
          </div>
          <div v-for="(item,index) in info" id="info">
            <div class="msgCard_me" v-if="item.type == 'rightinfo'">
              <div class="cardBox">
                <div :class = "showMore ? 'closeCard' : 'customCard'">{{item.content}}</div>
              </div>
            </div>
            <div class="msgCard_you" v-if="item.type == 'leftinfo' && item.show == '1'">
              <div class="textContainer">
                <img class="userIcon" src="../static/userIcon.png">
                <div class="text">{{item.content}}</div>
              </div>
            </div>
            <div class="msgCard_you" v-if="item.type == 'leftinfo' && item.show == '2'">
              <div class="recommend">
                <img class="userIcon" src="../static/userIcon.png">
                <div class="recommendCard">
                  <div class="header">请问您想咨询以下哪类问题？</div>
                  <div class="body" v-for="(item1,index) in item.question">
                    <div class="item" @click="appendRobotMsg(item1.questionId,item1.questionName)">{{item1.questionName}}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="footer">
        <div class="autoInput">
          <div class="inputWrap">
            <div style="flex:1 1 0%;">
              <div class="inputBox item input-item">
                <div class="line">
                  <div class="input-control">
                    <input placeholder="请输入您的问题" type="text" v-model="customerText" @keyup.enter="sentMsg()">
                  </div>
                </div>
              </div>
            </div>
            <span class="btnSend" @click="sentMsg()">
              <img  class="btnIcon" :src="btnIconUrl">
            </span>
          </div>
          <span>
            <img  class="btnIcon" src="../static/icon1.png">
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import BScroll from 'better-scroll';
import btnIcon from "../static/send.png"
import btnIcon1 from "../static/btnIcon1.png"
export default {
  name: 'app',
  data() {
  	return {
      showMore:false,
      btnIconUrl:btnIcon,
      customerText: "",
  		message:"您好，我是山西健康码便民热线，很高兴为您服务",
  		cardBox:[
  			{
          id:1,
  				url:"static/healthCode.png",
  				name:"健康码",
          type:1,
          rows: [{              "questionContent": "红码隔离",              "questionId": "8",              "questionName": "红码"          }, {              "questionContent": "黄码隔离",              "questionId": "9",              "questionName": "黄码怎么办"          }]
  			},
  			{
          id:2,
  				url:"static/sanjin.png",
  				name:"场所码",
          type:1,
          answer:[
            {content:"1"},
            {content:"2"},
          ]
  			},
  			{
          id:3,
  				url:"static/yiqing.png",
  				name:"防控通告",
          type:1
  			},
  			{
          id:4,
  				url:"static/shenghuo.png",
  				name:"抗原检测",
          type:1
  			},
  			{
          id:5,
  				url:"static/shenpi.png",
  				name:"核酸检测",
          type:1
  			},
  			{
          id:6,
  				url:"static/renshe.png",
  				name:"出行政策",
          type:1
  			},
  			{
          id:7,
  				url:"static/gongjijin.png",
  				name:"中高风险地区",
          type:1
  			},
  			{
          id:8,
  				url:"static/shuiwu.png",
  				name:"就医买药",
          type:1
  			},
  		],
      option:[],
      info:[],
      key:[]
  	}
  },
  methods:{
    // getKeys(){
    //   axios.get("http://stwk7g.natappfree.cc/keyword/listByWight").then( res =>{
    //     console.log("关键字列表",res.data)
    //   })
    // },
    init(){
      var that = this
      axios.get("http://stwk7g.natappfree.cc/question/listQuestionByWeight").then(function(res) {
        console.log("猜你想问",res.data.rows)
        that.option = res.data.rows
      })
    },
    clickRobot(id,name){
      var that = this
     axios.get("http://stwk7g.natappfree.cc/question/listQuestionByKeywordId",{params:{keywordId:id}}).then( function(res){
       console.log("aaa",res.data.rows)
       let list = res.data.rows
       console.log("ddd",list)
       console.log(name)
       let obj_l = {
         type: "leftinfo",
         name: "robot",
         content: "",
         question: list,
         show:"2"
       };
       console.log(obj_l)
       let obj_r = {
         type: "rightinfo",
         name: "robot",
         content: name,
         question: [],
       };
       console.log(obj_r)
       that.info.push(obj_r)
       that.info.push(obj_l)

       that.$nextTick(() => {
         var contentHeight = document.getElementById("info");
         console.log(contentHeight)
         contentHeight.scrollTop = contentHeight.scrollHeight;
         console.log(contentHeight.scrollTop)
       });
     })
    },
    appendRobotMsg(id,val){
     axios.get("http://stwk7g.natappfree.cc/question/getQuestionById",{params:{questionId:id}}).then(res =>{
       console.log("返回",res.data)
       let content = res.data.data.questionContent
       console.log(content)
       let obj_l = {
         type: "leftinfo",
         name: "robot",
         content: content,
         question: [],
         show:"1"
       };
       let obj_r = {
         type: "rightinfo",
         name: "robot",
         content: val,
         question: [],
       };
       this.info.push(obj_r)
       this.info.push(obj_l)
     })
       //console.log(res.data.questionContent)

    },
    sentMsg(){
      let text = this.customerText.trim();
      console.log(text)
      if(text != ""){
        var obj_r = {
          type: "rightinfo",
          content: text,
        };
        this.info.push(obj_r);
        //this.appendRobotMsg(this.customerText);
        this.customerText = "";
        this.$nextTick(() => {
          let contentHeight = document.getElementById("info");
          contentHeight.scrollTop = contentHeight.scrollHeight;
          console.log(contentHeight.scrollTop)
        });

        this.$nextTick(() => {
          let contentDom = document.querySelectorAll('.customCard')
          contentDom = [... contentDom]
          console.log(contentDom)
          console.log(contentDom[contentDom.length-1])
          let height = contentDom[contentDom.length-1].offsetHeight
          console.log(height)
          if(height > 64){
            this.showMore = true
          }
          // contentDom.forEach((item,index)=>{
          //   let height = item.offsetHeight
          //   console.log(height)
          //   if(height>64){
          //     this.showMore = true
          //   }
          // })

        });

        //this.appendRobotMsg(this.customerText);
        axios.get("http://stwk7g.natappfree.cc/question/getQuestionByChat",{params:{question:text}}).then(res =>{
          console.log(res.data.rows)
        let list = res.data.rows
        let obj_l = {
          type: "leftinfo",
          name: "robot",
          content: "",
          question: list,
          show:"2"
        };
        this.info.push(obj_l);
        })
      }
    },
    _initScroll(){
      this.scroll = new BScroll(this.$refs.boxWrapper,{click:true} );
    },
    // async getSeller(){
    //   const seller =await Service.getSeller();
    //   this.seller=seller;
    // },
  },
  created(){
    //this.getSeller()
    this.$nextTick(()=>{
      this._initScroll();
      console.log(this.scroll)
    })
  },
  watch : {
    customerText(val,oldVal){
      if(val){
        this.btnIconUrl = btnIcon1
      }
      else{
        this.btnIconUrl = btnIcon
      }
    }
  },
  mounted(){
    this.init();
    //this.getKeys()
  }
}
</script>

<style lang="scss" scoped>
.content {
		width: 100vw;
		height: 100vh;
		display: flex;
		flex-direction: column;
		padding-bottom: constant(safe-area-inset-bottom);
		padding-bottom: env(safe-area-inset-bottom);
		background: #eef1f1;
		box-sizing: border-box;
	 .sectionWrap{
		position: relative;
		width: 100%;
		flex: 1 1;
		overflow-y: auto;
		overflow-x: hidden;
		border-radius: 18px 18px 0 0;
		background: #eef1f1;
		.box{
			width: 100%;
			padding: 12px;
			.boxHead{
				margin-bottom: 10px;
				width: 100%;
				position: relative;
				.textContainer{
					display: flex;
					.userIcon{
						width: 38px;
						height: 38px;
						margin-right: 8px;
					}
					.text{
						padding: 8px;
						font-size: 16px;
						font-family: PingFangSC-Regular,PingFang SC;
						font-weight: 400;
						color: #333;
						line-height: 24px;
						max-width: 260px;
						margin-right: 12px;
						margin-bottom: 3px;
						background: #fff;
						border-radius: 0 8px 8px 8px;
						position: relative;
						min-width: 40px;
						margin-top: 20px;
						text-align: justify;
						word-wrap: break-word;
						white-space: pre-wrap;
					}
				}
			}
		  .boxCard{
				margin-bottom: 10px;
				width: 100%;
				position: relative;
				.container{
					padding: 0 12px;
					display: flex;
					flex-direction: row;
					flex-wrap: wrap;
					justify-content: space-between;
					.card{
						margin: 5px 0;
						display: inline-block;
						flex-grow: 0;
						flex-shrink: 0;
						flex-basis: calc(25% - 10px);
						box-shadow: 0 2px 6px 0 #d4d4d4;
						border-radius: 4px;
						background: #fff;
						display: flex;
						flex-direction: column;
						box-sizing: border-box;
						.image{
							display: flex;
							justify-content: center;
							align-items: center;
							height: 68px;
							img{
								max-width: 24px;
								max-height: 24px;
							}
						}
						.name{
							height: 25px;
							box-sizing: border-box;
							width: 100%;
							text-align: center;
							line-height: 10px;
              font-size:10px;
						}
					}
				}

			}
		  .boxMessage{
				margin-bottom: 10px;
				width: 100%;
				position: relative;
				.wrapper{
					margin: 0 12px;
					padding: 10px 0 10px 10px;
					min-height: 152px;
					display: flex;
					background: #fff;
					box-shadow: 0 2px 4px 0 rgb(0 0 0 / 6%);
					border-radius: 8px;
          .left{
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            margin-right: 14px;
            width:36px;
            .guess{
              font-size: 16px;
              font-family: PingFangSC-Regular,PingFang SC;
              font-weight: 400;
              color: #333;
              line-height: 20px;
              display: flex;
              flex-direction: column;
              align-items: center;
              .a{
                color: #ff952f;
              }
            }
            .icon{
              display: flex;
              flex-direction: column;
              font-size: 10px;
              font-family: PingFangSC-Regular,PingFang SC;
              font-weight: 400;
              color: #999;
              line-height: 1;
              align-items: center;
              img{
                width: 16px;
                height: 16px;
                margin-bottom: 8px;
                transition: .3s;
                transform-origin: center center;
              }
            }
          }
          .right{
            font-size: 16px;
            font-family: PingFangSC-Regular,PingFang SC;
            font-weight: 400;
            color: #333;
            line-height: 20px;
            flex: 1 1;
            .option{
              line-height: 24px;
              position: relative;
              word-wrap: break-word;
              word-break: break-all;
              display: flex;
              justify-content: space-between;
              padding-top: 6px;
              padding-bottom: 6px;
              .rightIcon{
                width: 22px;
                height: 22px
              }
            }
          }
				}
			}
      .msgCard_me{
        margin-bottom: 10px;
        .cardBox{
          display: flex;
          flex-direction: row-reverse;
          padding-bottom: 3px;
          .customCard{
            max-width: 260px;
            border-radius: 8px 0 8px 8px;
            background: #bae4fe;
            font-size: 16px;
            font-family: PingFangSC-Regular,PingFang SC;
            font-weight: 400;
            color: rgba(0,0,0,.85);
            line-height: 24px;
            padding: 8px 12px 8px 14px;
            float: right;
            position: relative;
            margin-top: 20px;
            box-shadow: 1px 0 8px 0 rgb(210 211 214 / 48%)
          }
          .closeCard{
            max-width: 260px;
            border-radius: 8px 0 8px 8px;
            background: #bae4fe;
            font-size: 16px;
            font-family: PingFangSC-Regular,PingFang SC;
            font-weight: 400;
            color: rgba(0,0,0,.85);
            line-height: 24px;
            padding: 8px 12px 8px 14px;
            float: right;
            position: relative;
            margin-top: 20px;
            box-shadow: 1px 0 8px 0 rgb(210 211 214 / 48%);
            max-height:64px;
            overflow: hidden;
          }
        }
      }
      .msgCard_you{
        margin-bottom: 10px;
        width: 100%;
        position: relative;
        .textContainer,.recommend{
          display: flex;
          .recommendCard{
            background: #fff;
            border-radius: 2px 8px 8px 8px;
            padding: 6px 8px 5px 14px;
            max-width: 260px;
            margin-right: 12px;
            font-family: PingFangSC-Regular,PingFang SC;
            font-weight: 400;
            color: #333;
            line-height: 24px;
            font-size: 16px;
            .header{
              line-height: 24px;
            }
            .item{
              line-height: 24px;
              padding-top: 6px;
              margin-top: 6px;
              position: relative;
              word-wrap: break-word;
              word-break: break-all;
              display: flex;
              justify-content: space-between;
              color: #3c84ff;
            }
          }
          .userIcon{
            width: 38px;
            height: 38px;
            margin-right: 8px;
          }
          .text{
            padding: 8px;
            font-size: 16px;
            font-family: PingFangSC-Regular,PingFang SC;
            font-weight: 400;
            color: #333;
            line-height: 24px;
            max-width: 260px;
            margin-right: 12px;
            margin-bottom: 3px;
            background: #fff;
            border-radius: 0 8px 8px 8px;
            position: relative;
            min-width: 40px;
            margin-top: 20px;
            text-align: justify;
            word-wrap: break-word;
            white-space: pre-wrap;
          }
        }
      }
     }
	 }
   .footer{
     width: 100%;
     background: #fff;
     .autoInput{
       width: 100%;
       display: flex;
       align-items: center;
       height: 58px;
       box-sizing: border-box;
       padding: 9px 10px;
       background: #eef1f2;
       position: relative;
       .inputWrap{
         flex: 1 1;
         display: flex;
         margin: 0 8px 0 0;
         align-items: center;
         .btnSend{
           margin-left: 8px;
           .btnSend{
             width: 27px;
             height: 27px;
           }
           img{
              vertical-align: middle;
           }
         }
         .inputBox{
           width: 100%;
           height: 40px;
           font-size: 16px;
           line-height: 40px;
           background: #fff;
           border-radius: 4px;
         }
         .item{
           min-height: auto;
           .line{
             position: relative;
             display: flex;
             flex: 1 1;
             align-self: stretch;
             padding-right: 15px;
             overflow: hidden;
           }
           .input-control{
             font-size: 17px;
             flex: 1 1;
             input{
               color: #000;
               font-size: 17px;
               -webkit-appearance: none;
               -moz-appearance: none;
               appearance: none;
               width: 100%;
               padding: 2px 0;
               border: 0;
               background-color: transparent;
               line-height: 1;
               box-sizing: border-box;
             }
           }
         }
         .input-item ,.item{
           padding-left: 12px!important;
         }

       }
     .btnIcon{
       width: 27px;
       height: 27px
     }
     img{
       vertical-align: middle;
     }
     }
   }
	}
</style>
