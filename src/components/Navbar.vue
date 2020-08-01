<template>
    <div>
      <v-app-bar
        color="dark"
        dark
      >
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

        <v-toolbar-title>Kaioflix</v-toolbar-title>
      </v-app-bar>
      <v-navigation-drawer
        v-model="drawer"
        absolute
        temporary
      >
        <v-list
          nav
          dense
        >
          <v-list-item-group
            v-model="group"
            active-class="deep-purple--text text--accent-4"
          >
            <v-list-item>
              <v-list-item-title>Adicionar Filme</v-list-item-title>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-navigation-drawer>
    </div>
</template>

<script>
import router from '@/router/index.js';

export default {
    name: 'Navbar',
    data: () => ({
        drawer: window.innerWidth > 900,
        items: [],
        menu: [{
          name: "Adicionar filme",
          page: "Add"
        },
        {
          name: "Atualizar filme",
          page: "Update"
        }],
        singleItems: [{
            icon: `mdi-view-dashboard`, 
            text: `Dashboard`, 
            link: `/dashboard`
        }],
        usuario: {},
        customization: {},
        router: {}
    }),
    methods: {
        logout(){
            localStorage.removeItem("usrId");
            router.push("/");   
        },
        back(){
            router.push(`/CRUD/${this.router.params.nameType}`);
        },
        menu(){
            const menu = JSON.parse(localStorage.getItem("menu"));
            
            let items = [];
            
            menu.forEach(i=>{
                const parent = items.map(it=>it.id).indexOf(i.parentID);
                if(parent >= 0)
                    items[parent].items.push({
                        icon: `mdi-${i.iconevue}`, 
                        text: `${i.caption}`, 
                        link: `/${i.formVue}`
                    });
                else
                    items.push({
                        icon: `mdi-${i.iconevue}`, 
                        text: `${i.caption}`, 
                        link: `/${i.formVue}`,
                        items: [],
                        id: i.idacao
                    });
            })

            items.forEach(im=>{
                if(im.items && im.items.length > 0)
                    this.items.push(im);
                else
                    this.singleItems.push(im);
            });
        }
    },
    created() {
      this.drawer = false;
    },
    mounted(){
        this.router = this.$route;

        this.customization = JSON.parse(localStorage.getItem("customization"));
        
        this.usuario = {
            nome: localStorage.getItem("nome"),
            login: localStorage.getItem("login")
        };
        this.usuario.displayName = this.usuario.nome.length <= 16 ? this.usuario.nome : this.usuario.nome.substring(0,16) + "...";
        
        document.getElementById("app").style.backgroundColor = this.customization.backgroundColor;
        document.body.style.backgroundColor = this.customization.backgroundColor;
        
        if(JSON.parse(localStorage.getItem("menu")) == null)
            setTimeout(() => {
                this.menu();
            }, 1000);
        else this.menu();
          
    }
}
</script>