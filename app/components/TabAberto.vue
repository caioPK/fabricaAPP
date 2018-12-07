<template>
  <GridLayout columns="*" rows="*">
    <GridLayout columns="*" rows="*"  v-show="(items.length === 0 && !isBusy)">
      <Label text="Não há nada aqui,por favor selecione um setor" textWrap="true"  class="container-aviso"/>
    </GridLayout>
    <GridLayout columns="*" rows="56, *" v-show="(items.length > 0 || isBusy)">
      <GridLayout columns="56, * , 56" row="0">
        <ListView
          for="i in um"
          @itemTap="selecionarTodas"
          col="0"
          style="height: 56px"
          separatorColor="transparent"
          v-show="filtro"
        >
          <v-template>
            <Image
              top="8"
              left="8"
              width="24"
              height="24"
              src="~/assets/images/select-all.png"
              class="icon"
            />
          </v-template>
        </ListView>

        <Button
          :text="'INICIAR ('+ selecao.length +')'"
          @tap="iniciarOPS"
          col="1"
          :isEnabled="selecao.length > 0"
        />

        <ListView
          for="i in um"
          @itemTap="abrirFiltro"
          col="2"
          style="height: 56px"
          separatorColor="transparent"
        >
          <v-template>
            <Image
              top="8"
              left="8"
              width="24"
              height="24"
              src="~/assets/images/filter-icon.png"
              class="icon"
            />
          </v-template>
        </ListView>
      </GridLayout>

      <ActivityIndicator :busy="isBusy" class="activity-indicator" row="1"/>
      <ListView class="list-group" for="op in filtrado" @itemTap="toggleSelecao" row="1">
        <v-template>
          <StackLayout orientation="vertical" :class="op.classe">
            <FlexboxLayout flexDirection="row" justifyContent="space-around">
              <StackLayout orientation="vertical">
                <Label text="Projeto" class="label-item"/>
                <Label :text="op.projeto" class="info-item"/>
              </StackLayout>
              <StackLayout orientation="vertical">
                <Label text="Nº OP" class="label-item"/>
                <Label :text="op.op" class="info-item"/>
              </StackLayout>
              <StackLayout orientation="vertical">
                <Label text="Código" class="label-item"/>
                <Label :text="op.codigo" class="info-item"/>
              </StackLayout>
              <StackLayout orientation="vertical">
                <Label text="QTD" class="label-item"/>
                <Label :text="op.qtd" class="info-item"/>
              </StackLayout>
            </FlexboxLayout>

            <Label :text="op.descricao" class="descricao-item"/>
          </StackLayout>
        </v-template>
      </ListView>

    </GridLayout>
    <!--<Button text="Button" @tap="onButtonTap" row="2"/>-->
  </GridLayout>
</template>

<script>
import { isAndroid, device } from "tns-core-modules/platform/platform";
let fetchModule = require("fetch");
import Detail from "./Filter";
import ModalMatricula from "./ModalMatricula" 

