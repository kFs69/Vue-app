<template>
    <div class="home">
        <v-alert
            class="alert"
            v-model="alert"
            dismissible
            color="error"
            icon="mdi-alert-circle"
            transition="slide-x-reverse-transition"
            dark
        >
            Erro ao encontrar sua conta no servidor!
        </v-alert>
        <v-container fluid fill-height>
            <v-row align="center" justify="center">
                <v-col class="text-center">
                    <v-card
                        class="mx-auto"
                        max-width="500"
                        style="border-radius: 10px"
                    >
                        <v-row align="center" justify="center">
                            <v-col cols="10" md="10" class="forms_input_mobile_index">
                              <br>
                                <v-text-field
                                    label="Usuário"
                                    :rules="fieldRules"
                                    v-model="userInput"
                                    id="usuario_field"
                                    @keydown.enter="logar"
                                    outlined
                                ></v-text-field> 
                            </v-col>
                            <v-col cols="10" class="forms_input_mobile_index2">
                                <v-text-field
                                    label="Senha"
                                    type="password"
                                    :rules="fieldRules"
                                    v-model="passwordInput"
                                    id="senha_field"
                                    @keydown.enter="logar"
                                    outlined
                                ></v-text-field>
                            </v-col>
                        </v-row>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                text
                                @click="logar"
                            >
                                Entrar
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-col>
            </v-row>
        </v-container>
    </div>
</template>

<style scoped>
.alert{
    position: fixed; 
    right: 5px; 
    bottom: 5px; 
    max-width: 500px; 
    width: calc(100% - 10px); 
    z-index: 1000000;
}
.forms_input_mobile_index{
    transform: translateY(0px);
}
.forms_input_mobile_index2{
    transform: translateY(-30px);
}
@media screen and (max-width: 960px){
    .forms_input_mobile_index{
        transform: translateY(-25px)
    }
    .forms_input_mobile_index2{
        transform: translateY(-55px);
    }
}
.home{
    height: 100%;
    background-color: #28396b;
}
</style>

<script>
import api from '@/plugins/apisauce.js';
import router from '@/router/index.js'

export default {
    name: 'Home',
    data: () => ({
        fieldRules: [
            r => (r && r.length > 0) || "Preencha esse campo."
        ],
        userInput: null,
        passwordInput: null,
        alert: false,
    }),
    methods: {
        logar(){
            if(!this.userInput)      return document.getElementById("conta_field").focus();
            if(!this.passwordInput)    return document.getElementById("usuario_field").focus();

            api.post("/login",{
                user: this.userInput,
                password: this.passwordInput
            }).then(result=>{
                if(result.ok) {
                    localStorage.setItem("user",result.data.user.user);
                    localStorage.setItem("id",result.data.user.id);
                    localStorage.setItem("name",result.data.user.name);
                    localStorage.setItem("token",result.data.token);

                    router.push('dashboard');
                }else this.alert = true;
            })
        },
    },
}
</script>
