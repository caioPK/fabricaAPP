<template>
    <Page >
        <ActionBar title="Home" class="action-bar" />
        <GridLayout columns="*" rows="16, 56, 16, 16, 56, 72" class="dialog-filter">
            <Label text="ORDENAR POR:" class="heading" row="0" />
            <SegmentedBar row="1" v-model="filtro">
                <SegmentedBarItem title="CC" />
                <SegmentedBarItem title="OP" />
                <SegmentedBarItem title="Desc." />
            </SegmentedBar>

            <Label text=" " class="heading" row="2"/>

            <Label text="FILTRAR: " class="heading" row="3"/>
            <TextField v-model="numero" hint="Enter text..." row="4" keyboardType="number"/>
            <Button text="FILTRAR"  row="5" @tap="$modal.close({numero, filtro});"/>
        </GridLayout>
    </Page>
</template>

<script>
export default {
    name: "Filter",
    data () {
        return {
            numero: '',
            filtro: 0,
            itens: ['CC', 'OP', 'Desc']
        };
    },
    method: {
        filtrar(){
                const obj = {
                    filtro: this.filtro,
                    numero: (this.numero === '')?null:this.numero
                }
                this.$modal.close(obj);
        }
    }
}
</script>

<style scoped>
ActionBar {
  background-color: #53ba82;
  color: #ffffff;
  height: 0;
}
.home-panel {
    vertical-align: center;
    font-size: 20;
    margin: 15;
}

.description-label {
    margin-bottom: 15;
}

.dialog-filter{
    padding: 8px;
}
</style>
