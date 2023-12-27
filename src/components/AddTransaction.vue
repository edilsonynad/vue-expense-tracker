<template>
    <h3>Add new transaction</h3>
    <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
            <label for="text">Text</label>
            <input type="text" id="text" v-model="text" placeholder="Enter text..." />
        </div>
        <div class="form-control">
            <label for="amount">Amount <br />
                (negative - expense, positive - income)</label>
            <input type="text" id="amount" v-model="amount" placeholder="Enter amount..." />
        </div>
        <button class="btn">Add transaction</button>
    </form>
</template>

<script setup>
import { ref } from 'vue';
import {useToast} from 'vue-toastification';

const text = ref('');
const amount = ref('');

//Create the event emiter
const emit = defineEmits(['trasactionSubmitted']);

const toast = useToast();

const onSubmit = () => {
    if(!text.value || !amount.value){
        toast.error('Both fields must be filled!');
        return;
    }

    //Create the data to send in the event 
    const transactionData = {
        text: text.value,
        amount: parseFloat(amount.value)
    };

    //Emit the event to parent component
    emit('trasactionSubmitted', transactionData);

    text.value = "";
    amount.value ="";
}
</script>