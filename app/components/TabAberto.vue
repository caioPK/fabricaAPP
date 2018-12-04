<template>
    <GridLayout columns="*" rows="56, *">
        <GridLayout columns="56, * , 56" row="0">
            <ListView for="i in um" @itemTap="selecionarTodas" col="0" style="height: 56px" separatorColor="transparent"
                      v-show="filtrado">
                <v-template>
                    <Image top="8" left="8" width="24" height="24" src="~/assets/images/select-all.png" class="icon"/>
                </v-template>
            </ListView>

            <Button text="INICIAR" @tap="onButtonTap" col="1" :isEnabled="selecionado"/>

            <ListView for="i in um" @itemTap="abrirFiltro" col="2" style="height: 56px" separatorColor="transparent">
                <v-template>
                    <Image top="8" left="8" width="24" height="24" src="~/assets/images/filter-icon.png" class="icon"/>
                </v-template>
            </ListView>
        </GridLayout>

        <ListView class="list-group" for="country in countries" @itemTap="buscarOps" row="1">
            <v-template>
                <StackLayout orientation="vertical" style="padding-left: 8px">
                    <FlexboxLayout flexDirection="row">
                        <StackLayout orientation="vertical">
                            <Label text="Projeto" class="label-item"/>
                            <Label text="99999" class="info-item"/>
                        </StackLayout>
                        <StackLayout orientation="vertical">
                            <Label text="Nº OP" class="label-item"/>
                            <Label text="999999999999" class="info-item"/>
                        </StackLayout>
                        <StackLayout orientation="vertical">
                            <Label text="Codigo" class="label-item"/>
                            <Label text="999999" class="info-item"/>
                        </StackLayout>
                        <StackLayout orientation="vertical">
                            <Label text="QTD" class="label-item"/>
                            <Label text="999999" class="info-item"/>
                        </StackLayout>
                    </FlexboxLayout>
                    <Label text="DESCRIÇÃO" class="descricao-item"/>
                </StackLayout>
            </v-template>
        </ListView>

        <!--<Button text="Button" @tap="onButtonTap" row="2"/>-->
    </GridLayout>
</template>

<script>
    const http = require("tns-core-modules/http");
    import {isAndroid, device} from "tns-core-modules/platform/platform";
    var fetchModule = require("fetch");

    export default {
        name: 'TabAberto',
        props: {
            setor: String
        },
        data() {
            return {
                um: [1],
                filtrado: false,
                msg: 'Hello World!',
                item: [],
                countries: [{
                    name: "Australia",
                    imageSrc: "https://play.nativescript.org/dist/assets/img/flags/au.png"
                },
                    {
                        name: "Belgium",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/be.png"
                    },
                    {
                        name: "Bulgaria",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/bg.png"
                    },
                    {
                        name: "Canada",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/ca.png"
                    },
                    {
                        name: "Switzerland",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/ch.png"
                    },
                    {
                        name: "China",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/cn.png"
                    },
                    {
                        name: "Czech Republic",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/cz.png"
                    },
                    {
                        name: "Germany",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/de.png"
                    },
                    {
                        name: "Spain",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/es.png"
                    },
                    {
                        name: "Ethiopia",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/et.png"
                    },
                    {
                        name: "Croatia",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/hr.png"
                    },
                    {
                        name: "Hungary",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/hu.png"
                    },
                    {
                        name: "Italy",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/it.png"
                    },
                    {
                        name: "Jamaica",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/jm.png"
                    },
                    {
                        name: "Romania",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/ro.png"
                    },
                    {
                        name: "Russia",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/ru.png"
                    },
                    {
                        name: "United States",
                        imageSrc: "https://play.nativescript.org/dist/assets/img/flags/us.png"
                    }
                ],
                selecionado: false
            }
        },
        created() {
            console.log("eaeeeeeeeee");
        },
        methods: {
            isEmulator() {
                if (device.model.includes("google_sdk") || device.model.includes("Emulator")) {
                    return true;
                }
                return false;
            },
            buscarOps() {
                const localnetwork = "192.168.0.210";
                const localhost = isAndroid ? (this.isEmulator() ? "10.0.2.2" : localnetwork) : "localhost";
                console.log('CONSULTA ---------');
                const url = "http://" + localhost + ":4000/api/user";
                const path = "http://192.168.0.210:4000/api/user";
                                const categoria = this.setor;


                fetchModule.fetch(url, {
                        method: "POST",
                    headers: {"Content-Type": "application/json"},
                    body: JSON.stringify({
                        cat: categoria
                    })
                })
                    .then(function(response) {
                        console.log(response)
                        // alert({title: "POST Response", message: JSON.stringify(response), okButtonText: "Close"});
                    }, function(error) {
                        console.log(JSON.stringify(error));
                    })
                // .then((r) => {
                //     r.json();
                //     console.log(r);
                //     // console.log(r._bodyInit);
                // })
                //     .then((response) => {
                //         console.log("Header");
                //         console.log(response);
                //     }).catch((e) => {
                // });

//                 const categoria = this.setor;
//                 http.request({
//                     url: url,
//                     method: "GET",
//                     // headers: {"Content-Type": "application/json"},
//                     // content: JSON.stringify({
//                     //     cat: categoria,
//                     // })
//                 }).then(response => {
// //                      C2_CC
//                     console.log('RESPOSTA ---------');
//                     response.forEach(item => {
//                         console.log(item.C2_CC);
//                     });
//                     console.error(response);
//                 }, error => {
//                     console.error(error);
//                 });
            },
            onButtonTap(setor) {
                this.setor = setor;
                this.index = 1;
                console.log(setor + "  " + this.index)
            },
            selecionarTodas() {

            },
            abrirFiltro() {
                this.filtrado = true;
            },
            filtrar() {

            }
        },
        watch: {}

    }
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

    .info-item {
        font-size: 16px;
        margin-right: 8px;
    }

    .descricao-item {
        font-size: 16px;
    }

    .label-item {
        font-size: 12px;
        font-weight: bold;
    }
</style>
