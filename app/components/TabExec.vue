<template>

  <GridLayout columns="*" rows="*">
        <GridLayout columns="*" rows="*" v-show="lista.listaOps.length === 0" >
            <Label text="Não exitem atividades em execução." textWrap="true"  class="container-aviso"/>
        </GridLayout>
        <GridLayout columns="*" rows="24, 48, 1, 24, *, 64" class="container-exec" v-show="lista.listaOps.length > 0">

            <GridLayout orientation="horizontal" row="0" columns="*, *">
                <Label text="INICIO:" class="text-heading"  col="0"/>
                <Label text="FIM:" class="text-heading"    col="1"/>
            </GridLayout>
        
            <GridLayout orientation="horizontal" row="1" rows="*" columns="*, 1, *">
                <Label  :text="(stringInicio==='') ? '-- : --' : stringInicio" class="text-horas" col="0" />
                <Label text="" class="text-horas" col="1" backgroundColor="lightgray" />
                <Label  :text="(stringFim==='') ? '-- : --' : stringFim" class="text-horas" col="2"/>
            </GridLayout>

            <Label text="" class="separador" row="2" />
            
            <Label :text="'OPs: (' + lista.listaOps.length + ' )'" class="text-heading" row="3" />
            
            <ListView class="list-group-exec" for="op in lista.listaOps" row="4">
              <v-template>
                  <FlexboxLayout flexDirection="row" height="24" class="list-group-exec-item">
                    <Label :text="op.op" class="heading"  />
                  </FlexboxLayout>
              </v-template>
            </ListView>

            <Button row="5" text="FINALIZAR" class="botao-finalizar" @tap="finalizarOps" />
            
        </GridLayout>
  </GridLayout>
    
</template>

<script>
export default {
  name: "TabExec",
  props: {
    lista: Object
  },
  data() {
    return {
      index: 0,
      msg: "Hello World!",
      inicio: null,
      stringInicio:'',
      stringFim:''
    };
  },
  mounted() {  
  },
  methods: {
    finalizarOps() {
      const tempo = new Date();
      this.stringFim = tempo.getHours() + ":" + tempo.getMinutes();
      const inicio = this.stringInicio;
      const fim = this.stringFim;
      const vue = this;
      confirm({
        title: "Finalizar?",
        message: "Deseja mesmo finalizar as Ops em execução? \n Inicio: " + inicio + "\n Fim: " + fim + ".",
        okButtonText: "SIM",
        cancelButtonText: "NÃO"
      }).then(result => {
        console.log(result);
        if(result){
          vue.$emit('finalizar');
        }else{
          vue.stringFim = '';
        }
      });
    }    
  },
  watch: {
    // MUDOU A LISTA DE EXECUÇAO
    lista: function () {
      this.stringFim = '';
      console.log("MUDOU LISTA");
      // VERIFICAR SE VAZIA
      if (this.lista.listaOps.length > 0){
      // GET TEMPO
      const tempo = new Date();
      this.stringInicio = tempo.getHours() + ":" + tempo.getMinutes();
      // SALVAR NA MEMORIA
      }
    }
  }
};
</script>

<style scoped>
  .container-exec{
    padding: 8px 16px;
  }
  .text-horas{
    font-size: 36px;
    text-align: center;
  }
  .text-heading{
    font-size: 18px;
    font-weight: bold;
    text-align: center;
  }
  .separador{
    background-color: #c9c9c9;
    margin: 0px 12px;
  }
  .list-group-exec{
    background-color: #c9c9c9;
    padding: 0px 8px;
    margin-top: 8px;
    border-radius: 5px;
  }
  .botao-finalizar{
    background-color: red;
    font-weight: bold;
    color: #fff;
  }
  .list-group-exec-item{
    height: 32px;
  }
  .container-aviso{
    font-size: 24px; 
    text-align:center;
    vertical-align: center;
    padding: 8px;
  }

</style>
