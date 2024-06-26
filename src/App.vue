<template>
    <Header />
    <div class="container">
        <Balance :total="+total"/>
        <IncomeExpense :income="+income" :expense="+expense"/>
        <TransactionList @transactionDeleted="handleTransactionDeleted" :transactions="transactions"/>
        <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
    </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpense from './components/IncomeExpense.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([
        {id: 1, text: 'flowers', amount: -20},
        {id: 2, text: 'supermarket', amount: -90},
        {id: 3, text: 'coffee', amount: 6},
]);

onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if(savedTransactions) {
        transactions.value = savedTransactions;
    }
});

// in browser, inspect: application-tab > storage > local storage
const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0);
});

const income = computed(() => {
    return transactions.value
    .filter((transaction) => (transaction.amount > 0))
    .reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0).toFixed(2);
});

const expense = computed(() => {
    return transactions.value
    .filter((transaction) => (transaction.amount < 0))
    .reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0).toFixed(2);
});

const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount,
    });
    saveTransactionsToLocalStorage();
    toast.success('Transaction added.');
}

const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
}

const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => 
        transaction.id !== id);
    saveTransactionsToLocalStorage();
    toast.success("Transaction deleted.")
}

</script>
