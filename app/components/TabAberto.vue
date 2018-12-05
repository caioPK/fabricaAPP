<template>
  <GridLayout columns="*" rows="56, *">
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

      <Button text="INICIAR" @tap="onButtonTap" col="1" :isEnabled="selecionado"/>

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

    <!--<Button text="Button" @tap="onButtonTap" row="2"/>-->
  </GridLayout>
</template>

<script>
import { isAndroid, device } from "tns-core-modules/platform/platform";
let fetchModule = require("fetch");

export default {
  name: "TabAberto",
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
  created() {
    console.log("eaeeeeeeeee");
  },
  methods: {
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
            // alert({title: "POST Response", message: JSON.stringify(response), okButtonText: "Close"});
          },
          function(error) {
            console.log(JSON.stringify(error));
          }
        );
    },

    onButtonTap(setor) {},
    selecionarTodas() {
      this.selecao = this.filtrado;
    },
    abrirFiltro() {
      this.filtro = true;
    },

    filtrar() {},
    ordenar() {},

    toggleSelecao(args) {
      const vue = this;
      let indexSelecao = -1;
      const encontrado = this.selecao.find(function(item, index) {
        if (item.op === vue.filtrado[args.index].op) {
          indexSelecao = index;
          return;
        }
      });
      if (indexSelecao > -1) {
        console.log("Econtrado " + this.selecao[indexSelecao]);
        this.filtrado[args.index].classe = "list-item";
        this.selecao.splice(indexSelecao, indexSelecao + 1);
      } else {
        this.filtrado[args.index].classe = "list-item-select";
        this.selecao.push(this.filtrado[args.index]);
      }
      console.log("Item with index: " + args.index + " tapped");
      console.log(this.selecao.length);
    }
  },
  watch: {
    // observar mudanças de setor
    setor: function() {
      if (this.setor != null) {
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
</style>
