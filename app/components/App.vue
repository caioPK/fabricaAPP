<template>
  <Page ref="page" id="page">
    <ActionBar title="Welcome to NativeScript-Vue!" android:flat="true"/>
    <TabView
      android:tabBackgroundColor="#53ba82"
      android:tabTextColor="#c4ffdf"
      android:selectedTabTextColor="#ffffff"
      androidSelectedTabHighlightColor="#ffffff"
      ref="tabMenu"
      id="tabMenu"
      :selectedIndex="index"
      @selectedIndexChange="mudou"
    >
      <TabViewItem title="SETOR">
        <ScrollView orientation="vertical">
          <GridLayout
            columns="* , *"
            rows="150, 150, 150"
            backgroundColor="lightgray"
            class="container-setor"
          >
            <Button
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
        <tab-aberto :setor="setorEscolhido"></tab-aberto>
      </TabViewItem>
      <TabViewItem title="EM EXECUÇÃO">
        <GridLayout columns="*" rows="*">
          <Label class="message" text="Tab 3 Content" col="0" row="0"/>
        </GridLayout>
      </TabViewItem>
    </TabView>
  </Page>
</template>

<script>
import TabAberto from "./TabAberto";
export default {
  components: { TabAberto },
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
      msg: "Hello World!"
    };
  },
  created() {
    console.log("eaeeeeeeeee");
  },
  methods: {
    mudou(value) {
      console.log(value.value);
      this.index = value.value;
    },
    onButtonTap(setor) {
      this.setorEscolhido = setor;
      this.index = 1;
      console.log(setor + "  " + this.index);
    }
  },
  watch: {
    index: function() {
      console.log("mudei");
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
  background: burlywood;
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
</style>
