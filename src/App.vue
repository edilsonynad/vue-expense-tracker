<template>
    <Header />
    <div class="container">
        <Balance 
            :total="total"
        />

        <IncomeExpenses 
            :income="+income" 
            :expenses="+expenses"
        />

        <TransactionList  
            :transactions="transactions" 
            @transactionDeleted="handleTrasactionDeleted"
        />

        <AddTransaction 
            @trasactionSubmitted="handleTrasactionSubmitted"
        />

    </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast} from 'vue-toastification';
import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([]);

onMounted(()=>{
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if(savedTransactions){
        transactions.value = savedTransactions;
    }
})

//Get total
const total = computed(()=>{
    return transactions.value.reduce((acc, transaction)=>{
        return acc + transaction.amount;
    }, 0);
});

//Get income
const income = computed(()=>{
    return transactions.value
    .filter((transaction)=> transaction.amount > 0)
    .reduce((acc, transaction)=>{
        return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Get expenses
const expenses = computed(()=>{
    return transactions.value
    .filter((transaction)=> transaction.amount < 0)
    .reduce((acc, transaction)=>{
        return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Added the inserted data to the transaction object 
const handleTrasactionSubmitted = (transactionData) =>{
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    });

    savedTransactionToLocalStorage();

    toast.success('Expenss added with success');
}

//Generate unique ID
const generateUniqueId = () =>{
    return Math.floor(Math.random() * 10000);
}

//Delete item from transaction 
const handleTrasactionDeleted = (id) =>{
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

    savedTransactionToLocalStorage();

    toast.success('Expense deleted from list!!');
}

//Save to local storage
const savedTransactionToLocalStorage = () =>{
    localStorage.setItem("transactions", JSON.stringify(transactions.value));
}

</script>