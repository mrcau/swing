<template>
  <div class="mainBox">
    <v-app-bar app dark style="padding: 0;" >
      <v-btn fab x-small elevation="0" @click="apiDialog=true" v-if="!options.title">
        <span class="mdi mdi-24px mdi-cog" ></span></v-btn>
      <v-btn fab x-small elevation="0" class="mr-2" v-if="options.title"
             @click="dialog=true;edit=true;addUser=false;dTitle='돌림판 관리';newItems=[...options.item];">
              <span class="mdi mdi-24px mdi-pencil"></span></v-btn>
      <v-spacer></v-spacer>
      <!-- <v-btn  text @click="dialog=true"><span class="mdi mdi-24px mdi-pencil"></span></v-btn> -->
        <div style="max-width: 500px; width: 100%;position: relative;">
        <select class="select2 rounded-pill"  v-model="options" @change="selectOption()">
          <option seleced="selected" :value="resetoption" >
            <h1>스마트 돌림판</h1>
          </option>
          <option v-for=" (n,i) in localItems.optionItems" :key="i" :value="n">
           <h1> {{ n.title }} </h1><span class="mdi mdi-24px mdi-pencil"></span>
          </option>
        </select>
        <span class="mdi mdi-24px mdi-menu-down" style="position: absolute;transform: translate(-30px,-3px);"></span>
      </div>
      <v-spacer></v-spacer>
      <v-btn  fab x-small elevation="0" 
             @click="dialog=true;dTitle='돌림판 추가';edit=false;addUser=false;newItems=[];addTitle=''">
              <span class="mdi mdi-24px mdi-plus"></span></v-btn>

    </v-app-bar>
      <!-- <span class="py-5"></span> -->
      <div class="top" >
        <v-btn fab x-small dark elevation="0" @click="reload">
        <span class="mdi mdi-24px mdi-refresh" ></span></v-btn>
          <div class="white--text pt-2">
            <h1>{{ name }}</h1>
          </div>
          <v-btn fab x-small dark :color="pop?'red':'black'" elevation="0" @click="pop=!pop">
        <span class="mdi mdi-24px mdi-location-exit" ></span></v-btn>
       </div>
    
    <div class="roulet mt-10">
      <div>
        <h1 style="color:#fbc700">{{ arrow }}</h1>
      </div>
      <div class="arrow"></div>
      <canvas id="canvas"> </canvas>
        <v-btn fab class="btn" color="primary" @click="spin" style="width: 100px;height: 100px;" v-if="options.item.length">
         <h2> START</h2>
        </v-btn>
        <v-btn fab class="btn" color="primary" @click="reset" v-else style="width: 100px;height: 100px;">
         <h2> RESET</h2>
        </v-btn>
    </div>
      <!-- <div class="white--text">
        <h1>{{ name }}</h1>
      </div> -->
    <div class="bottom"> 
      <v-card class="mx-auto rounded-xl" color="#37474F" rounded dark min-width="300" max-width="580" >
          <v-card-text class="d-flex">
            <span class="mt-1 mdi mdi-36px mdi-account" v-if="!users.title&&pencil"></span>
            <v-btn fab x-small elevation="0" color="#243740" class="mr-2" v-if="users.title&&pencil"
             @click="dialog=true;edit=true;addUser=true;dTitle='사용자 관리';newItems=[...users.item];">
              <span class="mdi mdi-24px mdi-pencil"></span></v-btn>

        <div style="max-width: 500px; width: 100%;position: relative;text-align: center;">
            <select class="select2 rounded-pill " v-model="users" @change="selectUser()" >
              <option seleced="selected" :value="reUser" >
                <h1 >사용자 선택</h1>
              </option>
              <option v-for="(n,i) in localItems.userItems" :key="i" :value="n">
                <!-- :value n은 v-model 의 users -->
                <h1> {{ n.title }} </h1>
              </option>
            </select>
        <span class="mdi mdi-24px mdi-menu-down" style="position: absolute;transform: translate(-25px,4px);"></span>
      </div>

            <v-btn fab x-small elevation="0" color="#243740" class="ml-2"
             @click="dialog=true;dTitle='사용자 추가';edit=false;addUser=true;newItems=[];addTitle=''">
              <span class="mdi mdi-24px mdi-plus"></span></v-btn>

          </v-card-text>
        <v-card-text class="bottom">
          <v-btn v-for="(n,i) in users.item" :key="i" class="mb-2" text dark 
          :disabled="n.length>5" @click="name=n;userIndex=i">
            {{n}}
          </v-btn>
        </v-card-text>
        <v-card-text  class="mx-auto rounded-b-xl"  v-if="resultUser.length"
        style="background-color:#263238;" >
          <span class="mx-2" v-for="(n,i) in resultUser" :key="i" style="color:#fbc700">{{ n }}</span>
          <v-divider class="my-2" ></v-divider>
          <v-btn block class="mt-2 white--text rounded-pill" dark  color="#37474F"
          :loading="loading" @click="resultGsave()" v-if="api"  >
          G-SAVE
          </v-btn>
      </v-card-text>
      </v-card> 
  </div> 


  <footer  style="position: absolute;bottom:0;left: 50%;transform: translateX(-50%); color:white"  >
         ©  2023 Kim-Builder 
         <span class="mdi mdi-flip-h mdi-arm-flex white--text"></span>  
  </footer> 


    <v-dialog v-model="dialog" persistent max-width="500px" style="position: relative;" >
          <v-card color="secondary" class="pb-1"  >
            <v-card-title style="padding: 0;margin: 0;" >
              <v-spacer></v-spacer>
              <!-- <h3 class="mt-2 white--text">{{ dTitle }}</h3> -->
              <v-spacer></v-spacer>
              <v-btn dark  icon small @click="dialog=false;newItems=[];addTitle=''" > 
                <span class="mdi mdi-close" style="font-size:large;"></span>  
              </v-btn>
            </v-card-title> 
              <v-form ref="form1" lazy-validation  >
                <v-card-title class="text-h5 white--text mx-5 pt-3" style="padding:0;">
                  <!-- <h4>{{ title }} </h4>  -->
                  <v-text-field v-model="options.title" label="TITLE" :rules="Rules" required dark @keyup.enter ="createItems"  dense v-if="edit&&!addUser" ></v-text-field>
                  <v-text-field v-model="users.title" label="TITLE" :rules="Rules" required dark @keyup.enter ="createItems"  dense v-if="edit&&addUser" ></v-text-field>
                  <v-text-field v-model="addTitle" label="TITLE" :rules="Rules" required dark @keyup.enter ="createItems"  dense v-if="!edit" ></v-text-field>
                </v-card-title> 
                <v-card-text class="d-flex"> 
                  <v-spacer></v-spacer>
                  <v-btn color="primary"  fab small @click="createItems"  >
                    <span class="mdi mdi-plus" style="font-size: 30px;color:white ;" ></span>
                  </v-btn>
                  <v-spacer></v-spacer>
                </v-card-text>
                  <div class="px-5 pt-5 rounded"  >
                    <div class="d-flex" v-for="(n,i) in newItems" :key="i">
                        <v-text-field v-model="newItems[i]" label="Name" :disabled="newItems[i].tf" 
                        :rules="Rules" required dark filled outlined dense color="var(--main-color)"
                        style="padding: 0; margin: 0; font-style: normal" @keyup.enter ="createItems" ></v-text-field>
                       <v-btn dark icon class="ml-1" color="error" @click="removeItem(i)" >
                           <span class="mdi mdi-close" style="font-size: 25px;"></span>
                       </v-btn> 
                    </div>
                  </div>
                <!-- 제출버튼 -->
                <v-card-title style="display: flex;">
                  <v-btn  class=" white--text rounded-pill" dark  color="error" :loading="loading" v-if="edit" @click="dataRemove()"  >
                    <span class="mdi mdi-delete" style="font-size:large;"></span>삭제
                  </v-btn> <v-spacer></v-spacer> 
                  <v-btn rounded class=" white--text" color="primary"  :loading="loading" @click="save()" v-if="newItems.length" >
                    <span class="mdi mdi-content-save-outline" style="font-size:large;"></span>저장 
                  </v-btn>
                </v-card-title>
              </v-form>
            </v-card>
      </v-dialog>

      <!-- google API -->
      <v-dialog v-model="apiDialog" persistent max-width="500px" style="position: relative;" >
          <v-card color="secondary" class="pb-1"  > 
              <v-form ref="form1" lazy-validation  >
                <v-card-title class="text-h5 white--text mb-3 rankTop" style="padding: 5px 12px 0 12px"> 
                <span class="mdi mdi-google"></span> - Spreadsheet <v-spacer></v-spacer> 
                <v-btn dark  icon small @click="apiDialog=false " > 
                  <span class="mdi mdi-close" style="font-size:large;"></span>  
                </v-btn>
                </v-card-title>
                <v-card-title class="text-h5 white--text mx-5 pt-3" style="padding:0;">
                  <v-text-field v-model="itemApi" label="API(User)" :rules="Rules" required dark  dense ></v-text-field>
                </v-card-title>  
                <v-card-title class="text-h5 white--text mx-5 pt-3" style="padding:0;">
                  <v-text-field v-model="api" label="API(Result)" :rules="Rules" required dark  dense ></v-text-field>
                </v-card-title>  
                <v-card-title class="text-h5 white--text mx-5 pt-3" style="padding:0;">
                  <v-spacer></v-spacer>
                 <a href="https://sheetdb.io/" target="blank" style="font-size: 16px;text-decoration: none;color: #fbc700;"> GET API </a>
                </v-card-title>  
                <v-card-actions  >
                  <v-btn rounded class="white--text" block color="primary" :loading="loading" @click="apiSave()" >
                    SAVE 
                  </v-btn>
                </v-card-actions>
              </v-form>
            </v-card>
      </v-dialog>
  </div>
