<template>
  <nb-container>
    <nb-header>
      <nb-body>
        <nb-title class="textCenter">{{title}}</nb-title>
      </nb-body>
    </nb-header>
    <nb-content padder>
      <nb-form>
        <InputWithError :error="$v.form.email.$dirty && !$v.form.email.required"
                        msg="Provide a valid Email!">
          <nb-input v-model="form.email" 
                    placeholder="Email" 
                    :on-blur="() => $v.form.email.$touch()"/>
        </InputWithError>
        <InputWithError :error="$v.form.password.$dirty && !$v.form.password.required" 
                        msg="Password is required">
          <nb-input v-model="form.password" 
                    placeholder="Password" 
                    auto-capitalize="none" 
                    secure-text-entry 
                    :on-blur="() => $v.form.password.$touch()"/>
        </InputWithError>
      </nb-form>
      <view :style="{marginTop:10}">
        <nb-button block :on-press="login">
          <nb-text>Login </nb-text>
        </nb-button>
        <nb-button transparent :on-press="goToRegister">
          <nb-text>Not Registered? Register here</nb-text>
        </nb-button>
      </view>
    </nb-content>
  </nb-container>
</template>

<script>
import { required } from 'vuelidate/lib/validators'
import { Toast } from 'native-base'

export default {
  props:{
    navigation:{
      type: Object
    }
  },
  data(){
    return{
      title:"Login",
      form:{
        email: '',
        password: '' 
      }
    }
  },
  validations: {
    form:{
      email:{ required },
      password:{ required }
    }
  },
  methods:{
    login(){
      this.$v.form.$touch()
      if(!this.$v.form.$invalid){
        this.$store.dispatch('auth/login', this.form)
          .then(res => {
            if(res === false){
              this.customToast("Wrong email or password","danger")
            } else if(res === "timeout"){
              this.customToast("Timeout..!! Check your internet connection","warning")
            } else {
              this.customToast("Login success","success")
              this.navigation.navigate('App')
            }
          })
      }
    },
    goToRegister(){
      this.navigation.navigate('Register')
    },
    customToast(text, type){
      Toast.show({
        text,
        buttonText: "Ok",
        type,
        duration: 3000
      })
    }
  }
}
</script>

<style scoped>
.textCenter{
  align-self: center;
}
</style>