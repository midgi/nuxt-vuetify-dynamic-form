<template>
    <v-card width="600" max-height="700" class="mx-auto my-8 px-6 py-6 overflow-y-auto">
        <div style="width:300px" class="mx-auto">
            <v-timeline direction="horizontal" side="end" align="center">
                <v-timeline-item 
                    :size="stage=='form'?'small':'x-small'"
                    :dot-color="stage=='form'?'green-lighten-2':'grey'"
                >
                    <template v-slot:opposite>
                        <span class="text-subtitle-1" :class="{'text-green-lighten-2': stage=='form', 'text-grey': stage!='form'}">Ingreso de datos</span>
                    </template>
                </v-timeline-item>

                <v-timeline-item 
                    :size="stage=='summary'?'small':'x-small'"
                    :dot-color="stage=='summary'?'green-lighten-2':'grey'"
                >
                    <template v-slot:opposite>
                        <span class="text-subtitle-1" :class="{'text-green-lighten-2': stage=='summary', 'text-grey': stage!='summary'}">Resumen</span>
                    </template>
                </v-timeline-item>
            </v-timeline>
        </div>
        <v-card-title class="text-h6 text-md-h5 text-lg-h4 text-center mb-4">{{ formConfig.name }}</v-card-title>
        <v-card-subtitle class="text-black">{{ formConfig.descripcion }}</v-card-subtitle>
        <v-form class="px-5 py-1" validate-on="submit lazy" @submit.prevent="submit" v-show="stage=='form'">
            <gen-input class="mt-5" v-for="input in inputList"
                :type="input.type"
                :rules="input.rules"
                :label="input.label"
                :items="input.items"
                @change="event=>{input.value = input.type=='SELECT'?ref(event.value):event.target.value; console.dir(event)}"
            ></gen-input>
            <v-btn
                :loading="loading"
                type="submit"
                class="float-right"
                text="Siguiente"
                rounded="lg"
                riple
                color="teal-accent-4"
                variant="flat"
            ></v-btn>
        </v-form>

        <div v-if="stage=='summary'">
            <v-list lines="two">
                <!-- <v-list-subheader>Today</v-list-subheader> -->
                <div v-for="(input, idx) in inputList">
                    <v-list-item
                        :title="input.label"
                        class="text-grey"
                        :key="idx"
                    >
                        <template v-slot:subtitle>
                            <p class="text-black mt-3 ml-3">{{ input.type=='SELECT'?input.value:input.value }}</p>
                        </template>
                    </v-list-item>

                    <v-divider inset :key="idx" class="mt-2 mb-3" v-if="idx!=inputList.length-1"></v-divider>
                </div>
            </v-list>
            <v-btn
                :loading="loading"
                class="float-right mt-4"
                text="Volver"
                rounded="lg"
                riple
                color="teal-accent-4"
                variant="flat"
                @click="()=>stage='form'"
            ></v-btn>
        </div>
    </v-card>
</template>
<script>

export default{
    async setup(){
        const url = 'https://run.mocky.io/v3/6bd01347-72e9-49da-809a-d5002ca63b2c'
        const data = await useFetch(url);
        const formConfig = data.data.value.formulario;
        console.log("form config data: ");console.dir(formConfig)
        function capitalizeFirstLetter(str){
            return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
        }
        let inputList = ref(formConfig.form.map(row=>{
            const input = {
                label: row.question,
                value: row.response,
                type: row.type,
                items: row.items? row.items.map((item=>{
                    if(row.type=='CHECKBOX')
                        return capitalizeFirstLetter(item)
                    item.value = capitalizeFirstLetter(item.value)
                    return item;
                })): null
            }
            return input;
        }))
        return {
            formConfig,
            loading: false,
            inputList,
            stage: ref('form')
        };
    },
    methods: {
        async submit (event) {
            this.loading = true

            const results = await event

            this.loading = false

            console.log(JSON.stringify(results))
            this.stage = 'summary'
        },
    }
}
</script>