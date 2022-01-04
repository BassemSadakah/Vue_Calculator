<template>
<v-container>
  <v-card dark  light elevation="2" rounded="xl" max-width="370" class="calc_container mx-auto mt-16 pa-5">
      <div class="px-4 py-10 d-flex flex-column justify-end align-end" style="height:250px;">
        <span style="max-width:310px;overflow-y:hidden" class="d-flex flex-column-reverse title grey--text mb-2" v-html="history.join('')"></span>
        <span class="display-2" >{{shownInput}}</span>
      </div>
      <div class="d-flex flex-row">
              <v-btn @click="handleClear" min-width="0" height="unset"  color="red" elevation="0" class="calc_btn title rounded-md">C</v-btn>   
              <v-btn min-width="0" height="unset"  color="teal accent-4" elevation="0" class="calc_btn title rounded-md">(</v-btn>   
              <v-btn min-width="0" height="unset"  color="teal accent-4" elevation="0" class="calc_btn title rounded-md">)</v-btn>   
              <v-btn @click="handleSignClick('&divide;')" min-width="0" height="unset"  color="orange" elevation="0" class="calc_btn title rounded-md"> &divide;</v-btn>   
      </div>
      <div class="d-flex flex-row">
        <div class="d-flex flex-row flex-wrap-reverse justify-space-around">
              <v-btn v-for="(n,i) in Array(9)" :key="i" @click="handleDigitClick(i+1)" min-width="0" height="unset"  color="gray" elevation="0" class="calc_btn title rounded-md">{{i+1}}</v-btn>   
        </div>
        <div class="d-flex flex-col flex-wrap justify-space-around">
              <v-btn @click="handleSignClick('&times;')" min-width="0" height="unset"  color="orange" elevation="0" class="calc_btn title rounded-md">&times;</v-btn>   
              <v-btn @click="handleSignClick('+')" min-width="0" height="unset"  color="orange" elevation="0" class="calc_btn title rounded-md">+</v-btn>   
              <v-btn @click="handleSignClick('-')" min-width="0" height="unset"  color="orange" elevation="0" class="calc_btn title rounded-md">-</v-btn>   
        </div>
      </div>
      <div class="d-flex flex-row">
              <v-btn @click="handleDigitClick(0)" min-width="0" height="unset"  color="" elevation="0" class="calc_btn title rounded-md flex-grow-1" style="aspect-ratio:unset">0</v-btn>   
              <v-btn @click="handleDecimalClick('.')" min-width="0" height="unset"  color="" elevation="0" class="calc_btn title rounded-md flex-grow-0">.</v-btn>   
              <v-btn @click="handleEqualClick()" min-width="0" height="unset"  color="green accent-4" elevation="0" class="calc_btn title rounded-md flex-grow-0" style="aspect-ratio:unset">=</v-btn>   
      </div>
      
  </v-card>
</v-container>

</template>

<script>
  export default {
    mounted(){
      window.addEventListener("keyup", e => {
        this.handleKeyPress(e);
      });

    },
    data: () => ({
      input:'0',
      shownInput:'0',
      history:[],
      result:null,
    }),
    methods:{
      handleDigitClick(val){
        if(this.result){
          this.input=''
          this.result=null
        }
        if(this.input.toString().length<11){
          this.input=this.input=='0'?val:(this.input+val.toString())
          this.shownInput=this.input
        }
      },
      handleDecimalClick(val){
        if(this.result){
         this.input=''
         this.result=null
       }
        if(this.input.toString().length<11){
          if(!this.input.includes('.')){
            this.input=this.input==''?'0.':(this.input+val.toString())
            this.shownInput=this.input
          }
        }
      },
      handleSignClick(sign){
            if(this.result) this.result=null
            if(this.input!==''){
              if(this.input.toString().endsWith('.')){
                this.input=this.input.slice(0,-1)
              }
              this.history.push(this.input)
              this.input=''
              let result=eval(this.history.join('').replace(/&times;/g,'*').replace(/&divide;/g,'/'))
                let resultLen=result.toString().length
                if(result%1!=0 && resultLen>11){
                      let digitsLength=Math.round(result).toString().length
                      result=result.toFixed(11-(digitsLength<10?digitsLength:2))
                }
                resultLen=result.toString().length
                if(resultLen>12){
                  result=Number.parseFloat(result).toExponential(5)
                }
              this.shownInput=result
              this.history.push(sign)
            }else{
              this.history.splice(this.history.length-1,1,sign)
            }
      },
      handleClear(){
        this.input='0'
        this.shownInput='0'
        this.history=[]
      },
      handleEqualClick(){
        if(this.history.length){
           if(this.input!==''){
              if(this.input.toString().endsWith('.')){
                this.input=this.input.slice(0,-1)
              }
              this.history.push(this.input)
           }
              if(isNaN(this.history.slice(-1)[0])){
                this.history.pop(-1)
              }
          //fetch result from api
          let result=eval(this.history.join('').replace(/&times;/g,'*').replace(/&divide;/g,'/'))
          let resultLen=result.toString().length
          if(result%1!=0 && resultLen>11){
                let digitsLength=Math.round(result).toString().length
                result=result.toFixed(11-(digitsLength<10?digitsLength:2))
          }
          resultLen=result.toString().length
          if(resultLen>12){
            result=Number.parseFloat(result).toExponential(5)
          }
          this.input=result
          this.result=result
          this.history=[]
          this.shownInput=result
        }
      },
      handleKeyPress(event){
        let key=event.key
        let keyCode=event.keyCode
        if(!isNaN(key)){
          this.handleDigitClick(key)
        }else if(keyCode==110){
          this.handleDecimalClick(key)
        }else if([106,107,109,111,].includes(keyCode)){
          this.handleSignClick(key.replace(/\*/g,'&times;').replace(/\//g,'&divide;'))
        }else if(keyCode==13){
          this.handleEqualClick()
        }else if(keyCode==46 || keyCode==8){
          this.handleClear()
        }
      }
    }
  }
</script>

<style scoped>

.calc_btn{
  margin: 8px;
  flex: 1 0 20%;
  border-radius: 25px;
  aspect-ratio: 1/1 ;
}
</style>