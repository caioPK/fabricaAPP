<template>
  <Page ref="page" id="page">
    <ActionBar title="Welcome to NativeScript-Vue!" android:flat="true"/>
    <TabView
      android:tabBackgroundColor="#e6b800"
      android:tabTextColor="#997a00"
      android:selectedTabTextColor="#ffffff"
      androidSelectedTabHighlightColor="#ffffff"
      ref="tabMenu"
      id="tabMenu"
      :selectedIndex="index"
      @selectedIndexChange="mudou"
    >
      <TabViewItem title="SETOR" 
      >
       
        <ScrollView orientation="vertical">
          <GridLayout
            columns="* , *"
            rows="150, 150, 150"
            class="container-setor"
          >
            <GridLayout columns="*" rows="*" v-show="emExec" colSpan="2" rowSpan="3" >
              <Label text="Já existe atividades em execução" textWrap="true"  class="container-aviso"/>
            </GridLayout>
            <Button v-show="!emExec"
              v-for="setor in setores"
              :text="setor.nome"
              :key="setor.nome"
              :row="setor.x"
              :col="setor.y"
              class="botao-setor"
              @tap="onButtonTap(setor.cat)"
            />
          </GridLayout>
        </ScrollView>
      </TabViewItem>
      <TabViewItem title="EM ABERTO">
        <GridLayout columns="*" rows="*" >
                <tab-aberto v-show="!emExec" :setor="setorEscolhido" @iniciar="iniciarExec" ></tab-aberto>
                <Label v-show="emExec" textWrap="true"  text="Já existe atividades em execução" class="container-aviso"/>
          </GridLayout>
      </TabViewItem>
      <TabViewItem title="EM EXECUÇÃO">
        <tab-exec :lista="executando" @finalizar="finalizarExec"></tab-exec>
      </TabViewItem>
    </TabView>
  </Page>
</template>

<script>
import TabAberto from "./TabAberto";
import TabExec from "./TabExec";
export default {
  components: { TabAberto, TabExec },
  data() {
    return {
      setorEscolhido: "",
      setores: [
        {
          nome: "CORTE",
          x: 0,
          y: 0,
          cat: "PUN001"
        },
        {
          nome: "DOBRA",
          x: 0,
          y: 1,
          cat: "DOB001"
        },
        {
          nome: "SOLDA",
          x: 1,
          y: 0,
          cat: "SOL001"
        },
        {
          nome: "ACABAMENTO",
          x: 1,
          y: 1,
          cat: "ACB001"
        },
        {
          nome: "PINTURA",
          x: 2,
          y: 0,
          cat: "PIT001"
        },
        {
          nome: "OUTROS",
          x: 2,
          y: 1,
          cat: "OUTROS"
        }
      ],
      index: 0,
      executando:{
        matricula: '',
        listaOps: []
      },
      emExec: false,
      msg: "Hello World!"
    };
  },
  created() {
    console.log("eaeeeeeeeee");
  },
  methods: {
    mudou(value) {
    },
    onButtonTap(setor) {
      this.setorEscolhido = setor;
      this.index = 1;
      console.log(setor + "  " + this.index);
    },
    iniciarExec(obj){
      // PASSAR OBJETO PARA EXEC
      this.executando = obj;
      // MUDAR ABA
      this.index=2;
      // TRAVAR ABAS
      this.emExec = true;
    },
    finalizarExec(){
      console.log('FINALIZANDO');
      this.emExec = false;
      this.index = 0;
      this.executando={
        matricula: '',
        listaOps: []
      };
    }
  },
  watch: {
    index: function(){
      
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
.container-setor {
  padding: 8px;
}
.botao-setor {
  background: #e6b800;
  font-size: 18px;
}
.container-botao {
  padding: 4px;
}
.message {
  vertical-align: center;
  text-align: center;
  font-size: 20px;
  color: #333333;
}
.container-aviso{
  font-size: 24px; 
  text-align:center;
  vertical-align: center;
}
</style>