</template>

<script>


  export default {
    name: 'HelloWorld',
    data(){
      return{
        version: '0211',
        api:'',
        itemApi:'https://sheetdb.io/api/v1/myga56uk7tey9',
        apiItem:'',
        apiUser:'',
        ctx:'',
        arc:'',
        text:'',
        userTitle:'',
        userName:'',
        userIndex:'',
        name:'',
        pencil:true,
        edit:false,
        loading:false,
        dialog:false,
        spinArcStart : 10,
        spinTime : 0,
        spinTimeTotal : 360,
        startAngle : 0,
        removeTF:'false',
        spinTimeout:'',
        arrow:'ROTATE',
        reUser:[],
        resetoption:{item:['1', '2','3','4','5','6','7']},
        options:{item:['1', '2','3','4','5','6','7']},
        newItems:[],
        users:[],
        userItem:[],
        userItems:[],
        optionItems:[],
        localData:{},
        localItems:{
          userItems:[
                    {title:'로봇반',item:['문재인','윤석렬','김어준']},
                    ],
          optionItems:[
                    {title:'스포츠',item:['배드민턴','축구','줄넘기']},
                    ]
         }, 
        Rules: [(v) => !!v || "필수입력란"],
        colors:[ "#F15F5F", "#F29661", "#F2CB61", "#E5D85C", "#BCE55C", "#86E57F", "#5CD1E5",
      "#6799FF", "#6B66FF", "#A566FF", "#F361DC", "#F361A6", "#A6A6A6", "#8C8C8C" ],
        dTitle:'title',
        addTitle:'',
        addUser:false,
        resultUser:[],
        pop:false,
        apiDialog:false,
        sound:new Audio(require('../assets/tic.mp3')),
        pang:new Audio(require('../assets/pang.mp3'))
      }
    },
    watch:{
      options(){
        this.name = ''
      },
      users(){
        this.name = ''
      },
      arrow(){
        this.sound.play()
      }
    },
    created(){
      this.getV()
      this.getApi()
    },
    mounted(){
      this.getLocal()
      // this.getItem()
      this.drawRouletteWheel();
    },  
    methods:{
      async getV () {
      fetch('https://mrcau.github.io/version/version.json')
      .then(response=>response.json())
      .then(json=>{
        if (json.swing !== this.version) {this.reload()}
      }).catch(error=>{console.log(error);});
      },
      async reload () {
        caches
          .keys()
          .then(c => {
            for (const i of c) {
              caches.delete(i)
            }
          })
          .then(() => {
            location.reload(true)
          })
      },
      getApi(){
      this.api=localStorage.getItem('sheetdb') 
      this.itemApi=localStorage.getItem('itemApi') 
      },
     async getLocal(){
         this.localData =   JSON.parse( localStorage.getItem('rouletItem'))||{userItems:[],optionItems:[]},
         this.localItems =  JSON.parse( localStorage.getItem('rouletItem'))||{userItems:[],optionItems:[]}
         let dataItem =''
         if(!this.itemApi){ return }
        await fetch(this.itemApi).then(response => response.json()).then((data)=>{
          if(data.error){ return}
          dataItem = data.map(n=>{
            const name = n.name
            if(name===undefined){return '오류'}
            else{return name}
          })
          this.apiItem ={title:'사용자',item:dataItem}
        })

         if(this.apiItem){
           const userIndex = this.localItems.userItems.findIndex(item=>item.title ==='사용자')
           const itemIndex = this.localItems.optionItems.findIndex(item=>item.title ==='사용자')
          if(userIndex>-1){
            this.localItems.userItems[userIndex] = {title:'사용자',item:[...dataItem]}
            this.localData.userItems[userIndex] = {title:'사용자',item:[...dataItem]}
          }
          if(itemIndex>-1){
            this.localItems.optionItems[itemIndex] = {title:'사용자',item:[...dataItem]}
            this.localData.optionItems[itemIndex] = {title:'사용자',item:[...dataItem]}
          } 
          if(!this.localItems.userItems.find(key=> key.title==='사용자')){
            this.localItems.userItems.unshift({title:'사용자',item:[...dataItem]})
            this.localData.userItems.unshift({title:'사용자',item:[...dataItem]})
          }
          if(!this.localItems.optionItems.find(key=> key.title==='사용자')){
            this.localItems.optionItems.unshift({title:'사용자',item:[...dataItem]})
            this.localData.optionItems.unshift({title:'사용자',item:[...dataItem]})
          } 
         }
      },
      drawRouletteWheel(){
      const size = window.innerWidth < 600 ? window.innerWidth  : 600;
      const canvas = document.getElementById("canvas");
      //Cansvas 가 변수에 할당된 후에 실행하기 위해 If 문으로 canvas 속성여부 판단후실행
        if (canvas&&canvas.getContext) {
        canvas.width = size;
        canvas.height = size;
        //원호 그리는 위치
        var insideRadius = 5;
        var outsideRadius = size/2-10;
        //글자 나타나는 위치
        var textRadius = size/3+20;
        
        this.arc = Math.PI / (this.options.item.length / 2);
        this.ctx = canvas.getContext("2d"); 
        this.ctx.clearRect(0, 0, size, size);
        for (var i = 0; i < this.options.item.length; i++) {
          var angle = this.startAngle + i * this.arc; 
          this.ctx.fillStyle = this.colors[i]
          this.ctx.beginPath();
          this.ctx.arc(size/2,size/2, outsideRadius, angle, angle + this.arc, false);
          this.ctx.arc(size/2, size/2, insideRadius, angle + this.arc, angle, true);
          this.ctx.stroke();
          this.ctx.fill();
          this.ctx.save();

          //글자 크기 글자수와 화면크기에 따라 조정
          this.ctx.font = `bold ${size/16-this.options.item[i].length*2+'px'} Helvetica, Arial`;
          this.ctx.shadowOffsetX = 1;
          this.ctx.shadowOffsetY = 1;
          this.ctx.shadowColor = "white";
          this.ctx.fillStyle = "black";
          this.ctx.translate(
            size/2 + Math.cos(angle + this.arc / 2) * textRadius,
            size/2 + Math.sin(angle + this.arc / 2) * textRadius
          );
          this.ctx.rotate(angle + this.arc / 2 + Math.PI / 2);
          var text = this.options.item[i];
          this.ctx.fillText(text, -this.ctx.measureText(text).width / 2, 0);
          this.ctx.restore();
         }
        }
      },
      spin() {
        this.spinAngleStart = Math.random() * 10 + 10;
        this.spinTime = 0;
        this.spinTimeTotal = Math.floor(Math.random() *10+1)*2000;
        this.rotateWheel();
      },
      rotateWheel() {
        this.spinTime += 30;
        var degrees = (this.startAngle * 180) / Math.PI + 90;
        var arcd = (this.arc * 180) / Math.PI;
        var index = Math.floor((360 - (degrees % 360)) / arcd);
        this.arrow = this.options.item[index]
        if (this.spinTime >= this.spinTimeTotal) {
          this.stopRotateWheel();
          return;
        }
        var spinAngle = this.spinAngleStart - this.easeOut(this.spinTime, 0, this.spinAngleStart, this.spinTimeTotal);
        this.startAngle += (spinAngle * Math.PI) / 180;
        this.drawRouletteWheel();

        this.spinTimeout = setTimeout(()=>{this.rotateWheel()}, 10);
      },
      stopRotateWheel() {
        // this.pang.play()
        clearTimeout(this.spinTimeout);
        var degrees = (this.startAngle * 180) / Math.PI + 90;
        var arcd = (this.arc * 180) / Math.PI;
        var index = Math.floor((360 - (degrees % 360)) / arcd);
        this.ctx.save();
        this.ctx.font = "bold 30px";
        this.text = this.options.item[index]
        this.ctx.restore();
        if(this.users.item&&this.name){
          this.changeUser(index)
        }else{
          if(this.pop){
          console.log(this.pop)
          this.options.item.splice(index,1)
          this.drawRouletteWheel();
        }
      }
      },
      changeUser(index){
        // const name = this.users.item[this.userIndex].substring(0, 3) + ' : ' + this.options.item[index]
        const item = {[this.users.item[this.userIndex]]:this.options.item[index]}
        this.pencil=false
        const i = this.resultUser.findIndex(e=>Object.keys(e).toString()==this.users.item[this.userIndex])
        if(i > -1){
        this.$set(this.resultUser, i, item)
          console.log('hi')
        }else{
          this.resultUser.push(item)
          console.log('hello')
        }
        
        if(this.pop){
          console.log(this.pop)
          this.options.item.splice(index,1)
          this.drawRouletteWheel();
        }
        //배열과 오브젝트 값은 실시간 변경해도 변경 안되므로 $set'배열,배열index,변경값'
        // this.$set(this.users.item, this.userIndex, name)
      },
      easeOut(t, b, c, d) {
        var ts = (t /= d) * t;
        var tc = ts * t;
        return b + c * (tc + -3 * ts + 3 * t);
      },
      selectOption(){
      this.drawRouletteWheel();
      },
      selectUser(){
        this.localItems = this.localData
        this.userTitle = this.users.title
        this.resultUser = []
        this.pencil=true
      },
      createItems(){
        this.newItems.unshift('')
      },
      removeItem(i){
        this.newItems.splice(i,1) 
      }, 
      reset(){
        this.options.item=this.resetoption.item;
        this.arrow='ROTATE',
        this.name='',
        this.users=[];
        this.userItem=[]
        this.resultUser = []
      this.drawRouletteWheel();
      },
     async dataRemove(){
        if(this.addUser){
          console.log('user')
          await this.userRemove()
        localStorage.setItem('rouletItem',[JSON.stringify(this.localItems)])
        }else{
          console.log('item')
          await this.itemRemove()
        localStorage.setItem('rouletItem',[JSON.stringify(this.localItems)])
          console.log('삭제저장')
        }
      },
      itemRemove(){
        const index = this.localItems.optionItems.findIndex(e=>e.title==this.options.title)
        this.localItems.optionItems.splice(index,1)
        this.options = this.resetoption
      this.drawRouletteWheel();
        this.dialog=false;
        console.log("삭제")
      },
      userRemove(){
        const index = this.localItems.userItems.findIndex(e=>e.title==this.users.title)
        this.localItems.userItems.splice(index,1)
        this.name='',
        this.users=[];
        this.userItem=[]
        this.dialog=false
      },
      save(){  
        console.log('저장',this.userItems)
        if(this.edit){
          this.userItems[this.index]=this.userItems
            const valid = this.$refs.form1.validate();
            if (!valid||this.newItems.length==0) {
              return;
            }
            if(this.addUser){
              this.users.item = this.newItems
              const index = this.localItems.userItems.findIndex(e=>e.title==this.users.title)
              this.localItems.userItems[index] = this.users
              localStorage.setItem('rouletItem',[JSON.stringify(this.localItems)])
            }else{
              this.options.item = this.newItems
              const index = this.localItems.optionItems.findIndex(e=>e.title==this.options.title)
              this.localItems.optionItems[index] = this.options
              localStorage.setItem('rouletItem',[JSON.stringify(this.localItems)])
          this.drawRouletteWheel();

            }
          }else{
            const valid = this.$refs.form1.validate();
            if (!valid||this.newItems.length==0) {
              return;
            }
            if(this.addUser){
              const item ={title:this.addTitle,item:this.newItems}
              console.log('user2',item,':',this.localItems,this.localItems.userItems)
              this.localItems.userItems.unshift(item)
            }else{
              const item ={title:this.addTitle,item:this.newItems}
              console.log('option2',item,this.localItems.optionItems,this.localItems)
              this.localItems.optionItems.unshift(item)
            }
            localStorage.setItem('rouletItem',[JSON.stringify(this.localItems)])
        }
          this.dialog=false; 
      }, 
      resultGsave(){
        console.log(this.resultUser) 
        this.resultUser.forEach((n)=>{
        const name = Object.keys(n).toString()
        const value = Object.values(n).toString()
        fetch(this.api,{
          method: 'POST',
          body: new URLSearchParams({ // 일반 객체를 fordata형식으로 변환해주는 클래스
            name: name,
            result: value
        })
        }).then(response => response.json())
        .then((data)=>{console.log(data,'success save')})
        })
      },
      apiSave(){
        if(!this.api||!this.itemApi){
          return
        }
        localStorage.setItem('sheetdb',this.api)
        localStorage.setItem('itemApi',this.itemApi)
        this.getLocal()

        this.apiDialog = false
      }
    }
  }
