<template>
    <div class="container page">
        <div class="top-banner"></div> 
        <b-col col class="title-line"><span>登录</span></b-col>
        <b-form-row>
            <b-col lg=6 md=10 sm=12 tag="form" class="border center rounded shadow p-3" @submit="onSubmit">
                <b-form-group label="用户名:" class="mt-2" label-for="usn">
                    <b-form-input id='usn' required v-model="form.userid" type="text" placeholder="输入用户名,以字母开头"/>
                </b-form-group>
                <b-form-group label="密码:" label-for="pwd">
                    <b-form-input id='pwd' required v-model="form.password" type="password" placeholder="请输入密码"/>
                </b-form-group>
                <div v-if="result">
                <b-alert style="height:2rem" :variant="result.result?'success':'danger'" show>
                  <p style="position:relative;top:-0.3rem;font-size:0.8rem">
                    登陆{{result.result?'成功':'失败'}}:
                    <b>{{result.msg}}</b>
                  </p>
                </b-alert>
                </div>
                <b-form-row class="mt-4 pt-2 border-top">
                    <b-col>
                        <b-button class="mt-3" block type="submit" variant="outline-primary">登陆</b-button>
                    </b-col><b-col>
                        <b-button class="mt-3" block @click="$router.push('/')" variant="outline-danger">取消</b-button>
                    </b-col>
                </b-form-row>
            </b-col>
        </b-form-row>
    </div>
</template>
<script>
import Cookies from 'js-cookie';
export default {
    data(){
        return{
            form:{
                userid:'',
                password:''
            },
            result:null
        }
    },
    methods:{
        async onSubmit(evt){
            this.result=null;
            evt.preventDefault();
            try{
                let ret = await this.axios.get("/signin",{
                    params:{
                        userid:this.form.userid,
                        password:this.form.password
                    }
                })
                console.log(ret.data);
                if(!ret.data.success){
                    this.result={
                        ret:false,
                        msg:ret.data.msg
                    }
                    return
                }
                Cookies.set('userid',this.form.userid,{path:'/'});
                Cookies.set('usertype',ret.data.usertype,{path:'/'});
                Cookies.set('username',ret.data.username,{path:'/'});

            }catch(ex){
                this.$bvToast.toast('登录错误', {
                    title: '提示',
                    autoHideDelay: 5000,
                    toaster: 'b-toaster-top-center',
                    appendToast: true,
                    variant:'warning'
                })
                return
            }
            this.$emit("signin");
            this.$router.push({name:"select"})
        }
    }
}
</script>
<style lang="less" scoped>
.page{
    height: 70vh;
}
.top-banner{
    height: 3rem;
}
.title-line{
    &>span{
        font-size: 38px;
    }
}
.center{
    text-align: left;
    margin: 1rem auto 1rem auto;
}
</style>

