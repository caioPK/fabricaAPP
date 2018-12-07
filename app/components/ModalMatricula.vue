<template>
    <Page >
        <ActionBar title="Home" class="action-bar" />
        <GridLayout columns="*" rows="16, 72, 72" class="dialog-filter">
            <Label text="Digite sua matrícula:" class="heading" row="0" />
            <TextField v-model="numero" hint="nº matrícula..." row="1" keyboardType="number"/>
            <Button text="OK"  row="2" @tap="$modal.close({numero});"/>
        </GridLayout>
    </Page>
</template>

<script>
export default {
    name: "ModalMatricula",
    data () {
        return {
            numero: '',
        };
    },
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
