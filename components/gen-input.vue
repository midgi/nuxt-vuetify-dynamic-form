<template>
    <div v-if="type=='INPUT'">
        <p class="label">{{ label }}</p>
        <v-text-field            
            v-model="inputValue"
            :rules="rules"
            @input="event=>$emit('change', event)"
        ></v-text-field>
    </div>

    <div v-else-if="type=='SELECT'">
        <p class="label">{{ label }}</p>
        <v-select        
            :items=items
            variant="solo"
            :rules="rules"
            v-model="inputValue"
            item-title="value"
            item-value="id"
            return-object
            @update:modelValue = "event=>$emit('change', inputValue)"
        ></v-select>
    </div>
    

    <!-- <v-checkbox
        v-else-if="type=='CHECKBOX'"
        v-model="inputValue"
        value="1"
        :label="label"
        type="checkbox"
    ></v-checkbox> -->
    <div v-else-if="type=='CHECKBOX'">
        <p class="label">{{ label }}</p>
        <v-radio-group 
            v-model="inputValue"
            inline
            @input="event=>$emit('change', event)"
        >
            <v-radio v-for="radio of items" :label="radio" :value="radio" color="teal-accent-4"></v-radio>
        </v-radio-group>
    </div>
    

    <!-- <div>
        <p>Valor: {{ inputValue }}</p>
    </div> -->

    <!-- <div v-if="error!=null" class="error">
        {{ error }}
    </div> -->
</template>
<script>
import { toRefs, toRef, ref } from 'vue'
export default {
    props: [
        // 'error',
        'rules',
        'items',
        'label',
        'type'
    ],
    emits: [
        'change'
    ],
    setup(props){
        let inputValue = ref('');
        const {type} = toRefs(props);
        if(type=='CHECKBOX') inputValue = ref(1);
        return {
            inputValue: inputValue
        }
    }
}
</script>
<style>
.v-timeline--horizontal .v-timeline-divider{
    width:190%;
}
.label{
    opacity: 0.7;
    font-size: 0.85rem;
    margin-bottom: 0.8rem;
}
</style>