</script>
<style scoped>
  .mainBox {
    text-align: center;
    width: 100%;
    height:100%;
    display: flex;
    flex-direction: column;
    /* justify-content: space-between; */
    align-items: center;
    position: relative;
  }
  .top{
    padding:15px;
    display: flex;
    justify-content: space-between;
    height:10px;
    width: 100%;
  }
  .topMenu {
    max-width: 100px;
    transform: translateY(-15px);
  }
  select{
    cursor: pointer;
    /* border-radius: 5px; */
    width: 100%;
    height: 30px;
    text-align: center;
    color:white;
    font-weight: 900;
    font-size: 18px;
    background-color: gray;
  }
  .select1{
    max-width: 500px;
    background-image: linear-gradient(to right, #25aae1, #4481eb, #04befe, #3f86ed);
    box-shadow: 0 4px 15px 0 rgba(65, 132, 234, 0.75);
  }
  .select2{
    max-width: 500px; 
        /* background-image: linear-gradient(to right, #ed6ea0, #ec8c69, #f7186a , #FBB03B); */
    background-image: linear-gradient(to right, #25aae1, #4481eb, #04befe, #3f86ed);
    box-shadow: inset 1px 1px 0px black;
  }
  .bottom{
    width: 100%;
    text-align: center;
    display: flex;
    justify-content: space-around;
    flex-wrap : wrap;
    padding: 1px;
  }
  .roulet{

    position: relative;
  }
  #canvas{
    background-color: ffffff;
    position: relative;
   }
   .arrow {
  left: 50%;
  position: absolute;
  z-index: 1;
  width: 0;
  height: 0;
  border-top: 30px solid white;
  transform: translate(-50%,-5px);
  /* 화살표 */
  border-left:  10px solid transparent;
  border-right: 10px solid transparent;
  }
  .btn{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-30%);
  }
</style>