export default {
  name: "TabAberto",
  // components: { Filter },
  props: {
    setor: String
  },
  data() {
    return {
      um: [1],
      isBusy: false,
      filtro: false,
      msg: "Hello World!",
      items: [],
      filtrado: [],
      selecao: [],
      selecionado: false
    };
  },
  methods: {
    irPara(to){
        // this.$showModal(this.$routes[to]);
    },
    isEmulator() {
      if (
        device.model.includes("google_sdk") ||
        device.model.includes("Emulator")
      ) {
        return true;
      }
      return false;
    },
    buscarOps() {
      // VERFICANDO QUAL O LOCAL HOST DO DISPOSITIVO
      const localnetwork = "192.168.0.210";
      const localhost = isAndroid
        ? this.isEmulator()
          ? "10.0.2.2"
          : localnetwork
        : "localhost";
      console.log("CONSULTA ---------");
      // MONTANDO URL PARA CONSULTA
      const url = "http://" + localhost + ":4000/api/user";
      // RECUPERANDO A CATEGORIA
      const categoria = this.setor;
      this.isBusy = true;
      const vue = this;
      vue.items = [];

      // FAZENDO REQUEST
      fetchModule
        .fetch(url, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            cat: categoria
          })
        })
        .then(
          function(response) {
            console.log("---------- response");
            const itens = JSON.parse(response._bodyInit);
            vue.isBusy = false;
            itens.forEach(element => {
              const novaOP = {
                projeto: element.C2_CC.startsWith("0105")
                  ? element.C2_CC.substring(4)
                  : element.C2_CC,
                op: element.C2_NUM + element.C2_ITEM + element.C2_SEQUEN,
                codigo: element.C2_PRODUTO,
                qtd: element.C2_QUANT,
                descricao: element.B1_DESC, // B1_DESC
                classe: "list-item"
              };
              vue.items.push(novaOP);
            });
            vue.filtrado = vue.items;
          },
          function(error) {
            console.log(JSON.stringify(error));
          }
        );
    },

    iniciarOPS() {
      const vue = this;
        // PEDIR MATRICULA
        this.$showModal(ModalMatricula).then(
          data => {
          // VERIFICAR SE PREENCHIDA
            if( data.numero === ''){
              alert({title: "AÇÃO NECESSÁRIA", message: "Você deve fornecer seu número de matrícula", okButtonText: "ok"}).then( () =>{
                  vue.iniciarOPS()
                }
              );
            }else{
                // MONTAR OBJETO
                const obj = {
                  matricula: data.numero,
                  listaOps: vue.selecao
                }
                // EMITIR EVENTO COM OBJETO
                vue.$emit("iniciar", obj);
                this.limparSelecoes();
            }
        });
      
    },
    // -------------------------------------
    // ------------- FUNÇÕES DE SELEÇÃO
    // -------------------------------------
    limparSelecoes(){
      this.selecao=[];
      this.filtrado=[];
      this.items=[];
      this.filtro=false;
    },
    selecionarTodas() {
      const vue = this; 
      if (this.selecao.length > 0){
          vue.selecao=[];
        this.filtrado.forEach(function(item) {
          item.classe = "list-item";
        });
      }else{
        this.filtrado.forEach(function(item) {
          item.classe = "list-item-select";
          vue.selecao.push(item);
        });
      }
    },
    removeItem(indexSelecao){
        this.selecao.splice(indexSelecao, 1);
    },

    toggleSelecao(args) {
      const vue = this;
      let indexSelecao = -1;

      this.selecao.find(function(item, index) {
        if (item.op === vue.filtrado[args.index].op) {
          indexSelecao = index;
          return;
        }
      });
      if (indexSelecao > -1) {
          vue.removeItem(indexSelecao);
          this.filtrado[args.index].classe = "list-item";
      } else {
        this.filtrado[args.index].classe = "list-item-select";
        this.selecao.push(this.filtrado[args.index]);
      }
    }, 

    // -------------------------------------
    // ------------- FUNÇÕES DE FILTRO
    // -------------------------------------
    abrirFiltro() {
      this.$showModal(Detail).then(
        data => {
          const vue = this;
           if (data.numero === ''){
             if(this.filtrado < this.items){
             // retirar todos os filtros
             this.filtrado = [];
               this.items.forEach((item)=>{
                 // add todos os items de volta
                  vue.filtrado.push(item);
               });
             }
             // ordenar os items
             this.ordenar(data.filtro);
           }else{
             this.filtrar(data.numero, data.filtro);
           }
          this.filtro = true;
        }
      );
    },

    filtrar(numero, ordernar) {
      this.filtrado = [];
      const vue = this;
      let processados = 2;
      this.items.forEach(function(item){
          if(item.projeto === numero){
              vue.filtrado.push(item);
          }
      });
      vue.ordenar(ordernar);
    },

    // -------------------------------------
    // ------------- FUNÇÕES DE ORDENAÇÃO
    // -------------------------------------

    ordenar(data) {
      switch (data) {
               case 0:
                 console.log("CC");
                 this.ordenarByCC();
                 break;
               case 1:
                 console.log("OP");
                 this.ordenarByOP();
                 break;
                case 2:                 
                 console.log("DESC");
                 this.ordenarByDESC();
                 break;
               default:
                 console.log("CC");
                 this.ordenarByCC();
                 break;
             }
    },
    ordenarByCC(){
      this.filtrado.sort(function(a, b){
        if (a.projeto > b.projeto) {
          return 1;
        }
        if (a.projeto < b.projeto) {
          return -1;
        }
        return 0;
      });
    },
     ordenarByOP(){
      this.filtrado.sort(function(a, b){
        if (a.op > b.op) {
          return 1;
        }
        if (a.op < b.op) {
          return -1;
        }
        return 0;
      });
    },
     ordenarByDESC(){
      this.filtrado.sort(function(a, b){
        if (a.descricao > b.descricao) {
          return 1;
        }
        if (a.descricao < b.descricao) {
          return -1;
        }
        return 0;
      });
    },
    
  },

  watch: {
    // observar mudanças de setor
    setor: function() {
      console.log(this.setor)
      if (this.setor != null) {
        this.limparSelecoes();
        this.buscarOps();
      }
    }
  }
};
</script>

<style scoped>
ActionBar {
  background-color: #53ba82;
  color: #ffffff;
  height: 0;
}

.icon {
  margin-top: 16px;
  margin-bottom: 16px;
}
.list-item {
  padding-left: 8px;
  padding-right: 8px;
  background: #ffffff;
}
.list-item-select {
  padding-left: 8px;
  padding-right: 8px;
  background: #c9c9c9;
}

.info-item {
  font-size: 14px;
  margin-right: 8px;
}

.descricao-item {
  font-size: 14px;
}

.label-item {
  font-size: 10px;
  font-weight: bold;
}
.container-aviso{
  font-size: 24px; 
  text-align:center;
  vertical-align: center;
  padding: 8px;
}
</style>
